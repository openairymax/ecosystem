# Airymax Ecosystem — Tools, Prompts, Examples & Skills

> Ecosystem layer management repository for the Airymax AI Agent Runtime Platform.
> One of five management repositories under the [airymaxhub](https://atomgit.com/openairymax/airymaxhub) umbrella.

**Language:** English | [简体中文](README_zh.md)

[![Version](https://img.shields.io/badge/version-0.1.1-5a6b7e)](https://atomgit.com/openairymax/ecosystem)
[![License](https://img.shields.io/badge/license-AGPL--3.0+Apache--2.0-4a90d9)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](https://www.python.org)

---

## Overview

The **`ecosystem` management repository** is the application-facing ecosystem layer of the Airymax platform. It aggregates **5 leaf repositories** as git submodules and provides the upper-layer ecosystem support that Agent applications consume at runtime — configuration management, prompt templates, reference examples, an open laboratory, and official skill definitions.

Within the Airymax four-layer architecture (`Applications → Ecosystem → Daemon Services → Atoms`), the ecosystem layer sits between end-user applications and the runtime daemons. It is the layer where Agent developers spend most of their time: they write agents against the SDK, then register them with `manager/`, compose prompts from `prompts/`, learn from `examples/`, extend the platform via `openlab/`, and reuse official capabilities from `skills/`.

This management repo only carries documentation, submodule wiring, and licensing. All implementation lives in the leaf repositories.

## Repository Structure

```
ecosystem/                 # Management repository (this repo)
├── manager/               # Ecosystem manager leaf repo (submodule)
├── prompts/               # Prompt library leaf repo (submodule)
├── examples/              # Example agents leaf repo (submodule)
├── openlab/               # Open lab leaf repo (submodule)
├── skills/                # Official skills leaf repo (submodule)
├── .gitmodules            # Submodule definitions
├── LICENSE                # AGPL-3.0 + Apache-2.0 dual license full text
├── NOTICE                 # Copyright, trademark and third-party notices
├── README.md              # This file (English)
└── README_zh.md           # Chinese translation
```

## Leaf Repositories

| Module | Directory | Repository URL | Description |
|--------|-----------|----------------|-------------|
| **manager** | `manager/` | `git@atomgit.com:openairymax/manager.git` | Unified configuration & lifecycle management center — 11 JSON Schemas (~272 rules), 10 registered skills, 12 registered agents, 3 environment overlays (dev/staging/prod), sanitizer suppressions, security policies, deployment templates |
| **prompts** | `prompts/` | `git@atomgit.com:openairymax/prompts.git` | Official prompt template library — 14 templates across 4 categories (Cognition / Memory / Security / System), registry, tuner framework (scorer / evaluator / A-B testing) |
| **examples** | `examples/` | `git@atomgit.com:openairymax/examples.git` | 10 runnable example agents with a graded learning path (Beginner → Intermediate → Advanced) — hello-agent, weather-agent, code-review-agent, research-agent, multi-agent-debate, etc. |
| **openlab** | `openlab/` | `git@atomgit.com:openairymax/openlab.git` | Open laboratory — multi-agent orchestration core, 4 reference applications (DocGen / E-Commerce / Research / VideoEdit), 7 role agents, community contributions, JSON-RPC 2.0 marketplace |
| **skills** | `skills/` | `git@atomgit.com:openairymax/skills.git` | 5 official skills (code_review / text_summarization / security_audit / data_analysis / web_search) built on the `SkillPlugin` base class from `sdk-python` |

## Ecosystem Architecture

The ecosystem layer is the second layer of the Airymax four-layer architecture. It consumes runtime capabilities exposed by the Daemon Services layer (via the SDK) and provides the building blocks that Agent applications assemble:

```
┌──────────────────────────────────────────────────────────────────┐
│  Applications                                                     │
│  End-user Agent apps built on ecosystem + SDK                    │
├──────────────────────────────────────────────────────────────────┤
│  ★ Ecosystem (this management repo) ★                            │
│  manager · prompts · examples · openlab · skills                 │
├──────────────────────────────────────────────────────────────────┤
│  Daemon Services                                                  │
│  12 runtime daemons (gateway_d / llm_d / tool_d / sched_d / ...)  │
├──────────────────────────────────────────────────────────────────┤
│  Atoms                                                            │
│  Microkernel primitives (corekern / coreloopthree / syscall / ...)│
└──────────────────────────────────────────────────────────────────┘
```

Each leaf repository plays a distinct role within the ecosystem layer:

| Leaf Repo | Role | Key Artifact |
|-----------|------|--------------|
| **manager** | Configuration root — single source of truth | `configs/agentrt.yaml` (v0.1.1 unified runtime config) |
| **prompts** | Prompt engineering — templates + evaluation | `registry.yaml` + tuner framework |
| **examples** | Learning path — runnable reference agents | 10 example agents with graded difficulty |
| **openlab** | Innovation surface — experimental & community | 4 reference applications + marketplace |
| **skills** | Reusable capabilities — official skill pack | 5 `SkillPlugin` subclasses |

### Upstream Dependencies

- **SDK** — connects to a running AgentRT instance through the language SDKs (`sdk/` management repo); the SDK exposes the double-layer API (Cognition / Safety / Tool / Chat nested clients)
- **Runtime** — `manager/configs/agentrt.yaml` is consumed by the AgentRT runtime at startup; `manager/sanitizer/` suppression files are consumed by the build toolchain at build/test time
- **Cupolas** — `manager/sanitizer/` and `manager/security/` are co-owned with the Cupolas security module under a dual-responsibility model

### Downstream Consumers

- **Agent developers** — use `prompts/` and `skills/` as building blocks; learn from `examples/`; deploy via `manager/` configs
- **Operators** — use `manager/` deployment templates and monitoring configs for production rollouts
- **Researchers** — use `openlab/` for experimental features and community contributions
- **CI / CD pipelines** — run `manager/tools/drift_detector.py` and `manager/tools/config_diff.py` as configuration validation gates

> **Note**: The official Hooks collection (formerly `ecosystem/hooks/`) was migrated to `sdk-python/agentrt/hooks/` during SP09.3. Import path changed to `from agentrt.hooks import ...`.

## Build & Usage

The ecosystem layer ships Python tooling and configuration rather than compiled artifacts. Typical usage:

```bash
# Clone with submodules
git clone --recurse-submodules git@atomgit.com:openairymax/ecosystem.git
cd ecosystem
git submodule update --remote --checkout

# Validate a configuration file against its schema (manager/)
python -c "
import json, yaml
from jsonschema import validate
schema = json.load(open('manager/schema/kernel-settings.schema.json'))
config = yaml.safe_load(open('manager/kernel/settings.yaml'))
validate(instance=config, schema=schema)
"

# Run the operations toolset (manager/)
python manager/tools/src/drift_detector.py --action both --output drift_report.json

# Run example agents (examples/)
cd examples/hello-agent && python main.py

# Run skill tests (skills/)
python -m pytest skills/tests/ -v
```

## Branch Strategy

- **This management repo** — `main` only. No feature branches are created here.
- **Leaf repositories** — active development happens on `feature/official-hubs-01`. The `main` branch on each leaf repo tracks the last stable release.

When cloning this repo with submodules:

```bash
git clone --recurse-submodules git@atomgit.com:openairymax/ecosystem.git
cd ecosystem
git submodule update --remote --checkout
```

## License

Dual-licensed under **AGPL v3 + Apache 2.0** (SPDX: `AGPL-3.0-or-later OR Apache-2.0`). You may choose either license at your option. See [LICENSE](LICENSE) for the full text of both licenses and [NOTICE](NOTICE) for copyright, trademark and third-party notices.

Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.
