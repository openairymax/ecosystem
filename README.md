# Airymax Ecosystem — Engineering Ecosystem Components

> Engineering ecosystem layer for the Airymax AI Agent Runtime Platform.
> One of four management repositories under the [airymaxhub](https://atomgit.com/openairymax/airymaxhub) umbrella.

**Language:** English | [简体中文](README_zh.md)

[![Version](https://img.shields.io/badge/version-0.1.1-5a6b7e)](https://atomgit.com/openairymax/ecosystem)
[![License](https://img.shields.io/badge/license-AGPL--3.0+Apache--2.0-4a90d9)](LICENSE)

---

## Overview

The **Ecosystem management repo** aggregates Airymax's engineering ecosystem components: ecosystem manager, prompt library, example agents, open lab, and skill definitions. These components sit at the top of the Airymax architecture, providing user-facing tools, configurations, and reference implementations.

## Leaf Repositories

| Module | Repository | Description |
|--------|-----------|-------------|
| **manager** | `git@atomgit.com:openairymax/manager.git` | Ecosystem manager: configs, schemas, sanitizer suppressions, deployment scripts |
| **prompts** | `git@atomgit.com:openairymax/prompts.git` | Prompt template library + evaluation/tuning framework |
| **examples** | `git@atomgit.com:openairymax/examples.git` | Official example Agent collection |
| **openlab** | `git@atomgit.com:openairymax/openlab.git` | Open lab (experimental features, research prototypes) |
| **skills** | `git@atomgit.com:openairymax/skills.git` | Official skill definitions (SkillPlugin base class) |

## Architecture

```
ecosystem/
├── manager/    # Ecosystem manager (sanitizer / schema / security / configs)
├── prompts/    # Prompt template library + evaluation tuning framework
├── examples/   # Official example agents
├── openlab/    # Open lab (experimental features)
├── skills/     # Official skill collection (SkillPlugin base class)
├── .gitmodules
└── README.md   ← This file
```

### Upstream Dependencies

- **Runtime**: Connects to a running AgentRT instance via SDK (`sdk/`)
- **Configuration**: `manager/` provides deployment configs consumed by the runtime
- **Sanitizer**: `manager/sanitizer/` provides ASan/LSan/UBSan suppression files used during build

### Downstream Consumers

- **Agent developers**: Use `prompts/` and `skills/` as building blocks
- **Operators**: Use `manager/` configs for deployment
- **Researchers**: Use `openlab/` for experimental features

> **Note**: The official Hooks collection (formerly `ecosystem/hooks/`) was migrated to `sdk-python/agentrt/hooks/` during SP09.3. Import path changed to `from agentrt.hooks import ...`.

## Repository Structure

```
ecosystem/
├── manager/
│   ├── sanitizer/     # ASan/LSan/UBSan suppression files
│   ├── schema/        # JSON/YAML schemas for configuration validation
│   ├── security/      # Security policies and RBAC rules
│   └── configs/       # Deployment configuration templates
├── prompts/
│   ├── templates/     # Prompt templates
│   └── evaluation/    # Evaluation and tuning framework
├── examples/
│   └── (example agents)
├── openlab/
│   └── (experimental features)
├── skills/
│   ├── base/          # SkillPlugin base class
│   └── official/      # Official skill implementations
└── README.md
```

## Branch Strategy

- This management repo: **`main`** only
- Leaf repos: **`feature/official-hubs-01`** (active development)

## License

Dual-licensed under **AGPL v3 + Apache 2.0** (SPDX: `AGPL-3.0-or-later OR Apache-2.0`). See [LICENSE](LICENSE) for details.

Copyright (c) 2025-2026 **SPHARX Ltd.** All Rights Reserved.
