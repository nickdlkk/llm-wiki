---
title: "agent-skills"
type: summary
created: 2026-05-07
updated: 2026-05-07
sources: ["raw/articles/agent-skills.md"]
tags: [agent, SDLC, engineering-workflow, Claude-Code, OpenCode, spec-driven]
---

# Summary — addyosmani/agent-skills

Addy Osmani 出品的 AI Coding Agent 工程技能库，把资深工程师的开发流程封装成 20 个可执行的 SKILL.md，覆盖软件开发生命周期（SDLC）的六个阶段。

## 核心价值

把"资深工程师的判断力"编码成结构化工作流——每个 skill 有明确步骤、验证门禁、反理智化表格（防止 agent 跳过关键步骤）。

## 六阶段 SDLC

```
DEFINE → PLAN → BUILD → VERIFY → REVIEW → SHIP
/spec   /plan   /build   /test   /review  /ship
```

## 20 Skills 分类

- **Define**: idea-refine, spec-driven-development
- **Plan**: planning-and-task-breakdown
- **Build**: incremental-implementation, test-driven-development, context-engineering, source-driven-development, frontend-ui-engineering, api-and-interface-design
- **Verify**: browser-testing-with-devtools, debugging-and-error-recovery
- **Review**: code-review-and-quality, code-simplification, security-and-hardening, performance-optimization
- **Ship**: git-workflow-and-versioning, ci-cd-and-automation, deprecation-and-migration, documentation-and-adrs, shipping-and-launch

## 关键设计理念

- **Process, not prose** — skill 是 agent 执行的工作流，不是参考文档
- **Anti-rationalization** — 每 skill 含反 excuses 表格（如"我后面再加测试"）
- **Verification non-negotiable** — 必须有测试通过/构建输出等证据
- **Progressive disclosure** — 按需加载支撑 reference，保持 token 消耗低

## 平台支持

Claude Code（slash commands + marketplace plugin）、Cursor（.cursor/rules/）、Gemini CLI（native skills）、Windsurf、OpenCode（AGENTS.md + skill tool）、GitHub Copilot（personas + copilot-instructions.md）。

## Google 工程文化基因

SWEBok + Google eng-practices 实践深度嵌入：Hyrum's Law（API 设计）、Beyonce Rule + Test Pyramid（测试）、Chesterton's Fence（代码简化）、Trunk-based Dev（git）、Shift Left + Feature Flags（CI/CD）、Code-as-Liability（弃用）。

## 与 Hermes 的关系

与 Hermes Skill 系统定位不同：agent-skills 是跨平台的工程技能库（通用），Hermes Skill 是 Hermes Agent 专用工具链。本 wiki 已有 SDD Tool Landscape 对比各工具，agent-skills 可作为该 landscape 的一个 entry。
