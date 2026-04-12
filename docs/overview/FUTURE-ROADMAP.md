# Gorai Future Roadmap

This document describes the planned evolution of Gorai from simple binary deployment to full distributed fleet management. The architecture is designed to be progressive — start simple, add complexity only when needed.

---

## Phase 1: Simple Binary -- The Product

**Status: This IS the product.**

`gorai run` is the Gorai runtime. A single Go binary with an embedded NATS server reads a JSON configuration file, brings up all components and services, and runs the robot. There is no external dependency to install. This is not a stepping stone -- it is the deployment model for all current and near-term use cases.

ORCA (autonomous submersible) runs `gorai run` on a Raspberry Pi inside a pressure housing -- the ultimate proof that single-binary deployment is the right model for embedded robotics.

```
┌─────────────────────────────────────────────────────────────────┐
│                    Single Go Binary                              │
│                                                                  │
│   ├── Components (GPS, GPIO, sensors, motors)                   │
│   ├── Behaviors (state machines, scripts)                       │
│   ├── Embedded NATS Server                                      │
│   └── Message Router (NATS client)                              │
│                                                                  │
│   No external dependencies.                                     │
└─────────────────────────────────────────────────────────────────┘
```

### Component Ecosystem: "npm for Robotics"

The component ecosystem is a key Phase 1 deliverable — this is the "npm for robotics" moment. It uses the **Caddy model**: blank imports in `main.go` as the component manifest, with Go modules handling all dependency management.

**Deliverables:**

- **`gorai-robot-template`** — Template repository giving users a working starting point (`main.go`, `go.mod`, `robot.json`, `Makefile`). Clone and customize.
- **`gorai-registry`** — JSON registry mapping friendly component names (e.g., `sensor/bno055`) to Go module paths. The index that makes `gorai component search` work.
- **`gorai component` CLI** — `search`, `add`, `list`, and `remove` commands. `gorai component add sensor/bno055` looks up the registry, runs `go get`, and adds the blank import to `main.go`.
- **Reference components as standalone Go modules** — GPS, IMU, motor controllers, and navigation behaviors published as independent Go modules that any robot project can import.
- **Custom component workflow** — users write components in their own repo with `init()` registration, extract and share by pushing to GitHub as standalone modules.

This is what makes Gorai accessible: `gorai component add` is the entire installation experience. No custom package manager, no containers, no submodules — Go modules provides versioning, checksums, and caching. The result is a single binary containing every component, verified at compile time.

### Characteristics
- Single binary (~10-20MB)
- Embedded NATS server (zero external dependencies)
- ~20-50MB RAM usage
- No containers, no K8s
- Direct systemd integration

### Target Users
- Makers building their first robot
- Students learning robotics
- Rapid prototyping
- Single-device deployments
- Production embedded robots (ORCA, Surf)

### Recommendation

Stay on Phase 1. K3s and process-compose were evaluated and deliberately deferred. The single-binary model handles everything from GPS trackers to autonomous submersibles. Move to Phase 2 only when a specific, proven need arises that cannot be solved within the Go binary.

---

## Phase 2: Optional Containers (Future)

**Status: Deferred -- revisit when user demand requires it**

Add containerized services for capabilities that benefit from Python or C++:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Robot System                                  │
│                                                                  │
│   ┌─────────────────┐     ┌─────────────────┐                   │
│   │  Go Binary      │     │  Container      │                   │
│   │  (core robot)   │────▶│  (vision/ML)    │                   │
│   └─────────────────┘     └─────────────────┘                   │
│            │                       │                             │
│            └───────────┬───────────┘                             │
│                        ▼                                         │
│                   NATS Server                                    │
└─────────────────────────────────────────────────────────────────┘
```

### When to Move to Phase 2
- Need vision processing (OpenCV, Python)
- Need ML inference (PyTorch, TensorFlow)
- Need SLAM (Cartographer, C++)
- Have sufficient resources (4GB+ RAM)

### Architecture Changes
- Add Podman for container management
- Container services communicate via NATS
- Go binary remains the orchestrator
- RDL extended with `services` section

### Preserved Documentation
- [Container Orchestration Design](archive/future-state/systemd-container-orchestration-v2.md)
- [Container Build Specification](archive/future-state/gorai-container-v2.md)

---

## Phase 3: K3s Fleet Management (Future)

**Status: Deferred -- revisit when user demand requires it**

Full Kubernetes-based orchestration for production fleets:

```
┌─────────────────────────────────────────────────────────────────┐
│                    K3s Cluster                                   │
│                                                                  │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│   │  Robot 1    │  │  Robot 2    │  │  Robot N    │             │
│   │  (namespace)│  │  (namespace)│  │  (namespace)│             │
│   └─────────────┘  └─────────────┘  └─────────────┘             │
│          │               │               │                       │
│          └───────────────┴───────────────┘                       │
│                          │                                       │
│                   ┌──────▼──────┐                                │
│                   │ NATS Cluster│                                │
│                   │ (JetStream) │                                │
│                   └─────────────┘                                │
└─────────────────────────────────────────────────────────────────┘
```

### When to Move to Phase 3
- Managing multiple robots (fleet)
- Need rolling updates without downtime
- Need resource isolation and limits
- Need health checks and auto-restart
- Production deployment requirements

### Architecture Changes
- K3s cluster (single-node or multi-node)
- Each robot in separate namespace
- NATS deployed as K3s service
- Full orchestration via `gorai deploy`
- Users still work with RDL (same format)

### Preserved Documentation
- [K3s Installation Guide](archive/future-state/k3s-installation.md)
- [K3s Deployment Design](archive/future-state/deployment-k3s-v3.md)

---

## Phase 4: Advanced Ecosystem (Future)

**Status: Planned**

Extended ecosystem capabilities:

### ROS 2 Bridge
- Bidirectional NATS <-> ROS 2 communication
- Use ROS 2 drivers when no Go alternative exists
- Gazebo simulation integration
- Migration path for ROS 2 users

### Fleet Management Dashboard
- Web-based fleet monitoring
- Mission planning interface
- Real-time telemetry visualization
- Multi-robot coordination

### Advanced AI/ML
- SLAM integration (Cartographer via C++)
- Object tracking and recognition
- VLM (Vision Language Models) integration
- Edge-cloud hybrid inference

---

## Migration Path

### Phase 1 → Phase 2

When you need containerized services:

1. Install Podman on your robot
2. Add `services` section to RDL
3. Build container images for vision/ML services
4. Services communicate via existing NATS topics

**RDL changes (additive):**
```json
{
  "name": "my-robot",
  "nats": {"url": "nats://localhost:4222"},
  "components": [...],
  "services": [
    {
      "name": "vision",
      "image": "gorai/vision-opencv:latest",
      "topics": {
        "subscribe": ["camera.frames"],
        "publish": ["vision.detections"]
      }
    }
  ]
}
```

### Phase 2 → Phase 3

When you need fleet management:

1. Install K3s on your robot(s)
2. Change deployment command: `gorai run` → `gorai deploy`
3. Add optional resource limits to RDL
4. Multi-robot coordination via NATS namespaces

**No RDL changes required** — the same configuration works, just deployed differently.

---

## Archived Documentation

The following documentation is preserved for future implementation:

### Container/Podman (Phase 2)
- [systemd-container-orchestration-v2.md](archive/future-state/systemd-container-orchestration-v2.md) — Container lifecycle management
- [gorai-container-v2.md](archive/future-state/gorai-container-v2.md) — Container build specification

### K3s/Kubernetes (Phase 3)
- [k3s-installation.md](archive/future-state/k3s-installation.md) — Platform-specific K3s setup
- [deployment-k3s-v3.md](archive/future-state/deployment-k3s-v3.md) — K3s deployment architecture

### Archived Code

Container and K3s-related code is preserved in `/archive/`:
- `archive/pkg/quadlet/` — Quadlet systemd integration
- `archive/pkg/runtime/container_runner.go` — Container orchestration
- `archive/examples/` — K3s-based examples

---

## Decision Framework

Use this framework to decide which phase is appropriate:

| Question | Phase 1 | Phase 2 | Phase 3 |
|----------|---------|---------|---------|
| How many robots? | 1 | 1 | 2+ |
| Need ML/vision? | No | Yes | Yes |
| Available RAM | < 2GB | 4GB+ | 4GB+ |
| Need rolling updates? | No | No | Yes |
| Production deployment? | No | Maybe | Yes |
| Container experience? | No | Some | Yes |

---

## Contributing to Future Phases

If you're interested in contributing to Phase 2 or Phase 3 development:

1. Review the archived documentation
2. Check existing designs for consistency
3. Propose changes via GitHub issues
4. Maintain backward compatibility with Phase 1 RDL

The core principle: **Users should be able to start with Phase 1 and progressively add complexity without rewriting their robot configurations.**

---

**Last Updated**: 2026-04-12
