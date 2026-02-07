# Gorai Documentation Repository

This is the canonical documentation repository for the **Gorai** robotics ecosystem.

**Pronunciation:** "go-ray" (like "sting-ray")

## Purpose

This repo holds all higher-level documentation — strategy, architecture, specifications, book content, hardware analysis, guides, and ecosystem documentation. The core [gorai](../gorai) repo focuses on Go source code and implementation.

## Repository Layout

```
docs/
├── overview/          # Strategic vision, framework comparisons (ROS 2, Viam, YARP)
├── architecture/      # Design patterns, component model, NATS messaging, abstractions
├── specifications/    # Technical specs (framework, RDL, mesh discovery, GSP protocol)
├── hardware/          # Sensor/motor analysis, SBC comparisons, platform support
├── guides/            # Setup guides, NATS installation, development tools
├── ecosystem/         # External components (gorai-nats-gw, gorai-pushprom, gorai-gps)
├── plans/             # Implementation plans, roadmaps
├── projects/          # Robot project definitions (pan-tilt, boat, surf-test)
├── examples/          # Example documentation
├── reference/         # API and CLI reference
└── book/              # Book chapters, publication infrastructure
    ├── chapters/      # 20 chapters covering introduction through advanced topics
    └── archive/       # Previous book versions (mdbook, mkdocs)

website/               # Hugo-based documentation site
archive/               # Archived designs (K3s, containers) and old examples
```

## Key Documents

| Document | Path | Description |
|----------|------|-------------|
| Strategic Summary | `docs/overview/STRATEGIC-SUMMARY.md` | Key strategic decisions and market positioning |
| Framework Spec | `docs/specifications/gorai-framework-specification.md` | Complete technical specification (137KB) |
| RDL Format | `docs/specifications/robot-definition-language.md` | Robot Definition Language JSON format |
| LLM Design Guide | `docs/architecture/LLM-DESIGN-GUIDE.md` | Guide for AI-assisted component development |
| Mesh Discovery | `docs/specifications/mesh-service-discovery.md` | NATS KV runtime service discovery |
| Dynamic Discovery | `docs/specifications/dynamic-discovery.md` | Auto-adoption and gateway patterns |
| Component Reference | `docs/architecture/component-reference.md` | Component types and interfaces |

## Gorai Ecosystem

The documentation covers components beyond the core repo:

- **gorai** — Core Go framework (runtime, CLI, components, drivers)
- **gorai-gsp** — Gorai Serial Protocol v2 (Go/TinyGo library, 40+ message types)
- **gorai-nats-gw** — NATS gateway for protocol bridging
- **gorai-pushprom** — Prometheus push metrics
- **gorai-gps** — GPS component service
- **rp2040-pwm** — TinyGo firmware for RP2040 PWM control

## What Gorai Is

- **Prosumer robotics framework** — between educational toys and enterprise platforms
- **Go-first, pragmatically polyglot** — Go core, Python/C++ services via NATS
- **Simple binary deployment** — single Go binary + NATS, no containers required
- **Not a ROS 2 replacement** — complementary, targeting different market

## Editing Guidelines

- Documentation files are primarily Markdown (`.md`)
- Specifications are the source of truth for technical decisions
- Book chapters follow a numbered ordering (00-18 + appendices)
- The `archive/` directory preserves historical designs — don't delete, they provide context
- When adding ecosystem component docs, place them in `docs/ecosystem/`
