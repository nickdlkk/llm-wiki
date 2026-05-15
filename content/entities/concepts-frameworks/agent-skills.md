---
title: "agent-skills"
type: entity
created: 2026-05-07
updated: 2026-05-07
sources: ["raw/articles/agent-skills.md"]
tags: [agent, SDLC, engineering-workflow, Claude-Code, OpenCode, spec-driven]
---

# agent-skills

> GitHub: [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)

Addy Osmani（Google 工程师，Chrome Team）出品的 AI Coding Agent 工程技能库。MIT License。

## 基本信息

- **作者**: Addy Osmani
- **Stars**: 持续增长中（2026-05-07 接入时为公开库）
- **License**: MIT
- **平台支持**: Claude Code, Cursor, Gemini CLI, Windsurf, OpenCode, GitHub Copilot, Kiro IDE

## 核心架构

```
skills/     — 20 个 SKILL.md（Define/Plan/Build/Verify/Review/Ship）
agents/     — 3 个 specialist personas（code-reviewer/test-engineer/security-auditor）
references/ — 4 个补充 checklist（testing/security/performance/accessibility）
.claude/    — 7 个 slash commands
.gemini/    — 7 个 slash commands（Gemini CLI 专用）
docs/       — 各平台 setup 指南
```

## 20 Skills vs Hermes Skill 对比

| 维度 | agent-skills | Hermes Skill |
|------|-------------|--------------|
| 触发方式 | slash command / 意图自动激活 | skill tool 调用 |
| 数量 | 20（固定）| 按需扩展 |
| 适用场景 | 通用工程流程 | 平台特定任务 |
| 反理智化 | 每 skill 内置表格 | 无内置机制 |
| 验证门禁 | 明确 evidence requirements | 依赖实现质量 |

## Agent Personas

- **code-reviewer** — Senior Staff Engineer 视角，五轴审查
- **test-engineer** — QA Specialist，Prove-It 模式
- **security-auditor** — OWASP 评估，威胁建模

## 接入方式（Claude Code 推荐）

```bash
/plugin marketplace add https://github.com/addyosmani/agent-skills.git
/plugin install agent-skills@addy-agent-skills
```

## 关键差异化价值

1. **Anti-rationalization 机制** — 明确列出 agent 常找的借口（如"这代码简单不需要测试"）并给出反驳，防止跳过关键步骤
2. **Google 工程文化深度嵌入** — Hyrum's Law、Beyonce Rule、Chesterton's Fence 等不是引用，而是直接进工作流步骤
3. **跨平台一致性** — 同一个 skill 可在 Claude Code/Gemini CLI/OpenCode 等多平台使用
