# Gorai Documentation Index

Use this file to find the right document fast. Scan the topic table first, then drill into the per-file entries if needed.

## Topic Quick-Reference

| Topic | Go to |
|-------|-------|
| Vision / north star (capabilities over NATS, Composite Robot) | `../gorai/VISION.md` |
| What is Gorai / full system overview | `docs/overview/system-overview.md` |
| Strategic positioning and market wedge | `thoughts/strategy-paper.md` |
| Strategic decisions (quick reference) | `docs/overview/STRATEGIC-SUMMARY.md` |
| Complete technical specification | `docs/specifications/gorai-framework-specification.md` |
| Robot configuration (RDL JSON) | `docs/specifications/robot-definition-language.md` |
| Building new components (AI guide) | `docs/architecture/LLM-DESIGN-GUIDE.md` |
| Component types and interfaces | `docs/architecture/component-reference.md` |
| NATS messaging patterns | `docs/architecture/nats-description.md` |
| Service discovery (mesh) | `docs/specifications/mesh-service-discovery.md` |
| Runtime device discovery | `docs/specifications/dynamic-discovery.md` |
| Serial protocol (GSP/2) | `docs/specifications/gsp-v2-protocol.md` |
| Runtime lifecycle | `docs/specifications/runtime.md` |
| Deployment modes | `docs/specifications/DEPLOYMENT-MODES.md` |
| Testing strategy and commands | `docs/specifications/testing-approach.md` |
| Running tests (how-to) | `docs/specifications/howto-run-tests.md` |
| Code organization / where things go | `docs/architecture/PACKAGE-LOCATIONS.md` |
| Development environment setup | `docs/guides/development-tools.md` |
| NATS installation | `docs/guides/install-nats.md` |
| Hardware platforms and SBCs | `docs/specifications/hardware-requirements.md` |
| Sensor types and specs | `docs/hardware/sensor-analysis.md` |
| Motor types and control | `docs/hardware/motor-analysis.md` |
| SBC comparison (RPi vs OPi) | `docs/hardware/sbc-comparison-rpi-to-opi.md` |
| Ecosystem components | `docs/ecosystem/README.md` |
| External services (ML, SLAM) | `docs/plans/external-services.md` |
| AI safety and governance | `docs/overview/ai-driven-robot-safety-governance-arch.md` |
| Framework comparisons (ROS 2, Viam, YARP) | `docs/overview/general-designs.md` |
| NATS authentication | `docs/architecture/gorai-nats-auth.md` |
| Third-party component guide | `docs/architecture/third-party-component-ecosystem.md` |
| Component packaging approach (Caddy model) | `../gorai/docs/package-dev-approach.md` (in gorai repo) |
| Roadmap / future phases | `docs/overview/FUTURE-ROADMAP.md` |
| Book (learn Gorai from scratch) | `docs/book/chapters/01-introduction.md` |
| Example robots | `docs/EXAMPLES.md` |
| Arguments for and against Gorai | `why-or-why-not-gorai.md` |

---

## docs/overview/

| File | Summary |
|------|---------|
| `system-overview.md` | Complete platform picture: five-layer architecture, resource model, NATS messaging, mesh discovery, RDL configuration, runtime, deployment, ecosystem, and comparisons with ROS 2 and Viam. **Read this first.** |
| `STRATEGIC-SUMMARY.md` | Quick-reference for strategic decisions: prosumer positioning, phased architecture, Go-first polyglot strategy, competitive positioning guidelines. |
| `gorai-overarching-strategy.md` | Internal strategy defining the strategic wedge, target users (software-first teams), and non-targets (experimental research). |
| `FUTURE-ROADMAP.md` | Evolution plan: Phase 1 (simple binary, current), Phase 2 (optional containers for ML/vision), Phase 3 (K3s fleet management). |
| `ai-driven-robot-safety-governance-arch.md` | Layered safety architecture separating AI decision-making from hard real-time control, runtime safety monitors, capability-node action governance (NCP). |
| `general-designs.md` | Deep comparison of ROS 2 (DDS), Viam (gRPC, cloud-native), and YARP (port-based). Architecture, strengths, weaknesses. |
| `ros2-design.md` | ROS 2 architecture deep dive: nodes, topics, services, actions, DDS, QoS, parameter server, rclcpp/rclpy. |
| `viam-design.md` | Viam architecture: resource-centric design, API/model triplets, modular plugins, gRPC + WebRTC. |
| `yarp-design.md` | YARP design: port-based communication, transport-neutral carriers, name server, loose coupling. |
| `vision-analysis.md` | Technology assessment: pure Go vs pragmatic hybrid, recommends Go core + Python/C++ for vision/ML via NATS. |
| `go-ai-material.md` | Go AI/ML ecosystem: GoMLX, Gorgonia, ONNX Runtime, gocv/OpenCV, hardware acceleration (CUDA, TPU, NPU). |

## docs/architecture/

| File | Summary |
|------|---------|
| `LLM-DESIGN-GUIDE.md` | AI-assisted component development guide: decision tree, component/service pattern templates, registration, NATS topic conventions. |
| `component-reference.md` | All component interfaces: Resource, Component, Sensor, Actuator with specs for IMU, AHRS, GPS, motor, servo, camera. |
| `simple-object-model.md` | Resource-centric object model: everything inherits from Resource (Name, Reconfigure, DoCommand, Close). |
| `PACKAGE-LOCATIONS.md` | Where code belongs: cmd/, pkg/, components/, driver/, services/, internal/. Decision tree. |
| `nats-description.md` | NATS as backbone: Core (pub/sub, request/reply), JetStream (streams, KV store, object store), clustering, leaf nodes, 18M msg/sec. |
| `gorai-nats-auth.md` | Five NATS auth methods: token, user/password, NKeys, JWT/accounts, TLS client certificates. Deployment recommendations. |
| `modules-approach.md` | Caddy model: blank imports in main.go as component manifest, Go Modules for distribution, container registries deferred to Phase 2. |
| `third-party-component-ecosystem.md` | Building components outside the main repo: Caddy model (blank imports + init() registration), Go components, container services (Phase 2 deferred), metadata standards, distribution. |
| `hardware-abstraction.md` | **ARCHIVED** — HAL removed 2026-02-06. Components now use GSP protocol or NATS. |
| `robot-description-formats.md` | Analysis of URDF, Xacro, SDF, MJCF robot description languages. |
| `web-dashboard-research.md` | Dashboard research: Chi + HTMX + Templ, websockets for real-time, uPlot charts, go2rtc video streaming. |
| `hugo-plan.md` | Website theme evaluation: Docsy vs Hextra for Hugo-based docs site. |
| `prometheus-idea.md` | Local Prometheus per robot: time-series, PromQL, alerting, 200-500MB RAM, federation for fleets. |

## docs/specifications/

| File | Summary |
|------|---------|
| `gorai-framework-specification.md` | **Complete spec (v0.2.0, 137KB)** — resource model, components, services, NATS, protobuf, AI/ML services, acceleration, CLI. |
| `robot-definition-language.md` | RDL v3.1 JSON format: robot identity, NATS connection, components, services, dependencies, logging. |
| `mesh-service-discovery.md` | NATS KV discovery: three JetStream buckets for runtime registration, channel discovery, health monitoring, schema registry. |
| `dynamic-discovery.md` | Runtime device discovery: gateways (GSP/2, Modbus, CAN), auto-adoption rules, proxy factory, @discovered dependencies. |
| `gsp-v2-protocol.md` | Gorai Serial Protocol v2: 5-byte header, CRC-16-CCITT, 40+ message types, UART/UDP/radio. Points to gorai-gsp repo. |
| `runtime.md` | Runtime lifecycle: startup, config loading, NATS connection, dependency resolution, hot reload, graceful shutdown. |
| `DEPLOYMENT-MODES.md` | Three modes: Mode 1 (simple binary, implemented), Mode 2 (containers, future), Mode 3 (K3s fleet, future). |
| `build-targets.md` | Supported platforms: linux-arm64, linux-amd64, RPi 5, Orange Pi, Jetson, NUCs. Cross-compilation, CGO. |
| `code-organization.md` | Mandatory code structure: hybrid monorepo, satellite repos for drivers, TinyGo repos for MCUs. |
| `testing-approach.md` | Testing pyramid: unit 60%, component 20%, integration 10%, module 5%, system 4%, hardware 1%. Build tags. |
| `howto-run-tests.md` | Test execution: go test commands, build tags, Makefile shortcuts, coverage, race detection. |
| `cli-component-commands.md` | CLI for third-party components: search, install, list, validate. Registry integration. |
| `hardware-requirements.md` | Hardware tiers: Primary (RPi 5), Performance (Jetson Orin Nano), Budget AI (Orange Pi 5B). Min 4GB RAM. |
| `hello-sensor-design.md` | Canonical example: CPU temperature sensor showing Sensor interface, protobuf, NATS publishing, fake implementations. |
| `linux-boards.md` | Supported SBCs: RPi 5 as reference, primary/secondary/gateway board roles. |
| `serial-interfaces.md` | **Moved** — GSP now in separate gorai-gsp repository. |
| `service-rdl-schema.md` | Schema for service.rdl.json: metadata for third-party container services. |
| `publication.md` | Doc tooling: mdBook (book), MkDocs (website), pkgsite (API docs). |

## docs/hardware/

| File | Summary |
|------|---------|
| `sensor-analysis.md` | Prosumer sensors: vision (ZED 2i, RealSense, OAK-D), range (LiDAR, ultrasonic, ToF), IMU/AHRS, GPS, environmental. Interfaces, specs, prices. |
| `motor-analysis.md` | Motor types: BLDC/FOC, servo (RC PWM, Dynamixel), DC geared, stepper, marine thrusters. Controllers: ODrive, VESC. |
| `sbc-comparison-rpi-to-opi.md` | RPi 5 + Hailo vs Orange Pi 5 with integrated NPU. Recommends RPi 5 for ecosystem and GPIO compatibility. |
| `orange-pi-future-support.md` | Deferred OPi support: RK3588 GPIO mappings, rationale for deferral. Preserved for future. |

## docs/guides/

| File | Summary |
|------|---------|
| `development-tools.md` | Dev setup: Go 1.22+, NATS server, protoc, linting, editor config. Full tool list. |
| `install-nats.md` | NATS installation: binary install, systemd service, JetStream config, verification. |
| `install-dependencies.md` | Ubuntu install guide: Go, NATS with JetStream, VictoriaMetrics as systemd services. |
| `nats-setup.md` | NATS configuration: server options, JetStream, auth, monitoring, clustering, leaf nodes. |

## docs/ecosystem/

| File | Summary |
|------|---------|
| `README.md` | All ecosystem components: gorai-gsp, gorai-nats-gw, gorai-pushprom, gorai-gps, rp2040-pwm. Satellite repo naming conventions. |

## docs/plans/

| File | Summary |
|------|---------|
| `core-implementation-plan.md` | Framework core v0.1.0: 6 phases from protobuf through hello-sensor validation. Gap analysis. |
| `spec-implementation.md` | v0.2.0 spec updates: Power, Space, Link components; Behavior, Coordinator services; AI/LLM interfaces. |
| `external-services.md` | Service RDL pattern for ML inference, SLAM, and other compute-intensive services via NATS. |
| `robot-build-deploy-architecture.md` | Build/deploy: Gorai as library, JSON RDL config, cross-compilation, scp/rsync, systemd. |
| `rdl-auto.md` | RDL-driven code generation: write RDL, auto-generate buildable project skeleton. |
| `gps.md` | GPS NMEA reader: serial → parsed data → JSON → NATS. |
| `hailo.md` | Containerized YOLOX person detection on Hailo NPU, RPi 5 + Hailo AI Kit. |
| `prometheus.md` | Prometheus as local mandatory dependency: time-series, /metrics, PromQL, alerting. |
| `book.md` | Book outline: 15 chapters, target audience: Go developers entering robotics. |
| `publication-implementation.md` | Dual-format publishing: mdBook (reading) + MkDocs (reference), single source. |
| `content-migration.md` | Migration from mdBook/MkDocs to Pandoc + Hugo. Eliminates symlinks. |
| `update-sensors-motors.md` | Interface expansion: AHRS, LiDAR, PresenceSensor, ThermalArray, ForceSensor, Servo, Stepper. |
| `integrated-container-build.md` | RDL schema extension for auto-building container services. |
| `migrate-to-monolithic.md` | **SUPERSEDED** — see DEPLOYMENT-MODES.md for current approach. |
| `understand-publish.md` | Current publishing system documentation before migration. |

## docs/projects/

| File | Summary |
|------|---------|
| `project-pan-tilt.md` | Gorai-Sentinel: pan-tilt sensor fusion with camera + VL53L5CX ToF. First reference implementation. |
| `project-pan-tilt-distributed-option.md` | Distributed architecture evaluation for Gorai-Sentinel: centralized vs distributed nodes. |
| `project-simple-boat.md` | Gorai-Skimmer: autonomous surface vehicle, differential thrust, sonar bathymetry, GPS nav. Under $500. |
| `surf-test/README.md` | Two-robot distributed control demo: ground station (PC) + main robot (RPi 5) via NATS. |

## docs/book/chapters/

| File | Summary |
|------|---------|
| `00-frontmatter.md` | Title, copyright, safety disclaimer, prerequisites (Go, Linux, basic electronics). |
| `01-introduction.md` | Introduction: Gorai as Go-based robotics platform, authors, learning objectives. |
| `02-why-gorai.md` | Motivation: ROS 2/YARP/Viam pain points and how Go + NATS addresses them. |
| `03-architecture.md` | Architecture mental model: robots as networks, resource abstraction, three-layer architecture. |
| `04-nats.md` | NATS deep dive: 18M msg/sec, comparison with DDS/ZeroMQ/gRPC, topic patterns, JetStream. |
| `05-sensors.md` | Sensor interface: Readings() → map[string]any, protobuf types, built-in sensors, fake pattern. |
| `06-actuators.md` | Actuator interfaces: Motor, Servo, Arm. Safety-first with Stop(), IsMoving(). |
| `07-vision.md` | Camera interface: USB/CSI/IP/depth cameras, image capture, streaming, CV integration. |
| `08-services.md` | Services vs components: VisionService for detection/classification, Service interface. |
| `09-behaviors.md` | Behavior patterns: FSM, behavior trees, reactive architectures, Update() method. |
| `10-coordinators.md` | Mission orchestration: Coordinator interface, Start/Status/Cancel/Pause, multi-robot. |
| `11-development-environment.md` | Zero-to-code in 15 minutes: Go, NATS, editor setup, verification. |
| `12-hello-sensor.md` | Tutorial: CPU temperature sensor, NATS publishing, platform abstraction, fake implementations. |
| `13-custom-components.md` | Building custom components: DRV8833 motor driver walkthrough, interface patterns. |
| `14-testing-strategies.md` | Testing pyramid for safety-critical systems, build tags, table-driven tests. |
| `15-ai-ml-integration.md` | Edge AI/ML: NPU acceleration (RK3588), real-time constraints, go-rknnlite. |
| `16-project-organization.md` | Monorepo best practices: api/, pkg/, components/, services/, driver/. |
| `17-ai-assisted-development.md` | AI-assisted development: prompting for component/test generation, developer amplification. |
| `18-conclusion.md` | Recap and framework comparison: Gorai vs ROS 2/Viam/YARP. |
| `99-appendices.md` | Command reference: NATS CLI, Go commands, Makefile targets, protobuf reference, glossary. |

## thoughts/

| File | Summary |
|------|---------|
| `strategy-paper.md` | **Canonical strategy paper**: Gorai's thesis, wedge, target users, tradeoffs, ecosystem discipline. Most refined version. |
| `new-talking-points.md` | Strategy framing for the physical AI era: 11-section pitch document. |
| `devils-advocate.md` | Critical analysis of Gorai's claims: 11 challenges from a skeptic's perspective. |
| `wedge.md` | Who Gorai is for, who it's not for, use-case archetype, stated tradeoffs, one-line mental model. |

## Root Files

| File | Summary |
|------|---------|
| `why-or-why-not-gorai.md` | Balanced summary of all core arguments for and against adopting Gorai. |
| `docs/EXAMPLES.md` | Example robots: blinky (LED), gps-tracker, hello-camera (V4L2). Prerequisites and build instructions. |
| `docs/gorai-branch-differences.md` | HAL migration branch changes: PWM gpiod and I2C bridge migration to dependency injection. |
