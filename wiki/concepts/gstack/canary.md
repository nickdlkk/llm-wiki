---
title: gstack /canary
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/canary/SKILL.md"]
tags: [gstack, post-deploy, monitoring, regression-detection, observability]
---

# /canary

Post-deploy canary monitoring. Watches the live app for console errors, performance regressions, and page failures using the browse daemon. Takes periodic screenshots, compares against pre-deploy baselines, and alerts on anomalies.

## 核心職責

**何時觸發**：
- 用戶說 "monitor deploy"、"canary"、"post-deploy check"、"watch production"、"verify deploy"
- 部署完成後主動建議

**監控維度**：
- Console Errors（控制台錯誤）
- Performance Regressions（性能回歸）
- Page Failures（頁面失敗）
- 定期截圖比對
- Pre-deploy 基線比較

## 工作流程

```
1. 部署完成
2. 啟動 browse daemon 監控
3. 定期抓取頁面狀態
4. 對比 pre-deploy 基線
5. 發現異常時 alert
```

## 與 /benchmark 的區別

| 維度 | /canary | /benchmark |
|------|---------|-----------|
| 時機 | Post-deploy | Pre/PR-deploy |
| 重點 | 實時監控、異常報警 | 基準建立、趨勢追蹤 |
| 觸發 | 部署後自動/手動 | 按需運行 |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/browse]] — headless browser（/canary 底層使用 browse daemon）
- [[concepts/gstack/benchmark]] — 性能回歸檢測
- [[concepts/gstack/ship]] — 部署觸發 canary 監控
