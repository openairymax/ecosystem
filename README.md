# Airymax Ecosystem — Tools, Prompts, Examples & Skills

> Ecosystem layer management repository for the Airymax AI Agent Runtime Platform.
> One of five management repositories under the [airymaxhub](https://atomgit.com/openairymax/airymaxhub) umbrella.

**Language:** English | [简体中文](README_zh.md)

[![Version](https://img.shields.io/badge/version-0.2.0-5a6b7e)](https://atomgit.com/openairymax/ecosystem)
[![License](https://img.shields.io/badge/license-AGPL--3.0+Apache--2.0-4a90d9)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](https://www.python.org)

---

## Overview

The **`ecosystem` management repository** is the application-facing ecosystem layer of the Airymax platform. It aggregates **5 leaf repositories** as git submodules and provides the upper-layer ecosystem support that Agent applications consume at runtime — configuration management, prompt templates, the official package marketplace, official skill definitions, and built-in agent executors.

Within the Airymax four-layer architecture (`Applications → Ecosystem → Daemon Services → Atoms`), the ecosystem layer sits between end-user applications and the runtime daemons. It is the layer where Agent developers spend most of their time: they write agents against the SDK, then register them with `manager/`, compose prompts from `prompts/`, install packages from `markets/`, and reuse official capabilities from `skills/` and `agents/`.

This management repo only carries documentation, submodule wiring, and licensing. All implementation lives in the leaf repositories.

## Repository Structure

```
ecosystem/                 # Management repository (this repo)
├── manager/               # Ecosystem manager leaf repo (submodule)
├── prompts/               # Prompt library leaf repo (submodule)
├── markets/               # Official package marketplace leaf repo (submodule)
├── skills/                # Official skills leaf repo (submodule)
├── agents/                # Built-in agent executors + orchestration leaf repo (submodule)
├── .gitmodules            # Submodule definitions
├── LICENSE                # AGPL-3.0 + Apache-2.0 dual license full text
├── NOTICE                 # Copyright, trademark and third-party notices
├── README.md              # This file (English)
└── README_zh.md           # Chinese translation
```

## Leaf Repositories

| Module | Directory | Repository URL | Description |
|--------|-----------|----------------|-------------|
| **manager** | `manager/` | `git@atomgit.com:openairymax/manager.git` | Unified configuration & lifecycle management center — 11 JSON Schemas (~272 rules), 15 registered skills, 14 registered agents, 3 environment overlays (dev/staging/prod), sanitizer suppressions, security policies, deployment templates |
| **prompts** | `prompts/` | `git@atomgit.com:openairymax/prompts.git` | Official prompt template library — 14 templates across 4 categories (Cognition / Memory / Security / System), registry, tuner framework (scorer / evaluator / A-B testing) |
| **markets** | `markets/` | `git@atomgit.com:openairymax/markets.git` | Official package marketplace — installable `tool` packages (e.g. `maths-toolkit`), marketplace client SDK, agent/skill contract validators & installers, package templates, reference example agents & applications |
| **skills** | `skills/` | `git@atomgit.com:openairymax/skills.git` | Official skills — 5 Python `SkillPlugin` skills (code_review / text_summarization / security_audit / data_analysis / web_search), 5 C plugin implementations, 3 contrib skills (browser / database / github); distribution via `markets/` |
| **agents** | `agents/` | `git@atomgit.com:openairymax/agents.git` | Built-in agent executors — 11 role agents (product_manager / architect / backend / frontend / devops / security / tester / coding / data_engineer / reviewer / analyst) + Rust coding agent; each ships `contract.json` + `prompts/system.md` + `AirymaxAgent` subclass; **orchestration** framework (Agent / Task / Tool / Storage / LLMClient + dispatching & planning strategies); mock-runnable end-to-end without API key |

## Ecosystem Architecture

The ecosystem layer is the second layer of the Airymax four-layer architecture. It consumes runtime capabilities exposed by the Daemon Services layer (via the SDK) and provides the building blocks that Agent applications assemble:

```
┌──────────────────────────────────────────────────────────────────┐
│  Applications                                                     │
│  End-user Agent apps built on ecosystem + SDK                    │
├──────────────────────────────────────────────────────────────────┤
│  ★ Ecosystem (this management repo) ★                            │
│  manager · prompts · markets · skills · agents                   │
├──────────────────────────────────────────────────────────────────┤
│  Daemon Services                                                  │
│  12 runtime daemons (gateway_d / llm_d / tool_d / sched_d / ...)  │
├──────────────────────────────────────────────────────────────────┤
│  Atoms                                                            │
│  Micro-core primitives (corekern / coreloopthree / syscall / ...)│
└──────────────────────────────────────────────────────────────────┘
```

Each leaf repository plays a distinct role within the ecosystem layer:

| Leaf Repo | Role | Key Artifact |
|-----------|------|--------------|
| **manager** | Configuration root — single source of truth | `configs/agentrt.yaml` (v0.1.1 unified runtime config) |
| **prompts** | Prompt engineering — templates + evaluation | `registry.yaml` + tuner framework |
| **markets** | Distribution — installable packages + marketplace client | `tools/maths-toolkit/` + `client/` |
| **skills** | Reusable capabilities — official skill pack | 5 `SkillPlugin` subclasses + 5 C plugins |
| **agents** | Built-in executors + orchestration kernel | 11 role agents + `orchestration/` framework |

### Upstream Dependencies

- **SDK** — connects to a running AgentRT instance through the language SDKs (`sdk/` management repo); the SDK exposes the double-layer API (Cognition / Safety / Tool / Chat nested clients)
- **Runtime** — `manager/configs/agentrt.yaml` is consumed by the AgentRT runtime at startup; `manager/sanitizer/` suppression files are consumed by the build toolchain at build/test time
- **Cupolas** — `manager/sanitizer/` and `manager/security/` are co-owned with the Cupolas security module under a dual-responsibility model

### Downstream Consumers

- **Agent developers** — use `prompts/` and `skills/` as building blocks; install packages from `markets/`; deploy via `manager/` configs
- **Operators** — use `manager/` deployment templates and monitoring configs for production rollouts
- **Runtime** — `market_d` resolves packages from `markets/`; `agent_d` drives executors from `agents/`; `plugin_d` scans skill plugins from `skills/plugins/`
- **CI / CD pipelines** — run `manager/tools/drift_detector.py` and `manager/tools/config_diff.py` as configuration validation gates

> **Note**: The official Hooks collection (formerly `ecosystem/hooks/`) was migrated to `sdk-python/agentrt/hooks/` during SP09.3. Import path changed to `from agentrt.hooks import ...`.

### 0.1.1 Ecosystem Progress (Linked with AgentRT Framework-ization)

- **Agents can be truly driven**: the agentrt Work Hall registers task-graph nodes as `agent:<role>` handlers and spawns/invokes this layer's `agents/` via `agent_d` (including Rust `coding_rs_v1`).
- **Rust coding agent with LLM**: `agents/airymax_agents_rs` `coding_agent` (v0.2.0) supports the OpenAI-compatible protocol with a Mock fallback; latency benchmarks vs the Python implementation live under `agents/tests/`.
- **LLM config SSoT consolidation**: `manager/model/model.yaml` (kept in sync with `model.json`) is the single source of truth for providers/models (with a `providers` section); the `llm` section in `manager/configs/agentrt.yaml` keeps only runtime policy (routing/cost/cache).

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

# Run a built-in agent end-to-end (agents/, mock mode auto-enabled)
python agents/examples/run_pm.py

# Run skill tests (skills/)
python -m pytest skills/tests/ -v
```

## Branch Strategy

- **This management repo** — `main` only. No feature branches are created here.
- **Leaf repositories** — active development happens on `develop/hubs-01`. The `main` branch on each leaf repo tracks the last stable release.

When cloning this repo with submodules:

```bash
git clone --recurse-submodules git@atomgit.com:openairymax/ecosystem.git
cd ecosystem
git submodule update --remote --checkout
```

## License

Dual-licensed under **AGPL v3 + Apache 2.0** (SPDX: `AGPL-3.0-or-later OR Apache-2.0`). You may choose either license at your option. See [LICENSE](LICENSE) for the full text of both licenses and [NOTICE](NOTICE) for copyright, trademark and third-party notices.

### Dual License Guide

You may choose **either** license at your option — not both, not neither.

**SPDX Expression**: `AGPL-3.0-or-later OR Apache-2.0`

| If you are... | Choose | Why |
|---------------|--------|-----|
| Building a **SaaS** or network service that modifies ecosystem tools | **AGPL v3** | Network service clause requires source disclosure |
| Developing **open-source** ecosystem derivatives (copyleft) | **AGPL v3** | Derivatives must remain open-source under AGPL |
| Using ecosystem tools in **commercial closed-source** products | **Apache 2.0** | Permissive, allows proprietary derivatives |
| Building **enterprise internal tools** | **Apache 2.0** | No source disclosure required |
| Needing **patent protection** | **Apache 2.0** | Explicit patent grant from contributors |
| Just learning or researching | **Either** | Both permit personal use |

For the authoritative license policy, see [12-license-policy.md](../docs/AirymaxOS/50-engineering-standards/12-license-policy.md).

Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.
