# Airymax Ecosystem — 工程生态组件

> Airymax AI 智能体运行时平台的工程生态层。
> [airymaxhub](https://atomgit.com/openairymax/airymaxhub) 伞仓下四个管理仓之一。

**语言:** [English](README.md) | 简体中文

[![Version](https://img.shields.io/badge/version-0.1.1-5a6b7e)](https://atomgit.com/openairymax/ecosystem)
[![License](https://img.shields.io/badge/license-AGPL--3.0+Apache--2.0-4a90d9)](LICENSE)

---

## 概述

**Ecosystem 管理仓**聚合 Airymax 的工程生态组件：生态管理器、提示词库、示例 Agent、开放实验室和技能定义。这些组件位于 Airymax 架构顶层，提供面向用户的工具、配置和参考实现。

## 叶子仓

| 模块 | 仓库 | 说明 |
|------|------|------|
| **manager** | `git@atomgit.com:openairymax/manager.git` | 生态管理器：配置、schema、sanitizer 抑制文件、部署脚本 |
| **prompts** | `git@atomgit.com:openairymax/prompts.git` | 提示词模板库 + 评估调优框架 |
| **examples** | `git@atomgit.com:openairymax/examples.git` | 官方示例 Agent 集合 |
| **openlab** | `git@atomgit.com:openairymax/openlab.git` | 开放实验室（实验性功能、研究原型） |
| **skills** | `git@atomgit.com:openairymax/skills.git` | 官方技能定义（SkillPlugin 基类） |

## 架构

```
ecosystem/
├── manager/    # 生态管理器（sanitizer / schema / security / configs）
├── prompts/    # 提示词模板库 + 评估调优框架
├── examples/   # 官方示例 Agent
├── openlab/    # 开放实验室（实验性功能）
├── skills/     # 官方技能集合（SkillPlugin 基类）
├── .gitmodules
└── README.md   ← 本文件
```

### 上游依赖

- **运行时**：通过 SDK（`sdk/`）连接到运行中的 AgentRT 实例
- **配置**：`manager/` 提供运行时消费的部署配置
- **Sanitizer**：`manager/sanitizer/` 提供构建时使用的 ASan/LSan/UBSan 抑制文件

### 下游消费者

- **Agent 开发者**：使用 `prompts/` 和 `skills/` 作为构建块
- **运维人员**：使用 `manager/` 配置进行部署
- **研究人员**：使用 `openlab/` 进行实验性功能研究

> **注意**：官方 Hook 集合（原 `ecosystem/hooks/`）已于 SP09.3 迁移至 `sdk-python/agentrt/hooks/`，import 路径变更为 `from agentrt.hooks import ...`。

## 仓库结构

```
ecosystem/
├── manager/
│   ├── sanitizer/     # ASan/LSan/UBSan 抑制文件
│   ├── schema/        # JSON/YAML schema（配置验证）
│   ├── security/      # 安全策略和 RBAC 规则
│   └── configs/       # 部署配置模板
├── prompts/
│   ├── templates/     # 提示词模板
│   └── evaluation/    # 评估调优框架
├── examples/
│   └── (示例 Agent)
├── openlab/
│   └── (实验性功能)
├── skills/
│   ├── base/          # SkillPlugin 基类
│   └── official/      # 官方技能实现
└── README.md
```

## 分支策略

- 本管理仓：仅 **`main`** 分支
- 叶子仓：**`feature/official-hubs-01`**（活跃开发）

## 许可证

采用 **AGPL v3 + Apache 2.0** 双许可证（SPDX: `AGPL-3.0-or-later OR Apache-2.0`）。详见 [LICENSE](LICENSE)。

Copyright (c) 2025-2026 **SPHARX Ltd.** All Rights Reserved.
