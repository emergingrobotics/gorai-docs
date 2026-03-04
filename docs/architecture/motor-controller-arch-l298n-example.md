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
              └── SetNormalized() on pwm/remote ──NATS──> gateway ──GSP/2──> Pico
                  (motor_fl_speed component)
```

### Data Flow Step-by-Step

1. `mecanum_controller` receives a velocity command (`vx`, `vy`, `omega`) on NATS.
2. It applies inverse kinematics to compute per-wheel power values.
3. It calls `SetPower(power)` on each `motor/remote` component (in-process Go interface call).
4. Each `motor/remote` (output_mode=nats) publishes `{"power": power}` to its configured `motor_topic`.
5. The `l298n_controller` service subscribes to each motor's topic.
6. On receiving a power command, the service applies the L298N H-bridge truth table:
   - Sets direction via `GPIO_SET` (IN1/IN2 pins) published directly to NATS.
   - Sets speed via `SetNormalized()` on the corresponding `pwm/remote` component, which publishes `PWM_SET` to NATS.
7. The NATS gateway bridges GPIO_SET and PWM_SET to the Pico via GSP/2.
8. The Pico drives the physical GPIO and PWM pins.

---

## L298N Truth Table

The L298N uses two GPIO pins (IN1, IN2) to control direction and one PWM pin (ENA or ENB) for speed. Each L298N board drives two motors.

| Power     | IN1  | IN2  | ENA/ENB   | Result         |
|-----------|------|------|-----------|----------------|
| > 0       | HIGH | LOW  | |power|   | Forward        |
| < 0       | LOW  | HIGH | |power|   | Backward       |
| == 0      | LOW  | LOW  | 0         | Coast (off)    |
| == 0 (brake) | HIGH | HIGH | 0      | Brake (hold)   |

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

### PWM Speed Components (ENA/ENB)

Each motor's speed pin is a `pwm/remote` component. The L298N service calls `SetNormalized()` on it.

```json
{
  "name": "motor_fl_speed",
  "type": "pwm",
  "model": "remote",
  "attributes": {
    "nats_subject_prefix": "gsp",
    "device_id": "gsp-pico",
    "pin": 2,
    "frequency_hz": 50,
    "min_pulse_us": 1000,
    "max_pulse_us": 2000,
    "initial_pulse_us": 1000,
    "failsafe_pulse_us": 1000,
    "auto_configure": true
  }
}
```

### L298N Controller Service

The service ties everything together. It subscribes to motor NATS topics, resolves PWM component dependencies, and publishes GPIO commands for direction.

```json
{
  "name": "l298n_controller",
  "type": "control",
  "model": "l298n",
  "attributes": {
    "nats_subject_prefix": "gsp",
    "device_id": "gsp-pico",
    "motors": [
      {
        "name": "motor_fl",
        "motor_topic": "gorai.main-robot.motor.motor_fl.command",
        "pwm_component": "motor_fl_speed",
        "in1_pin": 3,
        "in2_pin": 4,
        "invert": false,
        "brake_on_stop": true
      }
    ]
  },
  "depends_on": ["motor_fl_speed"]
}
```

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
5. **Resolve PWM component dependencies** for speed control pins.
6. **Send GPIO commands** for direction/mode pins by publishing directly to NATS.
7. **Handle cleanup** in `Close()` by setting all outputs to safe defaults.

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
