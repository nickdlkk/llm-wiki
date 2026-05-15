---
title: gstack /qa
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/qa/SKILL.md"]
tags: [gstack, qa, playwright, browser-testing, bug-fixing]
---

# /qa

Systematically QA test a web application and fix bugs found — 用真实 Playwright Chromium 打开网站、找 bug、修复 bug、原子提交、重新验证。测试 → 修复 → 测试闭环，直到 site 达到 ship-ready。

## 核心职责

**何时触发**：
- 用户说 "qa"、"QA"、"test this site"、"find bugs"、"test and fix"、"fix what's broken"
- 用户说某功能"ready for testing"或问"does this work?"时**主动建议**

**核心创新**：persistent Chromium daemon（约 100ms/命令），而非冷启动浏览器。这是 gstack 的 `/browse` skill 提供的。

## 三层测试深度

| 层级 | 覆盖范围 | 场景 |
|------|---------|------|
| **Quick** | Critical + High 级别 | 快速验证、hotfix 验证 |
| **Standard** | + Medium 级别 | 日常 QA、PR 验证 |
| **Exhaustive** | + Cosmetic 级别 | 正式发版前 |

## 工作流

```
URL/功能描述 → $B browse → 扫描页面 → 发现 bug
→ 定位源码 → 修复 bug → 原子 commit
→ 重新 browse 验证 → 循环直到 clean
→ Ship-readiness summary
```

每个 bug fix 都是**原子提交**（一次 commit 一个 fix），方便回滚。

## 产出

- **Before/After Health Score**：QA 开始前和完成后的质量评分
- **Fix Evidence**：每个 fix 的证据（截图 + 修复 diff）
- **Ship-readiness Summary**：总结是否达到可发布标准

## 与 /qa-only 的区别

| 维度 | /qa | /qa-only |
|------|-----|---------|
| 行为 | 找 bug + 修复 | 只报告，不修改代码 |
| 用途 | 开发中测试、hotfix | 验收测试、给 PM/设计师的测试报告 |

## Proactive 触发

skill 设置了 proactive 模式：当用户说某功能"ready"或问"does this work?"时，自动建议运行 `/qa`，而不是直接告诉用户"可以测试了"。

## 相关技能

- [[concepts/gstack/review]] — pre-landing 代码审查（qa 前置 gate）
- [[concepts/gstack/ship]] — QA 通过后合并+PR
- [[concepts/gstack/investigate]] — QA 发现 bug 后根因调试
- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
