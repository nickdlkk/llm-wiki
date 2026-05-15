---
title: gstack /guard
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/guard/SKILL.md"]
tags: [gstack, safety, guardrails, freeze, careful, maximum-safety]
---

# /guard

Full safety mode: destructive command warnings + directory-scoped edits. Combines /careful (warns before rm -rf, DROP TABLE, force-push, etc.) with /freeze (blocks edits outside a specified directory).

## 核心職責

**何時觸發**：
- 用戶說 "guard mode"、"full safety"、"lock it down"、"maximum safety"

**組合功能**：
- /careful：危險命令警告（rm -rf、DROP TABLE、force-push 等）
- /freeze：目錄範圍編輯限制

## 使用場景

觸摸 prod 或調試 live systems 時的最大安全模式。

## 與各自組成的區別

| 維度 | /guard | /careful | /freeze |
|------|--------|----------|---------|
| 命令警告 | ✓ | ✓ | - |
| 目錄限制 | ✓ | - | ✓ |
| 組合效果 | 完整安全 | 單一警告 | 單一限制 |

## Hook 機制

```
Bash → check-careful.sh（危險命令檢查）
Edit → check-freeze.sh（目錄邊界檢查）
Write → check-freeze.sh（目錄邊界檢查）
```

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/careful]] — 危險命令警告
- [[concepts/gstack/freeze]] — 目錄範圍編輯限制
