# Gorai Ecosystem Components

The Gorai ecosystem extends beyond the core framework. This directory documents the external components and services that are critical to Gorai's operation.

> See [VISION.md](../../../gorai/VISION.md) for the north star. The protocol gateways below (gorai-nats-gw and friends) are how non-NATS devices — a $5 microcontroller, an off-the-shelf smart plug — join the mesh as native NCP capabilities (resources and tools) and become part of a Composite Robot.

## Core Framework

### gorai
The core robotics framework. Go-based runtime, CLI, component interfaces, drivers, and services.
- **Repo:** [../gorai](../../gorai)
- **Language:** Go
- **Key features:** Single binary deployment, NATS messaging, RDL configuration, mesh service discovery

## Communication & Protocols

### gorai-gsp
Go/TinyGo library implementing the Gorai Serial Protocol v2 (GSP/2).
- **Repo:** [../gorai-gsp](../../gorai-gsp)
- **Language:** Go / TinyGo
- **Key features:**
  - Transport-agnostic binary protocol for host-device communication
  - 5-byte header with version, flags, type, sequence number, and length
  - CRC-16-CCITT error detection
  - Bidirectional with selective ACK
  - 40+ message types for PWM, motors, encoders, IMU, sensors, GPIO
  - Works over UART, UDP, or radio links

### gorai-nats-gw
NATS gateway for bridging hardware protocols to the NATS messaging bus.
- Bridges GSP/2, Modbus, and other protocols to NATS subjects
- Enables distributed robot architectures
- See also: [../specifications/mesh-service-discovery.md](../specifications/mesh-service-discovery.md)

## Monitoring & Telemetry

### gorai-pushprom
Prometheus push metrics for resource-constrained robots.
- Pushes metrics from robots to a Prometheus-compatible endpoint
- Designed for robots that can't run a full Prometheus exporter
- See also: [../plans/prometheus.md](../plans/prometheus.md)

## Sensors & Peripherals

### gorai-gps
GPS component service for location-aware robots.
- Provides GPS data as a Gorai component
- NMEA sentence parsing
- See also: [../plans/gps.md](../plans/gps.md)

## Firmware

### rp2040-pwm
TinyGo firmware for RP2040-based boards (Raspberry Pi Pico, etc.).
- **Repo:** [../rp2040-pwm](../../rp2040-pwm)
- **Language:** TinyGo
- **Key features:**
  - 16-channel hardware PWM for servos and ESCs
  - USB serial interface using GSP/2
  - Configurable failsafe and pulse limits
  - Persistent configuration in flash

## Satellite Repository Pattern

Components requiring CGo, platform-specific dependencies, or non-Go languages use satellite repos:

| Pattern | Purpose | Examples |
|---------|---------|----------|
| `gorai-driver-*` | Hardware drivers with CGo | Camera drivers, sensor libraries |
| `gorai-accel-*` | ML accelerator backends | Coral, CUDA, Rockchip NPU |
| `gorai-service-*` | Complex standalone services | Vision pipeline, path planning |
| `gorai-tiny-*` | TinyGo microcontroller code | Sensor nodes, actuator controllers |

## Adding New Ecosystem Documentation

When documenting a new ecosystem component:
1. Create a dedicated `.md` file in this directory (e.g., `gorai-vision.md`)
2. Include: purpose, repository location, language, key features, configuration, and usage
3. Cross-reference relevant specifications and plans
4. Update this README with a summary entry
