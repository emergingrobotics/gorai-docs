# Go Channel Fan-Out Pattern

**Status**: Required  
**Applies to**: Any component or service that exposes a Go channel via a method like `Events()`, `Stream()`, or `Frame()`

---

## Problem

Go channels deliver each message to exactly **one** reader. When a method like `Events(ctx) (<-chan T, error)` returns the same internal channel to multiple callers, each event is randomly consumed by a single reader while all others miss it.

This caused a production bug where `velocity_input` and `keypress_motor_controller` both called `remote_keyboard.Events()`. Key release events were non-deterministically consumed by the wrong service, leaving phantom "pressed" keys in `velocity_input`'s state that eventually cancelled each other out, driving velocity to zero.

## Rule

**Every method that returns a `<-chan T` to callers MUST create a new channel per call and broadcast incoming data to all registered channels.**

Never return a shared internal channel. This applies to components, services, and any package that exposes event streams.

---

## Implementation

### Subscriber struct

```go
type eventSubscriber struct {
    ch  chan T
    ctx context.Context
}
```

### Component fields

Replace a single `eventCh` with a subscriber registry:

```go
type MyComponent struct {
    // ...

    subscribers   []*eventSubscriber
    subscribersMu sync.Mutex
    closed        bool
}
```

### Events() -- create a unique channel per caller

```go
func (c *MyComponent) Events(ctx context.Context) (<-chan T, error) {
    ch := make(chan T, bufferSize)
    sub := &eventSubscriber{ch: ch, ctx: ctx}

    c.subscribersMu.Lock()
    if c.closed {
        c.subscribersMu.Unlock()
        close(ch)
        return nil, fmt.Errorf("component is closed")
    }
    c.subscribers = append(c.subscribers, sub)
    c.subscribersMu.Unlock()

    go c.watchSubscriberContext(sub)
    return ch, nil
}
```

### Broadcast -- deliver to every subscriber

```go
func (c *MyComponent) sendEvent(event T) {
    c.subscribersMu.Lock()
    defer c.subscribersMu.Unlock()

    if c.closed {
        return
    }

    for _, sub := range c.subscribers {
        select {
        case sub.ch <- event:
        default:
            // Buffer full -- drop oldest, then retry
            select {
            case <-sub.ch:
            default:
            }
            select {
            case sub.ch <- event:
            default:
            }
        }
    }
}
```

### Context-based cleanup

When a caller's context is cancelled, remove its subscriber and close the channel so the goroutine reading from it sees the close and exits cleanly:

```go
func (c *MyComponent) watchSubscriberContext(sub *eventSubscriber) {
    <-sub.ctx.Done()
    c.removeSubscriber(sub)
}

func (c *MyComponent) removeSubscriber(sub *eventSubscriber) {
    c.subscribersMu.Lock()
    defer c.subscribersMu.Unlock()

    for i, s := range c.subscribers {
        if s == sub {
            c.subscribers = append(c.subscribers[:i], c.subscribers[i+1:]...)
            close(sub.ch)
            return
        }
    }
}
```

### Close -- tear down all subscribers

```go
func (c *MyComponent) Close(ctx context.Context) error {
    c.subscribersMu.Lock()
    c.closed = true
    for _, sub := range c.subscribers {
        close(sub.ch)
    }
    c.subscribers = nil
    c.subscribersMu.Unlock()

    return nil
}
```

---

## When this pattern is NOT needed

- **NATS subscriptions**: NATS delivers messages to all subscribers independently. Components that expose data solely through NATS topics do not need fan-out.
- **Single-consumer internal channels**: Channels used within a single goroutine pipeline (e.g., a worker pool) that are never exposed to callers are fine as-is.
- **Methods that create a new channel per call already**: For example, `v4l2.Camera.Stream()` creates a fresh channel and goroutine each time. This is already correct.

## When this pattern IS needed

- Any public method signature of the form `Foo(ctx) (<-chan T, error)` where the returned channel could be called more than once across the component's lifetime.
- Keyboard `Events()`, camera `Stream()`, sensor data streams, mesh `Events()` -- anything where a second consumer might subscribe.

---

## Components using this pattern

| Component | Method | File |
|-----------|--------|------|
| `RemoteKeyboard` | `Events()` | `gorai/components/input/remote/remote.go` |
| `Keyboard` (local) | `Events()` | `gorai/components/input/keyboard/keyboard.go` |
| `FakeKeyboard` | `Events()` | `gorai/components/input/keyboard/fake/fake.go` |
| `RemoteCamera` | `Stream()` | `gorai/components/camera/remote/remote.go` |

---

## Testing fan-out

Every component that implements this pattern should have a multi-consumer test:

```go
func TestFanOutMultipleConsumers(t *testing.T) {
    // Create component
    // Subscribe twice with independent contexts
    ch1, _ := component.Events(ctx1)
    ch2, _ := component.Events(ctx2)

    // Send one event
    // Assert BOTH ch1 and ch2 receive it

    // Cancel ctx1
    // Assert ch1 is closed
    // Assert ch2 still receives new events
}
```
