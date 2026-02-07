# Gorai Documentation

<img src="./images/gorai.png" width="25%">

**The robotics platform for software teams.**

*Pronounced "go-ray" (like "sting-ray")*

## Why Gorai?

> **Autonomy without replay is folklore.** Gorai treats action logs, state streams, and replay as first-class platform concerns — not optional add-ons.

> **Agent-compatible. Not agent-dependent.** AI-driven execution is a first-class citizen, but the platform works just as well with deterministic state machines, scripts, and rule-based planners. No autonomy method is mandatory. All are constrained.

> **Build robots like software. Run them like systems.** Gorai is a software engineer's robotics platform — opinionated, pragmatic, and operational. If you already think in APIs, distributed systems, and deployments, you'll be productive in days, not months.

---

This is the canonical documentation for the Gorai robotics platform. If you want to understand what Gorai is, how it works, how to build with it, or where it's headed — it's in here.

The core framework implementation lives in [gorai](../gorai). This repo is everything else: the design, the specs, the book, the hardware research, and the ecosystem documentation for components that live outside the core.

---

## Start Here

**[System Overview](docs/overview/system-overview.md)** — Read this first. It explains the entire Gorai system in one document: what it is, the architecture (resource model, NATS messaging, mesh discovery), how robots are configured and deployed, the ecosystem of components, and how it all fits together.

### Use an AI agent to explore

This repo has ~100 documents. Point an AI coding agent at this repo (it will read `CLAUDE.md` and `INDEX.md` automatically) and ask it what you need:

- *"Explain the Gorai architecture and how components communicate"*
- *"I want to build a new sensor component — what patterns should I follow?"*
- *"Compare Gorai's approach to ROS 2 and Viam"*
- *"What hardware do I need to get started with a simple robot?"*
- *"Walk me through the RDL configuration format"*

### Or pick your path

| I want to... | Read |
|--------------|------|
| Understand the strategic decisions | [Strategic Summary](docs/overview/STRATEGIC-SUMMARY.md) |
| See the full technical specification | [Framework Specification](docs/specifications/gorai-framework-specification.md) |
| Build a new component or service | [LLM Design Guide](docs/architecture/LLM-DESIGN-GUIDE.md) |
| Learn Gorai from scratch (book) | [Book Chapters](docs/book/chapters/) (start with [01-introduction](docs/book/chapters/01-introduction.md)) |
| Set up my development environment | [Development Tools](docs/guides/development-tools.md) |
| Configure a robot (RDL) | [Robot Definition Language](docs/specifications/robot-definition-language.md) |
| Understand NATS messaging | [NATS Description](docs/architecture/nats-description.md) |
| Pick hardware | [Hardware Requirements](docs/specifications/hardware-requirements.md) and [SBC Comparison](docs/hardware/sbc-comparison-rpi-to-opi.md) |
| See what's in the ecosystem | [Ecosystem Components](docs/ecosystem/README.md) |
| Build a project | [Pan-Tilt Platform](docs/projects/project-pan-tilt.md), [Autonomous Boat](docs/projects/project-simple-boat.md), [Surf Test](docs/projects/surf-test/README.md) |

---

## What's in This Repo

### [docs/overview/](docs/overview/) — Vision and Strategy
Why Gorai exists, who it's for, and how it positions against ROS 2, Viam, and YARP. The system overview, strategic summary, roadmap, and framework comparisons.

### [docs/architecture/](docs/architecture/) — Design and Patterns
How Gorai is designed internally. The component model, NATS messaging patterns, authentication, hardware abstraction, package organization, and the guide for building new components with AI assistance.

### [docs/specifications/](docs/specifications/) — Technical Specs
The formal specifications: the complete framework spec, Robot Definition Language (RDL), mesh service discovery, dynamic discovery, Gorai Serial Protocol (GSP/2), runtime lifecycle, testing approach, and deployment modes.

### [docs/hardware/](docs/hardware/) — Hardware Analysis
Sensor and motor analysis for prosumer robotics, single-board computer comparisons (RPi vs OPi), and platform support documentation.

### [docs/guides/](docs/guides/) — Setup and How-To
Development environment setup, NATS installation, configuration, and verification scripts.

### [docs/ecosystem/](docs/ecosystem/) — External Components
Documentation for components outside the core repo: gorai-gsp (serial protocol), gorai-nats-gw (protocol gateway), gorai-pushprom (metrics), gorai-gps (GPS service), and rp2040-pwm (microcontroller firmware).

### [docs/plans/](docs/plans/) — Implementation Plans
Roadmaps and implementation plans for features in progress or planned: core framework, external services, Prometheus monitoring, Hailo AI integration, GPS, publication system, and more.

### [docs/projects/](docs/projects/) — Robot Projects
Complete robot project definitions with hardware specs, architecture, and build instructions. Pan-tilt sensor fusion platform, autonomous surface vehicle, and distributed control demos.

### [docs/book/](docs/book/) — The Gorai Book
"Gorai: Building Modern Robots with Go and NATS" — 20 chapters from introduction through advanced topics (AI/ML integration, testing strategies, AI-assisted development). Includes the publication build system and archived previous versions.

### [website/](website/) — Documentation Website
Hugo-based documentation website with getting-started guides, component guides, and reference material.

### [archive/](archive/) — Historical Designs
Preserved designs for future features (K3s deployment, containerization) and archived example projects (hello-robot, hello-people-detector). Kept for context — don't delete.

---

## The Gorai Ecosystem

Gorai is not a single repository. The platform is a constellation of focused components:

| Component | What it does |
|-----------|-------------|
| [**gorai**](../gorai) | Core framework — Go runtime, CLI, components, drivers, services |
| [**gorai-gsp**](../gorai-gsp) | Serial protocol library (Go/TinyGo) — 40+ message types for host-to-MCU communication |
| **gorai-nats-gw** | Gateway bridging hardware protocols (GSP/2, Modbus) to NATS |
| **gorai-pushprom** | Push metrics from robots to Prometheus |
| **gorai-gps** | GPS component service with NMEA parsing |
| [**rp2040-pwm**](../rp2040-pwm) | TinyGo firmware — 16-channel hardware PWM for servos/ESCs |

Satellite repos follow naming conventions: `gorai-driver-*` (hardware drivers), `gorai-accel-*` (ML accelerators), `gorai-service-*` (standalone services), `gorai-tiny-*` (MCU firmware).

---

## What's Here vs. What's in `gorai`

| This repo (gorai-docs) | Core repo (gorai) |
|-------------------------|-------------------|
| System overview and strategy | Go source code |
| Architecture and design docs | Component and service implementations |
| Technical specifications | CLI commands |
| Book chapters | Driver code |
| Hardware analysis | Unit and integration tests |
| Setup guides | Build system (Makefile, go.mod) |
| Ecosystem docs | Example robot configurations |
| Plans and roadmaps | Runtime and NATS client |

---

## Finding Things

This repo has ~100 documentation files across 10+ directories. Two tools help you navigate:

**[INDEX.md](INDEX.md)** — A machine-readable index of every document with file path, summary, and keywords. Useful for AI assistants and for quick searches when you know roughly what you're looking for but not which file it's in.

**[CLAUDE.md](CLAUDE.md)** — Context for AI coding assistants. Directs them to INDEX.md first, explains the repo layout, and provides a lookup workflow. If you use AI tools to work with this codebase, they'll read this file automatically.

For humans, the [System Overview](docs/overview/system-overview.md) and the "I want to..." table at the top of this README are the fastest paths to the right document.
