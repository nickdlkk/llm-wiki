---
title: gstack /benchmark
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/benchmark/SKILL.md"]
tags: [gstack, performance, regression-detection, web-vitals, lighthouse]
---

# /benchmark

Performance regression detection using the browse daemon. Establishes baselines for page load times, Core Web Vitals, and resource sizes. Compares before/after on every PR. Tracks performance trends over time.

## 核心職責

**何時觸發**：
- 用戶說 "performance"、"benchmark"、"page speed"、"lighthouse"、"web vitals"、"bundle size"、"load time"
- 主動建議在部署前檢查性能表現

**測量維度**：
- Page Load Times（頁面載入時間）
- Core Web Vitals（LCP、FID、CLS）
- Resource Sizes（資源大小）
- Bundle Size（打包大小）

## 與 /benchmark-models 的區別

| 維度 | /benchmark | /benchmark-models |
|------|-----------|------------------|
| 測量對象 | 網頁性能 | AI 模型表現 |
| 用途 | 性能回歸檢測 | 模型橫向對比 |
| 工具 | Browse daemon、Lighthouse | Codex CLI、LLM Judge |

## 工作流程

```
1. 建立基準線（首次運行）
2. 每次 PR 運行性能測試
3. 對比基準線與當前表現
4. 追蹤趨勢變化
```

## Voice Triggers

- "speed test"
- "check performance"

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/browse]] — headless browser（/benchmark 依賴 browse daemon）
- [[concepts/gstack/canary]] — post-deploy 監控（也追蹤性能指標）
