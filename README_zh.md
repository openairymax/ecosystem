# Airymax 生态层 — 工具、提示词、市场与技能

> Airymax AI 智能体运行时平台的生态层管理仓库。
> [airymaxhub](https://atomgit.com/openairymax/airymaxhub) 伞仓下五个管理仓之一。

**语言:** [English](README.md) | 简体中文

[![Version](https://img.shields.io/badge/version-0.1.1-5a6b7e)](https://atomgit.com/openairymax/ecosystem)
[![License](https://img.shields.io/badge/license-AGPL--3.0+Apache--2.0-4a90d9)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](https://www.python.org)

---

## 概述

**`ecosystem` 管理仓**是 Airymax 平台面向应用的生态层。它以 git submodule 方式聚合 **5 个叶子仓**，提供 Agent 应用在运行时消费的上层生态支持 —— 配置管理、提示词模板、官方分发包市场、官方技能定义和内置 Agent 执行器。

在 Airymax 四层架构（`Applications → Ecosystem → Daemon Services → Atoms`）中，生态层位于终端用户应用与运行时守护进程之间。这是 Agent 开发者最常驻留的层：他们基于 SDK 编写 Agent，然后在 `manager/` 中注册、从 `prompts/` 组合提示词、从 `markets/` 安装分发包、从 `skills/` 与 `agents/` 复用官方能力。

本管理仓仅承载文档、submodule 接线和许可证。所有实现均在叶子仓中。

## 仓库结构

```
ecosystem/                 # 管理仓（本仓库）
├── manager/               # 生态管理器叶子仓（submodule）
├── prompts/               # 提示词库叶子仓（submodule）
├── markets/               # 官方分发包市场叶子仓（submodule）
├── skills/                # 官方技能叶子仓（submodule）
├── agents/                # 内置 Agent 执行器 + 编排内核叶子仓（submodule）
├── .gitmodules            # Submodule 定义
├── LICENSE                # AGPL-3.0 + Apache-2.0 双许可证全文
├── NOTICE                 # 版权、商标与第三方声明
├── README.md              # 英文版
└── README_zh.md           # 本文件（中文）
```

## 叶子仓

| 模块 | 目录 | 仓库 URL | 说明 |
|------|------|----------|------|
| **manager** | `manager/` | `git@atomgit.com:openairymax/manager.git` | 统一配置与生命周期管理中心 — 11 个 JSON Schema（~272 条规则）、10 个已注册技能、12 个已注册 Agent、3 套环境覆盖层（dev/staging/prod）、sanitizer 抑制文件、安全策略、部署模板 |
| **prompts** | `prompts/` | `git@atomgit.com:openairymax/prompts.git` | 官方提示词模板库 — 4 类共 14 个模板（Cognition / Memory / Security / System）、registry、tuner 框架（评分器 / 评估器 / A-B 测试） |
| **markets** | `markets/` | `git@atomgit.com:openairymax/markets.git` | 官方分发包市场 — 可安装 `tool` 包（如 `maths-toolkit`）、市场客户端 SDK、Agent/技能契约校验器与安装器、包模板、参考示例 Agent 与应用 |
| **skills** | `skills/` | `git@atomgit.com:openairymax/skills.git` | 官方技能 — 5 个 Python `SkillPlugin` 技能（code_review / text_summarization / security_audit / data_analysis / web_search）、5 个 C 插件实现、3 个社区贡献技能（browser / database / github）、技能市场契约校验器与安装器 |
| **agents** | `agents/` | `git@atomgit.com:openairymax/agents.git` | 内置 Agent 执行器 — 11 个角色 Agent（product_manager / architect / backend / frontend / devops / security / tester / coding / data_engineer / reviewer / analyst）+ Rust coding agent；每个含 `contract.json` + `prompts/system.md` + `AirymaxAgent` 子类；**编排框架**（Agent / Task / Tool / Storage / LLMClient + 调度与规划策略）由原 `openlab` 叶子仓并入；无 API Key 亦可 Mock 端到端运行 |

## 生态架构

生态层是 Airymax 四层架构的第二层。它消费守护服务层（通过 SDK）暴露的运行时能力，并提供 Agent 应用组装所需的构建块：

```
┌──────────────────────────────────────────────────────────────────┐
│  Applications（应用层）                                           │
│  终端用户 Agent 应用（基于生态层 + SDK 构建）                      │
├──────────────────────────────────────────────────────────────────┤
│  ★ Ecosystem（生态层 — 本管理仓）★                                │
│  manager · prompts · markets · skills · agents                    │
├──────────────────────────────────────────────────────────────────┤
│  Daemon Services（守护服务层）                                    │
│  12 个运行时守护进程（gateway_d / llm_d / tool_d / sched_d / ...） │
├──────────────────────────────────────────────────────────────────┤
│  Atoms（原子层）                                                  │
│  微核心原语（corekern / coreloopthree / syscall / ...）           │
└──────────────────────────────────────────────────────────────────┘
```

每个叶子仓在生态层中承担不同职责：

| 叶子仓 | 职责 | 关键产物 |
|--------|------|----------|
| **manager** | 配置根 — 唯一真相源 | `configs/agentrt.yaml`（v0.1.1 统一运行时配置） |
| **prompts** | 提示词工程 — 模板 + 评估 | `registry.yaml` + tuner 框架 |
| **markets** | 分发 — 可安装包 + 市场客户端 | `tools/maths-toolkit/` + `client/` |
| **skills** | 可复用能力 — 官方技能包 | 5 个 `SkillPlugin` 子类 + 5 个 C 插件 |
| **agents** | 内置执行器 + 编排内核 | 11 个角色 Agent + `orchestration/` 框架 |

### 上游依赖

- **SDK** — 通过语言 SDK（`sdk/` 管理仓）连接到运行中的 AgentRT 实例；SDK 暴露双层 API（Cognition / Safety / Tool / Chat 嵌套客户端）
- **运行时** — `manager/configs/agentrt.yaml` 在启动时被 AgentRT 运行时消费；`manager/sanitizer/` 抑制文件在构建/测试时被构建工具链消费
- **Cupolas** — `manager/sanitizer/` 和 `manager/security/` 与 Cupolas 安全模块在双责模型下共同所有

### 下游消费者

- **Agent 开发者** — 使用 `prompts/` 和 `skills/` 作为构建块；从 `markets/` 安装分发包；通过 `manager/` 配置部署
- **运维人员** — 使用 `manager/` 部署模板和监控配置进行生产环境部署
- **运行时** — `market_d` 从 `markets/` 解析分发包；`agent_d` 驱动 `agents/` 的执行器；`plugin_d` 扫描 `skills/plugins/` 的技能插件
- **CI / CD 流水线** — 运行 `manager/tools/drift_detector.py` 和 `manager/tools/config_diff.py` 作为配置验证门禁

> **注意**：官方 Hook 集合（原 `ecosystem/hooks/`）已于 SP09.3 迁移至 `sdk-python/agentrt/hooks/`，import 路径变更为 `from agentrt.hooks import ...`。

### 0.1.1 生态层进展（与 agentrt 框架化联动）

- **Agent 可被真实驱动**：agentrt 工作大厅（Work Hall）以 `agent:<role>` handler 注册任务图节点，经 `agent_d` spawn/invoke 驱动本层 `agents/` 下的 Agent 执行（含 Rust `coding_rs_v1`）。
- **Rust coding agent 接入 LLM**：`agents/airymax_agents_rs` 的 `coding_agent`（v0.2.0）支持 OpenAI 兼容协议 + Mock 降级，与 Python 实现的延迟对比基准位于 `agents/tests/`。
- **LLM 配置 SSoT 收敛**：`manager/model/model.yaml`（同源 `model.json`）为提供商/模型唯一真相源（含 `providers` 段）；`manager/configs/agentrt.yaml` 的 `llm` 段仅保留运行时策略（路由/成本/缓存）。

## 构建与使用

生态层交付 Python 工具和配置，而非编译产物。典型用法：

```bash
# 克隆（含 submodule）
git clone --recurse-submodules git@atomgit.com:openairymax/ecosystem.git
cd ecosystem
git submodule update --remote --checkout

# 验证配置文件（manager/）
python -c "
import json, yaml
from jsonschema import validate
schema = json.load(open('manager/schema/kernel-settings.schema.json'))
config = yaml.safe_load(open('manager/kernel/settings.yaml'))
validate(instance=config, schema=schema)
"

# 运行运维工具集（manager/）
python manager/tools/src/drift_detector.py --action both --output drift_report.json

# 运行示例 Agent（markets/examples/）
cd markets/examples/hello-agent && python main.py

# 运行技能测试（skills/）
python -m pytest skills/tests/ -v
```

## 分支策略

- **本管理仓** — 仅 `main` 分支。不在此创建 feature 分支。
- **叶子仓** — 活跃开发在 `feature/official-hubs-01` 分支上进行。各叶子仓的 `main` 分支跟踪最近一次稳定发布。

克隆本仓库（含 submodule）：

```bash
git clone --recurse-submodules git@atomgit.com:openairymax/ecosystem.git
cd ecosystem
git submodule update --remote --checkout
```

## 许可证

采用 **AGPL v3 + Apache 2.0** 双许可证（SPDX: `AGPL-3.0-or-later OR Apache-2.0`）。可任选其一适用。两份许可证的完整文本均见 [LICENSE](LICENSE)，版权、商标与第三方组件声明详见 [NOTICE](NOTICE)。

### 双许可证使用指南

你可以**任选其一**适用——不是同时遵守两个，也不是都不遵守。

**SPDX 表达式**：`AGPL-3.0-or-later OR Apache-2.0`

| 你的场景 | 选择 | 原因 |
|----------|------|------|
| 构建**SaaS 网络服务**并修改生态工具 | **AGPL v3** | 网络服务条款要求公开修改后的源代码 |
| 开发**开源生态衍生作品**（copyleft 项目） | **AGPL v3** | 衍生作品必须同样以 AGPL 开源 |
| 在**商业闭源产品**中集成生态工具 | **Apache 2.0** | 宽松许可证，允许闭源衍生 |
| 构建**企业内部工具** | **Apache 2.0** | 无需公开源代码 |
| 需要**专利保护** | **Apache 2.0** | 贡献者明确授予专利使用权 |
| 仅用于学习与研究 | **任一** | 两者均允许个人使用 |

权威许可证政策见 [12-license-policy.md](../docs/AirymaxOS/50-engineering-standards/12-license-policy.md)。

Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.
