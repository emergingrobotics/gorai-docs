# Motor Controller Service Architecture

This document describes the motor controller service pattern in Gorai, using the L298N H-bridge driver as a concrete example. The pattern is designed to be reusable for other motor controller ICs (DRV8833, TB6612, etc.) by implementing new services that follow the same interface.

---

## Design Principles

1. **Motor control logic lives in Go, not on firmware.** The Pico receives raw GPIO and PWM commands -- it does not understand "motor forward" or "motor speed." This keeps the firmware generic and moves controller-specific behavior to the Go service layer.

2. **All signals go over NATS.** The motor controller service publishes GPIO_SET and PWM_SET commands to NATS. The gateway bridges these to the Pico via GSP/2.

3. **Components and services are decoupled via NATS topics.** The motor component publishes power commands to a NATS topic. The motor controller service subscribes. Neither knows about the other's implementation.

4. **The motor component supports multiple output modes.** The `motor/remote` component has a configurable `output_mode`:
   - `"firmware"` (default): sends MOTOR_SET/MOTOR_CONFIG/MOTOR_ENABLE directly to Pico firmware for motor controllers wired without a separate driver board.
   - `"nats"`: publishes `{"power": float64}` to a configurable NATS topic, consumed by a motor controller service.

---

## Architecture

```
                        In-process Go calls                 NATS
                        ===================                 ====

velocity_input ──NATS──> mecanum_controller
                              │
                    SetPower() on each motor
                              │
              ┌───────────────┼───────────────┐
              │               │               │
         motor_fl        motor_fr         motor_rl ...
      (output_mode=nats)
              │
              │  publishes {"power": 0.5}
              │  to motor topic via NATS
              │
              ▼
       l298n_controller service
              │
              ├── GPIO_SET (IN1=HIGH, IN2=LOW)  ──NATS──> gateway ──GSP/2──> Pico
              │
              └── PWM_SET (speed_pin)           ──NATS──> gateway ──GSP/2──> Pico
                  (internal pwm/remote instance)
```

### Data Flow Step-by-Step

1. `mecanum_controller` receives a velocity command (`vx`, `vy`, `omega`) on NATS.
2. It applies inverse kinematics to compute per-wheel power values.
3. It calls `SetPower(power)` on each `motor/remote` component (in-process Go interface call).
4. Each `motor/remote` (output_mode=nats) publishes `{"power": power}` to its configured `motor_topic`.
5. The `l298n_controller` service subscribes to each motor's topic.
6. On receiving a power command, the service applies the L298N H-bridge truth table:
   - Sets direction via `GPIO_SET` (IN1/IN2 pins) published directly to NATS.
   - Sets speed via `SetDuty()` (0.0-1.0 duty cycle) on an internally-created `pwm/remote` instance for the motor's `speed_pin`, which publishes `PWM_SET` to NATS.
7. The NATS gateway bridges GPIO_SET and PWM_SET to the Pico via GSP/2.
8. The Pico drives the physical GPIO and PWM pins.

---

## L298N Truth Table

The L298N uses two GPIO pins (IN1, IN2) to control direction and one PWM pin (ENA or ENB) for speed. Each L298N board drives two motors.

| Power     | IN1  | IN2  | ENA/ENB Duty | Result         |
|-----------|------|------|--------------|----------------|
| > 0       | HIGH | LOW  | |power| * 100% | Forward     |
| < 0       | LOW  | HIGH | |power| * 100% | Backward    |
| == 0      | LOW  | LOW  | 0%           | Coast (off)    |
| == 0 (brake) | HIGH | HIGH | 0%        | Brake (hold)   |

The `brake_on_stop` flag in the motor definition controls whether a zero-power command results in coasting (LOW/LOW) or braking (HIGH/HIGH).

The `invert` flag swaps the forward/backward direction, useful when motors are mounted in opposite orientations.

---

## RDL Configuration Example

### Motor Components (output_mode=nats)

Each motor is a `motor/remote` component in NATS output mode. It implements the `motor.Motor` interface, so upstream consumers (e.g. `mecanum_controller`) call `SetPower()` on it.

```json
{
  "name": "motor_fl",
  "type": "motor",
  "model": "remote",
  "attributes": {
    "output_mode": "nats",
    "motor_topic": "gorai.main-robot.motor.motor_fl.command"
  }
}
```

### L298N Controller Service

The service ties everything together. It subscribes to motor NATS topics, internally creates `pwm/remote` instances for each motor's speed pin, and publishes GPIO commands for direction. No standalone PWM components are needed -- the speed pin is defined inline per motor.

```json
{
  "name": "l298n_controller",
  "type": "control",
  "model": "l298n",
  "attributes": {
    "nats_subject_prefix": "gsp",
    "device_id": "gsp-pico",
    "pwm_frequency_hz": 5000,
    "motors": [
      {
        "name": "motor_fl",
        "motor_topic": "gorai.main-robot.motor.motor_fl.command",
        "speed_pin": 2,
        "in1_pin": 3,
        "in2_pin": 4,
        "invert": false,
        "brake_on_stop": true
      }
    ]
  }
}
```

Each motor entry defines all three pins (speed, in1, in2) directly. The service uses the top-level `nats_subject_prefix` and `device_id` to configure the internal PWM instances.

### PWM Speed Control

The L298N ENA/ENB pins are duty-cycle driven, not servo-style pulse-width driven. The service defaults to **5kHz** PWM with **0-100% duty cycle** control via `SetDuty()`. This is configurable via the `pwm_frequency_hz` attribute (default: 5000).

At 5kHz (200us period), a motor at 50% power receives a 100us pulse per cycle (50% duty). This is fundamentally different from servo PWM (50Hz, 1000-2000us pulse width) and provides much better motor torque and responsiveness.

| Frequency | Period | Duty 0% | Duty 50% | Duty 100% |
|-----------|--------|---------|----------|-----------|
| 5kHz      | 200us  | 0us     | 100us    | 200us     |
| 50Hz (servo, **wrong** for L298N) | 20000us | 1000us | 1500us | 2000us |

### Full Wiring for Four Motors (Two L298N Boards)

| Motor | ENA/ENB Pin (PWM) | IN1 Pin | IN2 Pin | L298N Board |
|-------|--------------------|---------|---------|-------------|
| FL    | GPIO 2             | GPIO 3  | GPIO 4  | Board 1     |
| FR    | GPIO 10            | GPIO 11 | GPIO 12 | Board 1     |
| RL    | GPIO 13            | GPIO 14 | GPIO 15 | Board 2     |
| RR    | GPIO 16            | GPIO 17 | GPIO 18 | Board 2     |

---

## Creating a New Motor Controller Service

To add support for a different motor controller IC (e.g. DRV8833), create a new service package under `gorai/services/control/` that follows this pattern:

1. **Register the service** with `registry.RegisterService("control", "drv8833", New)`.
2. **Define a config** with motor definitions including pin mappings and controller-specific parameters.
3. **Subscribe to motor NATS topics** in `Reconfigure()`.
4. **Translate power commands** to the controller's specific GPIO/PWM sequences.
5. **Create PWM instances internally** using `pwm/remote.New()` for speed control pins, rather than depending on standalone PWM components. Use `SetDuty()` for motor speed (0-100% duty cycle at kHz-range frequency), not `SetNormalized()` which is designed for servo pulse-width control.
6. **Send GPIO commands** for direction/mode pins by publishing directly to NATS.
7. **Handle cleanup** in `Close()` by setting all outputs to safe defaults and closing internal PWM instances.

The key insight is that the `motor/remote` component in NATS output mode is controller-agnostic. It just publishes `{"power": float64}` to a topic. The motor controller service is what knows the specific IC's truth table and pin protocol.

---

## Code Locations

| Item | Path |
|------|------|
| motor/remote component | `gorai/components/motor/remote/` |
| L298N service | `gorai/services/control/l298n/` |
| Mecanum controller | `gorai/services/control/mecanum/` |
| Velocity input service | `gorai/services/control/velocity_input/` |
| PWM remote component | `gorai/components/pwm/remote/` |
| GPIO remote component | `gorai/components/gpio/remote/` |
| Drive-kit RDL | `drive-kit/main-robot.json` |
