---
title: open-ralph-wiggum
type: entity
created: 2026-04-21
updated: 2026-04-21
sources:
  - https://github.com/Th0rgal/open-ralph-wiggum
tags:
  - ai-agent
  - autonomous-loop
  - coding-agent
  - cli
  - bun
  - typescript
---

# open-ralph-wiggum

**open-ralph-wiggum** (`ralph`) 是 [Ralph Wiggum technique](https://ghuntley.com/ralph/) 的开源实现——一个将 AI 编码 agent（Claude Code、Codex、Copilot CLI、OpenCode）包装在持久循环中的 CLI 工具。

## 核心机制

Ralph Wiggum 技术的本质是一个**自参照循环**：

```bash
while true; do
  claude-code "Build feature X. Output <promise>DONE</promise> when complete."
done
```

关键洞察：**每次迭代 agent 看到的是同一个 prompt，但代码库已经因为前几次迭代而改变了**。这创造了一个强大的反馈闭环——agent 观察测试失败、文件变更、git 历史，逐步自我修正，直到输出 `<promise>COMPLETE</promise>`。

## 支持的 Agent

| Agent | `--agent` | 默认模型 | 核心命令 |
|-------|-----------|----------|---------|
| OpenCode | `opencode` | 由 `opencode.json` 配置 | `opencode run -m <model> <prompt>` |
| Claude Code | `claude-code` | `claude-sonnet-4` | `claude -p <prompt> --model <model>` |
| OpenAI Codex | `codex` | `gpt-5-codex` | `codex exec --model <model>` |
| Copilot CLI | `copilot` | `claude-sonnet-4.5` | `copilot`（交互式） |

## 核心实现（`ralph.ts`）

### 循环驱动逻辑

Ralph 的主循环在 `ralph.ts` 中实现，核心流程：

1. **解析 CLI 参数** — agent 类型、model、iteration 限制、completion promise
2. **构建 agent 命令** — 每个 agent 有独立的 `ARGS_TEMPLATES`（`opencode`、`claude-code`、`codex`、`copilot`）
3. **执行 agent** — 通过 `bun` spawn 子进程，捕获 stdout/stderr
4. **解析工具调用** — 每个 agent 输出格式不同，`PARSE_PATTERNS` 适配 ANSI 彩色输出中的工具名提取
5. **检测完成信号** — `completion.ts` 中的 `checkTerminalPromise()` 检查最后一行是否匹配 `<promise>TAG</promise>`
6. **自动 git commit** — 每次迭代结束自动 `git add -A && git commit`，保存历史

### 状态文件（`.ralph/` 目录）

```
.ralph/
├── ralph-loop.state.json    # 当前循环状态（iteration、startTime、prompt）
├── ralph-context.md         # mid-loop 注入的提示
├── ralph-history.json       # 迭代历史（每次的工具使用、时间）
├── ralph-tasks.md           # Tasks Mode 任务列表
└── ralph-questions.json     # agent 提问的待处理队列
```

### Tasks Mode

复杂项目拆分为 `.ralph/ralph-tasks.md` 中的任务列表，每个任务消耗一次迭代：

```markdown
- [ ] Set up project structure
- [x] Initialize git repository
- [/] Implement user authentication
  - [ ] Create login page
  - [ ] Add JWT handling
- [ ] Build dashboard UI
```

当 agent 输出 `<promise>READY_FOR_NEXT_TASK</promise>` 时，Ralph 自动将当前任务标为 `[x]` 并切换到下一个。

### Mid-Loop Context Injection

在不停止循环的情况下从另一个终端注入提示：

```bash
ralph --add-context "The bug is in utils/parser.ts line 42"
```

context 会在下一次迭代被消费，然后自动清除。

### Agent Rotation

`--rotation` 支持每次迭代轮换不同的 agent/model 组合：

```bash
ralph "Build a REST API" \
  --rotation "opencode:claude-sonnet-4,claude-code:claude-sonnet-4,codex:gpt-5-codex" \
  --max-iterations 15
```

## 安装与分發

- **npm**: `@th0rgal/ralph-wiggum`，全局安装后 `ralph` 命令可用
- **Bun**: `bun add -g @th0rgal/ralph-wiggum`
- **源码**: `git clone` 后运行 `./install.sh`
- **入口**: `bin/ralph.js` 是编译后的 wrapper，实际逻辑在 `ralph.ts`

## 与 acp-client 的关系

acp-client 项目（Nick 在 `/root/acp-client/` 构建的 Python ACP Client）提供了另一种 spawn/管理 coding agents 的方案。与 Ralph 的关键区别：

| 维度 | Ralph Wiggum | acp-client |
|------|-------------|-----------|
| 语言 | TypeScript/Bun | Python |
| 核心机制 | prompt 循环 + 代码库状态 | ACP 协议 + spawn 管理 |
| 适用 agent | OpenCode/Claude Code/Codex/Copilot | 任意 ACP 兼容 agent |
| 复杂度 | 单一 CLI，~2496 行 | 包 + Docker 测试套件 |
| 完成检测 | `<promise>` tag 输出 | ACP 协议状态 |

Ralph 更轻量，直接操作 agent 的 stdout；acp-client 更结构化，通过 ACP 协议通信。

## 技术细节

- **Runtime**: Bun（TypeScript 直接运行，无需编译）
- **构建**: `bun build ralph.ts --outfile bin/ralph --compile` 可编译为独立二进制
- **CI**: GitHub Actions（Bun install → bun test → type check）
- **状态持久化**: JSON + Markdown 文件，纯文件系统，无数据库依赖
- **Windows 支持**: 自动解析 `.cmd` 扩展名，支持 `install.ps1`

## 来源

- GitHub: [Th0rgal/open-ralph-wiggum](https://github.com/Th0rgal/open-ralph-wiggum)
- npm: [@th0rgal/ralph-wiggum](https://www.npmjs.com/package/@th0rgal/ralph-wiggum)
- 原理论文: [ghuntley.com/ralph/](https://ghuntley.com/ralph/)
