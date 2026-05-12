# LLM Wiki Knowledge Base — Harness Engineering

> Schema document — read at the start of every session together with `wiki/index.md`.
> Update after every major compile, ingest batch, or structural change.

## Scope

What this wiki covers:
- **Harness Engineering**: 让 AI Coding Agent 可靠工作的工程化方法论
- **Agent Workflow Patterns**: Agent 任务管理、审查机制、反馈闭环
- **Multi-Agent Systems**: 多个 Agent 协作（Supervisor/Worker、审查者模式）
- **Engineering Primitives for Agents**: 隔离、分解、协调

What this wiki deliberately excludes:
- 模型训练/RLHF（那是模型厂商的事）
- 非 coding 场景的 agent（如 farm/hotel agent），但模式通用、仅供参考

## Operations

This wiki follows the llm-wiki skill's five operations: `compile`, `ingest`, `query`, `lint`, `audit`.
Every operation appends an entry to `log/YYYYMMDD.md`.

## Naming conventions

- **Concept pages** (`wiki/concepts/`): Title Case noun phrases.
- **Folder-split concepts** (`wiki/concepts/<topic>/`): used when a topic exceeds ~1200 words. Contains `index.md` + one file per aspect.
- **Entity pages** (`wiki/entities/`): Proper names (GitHub repos, specific projects).
- **Summary pages** (`wiki/summaries/`): kebab-case source slug.

All pages require YAML frontmatter: `title`, `type`, `created`, `updated`, `sources`, `tags`.

### Diagrams and formulas
- All diagrams are **mermaid**. No ASCII art.
- All formulas are **KaTeX** (inline `$...$` or block `$$...$$`).

### Raw file policy
- Small text sources → copy into `raw/<subfolder>/`.
- Large binaries → create a pointer file at `raw/refs/<slug>.md` with `kind: ref` and `external_path` fields. Do not copy the binary.

## Current articles

### Concepts
- [[Harness Engineering]] — 让 Agent 可靠工作的五大子系统
- [[Agent Review Pattern]] — Agent 审 Agent 的机制（Reviewer Agent）
- [[Four-Layer Feedback Loop]] — 编译→单测→e2e→CI 四层反馈闭环
- [[Three Engineering Primitives]] — 隔离 / 分解 / 协调
- [[Cognitive Debt and LLM Use]] — 普通人用好大模型：认知债务 + 认知摩擦 + 思考伙伴定位
- [[Hermes Skill Nudge]] — Hermes 自动建议生成 Skill 的机制：iteration-based 触发、异步投递链路、已知 bug（send() 静默失败）

### Entities
- [[Multica]] — 开源多 Agent 队友平台（26k ⭐）：任务面板+Agent生命周期管理+Skills积累，支持 11 种 Agent CLI，Go+Next.js 自托管
- [[Chorus]] — 给 Agent 用的任务管理系统（v0.1→v0.6.1 演进）
- [[learn-claude-code]] — Bash is all you need 课程（54k ⭐）
- [[learn-harness-engineering]] — Harness 工程实践课程（752 ⭐）
- [[gstack]] — Garry Tan 的 Claude Code 技能包（79k ⭐）：多角色工程团队、Harness Engineering 生产级参考实现
- [[agent-skills]] — Addy Osmani 出品：20 工程技能库（Define/Plan/Build/Verify/Review/Ship），Claude Code/Gemini CLI/OpenCode 多平台，MIT License，Google eng-practices 深度嵌入

### Memory Systems (记忆系统)
- [[Supermemory]] — 基准第一（SOTA），#1 on LongMemEval/LoCoMo/ConvoMem，Research Lab + 多 SDK
- [[Honcho]] — Hermes Agent 专用记忆层，Neuromancer XR 推理模型，LoCoMo 86.9%
- [[Hindsight]] — Nick 当前本地部署方案，向量记忆存储，Docker 部署
- [[Holographic]] — 全息记忆范式，分布式记忆编码理念（架构范式，非单一产品）
- [[RetainDB]] — Postgres/pgvector 自托管，关系图记忆，MCP Server，开源 Apache 2.0/BSL
- [[ByteRover]] — 编程 Agent CLI，LoCoMo 96.1%，Git 式版本控制，支持 22+ 编程工具
- [[OpenViking]] — 字节跳动上下文数据库，AGPLv3，多租户，L0/L1/L2 分层架构
- [[Claude-Mem]] — Claude Code 专用插件，勿与 Supermemory 混淆
- [[memorizz]] — Python 多记忆类型（10种）× 可插拔存储（Oracle/Mongo/FS），实验性/教育用，PolyForm Noncommercial（禁止商用）

### Summaries
- [[harness-engineering-java-brownfield-20260507]] — 存量 Java 工程的 AI Coding 率从 25% 提升至 90% 的完整实战

### Open research questions

- 什么样的 `init.sh` 起步最轻量且有效？
- 多 Agent 协作中，Supervisor Agent 的信任边界如何量化？
- 技术债务清理的"阈值"怎么定？Agent 什么时候该停下来重构而不是继续堆功能？
- Hermes Skill Nudge: `_deliver_bg_review_message` 调用 `.send()` 而非 `.send_message()` — 是 Feishu adapter 的接口命名问题还是 caller 的调用错误？需要确认 BasePlatformAdapter 是否正确定义了 `send()` 接口。

## Research gaps

Sources to ingest:
- [x] [Anthropic: Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — 已在 [[harness-engineering-java-brownfield-20260507]] 摄入
- [x] [Anthropic: Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps) — 已在 [[harness-engineering-java-brownfield-20260507]] 摄入
- [ ] baoyu.io AI First 文章 — 还未摄入

## Audit backlog

*(none — run `python3 scripts/audit_review.py <wiki-root> --open` to refresh)*

## Notes for the LLM

- Language: bilingual（中英文混合，术语保留英文）
- Tone: technical, precise, concise
- Depth: deep technical — 要写出可操作的工程细节
- Handling contradictions: state both, cite each, add to Open Research Questions.
