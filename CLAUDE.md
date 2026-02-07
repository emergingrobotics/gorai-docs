# Gorai Documentation Repository

**The robotics platform for software teams.** Pronounced "go-ray" (like "sting-ray").

## How to Navigate This Repo

**Read `INDEX.md` first.** It has a topic quick-reference table at the top and per-file summaries for every document. This gets you to the right file in one lookup instead of searching.

### If you need to understand what Gorai is

Start with `docs/overview/system-overview.md` — it covers the entire platform in one document.

### If you need to build or modify something

Read `docs/architecture/LLM-DESIGN-GUIDE.md` — it's written specifically for AI-assisted component development with decision trees and code templates.

### If you need the technical specification

`docs/specifications/gorai-framework-specification.md` is the complete spec (137KB, v0.2.0).

### If you need strategy or positioning context

`thoughts/strategy-paper.md` is the canonical strategy paper. `why-or-why-not-gorai.md` has the balanced for/against summary.

## What This Repo Is

This repo holds all documentation — strategy, architecture, specifications, book content, hardware analysis, guides, and ecosystem docs. The core [gorai](../gorai) repo has the Go source code.

## What Gorai Is

- Go-based robotics platform for software-first teams building AI-driven autonomous systems
- Single binary deployment (Go binary + NATS), no containers required
- Resource/capability model: robots expose state (resources) and actions (tools) through typed contracts
- Agent-compatible, not agent-dependent — supports deterministic behaviors, learned policies, and agentic orchestration
- Not a ROS 2 replacement — different market, complementary

## Repo Structure

```
INDEX.md                # Document index — READ THIS FIRST
docs/overview/          # Strategy, system overview, framework comparisons
docs/architecture/      # Design patterns, component model, NATS messaging
docs/specifications/    # Technical specs (framework, RDL, mesh, GSP, runtime)
docs/hardware/          # Sensor/motor analysis, SBC comparisons
docs/guides/            # Dev setup, NATS installation
docs/ecosystem/         # External components (gorai-gsp, gorai-nats-gw, etc.)
docs/plans/             # Implementation plans, roadmaps
docs/projects/          # Robot project definitions (pan-tilt, boat, surf-test)
docs/book/chapters/     # 20 chapters from introduction through advanced topics
thoughts/               # Strategy papers, devil's advocate, wedge positioning
website/                # Hugo-based documentation site
archive/                # Historical designs — don't delete, kept for context
```

## Ecosystem Repos

| Repo | Purpose |
|------|---------|
| `gorai` | Core Go framework (runtime, CLI, components, drivers) |
| `gorai-gsp` | Serial protocol library (Go/TinyGo, 40+ message types) |
| `gorai-nats-gw` | NATS gateway for protocol bridging |
| `gorai-pushprom` | Push metrics to Prometheus |
| `gorai-gps` | GPS component service |
| `rp2040-pwm` | TinyGo firmware for RP2040 PWM control |

## Editing Rules

- Specifications are the source of truth for technical decisions
- Book chapters follow numbered ordering (00-18 + appendices)
- Don't delete `archive/` — it preserves historical context
- Ecosystem component docs go in `docs/ecosystem/`
- **When adding or renaming documents, update `INDEX.md`**
