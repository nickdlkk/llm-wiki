---
title: "Agent 开发指南：技术太多，该怎么学？"
summary: "lencx 的 2026 Agent 技术趋势报告：把分散的开源、收购、语言重构、浏览器能力汇聚到同一套 Agent 原生基础设施；核心判断是「Agent 不缺代码，缺可信完成」，终点是让不可靠推理者在可靠系统中持续承担责任"
type: summary
source: "微信公众号"
url: https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA
author: lencx
date: 2026-07-28
created: 2026-08-06
updated: 2026-08-06
tags: [harness-engineering, agent-infrastructure, trends, 2026, lencx, vercel, cloudflare, gateway, desktop, browser, skills, language-stack]
sources:
  - https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA
confidence: high
---

# Agent 开发指南：技术太多，该怎么学？— 蒸馏

> **一句话总结：** Agent 不缺代码，**缺可信完成**。在概率性推理核心外建立确定性执行外壳，是 Agent 原生基础设施的共同方向；Harness、Skills、Gateway、Browser、桌面宿主、语言栈都围绕这一命题展开。

> 本文是 lencx（2026-07-28）发布的 1999 行长文。蒸馏保留原文十一节骨架、核心论断、关键项目/收购证据与原话，剔除相同章节的重复论证与可省略的代码块；所有引用以 `[n]` 形式保留，可在 `raw/articles/lencx-agent-dev-guide-20260806.md` 末尾 References 还原。

## 核心判断

1. **因果链**：生成与试错成本下降 → 同一任务产生更多代码/分支/工具调用 → 环境、状态、权限、失败面复杂度上升 → **稀缺性从「代码」转向「可信结果」**
2. **终点重定义**：Agent 原生软件的目标不是「最大自治」，而是「让一个不完全可靠的推理者，能够在可靠系统中持续承担责任。」
3. **Monetization 原则**：每一层都可开源一部分，**真正能收费的通常是托管的执行、身份、状态、浏览器、GPU、部署、可观测性控制面**。
4. **观察尺度**：高于 LangGraph 等具体框架，聚焦跨框架长期存在的 7 类系统责任：目标、状态、身份、执行、验证、副作用、人工接管。

## 一、回答不等于负责

经典 LLM 应用：`prompt → model → response`
Agent 环路：`observe → plan → act → wait → verify → settle → pause/approve/retry/recover`

**差别在于状态模型**：一次回答可以失败后重问，一次真实动作可能已发邮件、创建资源、扣款，进程却在收到结果前崩溃。盲目重试可能造成重复副作用。

生产 Agent 因而需要：

- 稳定的任务、步骤、动作身份
- 幂等键和副作用收据
- 超时、取消、预算、资源租约
- dispatch 前/后/确认后的明确阶段
- 对结果不确定的动作执行 reconcile，避免盲目重放
- 检查点、恢复、补偿、人工接管
- 以可核验证据驱动完成判定

**核心命题**：为「不确定状态」建立「确定的所有权」。

## 二、Harness 持有全程

### Prompt 管一次，Harness 管全程

| 层级 | 持有状态 | 覆盖时间 | 负责执行段 |
|------|---------|---------|-----------|
| **Context** | 每次采样的视野 | 单次推理 | 模型输入 |
| **Loop** | 时间轴 | 任务生命周期 | 动作序列 |
| **Graph** | 任务拓扑 | 编排窗口 | 节点依赖 |
| **Skill** | 可加载的程序性模块 | 调用期 | 一次操作 |
| **Harness** | 目标 | 整个可恢复执行 | 全部 |

> Context 是每次采样的视野，Loop 是时间轴，Graph 是任务拓扑，Skill 是可加载的程序性模块；**Harness 持有目标**，并把它们组织成一次可恢复执行。

### OpenAI Harness Engineering 正式定义

不再只优化模型「看到什么」，而是让工程师**设计环境、表达意图、建设反馈环路**，使 Agent 能可靠工作 <sup><a href="#ref-4">[4]</a></sup>[raw/articles/lencx-agent-dev-guide-20260806.md]。

### Anthropic Managed Agents 拆分

- **Session** = 追加写的事件日志
- **Harness** = 调用模型并路由工具的循环
- **Sandbox** = 执行代码与文件动作的环境 <sup><a href="#ref-5">[5]</a></sup>[raw/articles/lencx-agent-dev-guide-20260806.md]

### Harness 删减 vs 模型升级

Anthropic 披露：Claude Code 为 Opus 5 / Fable 5 删掉 **>80%** system prompt，coding eval 无可测量损失 <sup><a href="#ref-6">[6]</a></sup>[raw/articles/lencx-agent-dev-guide-20260806.md]。每条长期指令都应有退出条件；模型升级时 Harness 应删除过时脚手架。

**方法转向**：

- 少写覆盖所有场景的规则 → 让模型结合上下文判断
- 少用示例限定工具探索 → 改为表达力更强的参数与状态
- 不再把所有 review/verification 信息前置 → 通过 Skills、deferred tools、progressive disclosure 按需加载
- Claude Code 的 `/doctor` 用于收缩过度膨胀的 Skills 与 CLAUDE.md

### Goal 定义何时停

Codex Goal <sup><a href="#ref-7">[7]</a></sup> 把长任务责任边界落实为产品原语。`/goal` 文本既是第一条 prompt，也是完成判据：

```
Intent → Goal { outcome, constraints, verifier, budget }
     → Plan / task graph
     → Observe → Act → Collect evidence
     → checkpoint + update state
     → continue / retry / needs-input / blocked / completed
```

**原则**：完成状态应由测试、可测指标、Diff、截图、外部状态或人工验收确认，而非模型自述。当 verifier 不可达、预算耗尽、权限不足、外部依赖永久失败时，Harness 必须进入 `blocked` / `needs-input` / `cancelled` / `budget-exhausted` 等明确终态。**缺少停止证明和资源上限的 Goal，本质上仍是无限循环。**

### Memory 不是向量库

至少应拆成 5 类：

| 类型 | 内容 | 恢复语义 |
|------|------|---------|
| **工作状态** | 当前计划、步骤、游标、预算、等待条件 | 可被新 checkpoint 取代 |
| **事件历史** | 观察、动作、工具结果、审批、结算记录 | 追加写 |
| **领域知识** | 文档、代码、业务事实及其来源、版本 | 保留版本与来源 |
| **情节与偏好** | 历史任务总结、用户习惯、可衰减经验 | 衰减和纠错 |
| **身份与隐私** | 凭据、授权、租约、人类决定 | 独立隐私边界 |

**设计顺序**：先回答「谁能写 / 什么是真相 / 何时失效 / 崩溃后从哪恢复」，再选择存储与检索技术。

### Subagent 拆上下文，Handoff 转责任

- **Agent as tool**：主 Agent 保留控制权，调用 specialist 完成有边界子任务，再统一验证和交付
- **Handoff**：当前 Agent 把会话控制权转给 specialist <sup><a href="#ref-9">[9]</a></sup>

**信任 handoff** 需要结构化移交：目标与范围、已验证事实、未决假设、当前 checkpoint、待确认副作用、能力与审批状态、资源租约、停止条件、结果应交还谁。**只传一段自然语言摘要，会把上下文丢失伪装成组织分工。**

### Claude Code Dynamic Workflows

Claude 动态写 orchestration script，把任务分发给数十至数百个并行 subagent，加独立尝试、反驳、review、聚合、修复循环，并持久化进度以便中断后继续 <sup><a href="#ref-10">[10]</a></sup>。Bun 的 Zig→Rust 迁移 <sup><a href="#ref-11">[11]</a></sup> 19 个合并后 regression 已足够说明边界：**Agent 可把机械迁移推到既有验收器覆盖的位置，测试没有表达的行为仍会逃逸。**

## 三、平台争夺 Agent 全栈

Vercel 从表示/开发体验向执行层下探，Cloudflare 从 runtime/状态/安全向开发体验上探。**两家公司表面不同，向外扩张的结构逐渐接近。**

### Vercel 向下补执行

| 项目 | 责任定位 | 关键差异 |
|------|---------|---------|
| **AI SDK 7** <sup><a href="#ref-13">[13]</a></sup> | 统一模型调用 + HarnessAgent + SandboxSession | 扩为"组织受控执行" |
| **Chat SDK** <sup><a href="#ref-14">[14]</a></sup> | 适配 Slack/Teams/Discord/Telegram 等 11 渠道 | |
| **Streamdown** <sup><a href="#ref-15">[15]</a></sup> | 流式 Markdown | 适应长输出 |
| **json-render** <sup><a href="#ref-16">[16]</a></sup> | 受 catalog/schema 约束的 UI 规范 | UI 变中间表示 |
| **Vercel Sandbox** <sup><a href="#ref-17">[17]</a></sup> | Firecracker microVM | 隔离与兼容环境 |
| **agent-browser** <sup><a href="#ref-18">[18]</a></sup> | 浏览器 CLI/daemon，accessibility snapshot | 观察—行动—再观察压缩成低 token 机器接口 |
| **portless** <sup><a href="#ref-19">[19]</a></sup> | 稳定 `.localhost` 域名、自动端口分配 | 解决 Agent 并行 worktree 寻址 |
| **ai-cli** <sup><a href="#ref-20">[20]</a></sup> | 图像/视频/音频/文本 stdin/stdout + JSON metadata | Agent 偏好的执行工具形状 |
| **scriptc** <sup><a href="#ref-21">[21]</a></sup> | TS → typed IR → LLVM/C → native | 静态证明 + QuickJS 动态层 + 显式拒绝 |
| **Native SDK** <sup><a href="#ref-23">[23]</a></sup> | Zig engine + `.native` markup，record/replay | UI 本身可观察、可驱动、可回放 |
| **vgpu** <sup><a href="#ref-24">[24]</a></sup> | Agent-first WebGPU + deterministic mock | 显式 GPU context + WGSL reflection |
| **Turborepo** <sup><a href="#ref-25">[25]</a></sup> <sup><a href="#ref-26">[26]</a></sup> <sup><a href="#ref-27">[27]</a></sup> <sup><a href="#ref-28">[28]</a></sup> <sup><a href="#ref-29">[29]</a></sup> <sup><a href="#ref-30">[30]</a></sup> | Task DAG + 哈希 + 远程缓存 | "可执行、可缓存、可检查的确定性工作图" |
| **SWC** <sup><a href="#ref-31">[31]</a></sup> <sup><a href="#ref-32">[32]</a></sup> <sup><a href="#ref-33">[33]</a></sup> | TS/JS 编译热路径 | Next.js Compiler 默认 |

**Vercel 关键收购补链**：

- Turborepo (2021) → 仓库任务图
- **Grep (2024)** → 代码搜索与真实用例检索
- Tremor (2025) → 数据 UI 原语（服务 v0）
- NuxtLabs (2025) → Nuxt + Nitro server runtime
- **Better Auth (2026)** → 每个 Agent/subagent 自有、可撤销、可缩小的身份

拼出的闭环：**找到正确上下文 → 生成受约束界面 → 在可移植 runtime 中执行 → 用独立身份承担副作用**。

### Cloudflare 向上补体验

| 项目 | 责任 |
|------|------|
| **Agents** <sup><a href="#ref-45">[45]</a></sup> | Durable Objects + SQLite + lifecycle + WebSocket + Schedule + MCP + Workflows/HITL |
| **Browser Run** <sup><a href="#ref-48">[48]</a></sup> <sup><a href="#ref-49">[49]</a></sup> | 原 Browser Rendering，headless Chrome + CDP + Puppeteer/Playwright |
| **Nimbus** <sup><a href="#ref-53">[53]</a></sup> | `.md/.mdx` twins + llms.txt/full + JSON-LD + Agent handoff |
| **Cloudflare Skills** <sup><a href="#ref-54">[54]</a></sup> | 平台操作知识 + commands + 远程 MCP |
| **Agent Readiness** <sup><a href="#ref-55">[55]</a></sup> | 站点机器界面 |
| **Code Mode** <sup><a href="#ref-56">[56]</a></sup> | 隔离 Worker 跑组合代码，凭据留宿主 |

**三类状态不同恢复路径**：

- Agent state：实时同步给用户的小型进度
- Sandbox filesystem：代码、依赖、日志、产物
- Workflows：跨请求、需重试或等待外部事件

**关键拆分**：已弃用 Sandbox SDK 的 desktop feature <sup><a href="#ref-52">[52]</a></sup>，把浏览器自动化迁到 Browser Run。**代码执行环境与浏览器身份/会话宿主分开**：前者承载不受信任计算，后者承载昂贵、长寿、带凭据的 Web 能力。统一称 "sandbox" 会丢失差异。

### Cloudflare 收购补链

| 收购 | 时间 | 补齐 |
|------|------|------|
| PartyKit | 2024 | 实时协作 + stateful serverless |
| Baselime | 2024 | 高基数日志、serverless observability |
| Outerbase | 2025 | 数据库 Agent DX |
| **Replicate** | 2025 | 模型打包、目录、GPU inference |
| **Human Native** | 2026 | 许可数据、内容结构化 |
| **Astro** | 2026 | 内容型 Web + 文档层 |
| **VoidZero** | 2026 | Vite + Vitest + Rolldown + Oxc + 本地到部署工具链 |

**纵向平台路径**：本地项目语义由 Vite/Oxc 理解 → 运行在 Workers/Containers → 状态落在 Durable Objects/D1/R2 → 调用 Replicate/Workers AI + Browser Run → Baselime 提供证据。

### Vercel × Cloudflare 共同方向

开源项目与 Skills **既服务开发者分发，也会进入模型上下文、代码语料与团队规范，形成"默认选择先验"**。平台公司开放边缘 SDK/协议，在托管执行、全局状态、身份、GPU、浏览器、审计层变现。

## 四、模型公司吞并数字工作

### Coding 是自然楔子

代码世界已为 Agent 准备好半套 OS：repository/filesystem = 结构化外部记忆；shell/Git/compiler/test runner = 可组合动作；Diff/类型检查/测试 = 客观 judge；branch/worktree/commit = 隔离可回滚；开发者容忍 CLI/日志/显式权限摩擦。**这就是 Claude Code/Codex/Gemini 首先在编程里证明长循环的原因。**

Harness 一旦学会读写、调用、保留状态、并行分解、验证，同一骨架可向研究、运营、财务、文档、演示、设计、跨应用流程迁移；**改变的主要是 Skills、连接器、artifact renderer、权限策略、judge**。

### 并购补链与三种控制关系

模型公司在执行链关键瓶颈上纳入反馈回路：

| 模型公司 | 收购 | 补齐 |
|---------|------|------|
| Anthropic | **Bun** <sup><a href="#ref-65">[65]</a></sup> (2025-12) | JS/TS runtime + 包管理 + bundler + test runner |
| Anthropic | Vercept <sup><a href="#ref-66">[66]</a></sup> | Computer use 视觉 + GUI |
| Anthropic | Stainless <sup><a href="#ref-67">[67]</a></sup> | OpenAPI → SDK/CLI/MCP |
| OpenAI | **Promptfoo** <sup><a href="#ref-68">[68]</a></sup> | Agent eval、red-team、安全 |
| OpenAI | 拟收购 Astral <sup><a href="#ref-69">[69]</a></sup> | uv/Ruff/ty |
| OpenAI | 拟收购 Ona <sup><a href="#ref-70">[70]</a></sup> | 持久云环境 |
| OpenAI | Sky <sup><a href="#ref-71">[71]</a></sup> | 屏幕理解 + macOS 操作 |

**三种生态控制关系**：

1. **所有权整合** — Bun 代表
2. **影响力合作** — **OpenClaw Foundation** <sup><a href="#ref-72">[72]</a></sup>：作者加入 OpenAI → 项目入 501(c)(3) → OpenAI 资助 + Claw Labs 合作。OpenAI 获得 Gateway/Harness 一手经验，保留 OpenClaw 跨模型位置
3. **中立标准治理** — MCP / AGENTS.md 入 Linux Foundation AAIF <sup><a href="#ref-73">[73]</a></sup>

模型公司争夺的也不只是源码；**维护团队、兼容性语料、默认集成、分发渠道、生产反馈往往更加稀缺**。

### Anthropic 产品线全景

| 产品 | 职责 |
|------|------|
| **Claude Chat** | 对话、思考、轻量生成 |
| **Claude Code** | repo + shell + Git + 测试的工程闭环 |
| **Claude Cowork** <sup><a href="#ref-74">[74]</a></sup> | Claude Code agentic approach 复用 → 本地文件 + Slack/Drive + 浏览器 + subagent + scheduled task + 知识工作 artifact |
| **Claude Design** <sup><a href="#ref-75">[75]</a></sup> | chat + canvas + direct manipulation + /design-sync 双向同步 |

**Anthropic 2026-07 披露**：Cowork **>90% 使用量来自软件开发之外**，主要集中在业务运营与内容创作 <sup><a href="#ref-77">[77]</a></sup>。

### OpenAI 桌面宿主共享

2026-07 ChatGPT desktop shell 同时容纳 **Chat / Work / Codex**。内置 browser 同时服务 Work 和 Codex <sup><a href="#ref-79">[79]</a></sup>。**桌面宿主与能力平面共享，但不抹平两个产品各自的任务状态**。

### 越通用，越难验收

- 编译器判定语法，测试检查部分行为
- 董事会材料是否抓住重点、客户沟通是否合适、视觉方向是否符合品牌、采购是否应该发生 → **没有同等便宜的 verifier**
- 通用 Agent 更依赖：来源引用 + 领域 Skill + 组织 policy + 人工审批 + 角色分离 + 可逆草稿
- **"测试绿了就合并" 无法照搬到通用场景**

## 五、入口分化，状态合流

从界面历史看：聊天框 → CLI/TUI → IDE → 桌面 GUI → Browser/Computer Use。**时间顺序制造"新界面替代旧界面"错觉，实际发生的是入口分工**：CLI 留在行动底座，其他表面围绕异步任务、监督、接管生长。

### CLI 是行动总线

终端为 Agent 提供的共同属性：

- 文本输入输出紧凑，适合进模型上下文
- stdin/stdout/stderr/退出码/文件产物易组合
- 本机/容器/CI/SSH/远程 sandbox 可复用
- Git diff + 测试日志 + 构建 artifact 天然构成验证证据
- 非交互模式易设置 timeout、cwd、env、权限边界
- 一次动作可记录、重放、比较

**CLI 演化**：Claude Code（2025-02）→ Codex CLI（2025-04）→ Gemini CLI（2025-06）→ Antigravity CLI（2026）→ Grok Build（2026-05）。

**Google 案例**：官方公告先肯定终端是优秀的 Agent 界面，再指出多 Agent/异步任务/跨界面需要统一后端。**新 Go CLI 支持后台异步工作，与 Antigravity 2.0 桌面应用共享 Agent harness**，会话可从终端导出到 GUI 继续 <sup><a href="#ref-87">[87]</a></sup>。**同一 core engine 两个表面**：CLI 面向键盘/SSH/脚本，桌面端面向视觉编排。

### Bun 缩短执行环

`Agent CLI/Harness → shell tool call → Bun/Node/Python/uv/rg/Git → filesystem/process/network/OS`

Anthropic 收购 Bun，正是把这条高频 JS/TS 执行链纳入 Claude Code 反馈回路。**它只服务采用 Bun 的工作流，尚未成为所有 Agent CLI 的共同底座。**

### rg 是低成本雷达

`rg` 启动快、只读、跨平台、无需常驻索引；`rg --files` 枚举候选；`--json` 接入脚本化循环。**进入 Agent 回路后**，默认规则跳过 ignored/hidden/binary、不跟 symlink，**直接影响搜索可见性与结果复现** <sup><a href="#ref-92">[92]</a></sup>。

### Gateway 让 Agent 常驻

把 Agent 接进 Telegram/Discord/Slack/WhatsApp。**IM 是异步任务的 interrupt、attention、delivery surface，价值远高于聊天皮肤。**

Gateway 必须处理模型之外的问题：

- 同一条 inbound 是否已消费
- 运行中新消息是排队还是 steer
- 进程崩溃时 final response 到底有没有发出
- 附件落在哪里
- 一个 channel 是否共享 memory
- 远程 `/approve` 究竟批准了哪条命令

**关键边界**：「能给机器人发消息」与「能批准在某台机器执行某 argv/cwd/credential scope」**必须是两份契约**。

### Pi：小内核，大扩展

```mermaid
pie monorepo
  pi-ai → provider / stream / tool schema / 跨供应商
  pi-agent-core → loop / event / steering / follow-up / tool hook
  pi-coding-agent → CLI/TUI / session tree / compaction / Skills / extension / RPC
```

**选择**：最小 Harness + 最大可塑性。plan/subagent/handoff/permission gate/产品形态可由 extension/package 组合。

**实现边界**：

- JSONL session tree 支持 branch/fork/compaction
- **compaction 维护 tool call/result 边界、文件操作累积、超长 turn**
- Active run 的 crash-durable resume 仍在推进
- 当前能力 = durable session，**尚未覆盖 durable execution**
- `pendingJobs` 不会从未完成的 `job_queued` 重建 → 队列未达 crash-durable
- Pi core 默认继承启动用户的文件/进程/网络/凭据权限 → sandbox 与逐动作审批需外部宿主

### OpenClaw 重 Gateway，Hermes 重自进化

| 维度 | **OpenClaw** | **Hermes Agent** |
|------|--------------|------------------|
| 核心 | 长期运行 Gateway + typed control plane | Python AIAgent loop + 多 surface Harness |
| 远程入口 | IM/Web/desktop/nodes 接入同一 Gateway | 20+ platform adapter + relay + CLI/TUI/API |
| Skills/Memory | 分层 skill roots + proposal/scan/rollback + Markdown + hybrid recall | `/learn` + background review + skill/memory 自改善 + FTS history |
| Subagent | SQLite registry + yield/wake/handoff + 重启后语义续跑 | fresh child + live transcript + cost/file rollup + durable completion |
| **结果投递** | adapter 对账时 reconcile，无法证明时拒绝盲目重发 | durable ledger，崩溃歧义时重发并显式标记"可能重复" |

**核心命题**：durable Agent 从布尔标签拆成可验证问题：

1. inbound/session/task record 是否耐重启
2. 运行中 child 从语义 checkpoint 继续，还是只送回已完成结果
3. final delivery 能对账，还是只能诚实 at-least-once
4. filesystem rollback / Agent computation checkpoint / 外部副作用补偿各覆盖什么
5. approval 是否绑定原动作，重启后是否仍有效

**Exactly-once 通常不可得，关键是识别并暴露未知状态。**

**OpenClaw 特色**：Gateway 统一 channel/operator client/node capability/session/approval/delivery/child lifecycle。**审批做成持久、可核验的授权记录**，绑定 node/argv/plan/cwd/agent/session/channel/thread/requester device，**`allow-once` 只能消费一次** <sup><a href="#ref-98">[98]</a></sup>。这比 Telegram 中一个脱离原动作的"同意"更接近 capability authorization。

**Hermes 特色**：loop guardrail + context compression + 超大工具输出落盘 + background memory/skill review + session lineage + delegation observability。完整输出先落到 local/Docker/SSH/cloud backend，再由 Agent 分页读取。**这类小机制往往比增加复杂 planner 更直接提高长任务完成率。**

### GUI 管监督

从"盯着 token 流"变为：「委托 → 离开 → 被需要时回来 → 看证据而不是重读全过程 → 批准/纠偏/接管/验收」。

桌面应用成为 **shell + 浏览器 + 后台 runtime 的监督层**，一起承载：

- task/thread/worktree + 并行 Agent
- Diff/测试/截图/trace/最终 artifact
- browser/terminal/filesystem + drag-and-drop 上下文
- 细粒度 approval/暂停/恢复/人工接管
- 系统通知/托盘/全局快捷键/防休眠
- 本地任务与远程任务之间的连续会话

**Anthropic 2026-04 重做 Claude Code Desktop** <sup><a href="#ref-100">[100]</a></sup>：并行 session + 集成终端 + 文件编辑器 + Diff + HTML/PDF preview + 拖拽布局。**新界面让并行任务同时在途，由人坐在 orchestrator seat**。GUI 增长发生在 Agent core 上方，承担监督、证据审阅、人工接管。

**Codex ` /app`** <sup><a href="#ref-101">[101]</a></sup>：CLI 端可把当前会话继续到桌面应用；桌面端放在同一监督工作台。

### Pet 管注意力

**Pet 在 vibe coding 中的真实作用**：把 Agent 状态从工作台内的主动查询，转化为周边视野中的被动感知 <sup><a href="#ref-102">[102]</a></sup>[raw/articles/lencx-agent-dev-guide-20260806.md]。

**三合一轻量表面**：

- **Peripheral awareness** — 免去持续轮询
- **Interrupt routing** — 把人叫回需要决定/恢复的任务
- **Emotional salience** — 提升关键通知的可感知性

**长任务改变人/软件时间关系**：人可以离开工作台，只在决策、恢复、验收时回来。

**拟人化必须服从运行时真相**：

- Pet 只读投影持久任务状态，能打开对应任务与证据
- `Blocked` 要如实呈现，动画只传达注意力优先级
- 结果由测试/Diff/trace/人工验收确认
- 无障碍、隐私、减少动画、通知节流都属于状态机

## 六、浏览器成为能力平面

Agent 浪潮重新放大浏览器，因为浏览器把 5 种通常分散的能力放进同一宿主：

| 能力 | 内容 |
|------|------|
| **传感器** | DOM + Accessibility Tree + 网络 + console + 截图 + 性能 |
| **执行器** | 点击/输入/下载/上传/导航/脚本 |
| **身份容器** | cookie + SSO + session + 设备权限 |
| **验证与接管界面** | 人类看见结果 + 敏感步骤接手 |
| **本地计算** | Wasm + WebGPU + 媒体 + 存储 |

**接口优先级**（越下兼容性越强，语义越弱、成本越高、风险越大）：

1. 稳定类型化 API
2. 后端 MCP / authenticated tool
3. 页面提供的 WebMCP
4. accessibility / semantic locator
5. DOM / JavaScript
6. screenshot + vision
7. OS computer use

**财务交易、高吞吐写入、关键生产变更**应优先用结构化 API；浏览器适合无 API 的兼容场景、最终视觉验证、人工接管。

### AI Browser 三条路线

1. **既有浏览器内置 AI** — Gemini in Chrome <sup><a href="#ref-104">[104]</a></sup>：side panel + 多标签上下文 + Connected Apps + auto browse
2. **新浏览器围绕 AI 重新设计** — Atlassian 收购 The Browser Company <sup><a href="#ref-107">[107]</a></sup> → **Dia** <sup><a href="#ref-106">[106]</a></sup>：跨 tabs/history/G Suite/Slack/Notion 上下文 + memory + Skills
3. **更大的 Agent 工作台吸收浏览器能力** — OpenAI **Atlas** <sup><a href="#ref-110">[110]</a></sup>：将在 2026-08-09 停止工作，浏览器能力进入 ChatGPT + Codex

**Dia 2026-06 现状** <sup><a href="#ref-109">[109]</a></sup>：Dia 正把 Teamwork Graph、Morning Brief、SSO、Chromium MDM、SOC 2 与 Guard 集成带入企业；产品重心从 Arc 式消费浏览器转向企业知识图谱与 Agent action 前台。

**梯度**：page assistant → cross-tab context → browser memory + connected apps → multi-step web action → enterprise identity/policy/audit

**护城河**：上下文隔离 + 敏感动作确认 + 组织策略 + 可审计 trace + 可靠人工接管。

### CDP vs WebMCP

- **CDP** <sup><a href="#ref-111">[111]</a></sup>：检查、调试、控制 Chromium 大量内部域 → browser agent 事实控制 ABI。但 tip-of-tree 经常变化，**Playwright 明确说 `connectOverCDP` 比自己协议"显著更低保真"**；WebDriver BiDi <sup><a href="#ref-112">[112]</a></sup> 截至研究日仍是 Working Draft
- **WebMCP** <sup><a href="#ref-113">[113]</a></sup> <sup><a href="#ref-114">[114]</a></sup> <sup><a href="#ref-115">[115]</a></sup>：后端 MCP 的客户端补充。页面把表单/JS 函数注册为带自然语言描述 + schema 的工具。**复用当前页面、登录态、可见 UI、人类上下文**；可在工具完成前等待人类确认

**WebMCP 安全性仍取决于宿主**：恶意或被攻破网站可注册误导性工具，宿主需校验 origin/参数/身份/权限/副作用，并要求高风险动作确认。

### WebGPU 把计算带进浏览器

**四类价值** <sup><a href="#ref-116">[116]</a></sup> <sup><a href="#ref-117">[117]</a></sup>：

- **本地推理与预处理**：embedding、小模型、语音识别、图像分类、视觉特征
- **隐私与离线**：敏感数据无需全部上传
- **交互延迟**：UI 内的局部模型、排序、过滤、视觉处理贴近用户
- **渲染与验证同域**：Agent 可生成、渲染、视觉检查 2D/3D、图表、视频、UI artifact

**vgpu 价值** <sup><a href="#ref-24">[24]</a></sup>：原生 API 显式而低级，WGSL/layout/buffer/pipeline 容易写错；Agent 生成代码尤其需要反射、稳定错误、无 GPU mock。未来 agent-friendly GPU 抽象应提供：capability discovery + 明确资源生命周期 + 静态/运行时 schema + 可序列化命令 + deterministic simulation + 可比较 trace + 真实 GPU 与 mock 的差异报告。

### 能力越大，风险越集中

**浏览器凭据近似物**：browser profile、CDP WebSocket URL、session token、下载目录都接近凭据。

**可靠设计至少需要**：

- 每任务或每信任域独立 profile/context
- 域名、导航、网络、下载 allowlist
- 页面内容按不可信数据处理，与系统指令分域
- 稳定的 observation epoch，动作必须绑定产生它的文档/frame
- dispatch 前审批，dispatch 后以证据 reconcile
- screenshot/network/console/trace/action receipt
- CAPTCHA/登录/支付/授权/高风险动作的人类接管
- 会话清理、凭据撤销、可审计重放

**纵深防御**：sandbox 限制爆炸半径、业务 verifier 判断动作是否正确、内容边界标记降低 prompt injection 成功率、身份/权限/人工接管承担剩余。

## 七、环境决定能力边界

长任务会反复执行相似动作。**即使单次环境差异只有 1%，在数十或数百次调用中也会从偶发故障变成经常遇到的问题。** 因此 scriptc/uv/Bun/rg/portless/Wasm/container/microVM 的共同价值，是减少两种"环境泄漏"：

- **ambient dependency**：隐式依赖、路径、版本、端口、系统包
- **ambient authority**：默认可访问的文件、网络、凭据、进程权限

**完整环境闭包**：固定工具链 + 依赖 + artifact digest，声明 filesystem snapshot + 可写范围、网络/资源策略、独立身份/隐私、时间/随机性，并为 stdout/stderr、产物、checkpoint、补偿、cleanup 提供统一契约。

### Electron vs Tauri 拓扑

| 维度 | **Electron** | **Tauri** |
|------|--------------|-----------|
| 浏览器 | 自带 Chromium，固定版本 | 动态链 OS WebView（WebView2/WKWebView/webkitgtk） |
| 体积 | 较大 | 显著小 |
| 进程模型 | main + renderer + utility process | Rust core + WebView |
| 能力闭包 | 统一 Chromium + Node 工具生态 + 原生桌面入口 | Capabilities 显式 \[window/webview/platform/remote origin\] |
| WebMCP/WebGPU | 内核版本固定可控 | **随平台变化** |

**稳健架构原则**：renderer 只投影状态 → main 管生命周期与准入 → utility/capability hosts 持有副作用 → sandbox/microVM/remote host 承载不可信代码。

**Tauri Capabilities 边界**：

- 主要约束 frontend→Rust core 的 IPC 暴露
- 同一 WebView 命中多份 capability 时权限合并
- 默认注册的自定义命令若不额外声明，可能对所有窗口开放
- 用户身份、逐动作审批、任务预算仍需上层策略
- **明确无法防御**：恶意 Rust core、过宽 scope、错误命令检查、WebView 0-day、供应链

**关键问题**：每加入一个 Node/Python/browser sidecar，就增加一套下载、签名、升级、进程监督、stdout 协议、崩溃恢复边界。**复杂 Agent 产品最终可能重新拥有接近 Electron 的运行时重量，只是被拆成不同 artifact。**

### Codex 验证桌面形态

OpenAI 官方将 Codex App 描述为管理并行 Agent/worktree/diff review/Skills/Automations/系统级 sandbox 的桌面 "command center"，后加 in-app browser/computer use/文件/终端等工作面 <sup><a href="#ref-127">[127]</a></sup>。**Codex Desktop 验证了"浏览器能力 + 本地执行 host + 多任务桌面工作台"的架构价值。**

## 八、上层求快，底层求稳

Agent Harness/入口/能力宿主分层，语言呈现清晰成本结构：

- **TypeScript / Python** → 高频反馈、生态连接、产品表达
- **Rust / Go / Zig / C++** → 热路径、CLI、runtime、隔离、分发

> "Rust/Zig 成为底层、TypeScript/React/Tailwind 成为上层标配" 描述的是**分工**，而非全栈语言替代。

### React 栈收敛

| 层 | 责任 | Agent 价值 |
|----|------|-----------|
| **React** | 异步 UI + 状态 + 事件 + 单向数据流 | 表达 streaming/tool call/approval/retry 组合 |
| **Vite 8** <sup><a href="#ref-131">[131]</a></sup> | dev server + HMR + transform + build + 插件图；Rust 编写的 Rolldown | **`server.forwardConsole` 让浏览器 console/error 回传 dev server 终端**，检测到 coding agent 时自动启用 |
| **Tailwind CSS v4** <sup><a href="#ref-132">[132]</a></sup> | first-party Vite plugin + CSS-first config + 原生 theme variables + data/state variants | design token/组件状态/源码修改在同一份可搜索文本 |
| **Base UI** <sup><a href="#ref-133">[133]</a></sup> | 无样式 + 可访问 + 可组合 primitives | focus/keyboard/ARIA/popup 边缘情况 |
| **shadcn/ui** <sup><a href="#ref-134">[134]</a></sup> <sup><a href="#ref-135">[135]</a></sup> | registry → 源码分发 | Agent 可直接读取/修改/diff/迁移；2026-07 Base UI 设为新项目默认 primitive |

**Agent Web 开发的高概率默认路径**：

```
Base UI / Radix / React Aria → 无样式行为与无障碍 primitive
shadcn registry → 组件抽象
Tailwind/token → 视觉层
项目源码 → 设计系统
```

共同取向：可组合、可局部替换；文档/schema 对机器可读；反馈循环短且错误能回终端；行为 primitive 与视觉 token 分离；源码/配置/验证证据都能进 Git。

### TypeScript 7 用原生内核提速

TS 7.0 <sup><a href="#ref-136">[136]</a></sup> (2026-07-08) 用 Go 原生重写 compiler + shared-memory multithreading → 完整构建通常 8-12 倍提升。**7.0 暂未提供原 compiler API，新程序化 API 计划在 7.1**；Vue/MDX/Astro/Svelte/Angular/自定义 transformer 可能仍需 TS 6。

### Python 守住生态，uv 重做工具链

uv <sup><a href="#ref-137">[137]</a></sup> 用 Rust 实现单一、预编译的 Python package/project manager，整合 pip/pip-tools/pipx/Poetry/pyenv/virtualenv。**Python 拥有模型/数据/Notebook/科研/PyTorch 生态，Rust/C++/CUDA 承担包管理/解析/tokenizer/数据路径/内核**。

### Bun 转 Rust：迁移快了，验证贵了

PR #30412 <sup><a href="#ref-138">[138]</a></sup> (2026-05-14) 合并 "Rewrite Bun in Rust"：~100 万新增行 / 2188 文件 / 6755 commits。**既有测试各平台通过，binary 缩小 3-8MB，性能持平或更快，架构基本不变，未采用 async Rust**。分支和提交历史显示大量 Claude 辅助工作。

**截至研究日 main/canary 已是 Rust-first Cargo workspace**，GitHub 标记最新稳定版 v1.3.14 仍是迁移前 Zig build；JavaScriptCore/C++ 与多个 C 库继续存在于新混合实现中。

**社区合并后立刻报告** safe Rust 下的潜在 UB/Miri 问题 <sup><a href="#ref-139">[139]</a></sup>：**测试通过只覆盖已有验收面，Rust 完整安全收益还依赖 invariant/sanitizer/Miri/fuzz/review/灰度发布**。**未来大型重写会更多，验证成本也会比生成速度更值钱。**

### 终端是系统语言压力测试场

**窄渲染与可编程宿主**：

- **Alacritty** <sup><a href="#ref-140">[140]</a></sup> — Rust + OpenGL 窄而可组合
- **kitty** <sup><a href="#ref-141">[141]</a></sup> <sup><a href="#ref-142">[142]</a></sup> — C/Python/Go 分层 + JSON remote-control 协议
- **WezTerm** <sup><a href="#ref-143">[143]</a></sup> <sup><a href="#ref-144">[144]</a></sup> — Rust core + Lua 配置 + multiplexer + SSH

**可嵌入内核**：

- **Ghostty** <sup><a href="#ref-145">[145]</a></sup> — Zig core + 原生 UI + C ABI
- **libghostty-vt** <sup><a href="#ref-146">[146]</a></sup> — 已暴露 VT parsing/screen/scrollback/input encoding/formatter 为 C/Zig API，覆盖 Wasm
- **turborepo-ghostty** <sup><a href="#ref-147">[147]</a></sup> — Turborepo 解析和渲染任务输出

**终端扩成工作台**：

- **Warp** <sup><a href="#ref-149">[149]</a></sup> <sup><a href="#ref-150">[150]</a></sup> — Rust + 自研 GPU UI + block model
- **Wave** <sup><a href="#ref-151">[151]</a></sup> — Go backend + Electron/React + durable SSH + wsh

**Rust 已形成完整 CLI 产品语法** **<sup><a href="#ref-152">[152]</a></sup> <sup><a href="#ref-153">[153]</a></sup> <sup><a href="#ref-154">[154]</a></sup> <sup><a href="#ref-155">[155]</a></sup> <sup><a href="#ref-156">[156]</a></sup> <sup><a href="#ref-157">[157]</a></sup> <sup><a href="#ref-158">[158]</a></sup> <sup><a href="#ref-159">[159]</a></sup> <sup><a href="#ref-160">[160]</a></sup> <sup><a href="#ref-161">[161]</a></sup>**：

- Shell 与提示：fish 4.0 (C++→Rust), Starship
- 只读观察：rg, bat, eza
- TUI 与工作区：Ratatui, Zellij, GitUI
- 记录与命令知识：asciinema, navi

**Wasm 只接收显式能力** ^[\[163-168\]^]：Rust 提供可预测性能 / 较小代码体积 / 低层控制；Wasm 增强 JS 处理密集任务；**组件只能使用宿主显式链接的 imports**——可移植的是计算与接口，文件/网络/PTY/凭据/OS 仍由宿主提供。

**Rust/Wasm/宿主三层责任**：

- Rust 约束实现
- Wasm 约束组件边界
- **宿主约束权力**

**Zig** 适合小型 runtime、GUI core、嵌入式边界（C ABI、显式内存、工具链控制）；**Rust** 适合长期基础设施（类型系统、并发、内存安全）；**Go** 擅长网络服务、并行工具、单一分发。**故障模型、团队能力、生态位置共同决定选择。**

## 九、Skills 将知识变成操作

**公司真正需要的标配，是面向机器的正式操作面。** 公开 SKILL.md 仓库是当前最轻的载体之一；拥有复杂 API/开发平台/内部流程的组织，还需把文档/schema/工具/身份/策略/评测组合。

**完整的机器操作面**：

- Skills → 何时/为何/按什么顺序做（程序性知识）
- 身份、权限、运行时能力 → 相邻层提供

**Vercel skills CLI** <sup><a href="#ref-39">[39]</a></sup> 已支持 Codex / Claude Code / Cursor 等大量 Agent 的 add/use/find/update/remove；Cloudflare 也维护自己的官方 Skills <sup><a href="#ref-54">[54]</a></sup>。**分发机制已形成，供应链治理仍处早期。**

### find-skills 本身也是 Skill <sup><a href="#ref-169">[169]</a></sup> — 递归能力获取

```
识别能力缺口 → 搜索 Skill → 评估来源 → 安装 → 获得新发现与执行能力
```

**Skill 安装会改变未来任务的操作规程，风险高于下载普通文档。** 发现/安装/激活/执行应是 4 个独立权限与审计阶段，全局安装需显式确认。

**Agent Skills specification** <sup><a href="#ref-170">[170]</a></sup> 强调渐进披露：先目录 metadata → 按需读 SKILL.md → 才加载引用和脚本。Cloudflare `.well-known/agent-skills` discovery RFC <sup><a href="#ref-171">[171]</a></sup> 要求 SHA-256 + 归档安全检查 + 默认不执行脚本。**Cloudflare Agents 对 Skills 的运行时支持仍明确为 experimental：格式已快速扩散，可信执行远未成熟。**

### Vercel skills lock 设计 <sup><a href="#ref-41">[41]</a></sup> <sup><a href="#ref-42">[42]</a></sup> <sup><a href="#ref-43">[43]</a></sup>

- 项目级 `skills-lock.json` 对目录内所有文件按路径排序后计算 SHA-256，**刻意不写 timestamp 以减少分支合并冲突**
- 全局 lock 记录 source URL、ref、GitHub tree hash、安装/更新时间
- 三个核心属性：**来源、解析、变更检测**
- 普通 branch ref 会漂移，folder hash 只能发现变化，签名发布/不可变版本/作者身份还需额外机制
- partner audit 超时或失败返回 `null`，只展示风险结果，advisory/fail-open
- Zip archive 限制 50MB/1000 files，拒绝绝对路径、`..`、symlink、hard link、加密 ZIP
- **脚本审查与执行权限仍由宿主负责**

### 模型吸收通用技巧，组织保留责任

Skills 价值分布会分化：

- **通用技巧**（常见 Git 操作、主流框架脚手架、固定格式转换）→ 逐渐进入模型权重、系统 prompt、内建工具、Harness 默认策略；从稀缺资产退化为兼容层
- **长期价值** → 需要持续更新、组织所有权、真实责任的部分

**护城河构成**：

```
procedure + current sources + tools + policy + verifier + recovery + provenance
```

- 通用 Skill 被模型/宿主吸收
- 供应商官方 Skill 成为随产品版本发布的机器文档
- 企业内部 Skill 成为可审计、可撤销的操作资产

**OpenClaw / Hermes** 已把 Skill 当成可变程序性记忆，并补上 proposal / hash / scanner / quarantine / write approval / rollback。**Agent 可从成功任务归纳流程，也可能持久化错误归纳、网页污染、偶然成功**；evidence/promotion/retirement 决定自改善最终沉淀为组织能力，还是长期污染。

**生产级 Skill 至少具备**：

- 固定版本、digest、来源
- owner、review date、兼容矩阵、弃用策略
- 所需能力、网络、文件、隐私声明
- 可执行脚本的审查与签名
- trigger 与行为 eval
- 失败恢复、negative path、回滚
- 变更日志与供应链扫描

### 开放权重 vs 闭源 <sup><a href="#ref-172">[172]</a></sup> <sup><a href="#ref-173">[173]</a></sup> <sup><a href="#ref-174">[174]</a></sup>

| 立场 | 主张 | 来源 |
|------|------|------|
| **Jensen Huang** | 开放权重 + 闭源并存前沿生态；闭源阻碍关键取证，开放权重帮助控制入侵 | NVIDIA |
| **Anthropic** | 类别式禁令无法约束真正恶意行为者；高能力权重发布后无法撤回；强制安全测试统一用于开放与闭源 | Anthropic 正式立场 |

**治理问题被推向**：能力分级、运行证据、训练数据来源、运行环境、允许工具、隐私边界、发布前评测、事件响应可隔离性、可更新性、可撤销性。

## 十、责任边界先标准化

Skills 封装规程，问题外溢到身份/环境/持久执行/动作证据/人工接管。**格式可先扩散，生产系统最终仍要对齐比 SKILL.md 更大的责任契约。**

**未来标准化重心** ^[\[raw/articles/lencx-agent-dev-guide-20260806.md\]^]：

- **Agent-readable surface**：结构化内容、API catalog、Skills discovery
- **可调用语义**：MCP、WebMCP、schema 化 action
- **独立身份**：每个 Agent/subagent 有可撤销、最小化授权
- **环境描述**：版本、文件快照、网络/资源策略可声明
- **持久执行**：事件、checkpoint、幂等、暂停、恢复
- **动作证据**：receipt、trace、截图、diff、结果 provenance
- **人类接管**：在正确时间把可见状态和控制权交还人类
- **注意力协议**：统一表达 running/needs-input/blocked/ready，让 CLI/GUI/Pet/通知投影同一份真相
- **效果评测**：不仅测答案，还测副作用、恢复、长期任务
- **模型与权重来源**：能力分级、版本与 digest、训练/评测声明、发布条件、事件响应边界

**边界原则**：协议/平台可提供公共边界，**无法替具体业务定义完成、风险、可接受失败**。最后一层落在人和组织身上。

## 十一、工程师转向结果责任

### 先定义完成，再设计恢复

面对 Agent 任务，先回答：

- **什么证据证明任务完成？**
- **哪些副作用不可重试？**
- **中断在每个阶段会怎样？**
- **失败后恢复还是补偿？**
- **人类什么时候必须介入？**

**能力越强，安全越要提前进入设计**：scoped identity / secret lifecycle / capability / sandbox / IPC / prompt injection / 审计 / 事故恢复会成为普通应用工程师日常工作。**先把不可重试的副作用、审批绑定、崩溃后的未知状态、补偿路径写清楚，再讨论更高自治。**

### 接口同时服务人和机器

可机器操作的接口需：

- JSON Schema + 稳定 ID + 版本化 contract
- 幂等键、dry-run、structured error
- 明确退出码、游标、artifact manifest
- 可恢复状态机、可比较 trace

**这些接口最终仍落在 git / rg / shell / test / profiler / browser DevTools / network / DB / log**。理解它们的默认行为、失败语义、安全边界，会比记住某个 Agent 框架的类名更长寿。

### 双栈学习，指标决策

- **TypeScript 或 Python** → 快速连接产品和生态
- **Rust / Go / Zig** → 理解 runtime、并发、内存、CLI、FFI、分发
- 选择一组深入即可，**不需要追逐每次重写新闻**

**重写前先量化**：

- 冷启动与常驻内存
- p95/p99 延迟与吞吐
- 安装/分发失败率
- crash / 泄漏 / 安全事故
- 跨平台成本
- 团队维护与招聘成本

> 如果收益只是"技术上更酷"，Agent 只会更快地产生一笔更大的维护债。

### 守住注意力与责任

CLI / 桌面 / 移动端 / Pet 应投影同一份运行时真相；**免打扰、隐私、通知节流、人工接管入口都是状态机的一部分**。人可以离开工作台，**责任链仍要保持连续**。

**长期价值**集中在：

- 把模糊问题变成正确约束
- 理解业务和用户真正承受的风险
- 设计可验证、可恢复的系统
- 愿意对生产结果负责

## 结语：能闭环，才可托付

> Agent 能否被托付，最后取决于系统能否回答四个具体问题：**它做了什么，依据是什么，结果是否成立，失败后如何恢复。**

生成变便宜，可靠性成本随之转移。**代码、分支、工具调用越多，身份混用、环境漂移、重复副作用、错误验收就越容易从偶发问题变成长任务的日常问题。**

模型能力会继续增长，平台也会把更多环节收进默认路径。**平台负责托管状态、沙箱、浏览器、身份、日志，业务负责定义可接受结果、必须阻断的风险、失败后的恢复或补偿。**

## 与本 Wiki 既有页面的关联

| 主题 | 文章论断 | 关联 Wiki 页 |
|------|---------|-------------|
| Harness Engineering 正式定义 | OpenAI 提出 <sup><a href="#ref-4">[4]</a></sup>；Anthropic Managed Agents 拆分 session/harness/sandbox <sup><a href="#ref-5">[5]</a></sup> | [[Harness Engineering]] |
| 删减型 Harness | 删 80% system prompt、无可测量损失 | [[Harness Engineering]] |
| 记忆五类分类 | 工作状态/事件历史/领域知识/情节偏好/身份隐私 | [[Memory-Systems]] |
| Subagent 编排 | Agent as tool vs Handoff；Dynamic Workflows 100+ subagent | [[Harness-Patterns/Dispatcher State Machine]] |
| OpenClaw Foundation 治理 | 作者入 OpenAI → 基金会资助 + Claw Labs 合作 | [[OpenClaw]] / [[OpenClaw Managed Agents]] |
| Hermes 自进化 | `/learn` + background review + skill/memory 自改善 | [[Hermes Agent]] |
| Electron / Tauri 拓扑 | renderer/main/utility hosts + Capability 边界 | （无既有页） |
| 浏览器 = 能力平面 | CDP + WebMCP + WebGPU + 身份容器 | [[WebPlaywright]]（空） |
| Pet 注意力机制 | peripheral awareness + interrupt routing + emotional salence | （无既有页） |
| Skills 治理 | Vercel skills CLI + lock + find-skills 递归 | [[SDLC-Agent-Skills]] |
| 语言分工 | TS/Python 上层 + Rust/Zig 底层 | （无既有页） |
| 责任边界 10 条契约 | 跨框架契约清单 | [[Harness-Engineering]] / [[Harness-镜子-显形运动]] |

## 引用资源

> 本文蒸馏自 lencx《Agent 开发指南》(2026-07-28)，原文含 **174 条**外部引用，summary 实际触及 **90 条**。下方完整列表覆盖全部 174 条，按编号排列，未被引用者也保留以便查阅。

### 关键原文（按热度 Top 12）

| 引用次数 | 编号 | 主题 |
|---|---|---|
| ×2 | [54](#ref-54) | Cloudflare Skills |
| ×1 | [4](#ref-4) | OpenAI 所说的 Harness Engineering |
| ×1 | [5](#ref-5) | Managed Agents 架构 |
| ×1 | [6](#ref-6) | Anthropic 的 Claude 5 context engineering 复盘 |
| ×1 | [7](#ref-7) | Codex Goal |
| ×1 | [9](#ref-9) | OpenAI Agents SDK 的编排模型 |
| ×1 | [10](#ref-10) | Claude Code Dynamic Workflows |
| ×1 | [11](#ref-11) | 后续迁移复盘 |
| ×1 | [13](#ref-13) | AI SDK 7 |
| ×1 | [14](#ref-14) | Chat SDK |
| ×1 | [15](#ref-15) | Streamdown |
| ×1 | [16](#ref-16) | json-render |

### 完整 References（174 条）

<details>
<summary>点击展开全部 174 条参考文献（含未被引用条目）</summary>

- **[1]** <a id="ref-1"></a>awesome-agents — [https://github.com/kyrolabs/awesome-agents](https://github.com/kyrolabs/awesome-agents)
- **[2]** <a id="ref-2"></a>awesome-ai-agents — [https://github.com/e2b-dev/awesome-ai-agents](https://github.com/e2b-dev/awesome-ai-agents)
- **[3]** <a id="ref-3"></a>Anthropic 对 Context Engineering 的定义 — [https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- **[4]** <a id="ref-4"></a>OpenAI 所说的 Harness Engineering ✓ — [https://openai.com/index/harness-engineering/](https://openai.com/index/harness-engineering/)
- **[5]** <a id="ref-5"></a>Managed Agents 架构 ✓ — [https://www.anthropic.com/engineering/managed-agents](https://www.anthropic.com/engineering/managed-agents)
- **[6]** <a id="ref-6"></a>Anthropic 的 Claude 5 context engineering 复盘 ✓ — [https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models](https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models)
- **[7]** <a id="ref-7"></a>Codex Goal ✓ — [https://learn.chatgpt.com/docs/long-running-work](https://learn.chatgpt.com/docs/long-running-work)
- **[8]** <a id="ref-8"></a>Codex Subagents 文档 — [https://learn.chatgpt.com/docs/agent-configuration/subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)
- **[9]** <a id="ref-9"></a>OpenAI Agents SDK 的编排模型 ✓ — [https://openai.github.io/openai-agents-js/guides/multi-agent/](https://openai.github.io/openai-agents-js/guides/multi-agent/)
- **[10]** <a id="ref-10"></a>Claude Code Dynamic Workflows ✓ — [https://claude.com/blog/introducing-dynamic-workflows-in-claude-code](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code)
- **[11]** <a id="ref-11"></a>后续迁移复盘 ✓ — [https://claude.com/blog/ai-code-migration](https://claude.com/blog/ai-code-migration)
- **[12]** <a id="ref-12"></a>AI SDK — [https://github.com/vercel/ai/tree/c1100c45af58ebac9935d2b81354a651100b6e12](https://github.com/vercel/ai/tree/c1100c45af58ebac9935d2b81354a651100b6e12)
- **[13]** <a id="ref-13"></a>AI SDK 7 ✓ — [https://vercel.com/blog/ai-sdk-7](https://vercel.com/blog/ai-sdk-7)
- **[14]** <a id="ref-14"></a>Chat SDK ✓ — [https://github.com/vercel/chat/tree/257a32d01c41d51f3ecabb3d25944482c37ad6bc](https://github.com/vercel/chat/tree/257a32d01c41d51f3ecabb3d25944482c37ad6bc)
- **[15]** <a id="ref-15"></a>Streamdown ✓ — [https://github.com/vercel/streamdown/tree/e5deed330aa4231751a106445d93d62e4716a22f](https://github.com/vercel/streamdown/tree/e5deed330aa4231751a106445d93d62e4716a22f)
- **[16]** <a id="ref-16"></a>json-render ✓ — [https://github.com/vercel-labs/json-render/tree/9d3dfc8917c1c6aa5568acbe0969523f3307376c](https://github.com/vercel-labs/json-render/tree/9d3dfc8917c1c6aa5568acbe0969523f3307376c)
- **[17]** <a id="ref-17"></a>Vercel Sandbox ✓ — [https://github.com/vercel/sandbox/tree/80d9421b4ef3b933b0811d1fa8f54ea67c48db5d](https://github.com/vercel/sandbox/tree/80d9421b4ef3b933b0811d1fa8f54ea67c48db5d)
- **[18]** <a id="ref-18"></a>agent-browser ✓ — [https://github.com/vercel-labs/agent-browser/tree/3cc7022271235694b5b5ce8aaea8bbfaa66e8cd5](https://github.com/vercel-labs/agent-browser/tree/3cc7022271235694b5b5ce8aaea8bbfaa66e8cd5)
- **[19]** <a id="ref-19"></a>portless ✓ — [https://github.com/vercel-labs/portless/tree/15ef06434c81523b1b24db2d52a17caf31edecf1](https://github.com/vercel-labs/portless/tree/15ef06434c81523b1b24db2d52a17caf31edecf1)
- **[20]** <a id="ref-20"></a>ai-cli ✓ — [https://github.com/vercel-labs/ai-cli/tree/77996062324a3f971192d99681b6e5fa047a2119](https://github.com/vercel-labs/ai-cli/tree/77996062324a3f971192d99681b6e5fa047a2119)
- **[21]** <a id="ref-21"></a>scriptc:20c3a6c ✓ — [https://github.com/vercel-labs/scriptc/blob/20c3a6c27da4807f607ebe496663842b67e87f0e/README.md](https://github.com/vercel-labs/scriptc/blob/20c3a6c27da4807f607ebe496663842b67e87f0e/README.md)
- **[22]** <a id="ref-22"></a>QuickJS-ng — [https://github.com/quickjs-ng/quickjs](https://github.com/quickjs-ng/quickjs)
- **[23]** <a id="ref-23"></a>Native SDK ✓ — [https://github.com/vercel-labs/native/tree/a7509a7fa6c467eaed021250538b482886f1c6bf](https://github.com/vercel-labs/native/tree/a7509a7fa6c467eaed021250538b482886f1c6bf)
- **[24]** <a id="ref-24"></a>vgpu ✓ — [https://github.com/vercel-labs/vgpu/tree/23b83d27c1a5d5aec6d04a6cf33f0de043d6ae3f](https://github.com/vercel-labs/vgpu/tree/23b83d27c1a5d5aec6d04a6cf33f0de043d6ae3f)
- **[25]** <a id="ref-25"></a>Turborepo ✓ — [https://github.com/vercel/turborepo/tree/ac6c28ff6fe82aaad2ae7f1cae32a6c4d4f094f3](https://github.com/vercel/turborepo/tree/ac6c28ff6fe82aaad2ae7f1cae32a6c4d4f094f3)
- **[26]** <a id="ref-26"></a>Vercel 在 2021 年正式收购 Turborepo ✓ — [https://vercel.com/blog/vercel-acquires-turborepo](https://vercel.com/blog/vercel-acquires-turborepo)
- **[27]** <a id="ref-27"></a>Turborepo 的 AI 指南 ✓ — [https://turborepo.dev/docs/guides/ai](https://turborepo.dev/docs/guides/ai)
- **[28]** <a id="ref-28"></a>turborepo-ai-agents ✓ — [https://github.com/vercel/turborepo/blob/ac6c28ff6fe82aaad2ae7f1cae32a6c4d4f094f3/crates/turborepo-ai-agents/src/lib.rs](https://github.com/vercel/turborepo/blob/ac6c28ff6fe82aaad2ae7f1cae32a6c4d4f094f3/crates/turborepo-ai-agents/src/lib.rs)
- **[29]** <a id="ref-29"></a>Go→Rust 迁移 ✓ — [https://vercel.com/blog/turborepo-migration-go-rust](https://vercel.com/blog/turborepo-migration-go-rust)
- **[30]** <a id="ref-30"></a>原文 ✓ — [https://vercel.com/blog/making-turborepo-ninety-six-percent-faster-with-agents-sandboxes-and-humans](https://vercel.com/blog/making-turborepo-ninety-six-percent-faster-with-agents-sandboxes-and-humans)
- **[31]** <a id="ref-31"></a>SWC ✓ — [https://github.com/swc-project/swc/tree/b83078644a7f0f1bbb56d6b45754ca9ed1bafc4b](https://github.com/swc-project/swc/tree/b83078644a7f0f1bbb56d6b45754ca9ed1bafc4b)
- **[32]** <a id="ref-32"></a>Next.js 11.1 公告 ✓ — [https://nextjs.org/blog/next-11-1](https://nextjs.org/blog/next-11-1)
- **[33]** <a id="ref-33"></a>Next.js Compiler ✓ — [https://nextjs.org/docs/architecture/nextjs-compiler](https://nextjs.org/docs/architecture/nextjs-compiler)
- **[34]** <a id="ref-34"></a>Grep — [https://vercel.com/blog/vercel-acquires-grep](https://vercel.com/blog/vercel-acquires-grep)
- **[35]** <a id="ref-35"></a>Tremor — [https://vercel.com/blog/vercel-acquires-tremor](https://vercel.com/blog/vercel-acquires-tremor)
- **[36]** <a id="ref-36"></a>v0 — [https://v0.app](https://v0.app)
- **[37]** <a id="ref-37"></a>NuxtLabs 团队加入 Vercel — [https://vercel.com/blog/nuxtlabs-joins-vercel](https://vercel.com/blog/nuxtlabs-joins-vercel)
- **[38]** <a id="ref-38"></a>Vercel 宣布收购 Better Auth — [https://vercel.com/blog/vercel-acquires-better-auth](https://vercel.com/blog/vercel-acquires-better-auth)
- **[39]** <a id="ref-39"></a>vercel-labs/skills ✓ — [https://github.com/vercel-labs/skills/tree/e173b8c88f2581cfdaa1b6767c6519a08155790e](https://github.com/vercel-labs/skills/tree/e173b8c88f2581cfdaa1b6767c6519a08155790e)
- **[40]** <a id="ref-40"></a>skills.sh — [https://skills.sh/](https://skills.sh/)
- **[41]** <a id="ref-41"></a>skills-lock.json ✓ — [https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/src/local-lock.ts](https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/src/local-lock.ts)
- **[42]** <a id="ref-42"></a>well-known provider ✓ — [https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/src/providers/wellknown.ts](https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/src/providers/wellknown.ts)
- **[43]** <a id="ref-43"></a>partner audit ✓ — [https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/src/telemetry.ts](https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/src/telemetry.ts)
- **[44]** <a id="ref-44"></a>Skills Night 原文 — [https://vercel.com/blog/skills-night-69000-ways-agents-are-getting-smarter](https://vercel.com/blog/skills-night-69000-ways-agents-are-getting-smarter)
- **[45]** <a id="ref-45"></a>Cloudflare Agents ✓ — [https://github.com/cloudflare/agents/tree/f089c5b6a13f98ad728f9c9cb9d729469b945233](https://github.com/cloudflare/agents/tree/f089c5b6a13f98ad728f9c9cb9d729469b945233)
- **[46]** <a id="ref-46"></a>Agents + Sandbox — [https://developers.cloudflare.com/agents/tools/sandbox/](https://developers.cloudflare.com/agents/tools/sandbox/)
- **[47]** <a id="ref-47"></a>Agents with Workflows — [https://developers.cloudflare.com/agents/concepts/workflows/](https://developers.cloudflare.com/agents/concepts/workflows/)
- **[48]** <a id="ref-48"></a>Browser Run ✓ — [https://blog.cloudflare.com/browser-run-for-ai-agents/](https://blog.cloudflare.com/browser-run-for-ai-agents/)
- **[49]** <a id="ref-49"></a>把底层重建到 Containers ✓ — [https://blog.cloudflare.com/browser-run-containers/](https://blog.cloudflare.com/browser-run-containers/)
- **[50]** <a id="ref-50"></a>Cloudflare Puppeteer — [https://github.com/cloudflare/puppeteer/tree/08707e0a188f8e1ac9c038bc118be6c56c7e2973](https://github.com/cloudflare/puppeteer/tree/08707e0a188f8e1ac9c038bc118be6c56c7e2973)
- **[51]** <a id="ref-51"></a>Playwright fork — [https://github.com/cloudflare/playwright/tree/693f8ac6d9d5ac5a3496184807f8f14708ddde8b](https://github.com/cloudflare/playwright/tree/693f8ac6d9d5ac5a3496184807f8f14708ddde8b)
- **[52]** <a id="ref-52"></a>Cloudflare 已弃用 Sandbox SDK 中的 desktop feature ✓ — [https://developers.cloudflare.com/sandbox/guides/2026-deprecation/](https://developers.cloudflare.com/sandbox/guides/2026-deprecation/)
- **[53]** <a id="ref-53"></a>Nimbus ✓ — [https://github.com/cloudflare/nimbus/tree/d14cddd7db48277e3b36b11f06a888a9864454ad](https://github.com/cloudflare/nimbus/tree/d14cddd7db48277e3b36b11f06a888a9864454ad)
- **[54]** <a id="ref-54"></a>Cloudflare Skills ✓ — [https://github.com/cloudflare/skills/tree/30553f89ae1ef1e3c2917cd09d72dac992bb4e9a](https://github.com/cloudflare/skills/tree/30553f89ae1ef1e3c2917cd09d72dac992bb4e9a)
- **[55]** <a id="ref-55"></a>Agent Readiness ✓ — [https://blog.cloudflare.com/agent-readiness/](https://blog.cloudflare.com/agent-readiness/)
- **[56]** <a id="ref-56"></a>Code Mode ✓ — [https://developers.cloudflare.com/agents/model-context-protocol/codemode/](https://developers.cloudflare.com/agents/model-context-protocol/codemode/)
- **[57]** <a id="ref-57"></a>workers-rs — [https://github.com/cloudflare/workers-rs/commit/5f2d6c9192377451d43910098738624474196364](https://github.com/cloudflare/workers-rs/commit/5f2d6c9192377451d43910098738624474196364)
- **[58]** <a id="ref-58"></a>PartyKit — [https://blog.cloudflare.com/cloudflare-acquires-partykit/](https://blog.cloudflare.com/cloudflare-acquires-partykit/)
- **[59]** <a id="ref-59"></a>Baselime — [https://blog.cloudflare.com/cloudflare-acquires-baselime-expands-observability-capabilities/](https://blog.cloudflare.com/cloudflare-acquires-baselime-expands-observability-capabilities/)
- **[60]** <a id="ref-60"></a>Outerbase — [https://blog.cloudflare.com/cloudflare-acquires-outerbase-database-dx/](https://blog.cloudflare.com/cloudflare-acquires-outerbase-database-dx/)
- **[61]** <a id="ref-61"></a>Replicate — [https://blog.cloudflare.com/why-replicate-joining-cloudflare/](https://blog.cloudflare.com/why-replicate-joining-cloudflare/)
- **[62]** <a id="ref-62"></a>Human Native — [https://blog.cloudflare.com/human-native-joins-cloudflare/](https://blog.cloudflare.com/human-native-joins-cloudflare/)
- **[63]** <a id="ref-63"></a>Astro — [https://blog.cloudflare.com/astro-joins-cloudflare/](https://blog.cloudflare.com/astro-joins-cloudflare/)
- **[64]** <a id="ref-64"></a>VoidZero — [https://www.cloudflare.com/press/press-releases/2026/cloudflare-acquires-voidzero-to-build-the-future-of-the-ai-native-web/](https://www.cloudflare.com/press/press-releases/2026/cloudflare-acquires-voidzero-to-build-the-future-of-the-ai-native-web/)
- **[65]** <a id="ref-65"></a>Anthropic 在 2025 年 12 月收购 Bun — [https://www.anthropic.com/news/anthropic-acquires-bun-as-claude-code-reaches-usd1b-milestone](https://www.anthropic.com/news/anthropic-acquires-bun-as-claude-code-reaches-usd1b-milestone)
- **[66]** <a id="ref-66"></a>Vercept 收购 — [https://www.anthropic.com/news/acquires-vercept](https://www.anthropic.com/news/acquires-vercept)
- **[67]** <a id="ref-67"></a>宣布收购 Stainless — [https://www.anthropic.com/news/anthropic-acquires-stainless](https://www.anthropic.com/news/anthropic-acquires-stainless)
- **[68]** <a id="ref-68"></a>Promptfoo 已加入 OpenAI — [https://www.promptfoo.dev/press/](https://www.promptfoo.dev/press/)
- **[69]** <a id="ref-69"></a>宣布拟收购 Astral — [https://openai.com/index/openai-to-acquire-astral/](https://openai.com/index/openai-to-acquire-astral/)
- **[70]** <a id="ref-70"></a>拟收购 Ona — [https://openai.com/index/openai-to-acquire-ona/](https://openai.com/index/openai-to-acquire-ona/)
- **[71]** <a id="ref-71"></a>已经完成的 Sky 收购 — [https://openai.com/index/openai-acquires-software-applications-incorporated/](https://openai.com/index/openai-acquires-software-applications-incorporated/)
- **[72]** <a id="ref-72"></a>OpenClaw Foundation ✓ — [https://openclaw.ai/blog/introducing-openclaw-foundation](https://openclaw.ai/blog/introducing-openclaw-foundation)
- **[73]** <a id="ref-73"></a>进入 Linux Foundation 旗下 AAIF ✓ — [https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- **[74]** <a id="ref-74"></a>Claude Cowork ✓ — [https://claude.com/product/cowork](https://claude.com/product/cowork)
- **[75]** <a id="ref-75"></a>Claude Design ✓ — [https://www.anthropic.com/news/claude-design-anthropic-labs](https://www.anthropic.com/news/claude-design-anthropic-labs)
- **[76]** <a id="ref-76"></a>Claude Design 与 Claude Code 的双向同步 — [https://claude.com/blog/claude-design-stays-on-brand-for-daily-work](https://claude.com/blog/claude-design-stays-on-brand-for-daily-work)
- **[77]** <a id="ref-77"></a>Cowork Web/Mobile 公告 ✓ — [https://claude.com/blog/cowork-web-mobile](https://claude.com/blog/cowork-web-mobile)
- **[78]** <a id="ref-78"></a>OpenAI 当前说明 — [https://help.openai.com/en/articles/20001275-chatgpt-work-and-codex](https://help.openai.com/en/articles/20001275-chatgpt-work-and-codex)
- **[79]** <a id="ref-79"></a>内置浏览器 ✓ — [https://help.openai.com/en/articles/20001277-using-the-built-in-browser-in-the-chatgpt-desktop-app](https://help.openai.com/en/articles/20001277-using-the-built-in-browser-in-the-chatgpt-desktop-app)
- **[80]** <a id="ref-80"></a>Claude Code — [https://www.anthropic.com/news/claude-3-7-sonnet](https://www.anthropic.com/news/claude-3-7-sonnet)
- **[81]** <a id="ref-81"></a>Codex CLI — [https://openai.com/index/introducing-upgrades-to-codex/](https://openai.com/index/introducing-upgrades-to-codex/)
- **[82]** <a id="ref-82"></a>Gemini CLI — [https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemini-cli-open-source-ai-agent/](https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemini-cli-open-source-ai-agent/)
- **[83]** <a id="ref-83"></a>Claude Code 的 CLI 参考 — [https://docs.anthropic.com/en/docs/claude-code/cli-usage](https://docs.anthropic.com/en/docs/claude-code/cli-usage)
- **[84]** <a id="ref-84"></a>Codex — [https://github.com/openai/codex](https://github.com/openai/codex)
- **[85]** <a id="ref-85"></a>Gemini CLI — [https://github.com/google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli)
- **[86]** <a id="ref-86"></a>官方公告 — [https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/)
- **[87]** <a id="ref-87"></a>会话还可以从终端导出到 GUI 继续 ✓ — [https://github.com/google-antigravity/antigravity-cli/blob/c6911187d1db55e4ae1d5fa4b6f40f7af5af7aee/README.md](https://github.com/google-antigravity/antigravity-cli/blob/c6911187d1db55e4ae1d5fa4b6f40f7af5af7aee/README.md)
- **[88]** <a id="ref-88"></a>Grok Build — [https://x.ai/news/grok-build-cli](https://x.ai/news/grok-build-cli)
- **[89]** <a id="ref-89"></a>交互式 TUI、headless 脚本模式与 ACP 嵌入 — [https://docs.x.ai/build/overview](https://docs.x.ai/build/overview)
- **[90]** <a id="ref-90"></a>Bun — [https://bun.sh/docs](https://bun.sh/docs)
- **[91]** <a id="ref-91"></a>Codex 的打包脚本 — [https://github.com/openai/codex/blob/fb6aad9ae34116128e537696c24e35fe6548e1c2/scripts/codex_package/README.md](https://github.com/openai/codex/blob/fb6aad9ae34116128e537696c24e35fe6548e1c2/scripts/codex_package/README.md)
- **[92]** <a id="ref-92"></a>ripgrep FAQ ✓ — [https://github.com/BurntSushi/ripgrep/blob/f9c05a949d1a0dc8e16dee28ca9605d38611faeb/FAQ.md](https://github.com/BurntSushi/ripgrep/blob/f9c05a949d1a0dc8e16dee28ca9605d38611faeb/FAQ.md)
- **[93]** <a id="ref-93"></a>earendil-works/pi — [https://github.com/earendil-works/pi/tree/c820aa26fe0907e053e881a957722693fc094c9c](https://github.com/earendil-works/pi/tree/c820aa26fe0907e053e881a957722693fc094c9c)
- **[94]** <a id="ref-94"></a>packages/server — [https://github.com/earendil-works/pi/tree/c820aa26fe0907e053e881a957722693fc094c9c/packages/server](https://github.com/earendil-works/pi/tree/c820aa26fe0907e053e881a957722693fc094c9c/packages/server)
- **[95]** <a id="ref-95"></a>pi-chat — [https://github.com/earendil-works/pi-chat/tree/9adbd29b40ee27ff1decf0fc87cbe180b40924f5](https://github.com/earendil-works/pi-chat/tree/9adbd29b40ee27ff1decf0fc87cbe180b40924f5)
- **[96]** <a id="ref-96"></a>OpenClaw — [https://github.com/openclaw/openclaw/tree/924004d35c23413ddc2861f1e0a10f069046e61a](https://github.com/openclaw/openclaw/tree/924004d35c23413ddc2861f1e0a10f069046e61a)
- **[97]** <a id="ref-97"></a>Hermes Agent — [https://github.com/NousResearch/hermes-agent/tree/d83e858507a9bdb7f96c7a163d89c34c60909dcf](https://github.com/NousResearch/hermes-agent/tree/d83e858507a9bdb7f96c7a163d89c34c60909dcf)
- **[98]** <a id="ref-98"></a>审批绑定实现 ✓ — [https://github.com/openclaw/openclaw/blob/924004d35c23413ddc2861f1e0a10f069046e61a/src/gateway/node-invoke-system-run-approval.ts](https://github.com/openclaw/openclaw/blob/924004d35c23413ddc2861f1e0a10f069046e61a/src/gateway/node-invoke-system-run-approval.ts)
- **[99]** <a id="ref-99"></a>Agent loop — [https://github.com/NousResearch/hermes-agent/blob/d83e858507a9bdb7f96c7a163d89c34c60909dcf/website/docs/developer-guide/agent-loop.md](https://github.com/NousResearch/hermes-agent/blob/d83e858507a9bdb7f96c7a163d89c34c60909dcf/website/docs/developer-guide/agent-loop.md)
- **[100]** <a id="ref-100"></a>重做 Claude Code Desktop ✓ — [https://claude.com/blog/claude-code-desktop-redesign](https://claude.com/blog/claude-code-desktop-redesign)
- **[101]** <a id="ref-101"></a>/app ✓ — [https://learn.chatgpt.com/docs/developer-commands](https://learn.chatgpt.com/docs/developer-commands)
- **[102]** <a id="ref-102"></a>OpenAI 的 Pets 文档 ✓ — [https://learn.chatgpt.com/docs/pets](https://learn.chatgpt.com/docs/pets)
- **[103]** <a id="ref-103"></a>通知文档 — [https://learn.chatgpt.com/docs/notifications](https://learn.chatgpt.com/docs/notifications)
- **[104]** <a id="ref-104"></a>Gemini in Chrome ✓ — [https://blog.google/products-and-platforms/products/chrome/gemini-3-auto-browse/](https://blog.google/products-and-platforms/products/chrome/gemini-3-auto-browse/)
- **[105]** <a id="ref-105"></a>AI Mode 进入 omnibox — [https://blog.google/products-and-platforms/products/chrome/chrome-reimagined-with-ai/](https://blog.google/products-and-platforms/products/chrome/chrome-reimagined-with-ai/)
- **[106]** <a id="ref-106"></a>Dia ✓ — [https://www.diabrowser.com/](https://www.diabrowser.com/)
- **[107]** <a id="ref-107"></a>宣布收购 The Browser Company ✓ — [https://www.atlassian.com/blog/announcements/atlassian-acquires-the-browser-company](https://www.atlassian.com/blog/announcements/atlassian-acquires-the-browser-company)
- **[108]** <a id="ref-108"></a>Dia 已使用 Atlassian identity — [https://www.diabrowser.com/security](https://www.diabrowser.com/security)
- **[109]** <a id="ref-109"></a>Atlassian 2026 年进一步披露 ✓ — [https://www.atlassian.com/blog/company-news/founder-update-team-26](https://www.atlassian.com/blog/company-news/founder-update-team-26)
- **[110]** <a id="ref-110"></a>迁移说明 ✓ — [https://help.openai.com/en/articles/20001371-evolving-atlas-into-chatgpt-for-browser-based-agentic-work](https://help.openai.com/en/articles/20001371-evolving-atlas-into-chatgpt-for-browser-based-agentic-work)
- **[111]** <a id="ref-111"></a>Chrome DevTools Protocol ✓ — [https://chromedevtools.github.io/devtools-protocol/](https://chromedevtools.github.io/devtools-protocol/)
- **[112]** <a id="ref-112"></a>WebDriver BiDi ✓ — [https://www.w3.org/TR/webdriver-bidi/](https://www.w3.org/TR/webdriver-bidi/)
- **[113]** <a id="ref-113"></a>Chrome WebMCP early preview ✓ — [https://developer.chrome.com/blog/webmcp-epp](https://developer.chrome.com/blog/webmcp-epp)
- **[114]** <a id="ref-114"></a>W3C Web Machine Learning Community Group 草案 ✓ — [https://github.com/webmachinelearning/webmcp/tree/58016782fa379c25bc9584433f29127a9855647b](https://github.com/webmachinelearning/webmcp/tree/58016782fa379c25bc9584433f29127a9855647b)
- **[115]** <a id="ref-115"></a>Cloudflare WebMCP ✓ — [https://developers.cloudflare.com/browser-run/features/webmcp/](https://developers.cloudflare.com/browser-run/features/webmcp/)
- **[116]** <a id="ref-116"></a>WebGPU ✓ — [https://www.w3.org/TR/webgpu/](https://www.w3.org/TR/webgpu/)
- **[117]** <a id="ref-117"></a>Transformers.js 的 WebGPU 指南 ✓ — [https://huggingface.co/docs/transformers.js/en/guides/webgpu](https://huggingface.co/docs/transformers.js/en/guides/webgpu)
- **[118]** <a id="ref-118"></a>Electron — [https://www.electronjs.org/docs/latest/tutorial/tutorial-prerequisites](https://www.electronjs.org/docs/latest/tutorial/tutorial-prerequisites)
- **[119]** <a id="ref-119"></a>官方进程模型 — [https://www.electronjs.org/docs/latest/tutorial/process-model](https://www.electronjs.org/docs/latest/tutorial/process-model)
- **[120]** <a id="ref-120"></a>WebContentsView — [https://www.electronjs.org/docs/latest/api/web-contents-view](https://www.electronjs.org/docs/latest/api/web-contents-view)
- **[121]** <a id="ref-121"></a>contextBridge 指南 — [https://www.electronjs.org/docs/latest/tutorial/context-isolation](https://www.electronjs.org/docs/latest/tutorial/context-isolation)
- **[122]** <a id="ref-122"></a>Tauri — [https://github.com/tauri-apps/tauri/tree/7164de39574d616b762ba658f797f9657ea03b20](https://github.com/tauri-apps/tauri/tree/7164de39574d616b762ba658f797f9657ea03b20)
- **[123]** <a id="ref-123"></a>官方进程模型 — [https://v2.tauri.app/concept/process-model/](https://v2.tauri.app/concept/process-model/)
- **[124]** <a id="ref-124"></a>Capabilities — [https://v2.tauri.app/security/capabilities/](https://v2.tauri.app/security/capabilities/)
- **[125]** <a id="ref-125"></a>外部 binary sidecar — [https://v2.tauri.app/develop/sidecar/](https://v2.tauri.app/develop/sidecar/)
- **[126]** <a id="ref-126"></a>把 Node.js 应用编译为 sidecar — [https://v2.tauri.app/learn/sidecar-nodejs/](https://v2.tauri.app/learn/sidecar-nodejs/)
- **[127]** <a id="ref-127"></a>官方产品介绍 ✓ — [https://openai.com/index/introducing-the-codex-app/](https://openai.com/index/introducing-the-codex-app/)
- **[128]** <a id="ref-128"></a>SSI 宣布与 NVIDIA 建立长期战略合作 — [https://x.com/ssi/status/2081732119194394763](https://x.com/ssi/status/2081732119194394763)
- **[129]** <a id="ref-129"></a>NVIDIA AI Infrastructure — [https://x.com/NVIDIAAIInfra/status/2081736062154740157](https://x.com/NVIDIAAIInfra/status/2081736062154740157)
- **[130]** <a id="ref-130"></a>Vite 等 build tool — [https://react.dev/blog/2025/02/14/sunsetting-create-react-app](https://react.dev/blog/2025/02/14/sunsetting-create-react-app)
- **[131]** <a id="ref-131"></a>Vite 8 ✓ — [https://vite.dev/blog/announcing-vite8](https://vite.dev/blog/announcing-vite8)
- **[132]** <a id="ref-132"></a>Tailwind CSS v4 ✓ — [https://tailwindcss.com/blog/tailwindcss-v4](https://tailwindcss.com/blog/tailwindcss-v4)
- **[133]** <a id="ref-133"></a>Base UI ✓ — [https://base-ui.com/react/overview/about](https://base-ui.com/react/overview/about)
- **[134]** <a id="ref-134"></a>shadcn 已把 Base UI 设为新项目默认 primitive ✓ — [https://ui.shadcn.com/docs/changelog/2026-07-base-ui-default](https://ui.shadcn.com/docs/changelog/2026-07-base-ui-default)
- **[135]** <a id="ref-135"></a>React Aria 纳入一等选项 ✓ — [https://ui.shadcn.com/docs/changelog/2026-07-react-aria](https://ui.shadcn.com/docs/changelog/2026-07-react-aria)
- **[136]** <a id="ref-136"></a>TypeScript 7.0 ✓ — [https://devblogs.microsoft.com/typescript/announcing-typescript-7-0/](https://devblogs.microsoft.com/typescript/announcing-typescript-7-0/)
- **[137]** <a id="ref-137"></a>uv ✓ — [https://github.com/astral-sh/uv/tree/9ae67546b933ec34ae4893f9b4df5f8b43aefca0](https://github.com/astral-sh/uv/tree/9ae67546b933ec34ae4893f9b4df5f8b43aefca0)
- **[138]** <a id="ref-138"></a>Bun PR:#30412 ✓ — [https://github.com/oven-sh/bun/pull/30412](https://github.com/oven-sh/bun/pull/30412)
- **[139]** <a id="ref-139"></a>safe Rust 下的潜在 UB/Miri 问题 ✓ — [https://github.com/oven-sh/bun/issues/30719](https://github.com/oven-sh/bun/issues/30719)
- **[140]** <a id="ref-140"></a>Alacritty ✓ — [https://github.com/alacritty/alacritty/tree/852e971cddfabe222d2d5bcda466e130f53af207](https://github.com/alacritty/alacritty/tree/852e971cddfabe222d2d5bcda466e130f53af207)
- **[141]** <a id="ref-141"></a>kitty ✓ — [https://github.com/kovidgoyal/kitty/tree/7ed3477d5caad50c06ea5e78777fe82241de3d5f](https://github.com/kovidgoyal/kitty/tree/7ed3477d5caad50c06ea5e78777fe82241de3d5f)
- **[142]** <a id="ref-142"></a>JSON remote-control protocol ✓ — [https://sw.kovidgoyal.net/kitty/rc_protocol/](https://sw.kovidgoyal.net/kitty/rc_protocol/)
- **[143]** <a id="ref-143"></a>WezTerm ✓ — [https://github.com/wezterm/wezterm/tree/76b606ec597a3c0263fa60321548637451c0a547](https://github.com/wezterm/wezterm/tree/76b606ec597a3c0263fa60321548637451c0a547)
- **[144]** <a id="ref-144"></a>wezterm cli ✓ — [https://wezterm.org/cli/cli/index.html](https://wezterm.org/cli/cli/index.html)
- **[145]** <a id="ref-145"></a>Ghostty ✓ — [https://github.com/ghostty-org/ghostty/tree/2dd79f3bc6af649e68422b08e21ad0300fd8b391](https://github.com/ghostty-org/ghostty/tree/2dd79f3bc6af649e68422b08e21ad0300fd8b391)
- **[146]** <a id="ref-146"></a>libghostty-vt ✓ — [https://github.com/ghostty-org/ghostty/blob/2dd79f3bc6af649e68422b08e21ad0300fd8b391/include/ghostty/vt.h](https://github.com/ghostty-org/ghostty/blob/2dd79f3bc6af649e68422b08e21ad0300fd8b391/include/ghostty/vt.h)
- **[147]** <a id="ref-147"></a>turborepo-ghostty ✓ — [https://github.com/vercel/turborepo/tree/ac6c28ff6fe82aaad2ae7f1cae32a6c4d4f094f3/crates/turborepo-ghostty](https://github.com/vercel/turborepo/tree/ac6c28ff6fe82aaad2ae7f1cae32a6c4d4f094f3/crates/turborepo-ghostty)
- **[148]** <a id="ref-148"></a>awesome-libghostty — [https://github.com/Uzaaft/awesome-libghostty](https://github.com/Uzaaft/awesome-libghostty)
- **[149]** <a id="ref-149"></a>Warp ✓ — [https://github.com/warpdotdev/warp/tree/a9a31226f3a04cf315df61aa3b8754593f869bb7](https://github.com/warpdotdev/warp/tree/a9a31226f3a04cf315df61aa3b8754593f869bb7)
- **[150]** <a id="ref-150"></a>block model ✓ — [https://www.warp.dev/blog/block-model-behind-warps-agentic-development-environment](https://www.warp.dev/blog/block-model-behind-warps-agentic-development-environment)
- **[151]** <a id="ref-151"></a>Wave ✓ — [https://github.com/wavetermdev/waveterm/tree/c99022c15bd1f17273728e728a61743e690d6423](https://github.com/wavetermdev/waveterm/tree/c99022c15bd1f17273728e728a61743e690d6423)
- **[152]** <a id="ref-152"></a>fish — [https://github.com/fish-shell/fish-shell/tree/790754d12adad72d542fc90886ae2f1689de6610](https://github.com/fish-shell/fish-shell/tree/790754d12adad72d542fc90886ae2f1689de6610)
- **[153]** <a id="ref-153"></a>Starship — [https://github.com/starship/starship/tree/cad50cd836533f5134b3bb75d21afa39cb024e1e](https://github.com/starship/starship/tree/cad50cd836533f5134b3bb75d21afa39cb024e1e)
- **[154]** <a id="ref-154"></a>rg — [https://github.com/BurntSushi/ripgrep/tree/f9c05a949d1a0dc8e16dee28ca9605d38611faeb](https://github.com/BurntSushi/ripgrep/tree/f9c05a949d1a0dc8e16dee28ca9605d38611faeb)
- **[155]** <a id="ref-155"></a>bat — [https://github.com/sharkdp/bat/tree/78951393e29bfd2f2a45f4326b9d2bb5e737dd2a](https://github.com/sharkdp/bat/tree/78951393e29bfd2f2a45f4326b9d2bb5e737dd2a)
- **[156]** <a id="ref-156"></a>eza — [https://github.com/eza-community/eza/tree/471bfbc7b03cbac8c738e8d9050edb06ee79132a](https://github.com/eza-community/eza/tree/471bfbc7b03cbac8c738e8d9050edb06ee79132a)
- **[157]** <a id="ref-157"></a>Ratatui — [https://github.com/ratatui/ratatui/tree/3d8639cbb2f910200f30e680a8923ccaf99ba1bf](https://github.com/ratatui/ratatui/tree/3d8639cbb2f910200f30e680a8923ccaf99ba1bf)
- **[158]** <a id="ref-158"></a>Zellij — [https://github.com/zellij-org/zellij/tree/ea07e2d5b6bbc9ea6f9c765b0838b3e91156a58d](https://github.com/zellij-org/zellij/tree/ea07e2d5b6bbc9ea6f9c765b0838b3e91156a58d)
- **[159]** <a id="ref-159"></a>GitUI — [https://github.com/gitui-org/gitui/tree/bc086cf2e5f32cc66627340f8642021c9bf1bc55](https://github.com/gitui-org/gitui/tree/bc086cf2e5f32cc66627340f8642021c9bf1bc55)
- **[160]** <a id="ref-160"></a>asciinema — [https://github.com/asciinema/asciinema/tree/3c610957c7dbbdfcb362a1cdfc39df4eb21f48ad](https://github.com/asciinema/asciinema/tree/3c610957c7dbbdfcb362a1cdfc39df4eb21f48ad)
- **[161]** <a id="ref-161"></a>navi — [https://github.com/denisidoro/navi/tree/1ac218cb1e0e80649ef23c8a916e67efc3086833](https://github.com/denisidoro/navi/tree/1ac218cb1e0e80649ef23c8a916e67efc3086833)
- **[162]** <a id="ref-162"></a>fish 4.0 的迁移复盘 — [https://fishshell.com/blog/rustport/](https://fishshell.com/blog/rustport/)
- **[163]** <a id="ref-163"></a>Rust 官方对 WebAssembly 的定位 — [https://rust-lang.org/what/wasm/](https://rust-lang.org/what/wasm/)
- **[164]** <a id="ref-164"></a>插件系统 — [https://zellij.dev/documentation/plugins.html](https://zellij.dev/documentation/plugins.html)
- **[165]** <a id="ref-165"></a>独立权限表 — [https://zellij.dev/documentation/plugin-api-permissions](https://zellij.dev/documentation/plugin-api-permissions)
- **[166]** <a id="ref-166"></a>Wasmtime 的安全文档 — [https://docs.wasmtime.dev/security.html](https://docs.wasmtime.dev/security.html)
- **[167]** <a id="ref-167"></a>ResourceLimiter — [https://docs.wasmtime.dev/api/wasmtime/trait.ResourceLimiter.html](https://docs.wasmtime.dev/api/wasmtime/trait.ResourceLimiter.html)
- **[168]** <a id="ref-168"></a>consume_fuel:或:epoch_interruption — [https://docs.wasmtime.dev/api/wasmtime/struct.Config.html](https://docs.wasmtime.dev/api/wasmtime/struct.Config.html)
- **[169]** <a id="ref-169"></a>find-skills:本身也是一个 Skill ✓ — [https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/skills/find-skills/SKILL.md](https://github.com/vercel-labs/skills/blob/e173b8c88f2581cfdaa1b6767c6519a08155790e/skills/find-skills/SKILL.md)
- **[170]** <a id="ref-170"></a>Agent Skills specification ✓ — [https://agentskills.io/specification](https://agentskills.io/specification)
- **[171]** <a id="ref-171"></a>.well-known/agent-skills:discovery RFC ✓ — [https://github.com/cloudflare/agent-skills-discovery-rfc](https://github.com/cloudflare/agent-skills-discovery-rfc)
- **[172]** <a id="ref-172"></a>Jensen Huang 以 Hugging Face 安全事件为例 ✓ — [https://x.com/JensenHuang/status/2081698060330250294](https://x.com/JensenHuang/status/2081698060330250294)
- **[173]** <a id="ref-173"></a>NVIDIA 宣布 Open Secure AI Alliance ✓ — [https://x.com/nvidia/status/2081666629264449730](https://x.com/nvidia/status/2081666629264449730)
- **[174]** <a id="ref-174"></a>Anthropic 对开放权重的正式立场 ✓ — [https://www.anthropic.com/news/position-open-weights-models](https://www.anthropic.com/news/position-open-weights-models)

</details>

### 引用热度图谱（全部被引用的 ref）

| 引用次数 | 编号 | 主题 |
|---|---|---|
| ×2 | [54](#ref-54) | Cloudflare Skills |
| ×1 | [4](#ref-4) | OpenAI 所说的 Harness Engineering |
| ×1 | [5](#ref-5) | Managed Agents 架构 |
| ×1 | [6](#ref-6) | Anthropic 的 Claude 5 context engineering 复盘 |
| ×1 | [7](#ref-7) | Codex Goal |
| ×1 | [9](#ref-9) | OpenAI Agents SDK 的编排模型 |
| ×1 | [10](#ref-10) | Claude Code Dynamic Workflows |
| ×1 | [11](#ref-11) | 后续迁移复盘 |
| ×1 | [13](#ref-13) | AI SDK 7 |
| ×1 | [14](#ref-14) | Chat SDK |
| ×1 | [15](#ref-15) | Streamdown |
| ×1 | [16](#ref-16) | json-render |
| ×1 | [17](#ref-17) | Vercel Sandbox |
| ×1 | [18](#ref-18) | agent-browser |
| ×1 | [19](#ref-19) | portless |
| ×1 | [20](#ref-20) | ai-cli |
| ×1 | [21](#ref-21) | scriptc:20c3a6c |
| ×1 | [23](#ref-23) | Native SDK |
| ×1 | [24](#ref-24) | vgpu |
| ×1 | [25](#ref-25) | Turborepo |
| ×1 | [26](#ref-26) | Vercel 在 2021 年正式收购 Turborepo |
| ×1 | [27](#ref-27) | Turborepo 的 AI 指南 |
| ×1 | [28](#ref-28) | turborepo-ai-agents |
| ×1 | [29](#ref-29) | Go→Rust 迁移 |
| ×1 | [30](#ref-30) | 原文 |
| ×1 | [31](#ref-31) | SWC |
| ×1 | [32](#ref-32) | Next.js 11.1 公告 |
| ×1 | [33](#ref-33) | Next.js Compiler |
| ×1 | [45](#ref-45) | Cloudflare Agents |
| ×1 | [48](#ref-48) | Browser Run |
| ×1 | [49](#ref-49) | 把底层重建到 Containers |
| ×1 | [53](#ref-53) | Nimbus |
| ×1 | [55](#ref-55) | Agent Readiness |
| ×1 | [56](#ref-56) | Code Mode |
| ×1 | [52](#ref-52) | Cloudflare 已弃用 Sandbox SDK 中的 desktop feature |
| ×1 | [72](#ref-72) | OpenClaw Foundation |
| ×1 | [73](#ref-73) | 进入 Linux Foundation 旗下 AAIF |
| ×1 | [74](#ref-74) | Claude Cowork |
| ×1 | [75](#ref-75) | Claude Design |
| ×1 | [77](#ref-77) | Cowork Web/Mobile 公告 |
| ×1 | [79](#ref-79) | 内置浏览器 |
| ×1 | [87](#ref-87) | 会话还可以从终端导出到 GUI 继续 |
| ×1 | [92](#ref-92) | ripgrep FAQ |
| ×1 | [98](#ref-98) | 审批绑定实现 |
| ×1 | [100](#ref-100) | 重做 Claude Code Desktop |
| ×1 | [101](#ref-101) | /app |
| ×1 | [102](#ref-102) | OpenAI 的 Pets 文档 |
| ×1 | [104](#ref-104) | Gemini in Chrome |
| ×1 | [107](#ref-107) | 宣布收购 The Browser Company |
| ×1 | [106](#ref-106) | Dia |
| ×1 | [110](#ref-110) | 迁移说明 |
| ×1 | [109](#ref-109) | Atlassian 2026 年进一步披露 |
| ×1 | [111](#ref-111) | Chrome DevTools Protocol |
| ×1 | [112](#ref-112) | WebDriver BiDi |
| ×1 | [113](#ref-113) | Chrome WebMCP early preview |
| ×1 | [114](#ref-114) | W3C Web Machine Learning Community Group 草案 |
| ×1 | [115](#ref-115) | Cloudflare WebMCP |
| ×1 | [116](#ref-116) | WebGPU |
| ×1 | [117](#ref-117) | Transformers.js 的 WebGPU 指南 |
| ×1 | [127](#ref-127) | 官方产品介绍 |
| ×1 | [131](#ref-131) | Vite 8 |
| ×1 | [132](#ref-132) | Tailwind CSS v4 |
| ×1 | [133](#ref-133) | Base UI |
| ×1 | [134](#ref-134) | shadcn 已把 Base UI 设为新项目默认 primitive |
| ×1 | [135](#ref-135) | React Aria 纳入一等选项 |
| ×1 | [136](#ref-136) | TypeScript 7.0 |
| ×1 | [137](#ref-137) | uv |
| ×1 | [138](#ref-138) | Bun PR:#30412 |
| ×1 | [139](#ref-139) | safe Rust 下的潜在 UB/Miri 问题 |
| ×1 | [140](#ref-140) | Alacritty |
| ×1 | [141](#ref-141) | kitty |
| ×1 | [142](#ref-142) | JSON remote-control protocol |
| ×1 | [143](#ref-143) | WezTerm |
| ×1 | [144](#ref-144) | wezterm cli |
| ×1 | [145](#ref-145) | Ghostty |
| ×1 | [146](#ref-146) | libghostty-vt |
| ×1 | [147](#ref-147) | turborepo-ghostty |
| ×1 | [149](#ref-149) | Warp |
| ×1 | [150](#ref-150) | block model |
| ×1 | [151](#ref-151) | Wave |
| ×1 | [39](#ref-39) | vercel-labs/skills |
| ×1 | [169](#ref-169) | find-skills:本身也是一个 Skill |
| ×1 | [170](#ref-170) | Agent Skills specification |
| ×1 | [171](#ref-171) | .well-known/agent-skills:discovery RFC |
| ×1 | [41](#ref-41) | skills-lock.json |
| ×1 | [42](#ref-42) | well-known provider |
| ×1 | [43](#ref-43) | partner audit |
| ×1 | [172](#ref-172) | Jensen Huang 以 Hugging Face 安全事件为例 |
| ×1 | [173](#ref-173) | NVIDIA 宣布 Open Secure AI Alliance |
| ×1 | [174](#ref-174) | Anthropic 对开放权重的正式立场 |
