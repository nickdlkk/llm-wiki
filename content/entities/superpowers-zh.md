---
title: superpowers-zh
type: entity
created: 2026-04-28
updated: 2026-04-28
sources:
  - https://github.com/jnMetaCode/superpowers-zh
tags:
  - skills
  - chinese-localization
  - ai-coding
  - hermes-agent
  - claude-code
  - multi-agent
---

# superpowers-zh

**jnMetaCode/superpowers-zh** — AI 编程超能力中文增强版，fork 自 [obra/superpowers](https://github.com/obra/superpowers)（159k+ ⭐），在完整汉化基础上新增 6 个中国原创 skills。v1.1.8，MIT License。

## 核心定位

教 AI 编程工具（Claude Code / Hermes Agent / Cursor 等 17 款）**怎么干活**的方法论库。每个 skill 都是可执行的工作流规范——不是框架或教程，而是让 AI 在真实项目中按正确步骤执行。

## 支持的 17 款工具

Claude Code · Copilot CLI · Hermes Agent · Cursor · Windsurf · Kiro · Gemini CLI · Codex · Aider · Trae · VS Code (Copilot) · DeerFlow 2.0 · OpenCode · OpenClaw · Qwen Code (通义灵码) · Antigravity · Claw Code

一条命令自动识别项目工具并安装：`npx superpowers-zh`

## Skills 总览

### 翻译技能（14 个，来自上游obra/superpowers）

| Skill | 用途 |
|-------|------|
| brainstorming | 需求分析 → 设计规格，不写代码先想清楚 |
| writing-plans | 把规格拆成可执行的实施步骤 |
| executing-plans | 按计划逐步实施，每步验证 |
| test-driven-development | 严格 TDD：先写测试，再写代码 |
| systematic-debugging | 四阶段调试法：定位→分析→假设→修复 |
| requesting-code-review | 派遣审查 agent 检查代码质量 |
| receiving-code-review | 技术严谨地处理审查反馈 |
| verification-before-completion | 证据先行——声称完成前必须跑验证 |
| dispatching-parallel-agents | 多任务并发执行 |
| subagent-driven-development | 每个任务一个 agent，两轮审查 |
| using-git-worktrees | 隔离式特性开发 |
| finishing-a-development-branch | 合并/PR/保留/丢弃四选一 |
| writing-skills | 创建新 skill 的方法论 |
| using-superpowers | 元技能：如何调用和优先使用 skills |

### 中国原创 Skills（6 个）

| Skill | 用途 | 创新点 |
|-------|------|--------|
| **chinese-code-review** | 符合国内团队文化的代码审查规范 | 建议式反馈 vs 命令式；提问代替否定 |
| **chinese-git-workflow** | 适配 Gitee/Coding/极狐 GitLab/CNB | 平台对比表 + CI/CD 配置示例 |
| **chinese-documentation** | 中文排版规范、中英混排 | 告别机翻味 |
| **chinese-commit-conventions** | 适配国内团队的 commit message 规范 | Conventional Commits 中文适配 |
| **mcp-builder** | 构建生产级 MCP 工具 | Tool/Resource/Prompt 三原语选择原则 |
| **workflow-runner** | 在 AI 工具内运行多角色 YAML 工作流 | 无需 API key，使用当前 LLM 作为执行引擎 |

## 架构亮点

### 1. 自动工具检测 + 一键安装

```bash
npx superpowers-zh           # 自动检测项目工具并安装到正确位置
npx superpowers-zh --tool hermes  # 显式指定工具
```

bin/superpowers-zh.js（492行）解析 package.json / .claude / .hermes 等目录，自动推断工具类型并复制 skills 到正确路径。

### 2. 多工具 Skill 格式兼容

同一套 SKILL.md 内容，通过不同安装路径适配 17 款工具：
- `.claude/skills/` → Claude Code / Copilot CLI
- `.hermes/skills/` → Hermes Agent
- `.cursor/rules/` → Cursor
- `.kiro/steering/` → Kiro
- `.windsurf/skills/` → Windsurf
- `skills/` → OpenClaw

### 3. agents/ + commands/ 扩展

- `agents/code-reviewer.md` — 高级代码审查 agent prompt（plan-aligned review）
- `commands/brainstorm.md`、`execute-plan.md`、`write-plan.md` — CLI 命令封装

### 4. hooks/ 会话管理

- `hooks/session-start` — 新会话启动钩子
- `hooks/hooks.json` — Claude Code 会话 hooks 配置

## 与上游（obra/superpowers）的关键区别

| 维度 | 上游（obra/superpowers） | superpowers-zh |
|------|-------------------------|----------------|
| ⭐ Stars | 159k+ | — |
| Skills 数 | 14 | **20**（+6 中国原创） |
| 工具支持 | 6 款 | **17 款** |
| 安装方式 | 每款单独安装命令 | **`npx superpowers-zh` 自动识别** |
| Git 平台 | GitHub 为主 | GitHub + Gitee + Coding + 极狐 + CNB |
| CI/CD | GitHub Actions | + Gitee Go + Coding CI + 极狐 CI + CNB |
| 贡献政策 | 一般不接受新 skill PR | **欢迎中国开发者痛点 PR** |

## 相关生态项目

| 项目 | 定位 |
|------|------|
| [agency-agents-zh](https://github.com/jnMetaCode/agency-agents-zh) | 211 个即插即用 AI 专家角色库（含 46 中国市场原创） |
| [agency-orchestrator](https://github.com/jnMetaCode/agency-orchestrator) | YAML 工作流编排引擎，调度 agency-agents-zh |
| [ai-coding-guide](https://github.com/jnMetaCode/ai-coding-guide) | 66 个 Claude Code 技巧 + 9 款工具最佳实践 |
| [shellward](https://github.com/jnMetaCode/shellward) | AI 编程安全中间件，8 层防御 + DLP + 注入检测 |

## 技术细节

- **引擎**：Node.js >= 14.0.0（package.json engines 约束）
- **入口脚本**：`bin/superpowers-zh.js`
- **插件格式**：`.claude-plugin/`、`.cursor-plugin/`、`.opencode/plugins/`
- **Hooks**：session-start（会话启动钩子）、hooks.json（JSON 配置）
- **安装方式**：`npx superpowers-zh` 或手动复制 skills/agents/commands/hooks 到项目对应目录
