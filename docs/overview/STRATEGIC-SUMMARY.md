# Gorai Strategic Summary

**Quick reference for developers and contributors**

This document summarizes the key strategic decisions that guide Gorai's development. For comprehensive analysis, see [vision-analysis.md](vision-analysis.md).

---

## Core Positioning

### What We Are
- **Prosumer robotics framework** — real autonomy between educational toys ($100-300) and enterprise platforms ($4,000+)
- **"npm for robotics"** — component registry with `gorai component add` for discovering and installing drivers, behaviors, and integrations
- **Single binary deployment** — `gorai run` with embedded NATS, no containers, no orchestrators
- **Go-first, pragmatically polyglot** — Go core, Python/C++ services via NATS when appropriate (future)
- **Dual hardware access** — supports RP2040 co-processor (via GSP/2 protocol) and native RPi GPIO/I2C for direct hardware control

### What We're NOT
- **Not a ROS 2 replacement** — we target different markets (prosumer vs. enterprise/research)
- **Not language-purist** — we use the best tool for each job
- **Not anti-ROS 2** — bridge planned (future) for ecosystem compatibility
- **Not enterprise-focused** — we optimize for accessibility, not feature completeness

---

## Flagship Products

### ORCA — Autonomous Submersible (Under $2,500)

ORCA is the flagship hardware project and Gorai's category-creating opportunity. An autonomous submersible under $2,500 has zero competition:

| Segment | Product | Price | Capability |
|---------|---------|-------|------------|
| **Consumer ROV** | BlueROV2 | $4,600+ | Remote-controlled only, no autonomy |
| **Professional AUV** | Various | $50,000+ | Autonomous, but priced for institutions |
| **Gorai ORCA** | ORCA | Under $2,500 | Autonomous, prosumer-accessible |

ORCA targets citizen scientists monitoring marine environments, hobbyist underwater explorers, and students learning marine robotics. There is no product in this price/capability space today.

### Surf — Autonomous Surface Vessel (Under $1,500)

Surf is the second hardware product. An autonomous surface vessel for water quality monitoring, bathymetric mapping, and coastal survey work at a price point accessible to individuals and small organizations.

### Drive — Land Robot (Deferred)

Land robotics is a competitive market with many existing platforms at multiple price points. Drive is deferred indefinitely in favor of the blue-ocean opportunities in marine robotics.

---

## Target Market Definition

### Primary Users (Prosumer)
- **Citizen scientists** — monitoring watersheds, marine environments, water quality
- **Makers** — building capable autonomous robots (not toys)
- **Students/educators** — teaching robotics without PhD toolchains
- **Small organizations** — needing autonomy without enterprise budgets
- **Hobbyists** — want more than Arduino, less complexity than ROS 2

### NOT Target Users
- Enterprise warehouse robotics -> use ROS 2 + AMR vendors
- Autonomous vehicle research -> use ROS 2 + Autoware
- PhD robotics research -> use ROS 2, YARP
- Defense/aerospace -> use certified systems
- Medical robotics -> use certified systems

---

## Runtime: `gorai run`

`gorai run` is THE deployment model. A single Go binary with embedded NATS — no K3s, no process-compose, no containers.

```
┌─────────────────────────────────────────────────────────────────┐
│                    User Experience                               │
│                                                                  │
│   robot.json (RDL)  →  gorai run  →  Robot running              │
│                                                                  │
│   Users work with: Robot Definition Language (JSON)             │
│   Output: Single binary with embedded NATS (~10-20MB)           │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    What Actually Runs                            │
│                                                                  │
│   Single Go Binary                                               │
│   ├── Embedded NATS Server                                      │
│   ├── Components (GPS, GPIO, sensors, motors)                   │
│   ├── Message Router (NATS client)                              │
│   └── Behaviors (state machines, scripts)                       │
└─────────────────────────────────────────────────────────────────┘
```

**Why `gorai run`?**
- **Fastest time to working robot** — under 1 hour
- **Zero external dependencies** — NATS is embedded, not a separate service
- **Low resource usage** — ~20-50MB RAM vs 512MB+ for containers
- **Easy debugging** — single process, standard Go tooling
- **Correct for the target market** — prosumers need simplicity, not orchestration

### Deferred: Containers and K3s

Containers and K3s are future-phase capabilities, not current priorities:

| Phase | Model | Status | Use Case |
|-------|-------|--------|----------|
| **Current** | `gorai run` (single binary, embedded NATS) | Active | All current use cases |
| **Future Phase A** | Binary + containers (Podman) | Deferred | ML/vision services requiring Python |
| **Future Phase B** | K3s fleet management | Deferred | Multi-robot fleet coordination |

These phases will be pursued only when concrete user demand requires them. See [Future Roadmap](FUTURE-ROADMAP.md) and [archived K3s designs](archive/future-state/) for details.

---

## Component Registry: "npm for Robotics"

Gorai's component ecosystem follows the package manager model that made npm, pip, and cargo successful:

```
gorai component add gps-nmea         # Install a GPS driver
gorai component add imu-bno055       # Install an IMU driver
gorai component add waypoint-nav     # Install waypoint navigation behavior
gorai component list                 # List installed components
gorai component search sonar         # Search the registry
```

Components are self-contained Go packages with typed NATS interfaces. The registry enables:
- **Discoverability** — search and browse available drivers, sensors, behaviors
- **Composability** — mix and match components via RDL configuration
- **Community contribution** — third-party components published to the registry
- **Version management** — pin component versions for reproducible builds

---

## Driver Model: Dual Hardware Access

Gorai supports two approaches to hardware access, chosen per-robot based on requirements:

### Approach 1: RP2040 Co-Processor (via GSP/2 Protocol)

```
┌──────────────┐     GSP/2 (serial)     ┌──────────────┐
│  RPi / SBC   │ ◄──────────────────► │   RP2040     │
│  gorai run   │                        │  PWM, ADC    │
│  (Go binary) │                        │  GPIO, I2C   │
└──────────────┘                        └──────────────┘
```

- RP2040 handles real-time hardware I/O (PWM, ADC, precise timing)
- Communication via GSP/2 serial protocol (40+ message types)
- Offloads timing-critical operations from Linux
- Preferred for motor control, servo-intensive applications

### Approach 2: Native RPi GPIO/I2C

```
┌──────────────────────────────┐
│  RPi / SBC                   │
│  gorai run                   │
│  ├── GPIO driver (sysfs/gpiod) │
│  ├── I2C driver (i2c-dev)   │
│  └── SPI driver (spidev)    │
└──────────────────────────────┘
```

- Direct hardware access from the Go binary on the RPi/SBC
- Simpler wiring, fewer components, lower cost
- Suitable for sensors, simple actuators, non-timing-critical I/O

The choice is per-robot, configured in RDL. Both approaches use the same component interface — a GPS component works identically whether the underlying UART comes from an RP2040 or directly from the RPi.

---

## Key Strategic Decisions

### 1. Language Strategy: Pragmatic Polyglot

**Decision**: Go core + polyglot services via NATS clients

| Component Type | Language | Rationale |
|----------------|----------|-----------|
| Framework core | Pure Go | Concurrency, deployment, AI-assisted coding |
| Simple sensors | Pure Go | GPIO, I2C, GPS, IMU — protocol parsing |
| Vision preprocessing | Python | OpenCV, scikit-image ecosystem (future) |
| ML inference | Python or ONNX Runtime (C++) | PyTorch/TensorFlow training; ONNX deployment (future) |
| SLAM | C++ with Go wrapper | Cartographer, ORB-SLAM too valuable to rewrite (future) |
| Camera drivers | cgo wrappers | V4L2, RealSense SDKs in C/C++ |
| Motor controllers | Go or cgo | If SDK exists (Dynamixel), wrap; else pure Go |
| Web UI | Go templates + HTMX | Avoid separate JS frontend complexity |

**Why**: NATS has clients for 40+ languages. Any language can be a Gorai service.

### 2. ROS 2 Positioning: Coopetition, Not Competition

**Decision**: Build compatibility bridge, don't compete directly

```
DO:
- Build ROS 2 bridge (future phase)
- Position as "ROS 2 for prosumers"
- Reuse ROS 2 message type designs
- Document ROS 2 migration paths
- Collaborate with ROS 2 community

DON'T:
- Try to replace ROS 2 in research/enterprise
- Ignore ROS 2 ecosystem
- Start language wars
- Lock users into Gorai-only world
```

**Why ROS 2 Bridge Matters**:
1. **Component reuse** — use ROS 2 drivers when no Go alternative exists
2. **Simulation** — Gazebo integration via bridge
3. **Migration path** — students can keep ROS 2 packages they know
4. **Ecosystem perception** — avoid "vendor lock-in" perception

### 3. Deployment: `gorai run` First, Everything Else Later

**Decision**: `gorai run` with embedded NATS is the only supported deployment model today

Containers and K3s are explicitly deferred. The prosumer market does not need orchestration — it needs a robot that works in under an hour. Complexity will be added only when real users with real workloads demand it.

### 4. Cloud Patterns vs. ROS 2

**Decision**: Embrace cloud-native patterns as competitive advantage

| Cloud Pattern | ROS 2 Approach | Gorai Approach | Advantage |
|---------------|----------------|----------------|-----------|
| Message Broker | DDS peer-to-peer | NATS (embedded) | Decouples components, easy monitoring |
| Service Mesh | Custom discovery | NATS queue groups | Automatic load balancing |
| Event Sourcing | rosbag (manual) | JetStream (built-in) | Replay streams, time-travel debug |
| Config Management | Per-node params | NATS KV store | Global config, hot reload |
| Observability | Custom diagnostics | Prometheus /metrics | Industry-standard tools |
| Orchestration | Manual | systemd (future: K3s) | Health checks, rolling updates |

**Why**: ROS 2's DDS is pre-cloud (2004 LAN design). We use infrastructure proven at global scale.

---

## Competitive Positioning

### vs. BlueROV2 and Consumer ROVs
- BlueROV2 ($4,600) is remote-controlled only — no autonomy
- ORCA (under $2,500) is autonomous — different category entirely
- No direct competitor exists in autonomous submersibles under $50,000

### vs. ROS 2
Not "vs. ROS 2" — different markets:
- **Gorai**: Prosumer, days to productivity, accessible, single binary
- **ROS 2**: Enterprise/research, months to mastery, comprehensive, multi-process
- Think: "ROS 2 for prosumers," not "ROS 2 killer"

### vs. Viam
- Viam requires cloud connectivity and a Viam account
- Gorai runs fully offline — critical for marine and field deployments
- Gorai is open source (Apache 2.0); Viam's core is proprietary

### vs. Arduino/MicroPython Ecosystem
- Arduino is microcontroller-level, no autonomy framework
- Gorai provides full autonomous behaviors, mission planning, NATS messaging
- Gorai can use Arduino-class hardware (RP2040) as a co-processor via GSP/2

---

## Development Priorities

### Phase 1: `gorai run` Framework (Current)
1. `gorai run` with embedded NATS — the runtime
2. Component registry (`gorai component add`)
3. Basic sensors (GPS, IMU, compass) in pure Go
4. Motor control (I2C/PWM) via both RP2040 (GSP/2) and native RPi
5. CLI: validate, run, build, component add/list/search
6. ORCA and Surf hardware platform support

### Phase 2: Marine Autonomy
1. Waypoint navigation and mission planning
2. Marine-specific sensors (depth, sonar, water quality)
3. Camera (v4l2) integration
4. Web dashboard for mission monitoring
5. Telemetry and data logging

### Phase 3: Containerized Services (Deferred)
1. Vision services (Python/OpenCV)
2. ML inference services
3. Container orchestration (Podman)
4. Mixed native/container deployment

### Phase 4: Fleet and Ecosystem (Deferred)
1. K3s deployment infrastructure
2. ROS 2 bridge MVP (ROS2 <-> NATS)
3. Gazebo simulation integration
4. Fleet management dashboard
5. SLAM integration (Cartographer via C++)

---

## Messaging Guidelines

### Positioning Statements

**DO say**:
- "The robotics platform for the AI era"
- "npm for robotics — `gorai component add`"
- "Autonomous submersible under $2,500 — zero competition"
- "Build a robot in under an hour"
- "`gorai run` — single binary, embedded NATS, no containers"
- "Go core, best tool for each job"
- "Cloud-native patterns proven at scale"

**DON'T say**:
- "Gorai is better than ROS 2" (different markets)
- "Pure Go or nothing" (we're pragmatic)
- "We reinvented everything" (we borrowed proven patterns)
- "ROS 2 is bad" (it's excellent for its target market)
- "K3s-based" or "container-based" (those are deferred, not current)

---

## Risk Mitigation

### Risk 1: Go Ecosystem Gaps
**Mitigation**:
- Support polyglot services (Python/C++ via NATS)
- Provide cgo wrappers for critical libraries
- Build community driver ecosystem via component registry
- Document escape hatches

### Risk 2: "Not Invented Here" Perception
**Mitigation**:
- Plan ROS 2 bridge for ecosystem compatibility
- Collaborate with ROS 2 community
- Credit ROS 2 for design inspiration
- Show pragmatism over purity

### Risk 3: Marine Hardware Complexity
**Mitigation**:
- ORCA uses proven components (BlueRobotics thrusters, standard pressure housings)
- Dual driver model (RP2040 co-processor or native RPi) provides flexibility
- Start with surface vessel (Surf) as simpler validation platform
- Open hardware designs enable community contribution and cost reduction

### Risk 4: Limited Adoption
**Mitigation**:
- Free tutorials on accessible hardware
- Clear examples with working code
- Open source core (Apache 2.0)
- No vendor lock-in (NATS = open)
- Component registry lowers barrier to building new robots

---

## Contributing Guidelines

When contributing to Gorai:

1. **Read vision-analysis.md** — understand strategic context
2. **Follow language strategy** — Go core, polyglot when appropriate
3. **Don't fight ROS 2** — we're complementary, not competitive
4. **Pragmatism over purity** — best tool for job
5. **Keep it simple** — `gorai run` is the deployment model, not containers
6. **Document design decisions** — AI-assisted dev requires clarity
7. **Component registry first** — new drivers should be installable via `gorai component add`

---

## Further Reading

- [Vision Analysis](vision-analysis.md) — Comprehensive strategic analysis
- [Design Comparison](general-designs.md) — ROS 2, Viam, YARP analysis
- [Framework Specification](../specs/gorai-framework-specification.md) — Technical spec
- [Code Organization](../specs/code-organization.md) — Module structure
- [Future Roadmap](FUTURE-ROADMAP.md) — Container/K3s expansion plans (deferred)
- [Archived K3s Designs](archive/future-state/) — Preserved future state documentation
- [Component CLI Commands](../specifications/cli-component-commands.md) — Component registry CLI spec
- [Third-Party Component Ecosystem](../architecture/third-party-component-ecosystem.md) — Ecosystem design

---

**Last Updated**: 2026-04-12
**Status**: Active strategic guidance
