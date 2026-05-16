# Index — LLM Wiki Knowledge Base

> Harness Engineering 知识库

## Navigation
- [[#Concepts]] · [[#Entities]] · [[#Summaries]] · [[#Open Questions]]

## Concepts

### AI行业趋势
- [[算力分配-311]] — 研究:预训练:后训练 = 3:1:1 算力分配公式，行业范式从"砸预训练"转向"研究驱动"
- [[Agent框架]] — 人和模型之间的厚中间层：OpenClaw vs Claude Code、框架弥补模型短板
- [[生产力变革时代]] — 2026年主旋律：从Chat时代转向Agent时代，两条路径（高价值 vs 社会普适）

### Harness Engineering
- [[LLM Wiki]] — AI 維護的持久知識庫：Index、Hot Cache、知識溯源
- [[Harness Engineering]] — AI 工程領域的第三次重心遷移：Prompt Eng → Context Eng → Harness Eng
- [[SDLC Agent Skills Pattern]] — 6阶段 SDLC 封装为 AI Agent 可执行 Skill 的范式：Define→Plan→Build→Verify→Review→Ship + Anti-rationalization 机制
- [[Cognitive Debt and LLM Use]] — 普通人用好大模型的核心框架：认知债务、认知摩擦、思考伙伴定位
- [[Agent Review Pattern]] — Agent 审 Agent 的机制（Reviewer Agent）
- [[Four-Layer Feedback Loop]] — 编译→单测→e2e→CI 四层反馈闭环
- [[Three Engineering Primitives]] — 隔离 / 分解 / 协调
- [[SDD Tool Landscape]] — Comparison of 12 spec-driven development tools
- [[Superpowers + OpenSpec Workflow]] — superpowers-zh + OpenSpec + superpowers-openspec 组合工作流：完整调用链 + 三层判断点 + 技能引用模式
- [[Agentic Loop]] — Gather Context → Take Action → Verify Results 三阶段循环
- [[Context Window Management]] — Claude Code 的上下文稀缺性与压缩策略
- [[SE and LLM Harness]] — 软件工程实践与 LLM Harness 的深层关联：测试→Evaluation Harness、CI/CD→Quality Gates、代码审查→Agent Review Pattern

### Security
- [[Linux Kernel Privilege Escalation]] — 内核提权漏洞：CVE-2016-5195 (Dirty COW) / CVE-2022-0847 (Dirty Pipe) / io_uring 系列

### AI人物
- [[罗福莉]] — 小米大模型负责人，前DeepSeek核心，MiMo-V2系列主导者
- [[OpenClaw]] — 开源Agent框架：分层记忆、多模型编排、源码可改
- [[小米_MiMo]] — 小米大模型产品线：MiMo-V2（1T参数，42B激活，1M context）

### Tools
- [[Chorus]] — 给 Agent 用的任务管理系统（v0.1→v0.6.1 演进）
- [[tessera]] — AI 编码 Agent 可视化工作区：Claude Code/Codex/OpenCode 并行 + Git worktree 管理 + Kanban + PR 追踪，Electron 桌面 + npm 双模式
- [[learn-claude-code]] — Bash is all you need 课程（54k ⭐）
- [[learn-harness-engineering]] — Harness 工程实践课程（752 ⭐）
- [[github-spec-kit]] — GitHub's programmable SDD workflow engine (Python)
- [[github-open-spec]] — Fission AI's spec + delta format SDD tool (TypeScript)
- [[github-gsd]] — Get Shit Done: context hygiene for AI coding agents (TypeScript)
- [[github-potpie]] — Knowledge graph from codebase (Python, Apache 2.0)
- [[github-claude-workflow]] — Spec → validated impl with parallel dispatch (Python)
- [[github-spec2ship]] — Multi-role deliberation for spec refinement (TypeScript)
- [[github-human-in-loop]] — Deterministic DAG + human checkpoints (Python)
- [[github-swarm]] — Parallel worktree orchestration with crash recovery (Python)
- [[github-generic-agent]] — Self-evolving autonomous agent, ~3K lines, real browser control (Python)
- [[github-vibe-skills]] — 340+ skills + governed runtime (VCO), M/L/XL grades, 129 governance rules
- [[github-trellis]] — Multi-platform spec injection, task continuity, 14 platforms (Node.js + Python)
- [[github-khazix-skills]] — 卡茲克 Skills 工具箱：hv-analysis 橫縱研究法 + khazix-writer 公號寫作（5K ⭐）
- [[github-architecture-diagram-generator]] — AI 生成暗色調架構圖 HTML（內嵌 SVG，Claude Skill）
- [[github-deer-flow]] — ByteDance 超級 Agent 馬車：62K ⭐，LangGraph + 18 Middleware + Skills 系統 + 子 Agent 並行
- [[github-hermes-lcm]] — Hermes 無損上下文管理插件：SQLite 不可變存儲 + DAG 摘要 + 3 層 escalation（L1→L2→L3）
- [[github-codeflow]] — 單 HTML 文件代碼庫可視化：粘貼 GitHub URL 秒級生成依賴圖 + 安全掃描 + 熱點定位
- [[agent-skills]] — Addy Osmani 出品：20 工程技能库，6阶段 SDLC，Claude Code/Gemini CLI/OpenCode 多平台，MIT License
- [[entities/ruflo]] — Multi-agent AI orchestration for Claude Code：100+ agents + Swarm 协调 + AgentDB/HNSW 向量记忆 + 联邦安全协作，原名 Claude Flow，MIT License
- [[entities/github-zcf]] — Zero-Config Code Flow（zcf）：Claude Code / Codex 环境自动化配置 CLI，10+ API 预设 + MCP + Workflow + Output Style 一键初始化
- [[cc-connect]] — 开源 AI Agent 桥接工具（7.9k ⭐）：将 Claude Code/Codex/Cursor 接入飞书/钉钉/微信/Telegram 等 11 个聊天平台
- [[aegis]] — Architecture-Driven Development 方法包（113 ⭐）：让 AI 编程 Agent 在动手前先理解项目基线、架构边界和验证路径
- [[slock-ai]] — 商业人类+AI Agent 平等队友协作平台（需自托管替代：multica/clawith）
- [[clawith]] — OpenClaw for Teams（3.5k ⭐）：多 Agent 持久身份+共享记忆+团队协作
- [[Multica]] — 开源多 Agent 队友平台（26k ⭐）：任务面板+Agent生命周期管理+Skills积累，支持 Claude Code/Codex/Hermes 等 11 种 Agent CLI
- [[paperclip]] — 心跳驱动的 Agent 编排平台：通过适配器层对接 Claude Code/Codex/OpenClaw，支持多租户、预算治理、实时监控
- [[gstack]] — Garry Tan 的 Claude Code 技能包（79k ⭐）：多角色工程团队、Harness Engineering 生产级参考实现
- [[coder-studio]] — 浏览器 AI 编程工作台（20 ⭐）：Supervisor 监督循环 + 跨设备会话延续，Phase 3 将实现目标驱动的评估-注入自动化
- [[kandev]] — 多Agent并行编排平台（AGPL-3.0）：Kanban + 5种工作流模板 + ACP协议 + agentctl边车，支持16款Agent在Local/Docker/Sprites上执行
- [[openhands]] — AI驱动开发平台（MIT，SWEBench 77.6%）：Python SDK + CLI/GUI/Cloud/Enterprise多形态，litellm模型无关，Docker容器隔离，MCP原生支持
- [[openhands]] — AI驱动开发平台（MIT，SWEBench 77.6%）：Python SDK + CLI/GUI/Cloud/Enterprise多形态，litellm模型无关，Docker容器隔离，MCP原生支持
- [[entities/vibe-kanban]] — Vibe Kanban（**已关闭**）：Rust+TypeScript monorepo，看板+AI Agent Workspace协作平台，支持10+种Agent（Claude Code/Codex/Gemini CLI等），Git Worktree隔离，MCP服务器
- [[entities/memory-systems/tencentdb-agent-memory]] — 腾讯四层记忆系统（MIT）：L0对话→L1原子→L2场景→L3画像分层架构，Mermaid符号化压缩短期记忆（Token省61%），OpenClaw/Hermes双平台支持，Host-Neutral TdaiCore设计
- [[entities/xians-ai]] — Xians.ai（Enterprise Agent ADK）：.NET 9 + Temporal.io + MongoDB，多租户多渠道通信，Chat/Data双消息模式，Semantic Kernel + RAG/CAG
- [[entities/openspawn]] — OpenSpawn（Control Plane）：ORG.md定义Agent组织 + L1-L10层级 + Autonomy Dial + MCP/A2A协议，Built on OpenClaw，无需Docker本地运行
- [[entities/ralph-claude-code]] — Bash 实现的 Autonomous Loop（v0.11.5，566 tests）：Dual-condition exit gate + Circuit Breaker + 5h API limit 三层保护
- [[superpowers-zh]] — AI 编程超能力中文增强版（20 skills，17款工具）
- [[superpowers-openspec]] — superpowers ↔ OpenSpec 桥接 skill：中文意图路由 + 方案先行门禁
- [[speccoding-template]] — Claude Code + OpenSpec + Superpowers 三件套全栈模板：两级 Spec + 七阶段工作流
- [[OpenSpec-Chinese]] — OpenSpec 中文本地化版：零侵入 + 完整中文 UI
- [[github-mattpocock-skills-grill-me]] — Socratic 面试式 skill：递归追问设计决策树，直至达成共识
- [[Mattpocock Skills 对比分析]] — 16 个 skills 横向对比：复杂度光谱、设计模式、grill-me 定位
- [[memorizz]] — Python 多记忆类型（10种）× 可插拔存储（Oracle/Mongo/FS），实验性/教育用，PolyForm Noncommercial（禁止商用）
- [[AI-DevOS]] — 首个面向软件工程长期自治的操作系统：8层架构（Goal→Planning→Governance→Scheduler→Runtime→Workspace→Verification→Memory），解决 durability + engineering continuity + governance 断层

## Summaries (by category)

### 🏗️ Harness Engineering
- 2026-05-20 — [[summaries/harness-engineering/eval-harness-production-agents-20260520]] — 生产级 AI Agent 12 指标评估框架（检索/生成/Agent/生产健康），100+ 企业部署验证
- 2026-05-20 — [[summaries/harness-engineering/multi-agent-harness-design-20260520]] — 从零设计生产级 Multi-Agent Harness：五大核心模块（架构编排/工具治理/状态记忆/评估体系/成本控制）+ MCP 接入
- 2026-05-20 — [[summaries/harness-engineering/ai-native-org-design-20260520]] — AI Native 时代研发组织转型：Org Chart → Execution Graph（重组成本季度→week级）+ Platform 三柱架构 + Architect 最高杠杆点
- 2026-04-21 — [[summaries/harness-engineering/harness-core-concept-20260421]] — Harness Engineering 一句话定义 + 五点核心：约束/反馈环/质量闸门三层架构
- 2026-04-21 — [[summaries/harness-engineering/claude-code-agentic-harness-20260421]] — Anthropic 官方：Claude Code 本身即完整 agentic harness，Agentic Loop 三阶段（Gather Context / Take Action / Verify Results）
- 2026-02-11 — [[summaries/harness-engineering/openai-harness-engineering-codex-20260211]] — OpenAI 五个月实验：无人工代码、1/10 时间交付百万行代码库的 Harness Engineering 实践
- 2026-05-07 — [[summaries/harness-engineering/harness-engineering-java-brownfield-20260507]] — 存量 Java 工程 AI Coding 率从 25% 提升至 90%：外部化约束体系（.harness/ 四要素：Rules + Skills + Wiki + Changes）

### 🤖 Claude Code 生态
- 2026-05-20 — [[summaries/claude-code-ecosystem/claude-code-large-codebases-20260520]] — Anthropic 官方：大代码库部署最佳实践：Agentic Search + 五大扩展点 + 三大配置模式
- 2026-04-21 — [[summaries/claude-code-ecosystem/anthropic-agent-sdk-architecture-20260421]] — Anthropic Agent SDK 架构解析：Tools/Hooks/Subagents/MCP/Permissions/Sessions 六大组件
- 2026-04-27 — [[summaries/claude-code-ecosystem/ralph-claude-code]] — Ralph（Bash Autonomous Loop）：Dual-condition exit gate + Circuit Breaker + 5h API limit 三层保护，566 tests 100% pass

### 🔀 Multi-Agent 编排平台
- 2026-05-09 — [[summaries/multi-agent-platforms/multica]] — Multica：开源多 Agent 队友平台（26k ⭐），Go+Next.js，pgvector知识积累，支持 11 种 Agent CLI 自托管
- 2026-05-09 — [[summaries/multi-agent-platforms/ruflo]] — ruflo：Claude Code 多 agent 编排框架，100+ agents + 三层模型路由 + HNSW 记忆 + 联邦协作，MIT
- 2026-05-09 — [[summaries/multi-agent-platforms/multica-vs-vibepro]] — Multica vs VibeKanBan 对比：Multica 面向 Agent 调度（底层扎实，UI 残），VibeKanBan 面向人审代码
- 2026-05-08 — [[summaries/multi-agent-platforms/slock-ai-alternatives]] — slock.ai 竞品：Multica 任务管理型 + Clawith 团队协作型 + Canopy 本地优先 + AgentDM 协议层

### 🧠 记忆与状态管理
- 2026-05-03 — [[summaries/memory-state-management/luo-fuli-3h-interview-zhangxiaojun-2026]] — 罗福莉 3.5h 访谈：AI 范式从 Chat 时代转向 Agent 时代，3:1:1 算力分配，OpenClaw 三天认知转变
- 2026-05-13 — [[summaries/memory-state-management/memorizz]] — Memorizz：10种记忆类型 × 3种存储后端（Oracle/Mongo/FS），实验性/教育用

### 🛠️ 开发者工具与基础设施
- 2026-05-09 — [[summaries/devtools-infrastructure/tessera]] — Tessera：AI 编码 Agent 可视化工作区，Claude Code/Codex/OpenCode 并行 + Git worktree + Kanban + PR 追踪，Electron 桌面 + npm 双模式
- 2026-05-07 — [[summaries/devtools-infrastructure/agent-skills]] — Addy Osmani 出品：20 工程技能库覆盖 SDLC 六阶段，Anti-rationalization 机制嵌入每个 Skill
- 2026-05-07 — [[summaries/devtools-infrastructure/github-zcf]] — zcf（Zero-Config Code Flow）：Claude Code / Codex 环境自动化配置 CLI，10+ API 预设 + MCP + Workflow 一键初始化
- 2026-05-09 — [[summaries/devtools-infrastructure/cc-connect-aegis]] — cc-connect（7.9k ⭐）+ Aegis（113 ⭐）：AI Coding Agent 工具双雄（通信桥接 + 架构先行验证）
- — [[summaries/devtools-infrastructure/gstack]] — Garry Tan 的 Claude Code 技能包（79k+ ⭐）：多角色虚拟工程团队 + 持久化 Browser Daemon + 铁律审查链

### 📚 通识
- 2026-05-12 — [[summaries/general-knowledge/perplexity-agent-skills-design]] — Perplexity Agent Skills 设计实践：技能本质是上下文加载而非代码执行，三层渐进式加载，gotchas 最价值

### ⏳ 待确认（无原文）
- 2026-05-15 — [[summaries/ai-devos-prd]] — AI DevOS PRD：面向长任务自治的软件工程操作系统，8层架构——Nick 未提供原文，无法确认内容

## Open Questions

- 什么样的 `init.sh` 起步最轻量且有效？
- 多 Agent 协作中，Supervisor Agent 的信任边界如何量化？
- 技术债务清理的"阈值"怎么定？Agent 什么时候该停下来重构而不是继续堆功能？
