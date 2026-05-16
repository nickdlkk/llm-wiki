---
title: "How Claude Code works in large codebases: Best practices and where to start"
type: summary
created: 2026-05-20
updated: 2026-05-20
sources:
  - https://claude.com/blog/how-claude-code-works-in-large-codebases-best-practices-and-where-to-start
tags:
  - claude-code
  - large-codebase
  - harness
  - claude-md
  - hooks
  - skills
  - mcp
  - lsp
  - subagents
  - enterprise
organization: Anthropic
author: Anthropic Applied AI Team
summary: Anthropic 官方总结大规模代码库下 Claude Code 部署最佳实践。核心：Agentic Search（RAG 的去中心化替代，无需维护 embedding pipeline）、Harness 的五大扩展点（CLAUDE.md / Hooks / Skills / Plugins / MCP）、三大配置模式（代码库可导航性建设 / CLAUDE.md 动态维护 / 组织归属与 Rollout 节奏）。
---

# How Claude Code works in large codebases

**一句话总结：** 大规模部署的关键不是选更强的模型，而是构建完整的 Harness——Claude Code 的能力由生态决定，模型只是底座。

## Agentic Search vs RAG

| | RAG | Agentic Search |
|--|-----|----------------|
| 原理 | embedding 整个代码库，查询时检索相关块 | 实时遍历文件系统、grep、追踪引用 |
| 致命弱点 | embedding pipeline 跟不上活跃工程团队；索引落后数天/数周 | 需要足够的起始上下文才知道往哪看 |
| 扩展性 | 千人团队共享一个索引 | 每个开发者实例独立跑 live codebase |
| 结论 | 大规模下索引失效率高 | **Claude Code 采用此方案** |

> RAG 会返回团队两周前重命名的函数，或已被删除的模块，且没有任何过期标记。Agentic Search 无需维护 embedding pipeline，数千工程师同时提交代码也不受影响。

## Claude Code 的扩展层（Harness）

```
┌─────────────────────────────────────────────────────┐
│  CLAUDE.md → Hooks → Skills → Plugins → MCP/LSP    │
│     (按顺序层层叠加，每层建立在前一层之上)              │
└─────────────────────────────────────────────────────┘
```

### 五大扩展点

| 扩展点 | 作用 | 加载时机 |
|--------|------|---------|
| **CLAUDE.md** | 上下文文件，Claude 每次会话自动读取；根文件管全局视角，子目录文件管本地约定 | 每次会话 |
| **Hooks** | 关键时间点运行的脚本；stop hook 可在上下文鲜活时反思并提议 CLAUDE.md 更新；start hook 可动态加载团队特定上下文 | 事件触发 |
| **Skills** | 按需加载的专业任务包（渐进式披露）；可绑定到特定路径，只在相关代码区域激活 | 任务相关时 |
| **Plugins** | 将 skills + hooks + MCP 配置打包为单一可安装包；新工程师 day 1 即可获得同等上下文和工具 | 配置后始终可用 |
| **MCP Servers** | 连接内部工具/数据源/API；最成熟的团队构建了结构化搜索工具供 Claude 直接调用 | 配置后始终可用 |

### 两个补充能力

| 能力 | 说明 |
|------|------|
| **LSP 集成** | 给 Claude 提供与 IDE 相同的"跳转到定义"和"查找所有引用"能力；grep 通用函数名返回千条结果，LSP 只返回同一符号的引用，多语言代码库最高价值投资之一 |
| **Subagents** | 隔离的 Claude 实例，有独立上下文窗口；可并行：读 only 子 agent 映射子系统写文件，主 agent 基于完整图景编辑 |

## 大规模代码库配置三大模式

### 模式一：让代码库可导航

**CLAUDE.md 文件保持精简、分层：**
- 根文件只放指针和关键 gotchas；其他内容会变成噪声
- Claude 沿目录树向上移动时自动累加加载每个 CLAUDE.md，不会丢失根级上下文

**在子目录而非仓库根初始化：**
- Claude 在与任务实际相关的代码库区域作用域内工作最佳
- monorepo 中这可能违反直觉（工具通常假设根访问），但 Claude 自动沿目录树向上走，根级上下文从不丢失

**子目录级别的 test/lint 命令作用域：**
- 改了一个服务跑全量套件会导致超时并浪费无关输出的上下文
- 子目录级 CLAUDE.md 应指定适用于该部分代码库的命令

**用 `.claudeignore` / `settings.json` 的 `permissions.deny` 排除生成文件：**
- 排除规则版本控制，团队每个开发者自动获得同等噪音消除
- 代码生成器开发者可覆盖项目级排除而不影响其他人

**代码结构不规整时构建代码库地图：**
- 在仓库根放一个轻量 markdown，列出每个顶级文件夹及其内容的单行描述
- 数百顶级文件夹时，分层处理：根文件只描述最高级结构，子目录 CLAUDE.md 提供下一级细节

**运行 LSP 服务器让 Claude 按符号搜索而非字符串搜索：**
- grep 通用函数名返回千条匹配，Claude 烧上下文打开文件才分出哪个重要
- LSP 在 Claude 读取任何内容之前过滤，只返回指向同一符号的引用

### 模式二：随模型智能进化主动维护 CLAUDE.md

| 信号 | 说明 |
|------|------|
| 新模型发布后性能感觉停滞 | 做有意义的配置审查 |
| 建议每 3-6 个月 | 全面审查现有配置 |
| 规则变束缚 | 曾经帮助模型避免犯错的 CLAUDE.md 规则在新模型上可能主动限制能力 |

> 例如：告诉 Claude 把每次重构分解为单文件更改的规则，可能帮助早期模型保持正轨，但会阻止新模型执行它能处理好的跨文件协调编辑。

### 模式三：Assign Ownership for Claude Code Management

**Rollout 最快的组织在广泛访问前有小团队做基础设施投资：**
- 2个工程师构建一套 plugins 和 MCP，day 1 就可用
- 或整个团队专注管理 AI coding 工具，在 rollout 开始前基础设施就位
- 开发者第一次体验是高效的而非沮丧的，然后 adoption 自然扩散

**角色归属：**
| 形态 | 说明 |
|------|------|
| 最小可行版本 | 一个 DRI：一人拥有配置所有权、设置决策权、权限政策、plugin marketplace、CLAUDE.md 约定的责任，并负责保持更新 |
| 理想形态 | Agent Manager：混合 PM/工程师职能，专注管理 Claude Code 生态 |
| 归属团队 | 通常在 developer experience 或 developer productivity 下 |

**自下而上 adoption 的问题：** 产生热情但无人集中化成功经验会导致知识 tribal化，adoption  plateau。需要有人汇编和推广正确的约定（如标准 CLAUDE.md 层级、精选 skills 和 plugins）。

**治理问题（受监管行业）：**
- 谁控制哪些 skills/plugins 可用
- 如何防止数千工程师独立重建相同东西
- 如何确保 AI 生成代码经过与人类代码相同的审查流程

**建议：** 从明确定义的已批准 skills 集、所需代码审查流程和有限初始访问开始，随着信心建立而扩展。

## 常见误区速查

| 误区 | 正确做法 |
|------|---------|
| CLAUDE.md 用来存放所有可复用专业知识 | 可复用 expertise 应放在 Skills |
| 用 prompts 做本应自动运行的事情 | Hooks 做确定性自动化检查（lint/format） |
| 假设 LSP 是自动工作的 | 需要安装 code intelligence plugin + language server binary |
| 等待所有基础完善后再构建 MCP 连接 | MCP 在基础工作后构建 |
| 把所有 skills 内容放进 CLAUDE.md | Skills 按需加载，不污染每次会话的上下文 |

## 相关概念

- [[claude-code-agentic-harness-20260421]] — Claude Code Agentic Harness 架构官方文档解读
- [[learn-claude-code]] — Bash is all you need 课程（54k ⭐）
- [[gstack]] — Garry Tan 的 Claude Code 技能包（79k ⭐）
- [[harness-core-concept-20260421]] — Harness Engineering 核心概念
- [[github-spec-kit]] — Claude Code + spec-driven workflow 集成
- [[github-gsd]] — Get Shit Done: context hygiene for AI coding agents
