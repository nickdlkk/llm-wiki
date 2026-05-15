---
title: gstack /careful
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/careful/SKILL.md"]
tags: [gstack, safety, guardrails, destructive-commands, prod-mode]
---

# /careful

Safety guardrails for destructive commands. Warns before rm -rf, DROP TABLE, force-push, git reset --hard, kubectl delete, and similar destructive operations. User can override each warning.

## 核心職責

**何時觸發**：
- 用戶說 "be careful"、"warn before destructive"、"safety mode"
- 主動介入：觸摸 prod、調試 live systems、共享環境

**保護模式**：Safety mode is active. Every bash command will be checked for destructive patterns before running.

## 受保護的操作

| 模式 | 範例 | 風險 |
|------|------|------|
| `rm -rf` | `rm -rf /var/data` | 遞歸刪除 |
| `DROP TABLE` | `DROP TABLE users;` | 數據丟失 |
| `TRUNCATE` | `TRUNCATE orders;` | 數據丟失 |
| `git push --force` | `git push -f origin main` | 歷史重寫 |
| `git reset --hard` | `git reset --hard HEAD~3` | 未提交工作丟失 |
| `kubectl delete` | `kubectl delete pod` | 生產環境影響 |
| `docker rm -f` | `docker system prune -a` | 容器/鏡像丟失 |

## 安全例外

以下模式允許而無需警告：
- `rm -rf node_modules` / `.next` / `dist` / `__pycache__` / `.cache` / `build` / `.turbo` / `coverage`

## 如何運作

Hook 讀取命令輸入 JSON，檢查是否匹配危險模式。如果匹配，返回 `permissionDecision: "ask"` 並附上警告。用戶可以選擇繼續或取消。

## 與 /freeze 的區別

| 維度 | /careful | /freeze |
|------|----------|---------|
| 防護機制 | 警告（可覆蓋） | 阻止編輯 |
| 作用範圍 | 危險命令 | 目錄範圍 |
| 組合 | 可單獨使用 | 可與 /careful 組合為 /guard |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/freeze]] — 目錄範圍編輯限制
- [[concepts/gstack/guard]] — /careful + /freeze 組合
