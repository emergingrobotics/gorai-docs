# Gorai Documentation Index

This file is a machine-readable index of every document in gorai-docs. Use it to find the right file for any topic. Each entry has the file path, a summary, and searchable keywords.

---

## How to Use This Index

1. **Scan keywords** to find documents matching your topic
2. **Read the summary** to confirm relevance before opening the file
3. **Follow cross-references** noted in summaries for related content

### Topic Quick-Reference

| Topic | Primary Document |
|-------|-----------------|
| Complete system overview (start here) | `docs/overview/system-overview.md` |
| What is Gorai / why it exists | `docs/overview/STRATEGIC-SUMMARY.md` |
| Framework comparison (ROS 2, Viam, YARP) | `docs/overview/general-designs.md` |
| Complete technical specification | `docs/specifications/gorai-framework-specification.md` |
| Robot configuration (RDL JSON) | `docs/specifications/robot-definition-language.md` |
| Building new components/services | `docs/architecture/LLM-DESIGN-GUIDE.md` |
| Component types and interfaces | `docs/architecture/component-reference.md` |
| NATS messaging patterns | `docs/architecture/nats-description.md` |
| Service discovery (mesh) | `docs/specifications/mesh-service-discovery.md` |
| Runtime device discovery | `docs/specifications/dynamic-discovery.md` |
| Serial protocol (GSP/2) | `docs/specifications/gsp-v2-protocol.md` |
| Hardware platforms | `docs/specifications/hardware-requirements.md` |
| Sensor types and specs | `docs/hardware/sensor-analysis.md` |
| Motor types and control | `docs/hardware/motor-analysis.md` |
| Testing strategy | `docs/specifications/testing-approach.md` |
| Running tests | `docs/specifications/howto-run-tests.md` |
| Where code goes | `docs/architecture/PACKAGE-LOCATIONS.md` |
| Code organization | `docs/specifications/code-organization.md` |
| Development environment setup | `docs/guides/development-tools.md` |
| NATS installation | `docs/guides/install-nats.md` |
| External/ecosystem components | `docs/ecosystem/README.md` |
| External services (ML, SLAM) | `docs/plans/external-services.md` |
| Prometheus monitoring | `docs/plans/prometheus.md` |
| GPS integration | `docs/plans/gps.md` |
| Hailo AI accelerator | `docs/plans/hailo.md` |
| Deployment modes | `docs/specifications/DEPLOYMENT-MODES.md` |
| Roadmap / future phases | `docs/overview/FUTURE-ROADMAP.md` |
| AI safety and governance | `docs/overview/ai-driven-robot-safety-governance-arch.md` |
| Book (full tutorial) | `docs/book/chapters/` |
| Example robots | `docs/EXAMPLES.md` |
| Pan-tilt project | `docs/projects/project-pan-tilt.md` |
| Autonomous boat project | `docs/projects/project-simple-boat.md` |
| Distributed control demo | `docs/projects/surf-test/README.md` |
| Third-party component guide | `docs/architecture/third-party-component-ecosystem.md` |
| NATS authentication | `docs/architecture/gorai-nats-auth.md` |
| AI/ML in Go | `docs/overview/go-ai-material.md` |

---

## docs/overview/

### docs/overview/system-overview.md
Summary: Complete picture of the Gorai platform in one document — what it is, the five-layer architecture (resource model, NATS messaging, mesh discovery, dynamic discovery), how robots are configured with RDL, runtime lifecycle, deployment modes, the full ecosystem of components, hardware platforms, language strategy, safety model, and comparison with ROS 2 and Viam.
Keywords: system overview, architecture, resource model, NATS, mesh discovery, dynamic discovery, RDL, runtime, deployment, ecosystem, gorai-gsp, gorai-nats-gw, hardware, language strategy, safety, ROS 2 comparison, Viam comparison, start here

### docs/overview/STRATEGIC-SUMMARY.md
Summary: Quick reference for core strategic decisions — prosumer market positioning, phased architecture (simple binary → containers → K3s), Go-first polyglot language strategy, and "coopetition" with ROS 2.
Keywords: strategy, prosumer, market positioning, phases, simple binary, containers, K3s, fleet, Go polyglot, ROS 2

### docs/overview/gorai-overarching-strategy.md
Summary: Internal strategy paper defining Gorai's strategic wedge, target users (software-first teams building AI-driven robots), and non-targets (experimental research). Covers the Physical AI market opportunity.
Keywords: strategy, product positioning, target market, software-first teams, AI-driven robots, Physical AI, strategic wedge

### docs/overview/vision-analysis.md
Summary: Strategic technology assessment analyzing pure Go vs pragmatic hybrid architecture, ROS 2 positioning, containerization strategy. Recommends pragmatic hybrid: Go core with selective Python/C++ for vision/ML via NATS.
Keywords: technology strategy, pure Go vs hybrid, ROS 2 comparison, prosumer market, pragmatic polyglot, containerization

### docs/overview/FUTURE-ROADMAP.md
Summary: Planned evolution — Phase 1 (simple binary, current), Phase 2 (optional containers for ML/vision), Phase 3 (K3s fleet management). Current: single binary + NATS, ~20-50MB RAM.
Keywords: roadmap, phases, simple binary, containerized services, K3s, fleet management, deployment evolution, Podman

### docs/overview/general-designs.md
Summary: Comprehensive comparison of ROS 2 (DDS-based, enterprise), Viam (Go-first, cloud-native, gRPC), and YARP (port-based, transport-neutral). Analyzes architecture, strengths, and weaknesses of each.
Keywords: framework comparison, ROS 2, Viam, YARP, DDS, gRPC, middleware, robotics platforms, design patterns, QoS

### docs/overview/ros2-design.md
Summary: Detailed ROS 2 architecture — nodes, topics, services, actions, Interface Definition Language (.msg/.srv/.action), parameters, discovery, QoS, and internal API stack (rclcpp, rclpy).
Keywords: ROS 2, nodes, topics, services, actions, DDS, QoS, parameter server, discovery, rclcpp, rclpy

### docs/overview/viam-design.md
Summary: Analysis of Viam's Go-based robotics framework — resource-centric design, API/model triplets, modular plugin system, configuration-driven operation, distributed via gRPC and WebRTC.
Keywords: Viam, resource interface, API triplets, model triplets, Go framework, gRPC, WebRTC, modules, configuration, AI/ML

### docs/overview/yarp-design.md
Summary: YARP's "reluctant middleware" — port-based communication, transport-neutral carriers (tcp, udp, mcast, shmem), name server for discovery, longevity through loose coupling.
Keywords: YARP, ports, carriers, transport neutrality, name server, buffered ports, RPC, loose coupling

### docs/overview/go-ai-material.md
Summary: Go ecosystem for AI/ML/CV — GoMLX (OpenXLA), Gorgonia, ONNX Runtime, TensorFlow bindings, OpenCV (gocv), hardware acceleration (CUDA, Coral TPU, RK3588 NPU).
Keywords: Go AI, GoMLX, Gorgonia, ONNX Runtime, gocv, OpenCV, CUDA, TPU, NPU, RK3588, inference, computer vision

### docs/overview/ai-driven-robot-safety-governance-arch.md
Summary: Safety architecture for AI-driven robots — layered safety, explicit authority boundaries, separation of control loop from executor loop, runtime safety monitors, MCP server integration for cloud AI agents.
Keywords: AI safety, governance, executor loop, control loop, safety monitors, MCP server, authority boundaries, hardware safety

---

## docs/architecture/

### docs/architecture/LLM-DESIGN-GUIDE.md
Summary: Everything an LLM needs to design new Gorai components and services — decision tree for code placement, component pattern templates, service pattern templates, registration patterns, NATS topic conventions.
Keywords: LLM guide, AI-assisted development, component pattern, service pattern, code templates, registry, resource interface, NATS messaging, fake implementations

### docs/architecture/component-reference.md
Summary: Complete reference for all component interfaces — base Resource, Component, Sensor, Actuator with specs for IMU, AHRS, GPS, motor, servo, camera types. Includes concurrency model and readings format.
Keywords: component reference, interfaces, Sensor, Actuator, IMU, AHRS, GPS, motor, servo, camera, readings, concurrency

### docs/architecture/hardware-abstraction.md
Summary: ARCHIVED — HAL removed as of 2026-02-06. Historical design for RPi 5 hardware abstraction (GPIO, I2C, SPI, PWM, serial). Components now use GSP protocol or NATS instead.
Keywords: HAL archived, hardware abstraction, GPIO, I2C, SPI, PWM, serial, gpiod, pin mapping, REMOVED

### docs/architecture/simple-object-model.md
Summary: Resource-centric object model — everything inherits from Resource (Name, Reconfigure, DoCommand, Close). Component categories: Sensor, Actuator, Power, Space, Link.
Keywords: object model, resource interface, components, services, sensors, actuators, Space, Link, resource base

### docs/architecture/third-party-component-ecosystem.md
Summary: Guide for creating components outside the main repo — Go components (in-process) vs container services (external), metadata standards, testing requirements, distribution via Go modules and container registries.
Keywords: third-party, ecosystem, Go modules, container services, metadata, distribution, testing, commercial drivers

### docs/architecture/modules-approach.md
Summary: Hybrid multi-repo architecture using Go Modules for in-process components and container registries for external services. RDL imports control available implementations.
Keywords: modules, multi-repository, Go Modules, container registry, RDL imports, satellite repositories, modular architecture

### docs/architecture/PACKAGE-LOCATIONS.md
Summary: Decision tree for where code belongs — cmd/ (entry points), pkg/ (core libraries), components/ (hardware), driver/ (low-level), services/ (algorithms), internal/ (private).
Keywords: package structure, code organization, cmd, pkg, components, driver, services, internal, directory layout

### docs/architecture/nats-description.md
Summary: NATS as messaging backbone — Core NATS (pub/sub, request/reply, queue groups), JetStream (streams, consumers, retention), Key-Value Store, Object Store, performance (18M msg/sec), leaf nodes, clustering.
Keywords: NATS, pub/sub, request/reply, queue groups, JetStream, streams, KV store, object store, messaging, performance, cluster, leaf nodes

### docs/architecture/gorai-nats-auth.md
Summary: Five authentication methods: token, user/password, NKeys, JWT/accounts, TLS client certificates. Comparison matrix, deployment scenario recommendations, RDL configuration, credential management.
Keywords: NATS authentication, security, token, NKeys, JWT, TLS, mTLS, credentials, deployment scenarios, authorization

### docs/architecture/robot-description-formats.md
Summary: Analysis of URDF, Xacro, SDF, MJCF robot description languages — links, joints, sensors, actuators, physics modeling. Covers Gazebo/MuJoCo simulation formats.
Keywords: robot description, URDF, Xacro, SDF, MJCF, kinematics, simulation, Gazebo, MuJoCo, mesh geometry

### docs/architecture/web-dashboard-research.md
Summary: Research on Go web frameworks for embedded robot dashboard — recommends Chi + HTMX + Templ, coder/websocket for real-time, uPlot for charts, go2rtc for video streaming.
Keywords: web dashboard, Chi, HTMX, Templ, websocket, real-time, uPlot, go2rtc, video streaming, WebRTC

### docs/architecture/hugo-plan.md
Summary: Hugo website theme evaluation — recommends Docsy (used by Kubernetes, gRPC) or Hextra. Needs search, dark mode, versioning, API reference, Mermaid diagrams.
Keywords: Hugo, documentation theme, Docsy, Hextra, website, search, dark mode, versioning, Mermaid

### docs/architecture/prometheus-idea.md
Summary: Running Prometheus locally on each robot — persistent time-series storage, PromQL queries, alerting, 200-500MB RAM, ~2GB/month disk, federation-ready for fleet metrics.
Keywords: Prometheus, monitoring, metrics, time-series, PromQL, alerting, local deployment, federation

---

## docs/specifications/

### docs/specifications/gorai-framework-specification.md
Summary: Complete framework spec (v0.2.0, 137KB) — resource model (components, services), NATS communication, Protocol Buffers, AI/ML services (vision, SLAM, navigation, behavior), acceleration layer, configuration, CLI.
Keywords: framework specification, resource model, components, services, NATS, protobuf, AI/ML, vision, SLAM, navigation, behavior, acceleration, RDL, CLI

### docs/specifications/robot-definition-language.md
Summary: RDL v3.1 JSON configuration — robot identity, NATS connection, component instances, service instances, dependencies, logging. Simple binary deployment with future container/K3s support.
Keywords: RDL, Robot Definition Language, JSON, robot.json, components config, services config, NATS config, dependencies, validation, schema

### docs/specifications/mesh-service-discovery.md
Summary: NATS KV service discovery — three JetStream buckets (gorai-services, gorai-channels, gorai-schemas) for runtime registration, channel discovery, health monitoring, schema registry.
Keywords: mesh, service discovery, NATS KV, JetStream, runtime registration, heartbeat, ServiceDescriptor, ChannelDescriptor, schemas

### docs/specifications/dynamic-discovery.md
Summary: Runtime device discovery — gateways (GSP/2, Modbus, CAN), mesh discovery, auto-adoption rules, proxy factory pattern, hybrid static/dynamic model, @discovered: dependencies.
Keywords: dynamic discovery, runtime, gateway, GSP, auto-adoption, proxy factory, RemoteMotor, RemoteSensor, discovery rules, @discovered

### docs/specifications/gsp-v2-protocol.md
Summary: Pointer to gorai-gsp repo — Gorai Serial Protocol v2 for host-microcontroller communication, 5-byte header, CRC-16-CCITT, 40+ message types (PWM, motor, encoder, IMU, sensor, GPIO), UART/UDP/radio.
Keywords: GSP protocol, serial, gorai-gsp, TinyGo, microcontroller, UART, USB CDC, CRC, message types, binary protocol

### docs/specifications/runtime.md
Summary: Robot runtime lifecycle — startup sequence, config loading, NATS connection, component/service instantiation, dependency resolution, signal handling, hot reload, graceful shutdown, error recovery.
Keywords: runtime, lifecycle, startup, shutdown, configuration, dependency resolution, signal handling, hot reload, reconfigure, graceful shutdown

### docs/specifications/code-organization.md
Summary: Mandatory code structure — hybrid monorepo with core gorai (standard Go), satellite repos for drivers/accelerators (standard Go), and TinyGo repos for microcontrollers. Linux required for production.
Keywords: code organization, monorepo, standard Go, TinyGo, satellite repos, Linux, platform requirements, GSP bridge

### docs/specifications/testing-approach.md
Summary: Testing pyramid — unit 60%, component 20%, integration 10%, module 5%, system 4%, hardware 1%. Build tags for categories, fake implementations, table-driven tests, CI config.
Keywords: testing, test pyramid, unit tests, integration tests, fake implementations, build tags, coverage, NATS embedded, table-driven

### docs/specifications/howto-run-tests.md
Summary: Practical test execution guide — go test commands, build tags, Makefile shortcuts for each test level, coverage flags, race detection, verbose output.
Keywords: testing guide, go test, build tags, Makefile, test commands, coverage, race detection, how-to

### docs/specifications/DEPLOYMENT-MODES.md
Summary: Three progressive deployment modes — Mode 1 (simple binary, implemented), Mode 2 (containerized services, future), Mode 3 (K3s fleet, future). Current: single Go binary + systemd + NATS.
Keywords: deployment modes, simple binary, systemd, containerized, K3s, Podman, progressive deployment

### docs/specifications/build-targets.md
Summary: Supported platforms — primary (linux-arm64, linux-amd64), RPi 5, Orange Pi, Jetson, NUCs. Cross-compilation, CGO requirements, Go 1.22+ minimum.
Keywords: build targets, cross-compilation, GOOS, GOARCH, ARM64, AMD64, Raspberry Pi, Jetson, CGO

### docs/specifications/cli-component-commands.md
Summary: CLI commands for third-party component management — search, install, list, validate. Registry integration for discovering and installing external components and services.
Keywords: CLI commands, component search, component install, service discovery, validation, registry, third-party

### docs/specifications/hardware-requirements.md
Summary: Hardware requirements for K3s deployments — three tiers: Primary (RPi 5), Performance (Jetson Orin Nano), Budget AI (Orange Pi 5B). Min 4GB RAM, 4 cores, SSD/NVMe/eMMC (not SD card).
Keywords: hardware requirements, K3s, Raspberry Pi 5, Jetson Orin, Orange Pi, RAM, SSD, storage, power supply

### docs/specifications/hello-sensor-design.md
Summary: Canonical design example — CPU temperature sensor showing Sensor interface, Protocol Buffers, NATS publishing, cross-platform support (Linux/macOS), platform detection, fake implementation, testing.
Keywords: hello sensor, design example, CPU temperature, Sensor interface, protobuf, NATS topics, platform detection, fake

### docs/specifications/linux-boards.md
Summary: Supported SBCs — RPi 5 as reference platform, primary compute boards, secondary nodes, small gateway boards. Board selection guide and distributed architecture roles.
Keywords: Linux boards, SBC, Raspberry Pi 5, reference platform, distributed architecture, gateway boards, board selection

### docs/specifications/publication.md
Summary: Documentation tooling — three components: mdBook (book/tutorials), Material for MkDocs (website/guides), pkgsite (Go API docs). Unified container build system.
Keywords: documentation, publication, mdBook, MkDocs, pkgsite, book, website, API docs, container build

### docs/specifications/serial-interfaces.md
Summary: Reference pointer — GSP has moved to separate gorai-gsp repository. Compact binary protocol with CRC-16-CCITT, 40+ message types, TinyGo compatible.
Keywords: serial, GSP, gorai-gsp, MOVED, binary protocol, TinyGo, CRC

### docs/specifications/service-rdl-schema.md
Summary: Schema for service.rdl.json — metadata file third-party container services include for discovery, installation, NATS topic documentation, configuration schema, and performance characteristics.
Keywords: Service RDL, service.rdl.json, metadata, discovery, NATS topics, configuration schema, third-party services

### docs/specifications/gorai-component-schema.yaml
Summary: YAML metadata schema for gorai-component.yaml — third-party Go component self-description for CLI discovery, compatibility validation, configuration docs, and quality signals.
Keywords: component metadata, YAML, gorai-component.yaml, discovery, validation, third-party, quality signals

### docs/specifications/service-rdl-schema.json
Summary: JSON Schema for Service RDL v1.0 — validates service name, type (vision/slam/navigation/motion/mlmodel/generic), model, version (semver), repository, container config.
Keywords: JSON Schema, Service RDL, validation, service types, semver, container configuration

### docs/specifications/archive/README.md
Summary: Archive manifest for deprecated specifications — points to current architecture, lists archived docs and reasons for archival.
Keywords: archive, deprecated, specifications, manifest

### docs/specifications/archive/deployment-k3s-v3.md
Summary: K3s-everywhere architecture — all robots on Kubernetes for consistency from single to fleet. Unified deployment model for edge AI.
Keywords: K3s, Kubernetes, deployment, orchestration, edge AI, container, archived

### docs/specifications/archive/deployment-podman-v1.md
Summary: Podman-everywhere alternative — Podman pods + systemd, lighter than K8s, container isolation with lower overhead.
Keywords: Podman, systemd, containers, deployment, lightweight, archived

### docs/specifications/archive/deployment-tiered-v2.md
Summary: Tiered deployment — Tier 1 (systemd, simple), Tier 2 (K3s single-node, complex), Tier 3 (K3s multi-node, fleet). Draft.
Keywords: tiered deployment, systemd, K3s, tiers, complexity, archived

### docs/specifications/archive/gorai-container-v2.md
Summary: Container spec for gorai CLI and tiered deployment models. Deployment strategy varies by robot complexity.
Keywords: containers, CLI, deployment tiers, NATS, systemd, K3s, archived

### docs/specifications/archive/hardware-v1.md
Summary: Historical hardware compatibility — SBCs, NPU-capable boards, Raspberry Pi models, Amazon links for procurement.
Keywords: hardware, SBC, Raspberry Pi, NPU, edge AI, BeagleBone, Orange Pi, archived

### docs/specifications/archive/systemd-container-orchestration-v2.md
Summary: Podman pods + systemd as Tier 2 for complex robots — when to use Podman over K3s, systemd service unit configuration.
Keywords: Podman, systemd, Tier 2, orchestration, containers, archived

---

## docs/hardware/

### docs/hardware/sensor-analysis.md
Summary: Comprehensive prosumer sensor analysis — vision (RGB, stereo, depth: ZED 2i, RealSense D435/D455, OAK-D), range (LiDAR, ultrasonic, ToF), IMU/AHRS, GPS, environmental. Interfaces, specs, and prices.
Keywords: sensors, cameras, depth, stereo, RealSense, ZED, OAK-D, LiDAR, ultrasonic, IMU, AHRS, GPS, I2C, SPI, UART, prices

### docs/hardware/motor-analysis.md
Summary: Motor types for prosumer robotics — BLDC (FOC control), servo (RC PWM, Dynamixel, Herkulex), DC geared with encoders, stepper, marine thrusters. Control boards: ODrive, VESC.
Keywords: motors, BLDC, FOC, servo, PWM, Dynamixel, Herkulex, ODrive, VESC, stepper, encoder, thruster, ESC, CAN bus

### docs/hardware/sbc-comparison-rpi-to-opi.md
Summary: Orange Pi 5 vs Raspberry Pi 5 + AI accelerators — recommends RPi 5 + Hailo for superior ecosystem, GPIO/HAT compatibility despite OPi's integrated 6 TOPS NPU.
Keywords: SBC comparison, Raspberry Pi 5, Orange Pi 5, Hailo, NPU, GPIO, HAT compatibility, price comparison

### docs/hardware/orange-pi-future-support.md
Summary: Deferred Orange Pi support — RK3588 GPIO calculations, pin mappings for OPi 5 Plus/Pro/B, rationale for deferral (overlay complexity, boot issues). Preserved for future.
Keywords: Orange Pi deferred, RK3588, GPIO mapping, device tree, future support, pin configuration

---

## docs/guides/

### docs/guides/README.md
Summary: Dependencies overview document listing Gorai's development tool requirements and setup instructions.
Keywords: dependencies, setup, requirements, overview

### docs/guides/development-tools.md
Summary: Development environment setup — Go 1.22+, NATS server, protoc compiler, linting tools, editor configuration. Full tool list with installation instructions.
Keywords: development tools, Go installation, NATS server, protoc, linting, editor setup, prerequisites

### docs/guides/install-nats.md
Summary: NATS server installation guide — binary installation, systemd service setup, configuration for JetStream, verification steps.
Keywords: NATS installation, server setup, systemd, JetStream, configuration, verification

### docs/guides/nats-setup.md
Summary: NATS configuration guide for Gorai — server configuration options, JetStream setup, authentication config, monitoring endpoints, cluster and leaf node setup.
Keywords: NATS configuration, server config, JetStream, authentication, monitoring, cluster, leaf nodes

### docs/guides/nats.conf
Summary: Reference NATS server configuration file with JetStream enabled, store directory, and default settings.
Keywords: NATS config file, JetStream, server settings, reference config

### docs/guides/gorai-nats-setup.sh
Summary: Shell script for automated NATS server setup including download, installation, systemd service creation, and JetStream configuration.
Keywords: NATS setup script, automation, systemd service, JetStream, installation script

### docs/guides/gorai-nats-verify.sh
Summary: Shell script to verify NATS server installation — checks service status, JetStream availability, connectivity, and basic pub/sub functionality.
Keywords: NATS verification, health check, JetStream check, connectivity test

### docs/guides/podman-compose.yml
Summary: Podman Compose file for running NATS server in a container with JetStream enabled and port mappings.
Keywords: Podman, container, NATS server, JetStream, compose, development environment

---

## docs/ecosystem/

### docs/ecosystem/README.md
Summary: Index of ecosystem components — gorai (core), gorai-gsp (serial protocol), gorai-nats-gw (gateway), gorai-pushprom (metrics), gorai-gps (GPS), rp2040-pwm (firmware). Includes satellite repo naming patterns.
Keywords: ecosystem, gorai-gsp, gorai-nats-gw, gorai-pushprom, gorai-gps, rp2040-pwm, satellite repos, firmware, monitoring, gateway

---

## docs/plans/

### docs/plans/core-implementation-plan.md
Summary: Framework core v0.1.0 implementation — 6 phases: protocol buffers, core packages, resource model, component interfaces, NWS/NWC network transparency, hello-sensor validation. Gap analysis vs specification.
Keywords: implementation plan, protocol buffers, core packages, resource model, NWS, NWC, hello-sensor, gap analysis

### docs/plans/external-services.md
Summary: Service RDL pattern for compute-intensive workloads (ML inference, SLAM) running as separate processes/containers via NATS. Service RDL files define interfaces with topic patterns.
Keywords: external services, Service RDL, NATS, ML inference, SLAM, containers, topic patterns

### docs/plans/gps.md
Summary: Go program reading NMEA GPS data from serial port at 4800 baud, parsing into structured data, converting to JSON, publishing to NATS "gps" topic.
Keywords: GPS, NMEA, serial port, NATS, JSON, 4800 baud

### docs/plans/hailo.md
Summary: Containerized AI service using Hailo NPU for YOLOX person detection — draws bounding boxes, publishes annotated images to NATS. RPi 5 + Hailo AI Kit architecture.
Keywords: Hailo NPU, YOLOX, person detection, container, AI inference, bounding boxes, Raspberry Pi 5

### docs/plans/prometheus.md
Summary: Prometheus as mandatory local dependency — time-series storage for sensor data, /metrics endpoint, PromQL dashboard queries, optional alerting. 200-500MB RAM.
Keywords: Prometheus, metrics, monitoring, time-series, /metrics, PromQL, alerting, dashboard

### docs/plans/book.md
Summary: Book outline — 15 chapters covering motivation, architecture, NATS, sensors, actuators, vision, services, development, testing, AI/ML, project organization. Target: Go developers interested in robotics.
Keywords: book outline, chapters, table of contents, learning path

### docs/plans/publication-implementation.md
Summary: Dual-format publication — mdBook for "lean-back" reading (tablet/e-reader) and MkDocs Material for "lean-forward" reference (quick lookup, code copy). Single-source content.
Keywords: publication, mdBook, MkDocs, lean-back, lean-forward, dual format, single source

### docs/plans/content-migration.md
Summary: Migration from mdBook/MkDocs with symlinks to Pandoc (PDF/ePub) + Hugo (website). Eliminates symlinks, format-specific content optimization.
Keywords: content migration, Pandoc, Hugo, mdBook, MkDocs, symlinks, documentation tooling

### docs/plans/rdl-auto.md
Summary: RDL-driven code generation — write Robot Definition Language, auto-generate complete buildable code skeleton. Minimizes boilerplate, RDL as source of truth.
Keywords: RDL, code generation, automation, boilerplate, scaffolding

### docs/plans/robot-build-deploy-architecture.md
Summary: Build/deploy architecture — Gorai is a library (not framework), JSON RDL configuration, cross-compilation + scp/rsync deployment, systemd integration.
Keywords: build architecture, deployment, library vs framework, RDL, cross-compilation, systemd, scp, rsync

### docs/plans/migrate-to-monolithic.md
Summary: SUPERSEDED — originally described containerized → monolithic migration. Now replaced by tiered deployment (Tier 1: native binaries, Tier 2: Podman, Tier 3: K3s).
Keywords: SUPERSEDED, monolithic, containerized, deployment tiers, architecture decision

### docs/plans/core-implementation-plan.md
Summary: Framework core v0.1.0 implementation — 6 phases: protocol buffers, core packages, resource model, component interfaces, NWS/NWC, hello-sensor validation.
Keywords: implementation plan, phases, protocol buffers, resource model, NWS, NWC

### docs/plans/spec-implementation.md
Summary: v0.2.0 spec updates — new types: Power, Space, Link (components), Behavior, Coordinator (services). AI/LLM interfaces, derived sensors. Five implementation phases.
Keywords: v0.2.0, Power, Space, Link, Behavior, Coordinator, AI/LLM, derived sensors

### docs/plans/update-sensors-motors.md
Summary: Interface updates for expanded sensor/actuator types — AHRS, LiDAR, PresenceSensor, ThermalArray, ForceSensor, CurrentSensor, ReflectanceSensor, Servo, Stepper.
Keywords: sensor updates, actuator updates, AHRS, LiDAR, force sensor, servo, stepper, interface expansion

### docs/plans/integrated-container-build.md
Summary: RDL schema extension for automatic container service building — adds build section to ContainerServiceConfig with context, containerfile, build args.
Keywords: container build, RDL extension, automation, Podman, Containerfile

### docs/plans/understand-publish.md
Summary: Current publishing system documentation — mdBook for book, MkDocs Material for website, symbolic links to canonical content, containerized builds.
Keywords: publishing system, mdBook, MkDocs, symbolic links, containerized builds

---

## docs/projects/

### docs/projects/project-pan-tilt.md
Summary: Gorai-Sentinel — pan-tilt sensor fusion platform with camera + VL53L5CX ToF sensor (8x8). First reference implementation validating Gorai with real-time control, actions, services, parameters.
Keywords: Gorai-Sentinel, pan-tilt, sensor fusion, camera, ToF, VL53L5CX, reference implementation

### docs/projects/project-pan-tilt-distributed-option.md
Summary: Distributed hardware evaluation for Gorai-Sentinel — centralized RPi 5 vs distributed nodes. Addresses single-point-of-failure, modularity, thermal constraints.
Keywords: distributed architecture, Gorai-Sentinel, Raspberry Pi 5, Hailo AI Kit, modularity, thermal

### docs/projects/project-simple-boat.md
Summary: Gorai-Skimmer autonomous surface vehicle — boogie-board, differential thrust, Open Echo sonar (bathymetry 0.5-50m), GPS navigation, water temperature. Under $500 BOM.
Keywords: ASV, Gorai-Skimmer, differential thrust, bathymetry, sonar, GPS, water monitoring, autonomous boat

### docs/projects/surf-test/README.md
Summary: Two-robot distributed control demo — ground station (PC keyboard + dashboard :8081) communicates via NATS with main robot (RPi 5, pan/tilt servos, camera, dashboard :8080).
Keywords: distributed control, two-robot, ground station, keyboard, NATS, dashboard, servo, camera

---

## docs/book/chapters/

### docs/book/chapters/00-frontmatter.md
Summary: Book title "Gorai: Building Modern Robots with Go and NATS", copyright, safety disclaimer, prerequisites (Go programming, Linux basics, basic electronics).
Keywords: frontmatter, title, copyright, prerequisites, safety disclaimer

### docs/book/chapters/01-introduction.md
Summary: Introduction — Gorai as Go-based ROS 2 alternative for AI, authors Greg Herlein (Navy nuclear, distributed systems) and Luca Herlein (FIRST robotics, aerospace), learning objectives.
Keywords: introduction, authors, Greg Herlein, Luca Herlein, learning objectives, background

### docs/book/chapters/02-why-gorai.md
Summary: Why Gorai exists — examines ROS 2, YARP, Viam pain points (C++ complexity, Python performance, steep learning curves) and how Go + NATS addresses the gaps.
Keywords: motivation, ROS 2 comparison, framework comparison, pain points, C++ complexity, DDS, rationale

### docs/book/chapters/03-architecture.md
Summary: Architecture mental model — robots as networks of communicating processes, resource abstraction (components, services, modules), three-layer architecture, location transparency.
Keywords: architecture, distributed systems, resource model, components, services, modules, NATS, location transparency

### docs/book/chapters/04-nats.md
Summary: NATS deep dive — why NATS was chosen, 18M msg/sec performance, comparison with DDS/ZeroMQ/gRPC, pub/sub, request/reply, Gorai topic patterns, QoS, JetStream.
Keywords: NATS, messaging, pub/sub, request/reply, JetStream, performance, DDS comparison, subjects, wildcards, QoS

### docs/book/chapters/05-sensors.md
Summary: Sensor abstraction — Sensor interface with Readings() returning map[string]any, standard reading keys, Protocol Buffer types, built-in sensors (IMU, GPS, temperature), fake pattern.
Keywords: sensors, Sensor interface, Readings, map string any, protobuf, IMU, GPS, temperature, fake pattern

### docs/book/chapters/06-actuators.md
Summary: Actuator interfaces — base Actuator (Stop()), Motor, Servo, Arm. Safety-first design with immediate stop, IsMoving() state queries, control patterns.
Keywords: actuators, Motor interface, Servo, Arm, safety, Stop, IsMoving, control patterns

### docs/book/chapters/07-vision.md
Summary: Vision systems — Camera interface for USB/CSI/IP/depth cameras, image capture (single frame and streaming), image formats, CV integration, depth sensing.
Keywords: vision, Camera interface, image capture, streaming, depth sensing, computer vision

### docs/book/chapters/08-services.md
Summary: Services vs components — services process data and coordinate actions (software capabilities). VisionService for detection/classification, Service interface.
Keywords: services, components vs services, VisionService, software capabilities, detection, classification

### docs/book/chapters/09-behaviors.md
Summary: Behavior patterns — finite state machines, behavior trees, reactive architectures. Behavior interface with Update() method, RobotState input.
Keywords: behaviors, FSM, behavior trees, reactive architecture, Update, RobotState, decision-making

### docs/book/chapters/10-coordinators.md
Summary: Coordinators — mission orchestration for single/multi-robot systems. Coordinator interface: Start/Status/Cancel/Pause, task sequencing, resource allocation.
Keywords: coordinators, mission orchestration, multi-robot, task sequencing, MissionStatus, Start, Cancel, Pause

### docs/book/chapters/11-development-environment.md
Summary: Development setup — zero-to-code in 15 minutes. Go 1.21+, NATS server, editor config, verification steps.
Keywords: development environment, setup, Go installation, NATS server, quick start, editor

### docs/book/chapters/12-hello-sensor.md
Summary: Hello-sensor walkthrough — CPU temperature reading, NATS publishing, platform abstraction (Linux/macOS), fake implementations, statistics tracking, graceful shutdown.
Keywords: hello-sensor tutorial, CPU temperature, platform abstraction, fake implementations, graceful shutdown

### docs/book/chapters/13-custom-components.md
Summary: Building custom components — when to create them, standard structure, step-by-step DRV8833 motor driver example, interface definition, reusability patterns.
Keywords: custom components, DRV8833, motor driver, component structure, reusability, fake implementations

### docs/book/chapters/14-testing-strategies.md
Summary: Testing pyramid for safety-critical systems — build tags (unit, component, integration, module, system, hardware), table-driven tests, catching bugs at lowest level.
Keywords: testing, test pyramid, build tags, table-driven tests, safety-critical, test categories

### docs/book/chapters/15-ai-ml-integration.md
Summary: AI/ML on the edge — hardware accelerators (RK3588 NPU 6 TOPS), real-time requirements, power constraints, go-rknnlite bindings.
Keywords: AI/ML, edge inference, NPU, RK3588, hardware accelerators, real-time, go-rknnlite

### docs/book/chapters/16-project-organization.md
Summary: Project best practices — monorepo structure: api/, pkg/, components/, services/, driver/, accel/, nws/, examples/. When to use monorepo.
Keywords: project organization, monorepo, directory layout, code organization, best practices

### docs/book/chapters/17-ai-assisted-development.md
Summary: AI-assisted development philosophy — effective prompting for component/test/protobuf generation. AI as developer amplification for speed, consistency, exploration.
Keywords: AI-assisted development, prompting, code generation, test generation, developer productivity

### docs/book/chapters/18-conclusion.md
Summary: Conclusion — recap of all topics, Gorai vs ROS 2/Viam/YARP comparison across language, middleware, discovery, build, AI/ML, MCU support.
Keywords: conclusion, framework comparison, ROS 2, Viam, YARP, summary, future vision

### docs/book/chapters/99-appendices.md
Summary: Command reference — Gorai scripts, NATS CLI, Go commands, Makefile targets, Protocol Buffer reference for gorai/std messages.
Keywords: appendices, command reference, NATS CLI, Go commands, Makefile, protobuf, glossary

---

## Root-Level Docs

### docs/EXAMPLES.md
Summary: Example robots — blinky (LED GPIO), gps-tracker (GPS simulator), hello-camera (V4L2). Prerequisites, hardware setup, build and run instructions.
Keywords: examples, blinky, gps-tracker, hello-camera, tutorial, getting started, LED, GPS, V4L2

### docs/gorai-branch-differences.md
Summary: HAL migration branch changes — PWM gpiod and I2C bridge component migration from direct hardware to HAL dependency injection.
Keywords: HAL migration, branch differences, PWM, I2C, dependency injection, GPIO
