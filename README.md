# Airymax Ecosystem

> Airymax 生态管理仓库

## 概述

Ecosystem 管理仓聚合 Airymax 的工程生态组件，包括生态管理器、提示词库、示例集合、开放实验室和技能定义。

## 子模块

| 模块 | 仓库 | 说明 |
|------|------|------|
| Manager | `git@atomgit.com:openairymax/manager.git` | 生态管理器（配置/部署/监控/schema） |
| Prompts | `git@atomgit.com:openairymax/prompts.git` | 提示词注册与调优 |
| Examples | `git@atomgit.com:openairymax/examples.git` | 示例 Agent 集合 |
| OpenLab | `git@atomgit.com:openairymax/openlab.git` | 开放实验室 |
| Skills | `git@atomgit.com:openairymax/skills.git` | 技能定义与实现 |

## 架构

```
ecosystem/
├── manager/    # 生态管理器（sanitizer/schema/security/configs）
├── prompts/    # 提示词模板库 + 评估调优框架
├── examples/   # 官方示例 Agent
├── openlab/    # 开放实验室（实验性功能）
└── skills/     # 官方技能集合（SkillPlugin 基类）
```

> **注意**: 官方 Hook 集合（原 `ecosystem/hooks/`）已于 SP09.3 迁移至 `sdk-python/agentrt/hooks/`，import 路径变更为 `from agentrt.hooks import ...`

## 仓库信息

- **仓库 URL**: `git@atomgit.com:openairymax/ecosystem.git`
- **归属组织**: openairymax
- **分支策略**: 仅 `main` 分支
- **许可证**: AGPL v3 + Apache 2.0 双许可证

Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.
SPDX-License-Identifier: AGPL-3.0-or-later OR Apache-2.0
