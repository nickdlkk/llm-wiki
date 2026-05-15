---
title: gstack /ship
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/ship/SKILL.md"]
tags: [gstack, deployment, release-engineering, ci-cd]
---

# /ship

Release Engineer skill — 同步 base branch → 运行测试 → 审查 diff → bump VERSION → 更新 CHANGELOG → commit → push → 创建 PR。Workspace-aware version queue 支持按顺序发版多个 PR。

## 核心职责

**何时触发**：
- 用户说 "ship"、"deploy"、"push to main"、"create a PR"、"merge and push"、"get it deployed"
- 当代码 ready、用户要求部署或创建 PR 时**主动建议**（不自己 push/PR）

**安全边界**：skill 会主动触发，但**不会直接 push/PR**，而是停下来请求确认。这是 gstack 的安全边界设计——让用户始终控制 production-facing 操作。

## 标准流程

```
1. Sync base branch（git fetch + merge）
2. Run tests（CI 模拟）
3. Review diff（/review gate）
4. Bump VERSION（semver）
5. Update CHANGELOG
6. Commit + Push
7. Create PR（包含 CHANGELOG summary）
```

## Workspace-Aware Version Queue

多个 PR 需要按顺序合并时，`/ship` 能识别 queue 状态：
- 检测当前 PR 是否在 queue 中
- 如果前一个 PR 还未合并，等待或提示
- 按正确顺序合并，避免 dependency 冲突

## 与 /land-and-deploy 的区别

| 维度 | /ship | /land-and-deploy |
|------|-------|-----------------|
| 止点 | PR 创建 | 部署到 production |
| 后续 | 人工合并 + CI | 等待 CI → 部署 → canary |
| 用途 | 代码 review 完成，准备合并 | 需要完整 CI/CD pipeline |

## Version Bump 策略

```
fix bug     → PATCH (1.2.3 → 1.2.4)
new feature → MINOR (1.2.3 → 1.3.0)
breaking    → MAJOR (1.2.3 → 2.0.0)
```

## 相关技能

- [[concepts/gstack/land-and-deploy]] — 合并 → CI → 部署 → canary 完整 pipeline
- [[concepts/gstack/canary]] — 部署后监控循环
- [[concepts/gstack/review]] — /ship 前的代码审查 gate
- [[concepts/gstack/qa]] — /ship 前的 QA gate
- [[entities/gstack]] — gstack 完整 entity 入口
