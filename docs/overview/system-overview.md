# Gorai System Overview

**A complete picture of the Gorai robotics platform — what it is, how it works, and how the pieces fit together.**

---

## Hardware Products

Gorai is the software platform. These are the hardware products it powers:

- **ORCA** — Autonomous submersible. 2 motors + dive planes, rated to 80ft depth, target price under $2,500. This is the flagship hardware project. There is no competition in this category below $50,000.
- **Surf** — Autonomous surface vessel. Target price under $1,500. Second product.
- **Drive** — Land robot. Deferred. The land robot market is competitive and crowded; Gorai's differentiation is strongest in marine autonomy.

---

## What Gorai Is

Gorai is a Go-based robotics platform for building autonomous robots. It targets the gap between educational kits (Arduino, micro:bit) and enterprise platforms (ROS 2) — the space where makers, citizen scientists, students, and small teams need real autonomy without a PhD in robotics infrastructure.

A Gorai robot is a single Go binary that reads a JSON configuration file, connects to a NATS message broker, and brings up components (sensors, motors, cameras) and services (navigation, behaviors, vision) that communicate through NATS subjects. That's the whole mental model.

```
robot.json  →  gorai run  →  Robot running
```

The framework is designed around three convictions:

1. **Robots are distributed systems.** Even a single robot has MCUs, SBCs, accelerators, and sometimes base stations. The programming model should assume this from the start.
2. **Autonomy is a spectrum.** From scripted state machines to learned models to LLM-driven agents — all should work through the same interfaces.
3. **Simple things should be simple.** A GPS tracker should take 20 minutes, not 20 hours. Complexity should be opt-in.

---

## Architecture

### The Layers

Gorai is organized into five layers. Each layer depends only on the layers below it.

```
┌─────────────────────────────────────────────────────────────┐
│  Application Layer                                          │
│  Behaviors, Coordinators, Missions, AI/ML Services          │
├─────────────────────────────────────────────────────────────┤
│  Communication Layer                                        │
│  NATS Pub/Sub, Request/Reply, JetStream, KV Store           │
├─────────────────────────────────────────────────────────────┤
│  Resource Layer                                             │
│  Components (Sensor, Motor, Camera, Servo, GPS, IMU...)     │
│  Services (Vision, Navigation, SLAM, Behavior, Coordinator) │
├─────────────────────────────────────────────────────────────┤
│  Acceleration Layer                                         │
│  Rockchip NPU, NVIDIA CUDA, Coral TPU, Hailo               │
├─────────────────────────────────────────────────────────────┤
│  Hardware Layer                                             │
│  GPIO, I2C, SPI, Serial/UART, USB, GSP/2 Protocol           │
└─────────────────────────────────────────────────────────────┘
```

### The Resource Model

Everything in Gorai is a **Resource**. Every resource implements the same base interface:

```go
type Resource interface {
    Name() resource.Name
    Reconfigure(ctx context.Context, deps Dependencies, conf Config) error
    DoCommand(ctx context.Context, cmd map[string]any) (map[string]any, error)
    Close(ctx context.Context) error
}
```

Resources come in two flavors:

- **Components** abstract hardware. A `Sensor` reads data (`Readings()`). An `Actuator` moves things (`Stop()`, `IsMoving()`). Specialized types like `Motor`, `Servo`, `Camera`, `IMU`, and `GPS` extend these with domain-specific methods.
- **Services** are software capabilities. A `VisionService` detects objects. A `Behavior` makes decisions. A `Coordinator` orchestrates multi-step missions.

All implementations register themselves at init time:

```go
func init() {
    registry.RegisterComponent("sensor", "gps-nmea", New)
}
```

The runtime instantiates them from configuration. You never manually wire components together — NATS handles all communication.

### NATS: The Messaging Backbone

NATS is the single communication mechanism in Gorai. Every component, service, and external process talks through NATS subjects.

**Why NATS and not DDS/ZeroMQ/gRPC?**

| Property | NATS | DDS (ROS 2) |
|----------|------|-------------|
| Setup | One binary, one config file | Complex discovery, QoS tuning |
| Performance | 18M msg/sec | Varies by implementation |
| Memory | ~10-20MB | 100MB+ with middleware |
| Persistence | JetStream built-in | rosbag (separate tool) |
| KV Store | Built-in | Not available |
| Client libraries | 40+ languages | C++, Python, limited others |

Gorai uses NATS for:

- **Pub/Sub** — sensor data streaming, state updates
- **Request/Reply** — RPC calls to components and services
- **Queue Groups** — load-balanced processing
- **JetStream** — persistent streams, event sourcing, replay
- **KV Store** — configuration, service discovery state, schemas
- **Subject hierarchy** — `gorai.<robot>.<component>.<name>.<action>`

### Mesh Service Discovery

Independent processes discover each other at runtime through three NATS JetStream KV buckets:

| Bucket | TTL | Purpose |
|--------|-----|---------|
| `gorai-services` | 30s (heartbeat) | Active service registrations |
| `gorai-channels` | Persistent | NATS subject descriptors |
| `gorai-schemas` | Persistent | JSON Schema for message types |

Services register at startup, heartbeat every 10 seconds, and announce join/leave events. Any process connected to the same NATS server can discover any other process's services, channels, and message schemas.

### Dynamic Discovery

Not everything is declared in configuration. Gateways bridge hardware protocols (GSP/2, Modbus, CAN) to NATS and register devices they find. The discovery manager applies adoption rules to map discovered capabilities to component types, creating proxy components that make remote devices look local:

```
Gateway discovers USB device → Registers in mesh → Discovery manager adopts →
Proxy component created → Service dependencies resolve → Robot operates
```

This creates a hybrid static/dynamic model: RDL defines the structure and rules; the mesh provides runtime state.

---

## Robot Configuration: RDL

Robots are defined in **Robot Definition Language (RDL)** — a JSON file that declares identity, components, services, and their configuration.

```json
{
  "version": "3",
  "robot": {
    "name": "gps-tracker",
    "namespace": "gorai"
  },
  "nats": { "url": "nats://localhost:4222" },
  "components": [
    {
      "name": "gps",
      "type": "serial/gps",
      "config": {
        "device": "/dev/gps-sim",
        "baud_rate": 9600
      }
    }
  ],
  "services": [
    {
      "name": "waypoint-nav",
      "type": "navigation/waypoint",
      "depends_on": ["gps"]
    }
  ]
}
```

RDL defines *what* the robot has and how it's wired — not physical geometry (that's URDF/SDF). The runtime validates the configuration at load time, resolves dependencies, and instantiates everything in the right order.

Key design choices:

- **Explicit over implicit** — everything visible in the file
- **Registry-driven** — types must be registered in code
- **Dependency-aware** — services declare what they need, loaded in order
- **Environment-friendly** — secrets go in environment variables, not inline

---

## Runtime Lifecycle

When you run `gorai run robot.json`, the runtime:

1. **Loads and validates** the RDL configuration
2. **Connects to NATS** (or starts an embedded server)
3. **Resolves dependencies** — topological sort of components and services
4. **Instantiates components** — each registers in the mesh
5. **Instantiates services** — with dependencies injected
6. **Starts the web dashboard** (enabled by default)
7. **Blocks** until SIGTERM/SIGINT
8. **Shuts down gracefully** — stops services, then components, then NATS

The runtime supports **hot reload**: send SIGHUP and it re-reads configuration, reconfiguring changed resources without restarting.

---

## Deployment

### Current and Primary: Simple Binary (Mode 1)

`gorai run` is the deployment model. One Go binary with an embedded NATS server, one JSON config, managed by systemd. This is not a stepping stone toward containers or Kubernetes -- it IS the product. The embedded NATS server means there is no external dependency to install or manage.

| Aspect | Value |
|--------|-------|
| Binary size | ~10-20MB |
| RAM usage | ~20-50MB |
| Dependencies | None (NATS is embedded) |
| Orchestration | systemd |
| Deploy method | Cross-compile + rsync/scp |
| Runtime command | `gorai run robot.json` |

```bash
GOOS=linux GOARCH=arm64 go build -o myrobot ./cmd/myrobot
rsync -avz myrobot robot.json pi@robot.local:/opt/myrobot/
ssh pi@robot.local "sudo systemctl restart myrobot"
```

This model works for every current use case, from GPS trackers to autonomous submersibles running on a Raspberry Pi inside a pressure housing at 80ft depth.

### Future: Progressive Complexity (Deferred)

The architecture is designed to scale without rewriting, but these modes are deferred until user demand requires them. K3s and process-compose were evaluated and deliberately set aside.

- **Mode 2 (Containers):** Deferred. Add Podman containers for Python/C++ services (vision, ML inference). Core remains a native binary. Same NATS backbone.
- **Mode 3 (K3s Fleet):** Deferred. Full Kubernetes orchestration for multi-robot fleets. Rolling updates, health checks, resource isolation. Same RDL format.

The same configuration format works across all modes. You add complexity only when the robot needs it -- but the recommendation is to stay on Mode 1 as long as possible.

---

## The Ecosystem

Gorai is not one repository. The full system is a constellation of focused components:

### Core

| Component | Language | Purpose |
|-----------|----------|---------|
| **gorai** | Go | Core framework — runtime, CLI, components, drivers, services |

### Communication

| Component | Language | Purpose |
|-----------|----------|---------|
| **gorai-gsp** | Go/TinyGo | Gorai Serial Protocol v2 — binary protocol for host-to-microcontroller communication (40+ message types, CRC-16, UART/UDP/radio) |
| **gorai-nats-gw** | Go | Gateway bridging hardware protocols (GSP/2, Modbus) to NATS subjects |

### Monitoring

| Component | Language | Purpose |
|-----------|----------|---------|
| **gorai-pushprom** | Go | Push metrics from resource-constrained robots to Prometheus |

### Sensors & Peripherals

| Component | Language | Purpose |
|-----------|----------|---------|
| **gorai-gps** | Go | GPS component service with NMEA parsing |

### Firmware

| Component | Language | Purpose |
|-----------|----------|---------|
| **rp2040-pwm** | TinyGo | RP2040 firmware: 16-channel hardware PWM for servos/ESCs via GSP/2, configurable failsafe |

### Satellite Repository Pattern

Components requiring CGo, platform-specific dependencies, or non-Go languages live in separate repositories following naming conventions:

| Pattern | Purpose |
|---------|---------|
| `gorai-driver-*` | Hardware drivers with CGo dependencies |
| `gorai-accel-*` | ML accelerator backends (Coral, CUDA, Rockchip NPU) |
| `gorai-service-*` | Complex standalone services (vision, SLAM) |
| `gorai-tiny-*` | TinyGo microcontroller firmware |

---

## Hardware Platforms

| Platform | Role | AI Capability |
|----------|------|---------------|
| Raspberry Pi 5 (8GB) | Primary reference | External via Hailo AI Kit (13-26 TOPS) |
| Raspberry Pi 5 (4GB) | Budget primary | External via Hailo |
| Orange Pi 5B (8GB) | Budget AI | Built-in RK3588 NPU (6 TOPS) |

The framework targets Linux ARM64 and AMD64. macOS is supported for development only. Microcontrollers (RP2040, ESP32) are supported through TinyGo firmware speaking GSP/2 over serial.

### Hardware Access Patterns

GoRAI supports two hardware access patterns:

1. **Co-processor (RP2040 via GSP/2):** An RP2040 board handles real-time hardware I/O (PWM, encoders, GPIO). The RPi communicates with it over USB serial using the Gorai Serial Protocol v2. Best for: precise timing-critical control, isolating hardware from the Linux scheduler.

2. **Native RPi hardware (GPIO/I2C/SPI):** Direct access to Raspberry Pi GPIO pins, I2C buses, and SPI buses from Go code. Best for: simple sensors, I2C devices, situations where an RP2040 is unnecessary overhead.

Both approaches produce GoRAI components with identical interfaces -- application code does not know or care which driver model is used underneath.

---

## Language Strategy

Gorai is Go-first but pragmatically polyglot. The core framework, CLI, components, and drivers are pure Go. Services that need Python (OpenCV, PyTorch) or C++ (Cartographer, RealSense SDKs) run as separate processes communicating through NATS — any language with a NATS client can be a Gorai service.

| What | Language | Why |
|------|----------|-----|
| Framework core, CLI | Go | Concurrency, single binary, fast compilation |
| Sensors, motors, GPIO | Go | Protocol parsing, hardware interfaces |
| Vision, ML inference | Python (future) | OpenCV, PyTorch/ONNX ecosystem |
| SLAM | C++ (future) | Cartographer, ORB-SLAM |
| Camera drivers | Go + cgo | V4L2, RealSense SDKs |
| Microcontroller firmware | TinyGo | RP2040 PWM, sensor nodes |
| Web dashboard | Go + HTMX | No separate JS frontend |

---

## Safety and Governance

As robots become more autonomous, safety must be a runtime concern rather than an afterthought. Gorai's safety architecture provides:

- **Layered safety** — hardware emergency stops, firmware limits, software constraints, AI governance
- **Explicit authority boundaries** — permissions on tool invocation, enforced preconditions
- **Separation of concerns** — control loop (deterministic) vs executor loop (AI-driven)
- **Auditability** — action logs, state streams, and replay via JetStream
- **Deterministic overrides** — emergency stops always work, regardless of AI state

---

## How Gorai Compares

| Aspect | Gorai | ROS 2 | Viam |
|--------|-------|-------|------|
| Language | Go core, polyglot services | C++/Python | Go core, polyglot |
| Messaging | NATS (broker) | DDS (peer-to-peer) | gRPC |
| Deployment | Single binary | Complex workspace | Cloud-managed |
| Target | Prosumers, small teams | Enterprise, research | Cloud robotics |
| Setup time | Minutes | Hours to days | Minutes (cloud) |
| AI support | First-class (accelerators, LLM agents) | Via packages | Cloud ML |
| MCU support | TinyGo + GSP/2 | micro-ROS | N/A |
| Persistence | JetStream built-in | rosbag (external) | Cloud storage |

Gorai is not competing with ROS 2. They serve different markets. A team that needs Gazebo simulation, Cartographer SLAM, and MoveIt motion planning should use ROS 2. A team that needs a working autonomous boat in a weekend should use Gorai.

---

## Where to Go From Here

| Goal | Start here |
|------|-----------|
| Understand the strategy | [STRATEGIC-SUMMARY.md](STRATEGIC-SUMMARY.md) |
| Read the full spec | [gorai-framework-specification.md](../specifications/gorai-framework-specification.md) |
| Build a component | [LLM-DESIGN-GUIDE.md](../architecture/LLM-DESIGN-GUIDE.md) |
| Learn the RDL format | [robot-definition-language.md](../specifications/robot-definition-language.md) |
| Understand NATS | [nats-description.md](../architecture/nats-description.md) |
| Set up your environment | [development-tools.md](../guides/development-tools.md) |
| Read the book | [Book chapters](../book/chapters/) |
| See example projects | [projects/](../projects/) |
| Explore the ecosystem | [ecosystem/](../ecosystem/) |
