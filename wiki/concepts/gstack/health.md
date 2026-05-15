---
title: gstack /health
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/health/SKILL.md"]
tags: [gstack, code-quality, health-check, dashboard, linting]
---

# /health

Code quality dashboard. Wraps existing project tools (type checker, linter, test runner, dead code detector, shell linter), computes a weighted composite 0-10 score, and tracks trends over time.

## 核心職責

**何時觸發**：
- 用戶說 "health check"、"code quality"、"how healthy is the codebase"、"run all checks"、"quality score"

**整合工具**：
- Type Checker（類型檢查）
- Linter（代碼風格）
- Test Runner（測試運行）
- Dead Code Detector（死碼檢測）
- Shell Linter（Shell 腳本檢查）

**輸出**：
- Weighted composite 0-10 score
- 趨勢追蹤

## 分數計算

多個工具的加權綜合分數，範圍 0-10。

## 與 /review 的區別

| 維度 | /health | /review |
|------|---------|---------|
| 重點 | 整體代碼質量 | Pre-landing 安全審查 |
| 觸發 | 按需/定期 | 代碼合併前 |
| 輸出 | 質量分數 + 趨勢 | 問題列表 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/review]] — 代碼安全審查
- [[concepts/gstack/qa]] — 功能 QA
