# Index — LLM Wiki Knowledge Base

> Harness Engineering 知识库

## Navigation
- [[#Concepts]] · [[#Entities]] · [[#Summaries]] · [[#Open Questions]]

## Concepts

- [[LLM Wiki]] — AI 維護的持久知識庫：Index、Hot Cache、知識溯源
- [[Harness Engineering]] — AI 工程領域的第三次重心遷移：Prompt Eng → Context Eng → Harness Eng
- [[Cognitive Debt and LLM Use]] — 普通人用好大模型的核心框架：认知债务、认知摩擦、思考伙伴定位
- [[Agent Review Pattern]] — Agent 审 Agent 的机制（Reviewer Agent）
- [[Four-Layer Feedback Loop]] — 编译→单测→e2e→CI 四层反馈闭环
- [[Three Engineering Primitives]] — 隔离 / 分解 / 协调
- [[SDD Tool Landscape]] — Comparison of 12 spec-driven development tools
- [[Superpowers + OpenSpec Workflow]] — superpowers-zh + OpenSpec + superpowers-openspec 组合工作流：完整调用链 + 三层判断点 + 技能引用模式
- [[Agentic Loop]] — Gather Context → Take Action → Verify Results 三阶段循环
- [[Context Window Management]] — Claude Code 的上下文稀缺性与压缩策略
- [[SE and LLM Harness]] — 软件工程实践与 LLM Harness 的深层关联：测试→Evaluation Harness、CI/CD→Quality Gates、代码审查→Agent Review Pattern

## Entities

- [[Chorus]] — 给 Agent 用的任务管理系统（v0.1→v0.6.1 演进）
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
- [[github-open-ralph-wiggum]] — Ralph Wiggum technique 开源实现：将 Claude Code/Codex/Copilot/OpenCode 包装在持久自参照循环中（Bun + TypeScript）
- [[gstack]] — Garry Tan 的 Claude Code 技能包（79k ⭐）：多角色工程团队、Harness Engineering 生产级参考实现
- [[ralph-claude-code]] — Bash 实现的 Autonomous Loop（v0.11.5，566 tests）：Dual-condition exit gate + Circuit Breaker + 5h API limit 三层保护
- [[superpowers-zh]] — AI 编程超能力中文增强版（20 skills，17款工具）
- [[superpowers-openspec]] — superpowers ↔ OpenSpec 桥接 skill：中文意图路由 + 方案先行门禁
- [[speccoding-template]] — Claude Code + OpenSpec + Superpowers 三件套全栈模板：两级 Spec + 七阶段工作流
- [[OpenSpec-Chinese]] — OpenSpec 中文本地化版：零侵入 + 完整中文 UI
- [[github-mattpocock-skills-grill-me]] — Socratic 面试式 skill：递归追问设计决策树，直至达成共识
- [[Mattpocock Skills 对比分析]] — 16 个 skills 横向对比：复杂度光谱、设计模式、grill-me 定位

## Summaries (chronological)

- 2026-04-21 — [[summaries/anthropic-agent-sdk-architecture]] — Anthropic Agent SDK 六大组件：Tools/Hooks/Subagents/MCP/Permissions/Sessions
- 2026-04-27 — [[summaries/ralph-claude-code]] — Bash Autonomous Loop 实现：Dual-condition exit gate + Circuit Breaker + 5h API limit 三层保护
- 2026-04-21 — [[summaries/claude-code-agentic-harness]] — Anthropic 官方定义 Claude Code 本身即完整 agentic harness
- 2026-04-21 — [[summaries/harness-core-concept]] — 一句话定义 + 五点讲透 Harness 核心：约束/反馈环/质量闸门三层架构
- 2026-02-11 — [[summaries/openai-harness-engineering-codex]] — OpenAI 五个月实验：无人工代码、1/10 时间交付百万行代码库的 Harness Engineering 实践

## Open Questions

- 什么样的 `init.sh` 起步最轻量且有效？
- 多 Agent 协作中，Supervisor Agent 的信任边界如何量化？
- 技术债务清理的"阈值"怎么定？Agent 什么时候该停下来重构而不是继续堆功能？
