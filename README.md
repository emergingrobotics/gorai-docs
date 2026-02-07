# Gorai Documentation

**Canonical reference for the Gorai robotics ecosystem**

This repository contains all higher-level documentation for the Gorai project — the strategy, architecture, specifications, guides, book content, and ecosystem documentation that spans beyond the core [gorai](../gorai) implementation.

## What's Here vs. What's in `gorai`

| This repo (`gorai-docs`) | Core repo (`gorai`) |
|---------------------------|---------------------|
| Strategic vision and positioning | Go source code |
| Architecture and design documents | Component/service implementations |
| Technical specifications | CLI commands |
| Book chapters and publication content | Driver code |
| Hardware analysis and comparisons | Unit/integration tests |
| Setup and deployment guides | Build system (Makefile, go.mod) |
| Ecosystem component documentation | Example robot RDL configs |
| Project plans and roadmaps | Runtime and NATS client code |
| Archived designs and examples | |

## Repository Structure

```
gorai-docs/
├── README.md                       # This file
├── CLAUDE.md                       # AI assistant context
│
├── docs/
│   ├── overview/                   # Strategic vision, positioning, framework comparisons
│   │   ├── STRATEGIC-SUMMARY.md
│   │   ├── gorai-overarching-strategy.md
│   │   ├── FUTURE-ROADMAP.md
│   │   ├── general-designs.md      # ROS 2 / Viam / YARP comparison
│   │   ├── ros2-design.md
│   │   ├── viam-design.md
│   │   ├── yarp-design.md
│   │   └── ...
│   │
│   ├── architecture/               # Design patterns, component model, NATS
│   │   ├── LLM-DESIGN-GUIDE.md     # Guide for AI-assisted component development
│   │   ├── component-reference.md
│   │   ├── hardware-abstraction.md
│   │   ├── nats-description.md
│   │   ├── gorai-nats-auth.md
│   │   ├── modules-approach.md
│   │   ├── PACKAGE-LOCATIONS.md
│   │   └── ...
│   │
│   ├── specifications/             # Technical specifications
│   │   ├── gorai-framework-specification.md   # Complete framework spec
│   │   ├── robot-definition-language.md       # RDL JSON format
│   │   ├── mesh-service-discovery.md          # NATS KV service discovery
│   │   ├── dynamic-discovery.md               # Auto-adoption
│   │   ├── gsp-v2-protocol.md                 # Gorai Serial Protocol
│   │   ├── runtime.md
│   │   ├── code-organization.md
│   │   ├── testing-approach.md
│   │   ├── archive/                           # Older spec versions
│   │   └── ...
│   │
│   ├── hardware/                   # Hardware analysis and platform support
│   │   ├── sensor-analysis.md
│   │   ├── motor-analysis.md
│   │   ├── sbc-comparison-rpi-to-opi.md
│   │   └── orange-pi-future-support.md
│   │
│   ├── guides/                     # Setup, installation, and how-to guides
│   │   ├── development-tools.md
│   │   ├── install-nats.md
│   │   ├── nats-setup.md
│   │   └── ...
│   │
│   ├── ecosystem/                  # External components and services
│   │   └── README.md               # Index of ecosystem components
│   │
│   ├── plans/                      # Implementation plans and roadmaps
│   │   ├── core-implementation-plan.md
│   │   ├── external-services.md
│   │   ├── prometheus.md
│   │   ├── hailo.md
│   │   ├── gps.md
│   │   ├── book.md
│   │   └── ...
│   │
│   ├── projects/                   # Robot project definitions
│   │   ├── project-pan-tilt.md
│   │   ├── project-simple-boat.md
│   │   ├── surf-test/
│   │   └── ...
│   │
│   ├── examples/                   # Example documentation
│   │
│   ├── reference/                  # API and CLI reference
│   │
│   ├── book/                       # Book: "Professional Robotics with Gorai"
│   │   ├── chapters/              # Current book chapters (00-18 + appendices)
│   │   ├── archive/               # Previous book versions (mdbook, mkdocs, shared)
│   │   ├── images/
│   │   ├── scripts/
│   │   └── Makefile
│   │
│   ├── EXAMPLES.md
│   └── gorai-branch-differences.md
│
├── website/                        # Hugo-based documentation website
│   ├── content/docs/
│   ├── hugo.yaml
│   └── ...
│
└── archive/                        # Archived materials
    ├── future-state/              # K3s/container designs (preserved)
    └── examples/                  # Archived example projects
```

## Ecosystem Components

Gorai is more than the core framework. The full ecosystem includes:

| Component | Repository | Description |
|-----------|------------|-------------|
| **gorai** | [gorai](../gorai) | Core framework — runtime, CLI, components, drivers |
| **gorai-gsp** | [gorai-gsp](../gorai-gsp) | Gorai Serial Protocol v2 library (Go/TinyGo) |
| **gorai-nats-gw** | gorai-nats-gw | NATS gateway for bridging protocols |
| **gorai-pushprom** | gorai-pushprom | Prometheus push metrics for robots |
| **gorai-gps** | gorai-gps | GPS component service |
| **rp2040-pwm** | [rp2040-pwm](../rp2040-pwm) | TinyGo firmware for RP2040 PWM control |

See [docs/ecosystem/README.md](docs/ecosystem/README.md) for detailed documentation on each component.

## Key Entry Points

- **New to Gorai?** Start with [docs/overview/STRATEGIC-SUMMARY.md](docs/overview/STRATEGIC-SUMMARY.md)
- **Building components?** Read [docs/architecture/LLM-DESIGN-GUIDE.md](docs/architecture/LLM-DESIGN-GUIDE.md)
- **Technical specs?** See [docs/specifications/gorai-framework-specification.md](docs/specifications/gorai-framework-specification.md)
- **Learning the framework?** Read the [book chapters](docs/book/chapters/)
- **Hardware questions?** Check [docs/hardware/](docs/hardware/)
- **Setting up dev environment?** See [docs/guides/](docs/guides/)

---

**Pronunciation:** "go-ray" (like "sting-ray")
