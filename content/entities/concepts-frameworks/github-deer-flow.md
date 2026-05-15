---
title: DeerFlow
type: entity
created: 2026-04-17
updated: 2026-04-17
sources:
  - https://github.com/bytedance/deer-flow
  - https://deerflow.tech
tags:
  - super-agent
  - langgraph
  - open-source
  - bytedance
  - agent-harness
  - multi-agent
  - skills
---

# DeerFlow

> **D**eep **E**xploration and **E**fficient **R**esearch **Flow** — 開源超級 Agent 馬車
> 62K ⭐ · MIT License · by ByteDance
> GitHub Trending #1 (2026-02-28, v2.0 發布當天)

## 概述

DeerFlow 是一個**超級 Agent 馬車（Super Agent Harness）**，從 Deep Research 框架進化而來。v2.0 完全重寫，基于 LangGraph + LangChain，專為「讓 Agent 真正把事情做完」而設計。

核心定位：Hermes Gateway 的思路類似——都是 Agent 運行時 + 消息通道整合，但 DeerFlow 更強調**技能系統**和**子 Agent 並行**。

## 核心架構

```
Nginx (port 2026) ─┬─→ Frontend (Next.js, :3000)
                   ├─→ LangGraph Server (:2024) ─→ Agent Runtime
                   └─→ Gateway API (:8001) ─→ REST API (models, skills, memory, uploads, channels)
```

### 兩種運行模式

| | Standard Mode | Gateway Mode (實驗性) |
|--|--|--|
| 架構 | Gateway + LangGraph 兩個進程 | Gateway 嵌入 Agent Runtime，單進程 |
| 進程數 | 4 (frontend + gateway + langgraph + nginx) | 3 (frontend + gateway + nginx) |
| LangGraph Platform License | 生產環境需要 | 不需要 |
| 並發控制 | `--n-jobs-per-worker`（每 worker 限制） | `--workers` × async tasks（無 per-worker 上限） |

### 進程架構

```
frontend/     ← Next.js Web UI
app/
  gateway/    ← FastAPI REST API (port 8001)
  channels/   ← IM 集成 (Feishu, Slack, Telegram, WeChat, WeCom)
packages/harness/deerflow/
  agents/     ← LangGraph Agent 系統
    lead_agent/     ← 主入口（工廠 + system prompt）
    middlewares/     ← 18 個 Middleware 鏈
    memory/          ← 長期記憶系統
    thread_state.py  ← ThreadState schema
  sandbox/    ← 隔離執行環境
    local/           ← 本地執行
    tools.py         ← bash / ls / read_file / write_file / str_replace
  subagents/  ← 子 Agent 委託系統
    builtins/        ← general-purpose / bash 兩種內置
    executor.py      ← 後台執行引擎
  tools/      ← 內置工具 (present_files, ask_clarification, view_image)
  mcp/        ← MCP 客戶端整合
  models/     ← 模型工廠（支持 thinking / vision / vLLM）
  skills/     ← Skills 發現、加載、解析
  community/  ← 社區工具 (Tavily, Jina, Firecrawl, AioSandbox)
  client.py   ← 嵌入式 Python Client (DeerFlowClient)
```

## Agent System 深入

### 18 個 Middleware 鏈

按執行順序（`build_lead_runtime_middlewares`）：

1. **ThreadDataMiddleware** — 創建 per-thread 目錄結構
2. **UploadsMiddleware** — 追蹤並注入新上傳文件
3. **SandboxMiddleware** — 獲取 sandbox，存 `sandbox_id`
4. **DanglingToolCallMiddleware** — 修補被中斷的 tool_call 鏈
5. **LLMErrorHandlingMiddleware** — 標準化 provider 錯誤
6. **GuardrailMiddleware** — 授權審查（可插拔 GuardrailProvider）
7. **SandboxAuditMiddleware** — 安全審計日誌
8. **ToolErrorHandlingMiddleware** — 工具異常 → 錯誤 ToolMessage
9. **SummarizationMiddleware** — 上下文壓縮
10. **TodoListMiddleware** — 任務追蹤（plan_mode）
11. **TokenUsageMiddleware** — Token 計量
12. **TitleMiddleware** — 自動生成 thread 標題
13. **MemoryMiddleware** — 異步記憶更新入隊
14. **ViewImageMiddleware** — 注入 base64 圖像
15. **DeferredToolFilterMiddleware** — 延遲工具可見性
16. **SubagentLimitMiddleware** — 限制並發子 Agent 數量
17. **LoopDetectionMiddleware** — 檢測重複 tool-call 循環
18. **ClarificationMiddleware** — 攔截 `ask_clarification` 請求（最後一個，必須）

### 子 Agent 系統

- **MAX_CONCURRENT_SUBAGENTS = 3**（由 SubagentLimitMiddleware 強制）
- **超時**：15 分鐘
- **執行池**：`_scheduler_pool` (3 workers) + `_execution_pool` (3 workers)
- **內置類型**：`general-purpose`（全工具，除 `task`）+ `bash`（命令專家）
- **流程**：`task()` tool → `SubagentExecutor` → 後台線程 → 輪詢 5s → SSE events → 結果
- **事件類型**：`task_started` / `task_running` / `task_completed` / `task_failed` / `task_timed_out`

### Sandbox 系統

- **LocalSandboxProvider**：單例本地執行，host bash 預設禁用（不安全隔離邊界）
- **AioSandboxProvider**：Docker 容器隔離（推薦）
- **虛擬路徑映射**：
  ```
  Agent 看到：/mnt/user-data/{workspace,uploads,outputs}, /mnt/skills
  實際：backend/.deer-flow/threads/{thread_id}/user-data/..., deer-flow/skills/
  ```
- **工具**：bash / ls / read_file / write_file / str_replace

## Skills 系統

### 技能格式

```
skill-name/
├── SKILL.md          ← 必需（YAML frontmatter + markdown 說明）
├── scripts/          ← 可選：腳本（可執行，自動化重複任務）
├── references/       ← 可選：參考文檔（需要時加載）
└── assets/           ← 可選：模板、字體等輸出資源
```

### SKILL.md Frontmatter

```yaml
---
name: skill-name
description: 觸發時機 + 功能描述（主要觸發機制，寫得稍微「激進」一些）
---
```

### 內建技能（skills/public/）

| 技能 | 功能 |
|------|------|
| `deep-research` | 系統化多角度 Web 研究方法 |
| `bootstrap` | 通過對話生成 SOUL.md 個性化配置 |
| `skill-creator` | 創建和改進技能（含量化評估框架）|
| `systematic-literature-review` | 學術文獻系統回顧 |
| `academic-paper-review` | 學術論文審閱 |
| `data-analysis` | 數據分析 |
| `chart-visualization` | 圖表可視化 |
| `ppt-generation` | PPT 生成 |
| `newsletter-generation` | 新聞郵件生成 |
| `podcast-generation` | 播客生成 |
| `video-generation` | 視頻生成 |
| `image-generation` | 圖像生成 |
| `web-design-guidelines` | Web 設計規範 |
| `frontend-design` | 前端設計 |
| `code-documentation` | 代碼文檔 |
| `github-deep-research` | GitHub 深度研究 |
| `consulting-analysis` | 諮詢分析 |
| `find-skills` | 技能發現 |
| `claude-to-deerflow` | 從 Claude Code 連接 DeerFlow |
| `vercel-deploy-claimable` | Vercel 部署 |
| `surprise-me` | 驚喜技能 |

### Skills 觸發機制

Skills 根據 `name` + `description` 出现在 `available_skills` 列表中，Claude 根據 description 決定是否諮詢該技能。

**注意**：Claude 只會為「無法輕鬆獨立完成的複雜任務」調用技能——簡單的單步查詢（如「讀取 PDF」）即使 description 匹配也不會觸發技能。

### Progressive Disclosure 原則

三層加載：
1. **Metadata**（name + description）— 始終在 context 中（~100 words）
2. **SKILL.md body** — 技能觸發時加載到 context（<500 lines）
3. **Bundled resources** — 按需加載（scripts 可直接執行，無需加載到 context）

### Skill 評估框架（skill-creator 技能）

完整的 TDD 循環：
1. 寫技能草稿
2. 創建 2-3 個真實測試提示
3. **並行**運行 with-skill 和 baseline subagent
4. 生成 benchmark viewer（`generate_review.py`）讓人主觀評估
5. 量化斷言（assertions）對比
6. 根據反饋改進，重複直到滿意

## Memory 系統

### 數據結構

```json
{
  "userContext": { "workContext", "personalContext", "topOfMind" },
  "history": { "recentMonths", "earlierContext", "longTermBackground" },
  "facts": [
    { "id", "content", "category", "confidence", "createdAt", "source" }
  ]
}
```

- **Fact categories**: preference / knowledge / context / behavior / goal
- **Confidence threshold**: 0.7
- **Max facts**: 100
- **Max injection tokens**: 2000

### 更新流程

1. `MemoryMiddleware` 過濾消息（user + AI responses）並入隊
2. 去重（30s debounce，per-thread）
3. 後台線程調用 LLM 提煉 context 更新
4. 原子寫入（temp file + rename）
5. 下次交互時通過 `<memory>` 標籤注入到 system prompt（Top 15 facts）

## IM 通道（Channels）

支持：Telegram / Slack / Feishu / WeChat / WeCom

### 消息流程

```
外部平台 → Channel impl → MessageBus.publish_inbound()
→ ChannelManager._dispatch_loop() 消費隊列
→ 查找/創建 LangGraph thread
→ runs.stream()（Feishu）或 runs.wait()（Slack/Telegram）
→ 累積 AI 響應 → 多個 outbound 補丁（Feishu）或單個響應（其他）
→ 發回平台
```

## 推薦模型

- **Long context**（100k+ tokens）：深度研究和多步任務
- **Reasoning capabilities**：自適應規劃和複雜分解
- **Multimodal inputs**：圖像理解和視頻理解
- **Strong tool-use**：可靠的函數調用

**推薦**：Doubao-Seed-2.0-Code / DeepSeek v3.2 / Kimi 2.5

## 與 LLM Wiki 的關係

DeerFlow 是**超級 Agent 馬車**的典型代表，與 Hermes Gateway 同類但更強調技能系統。其 `skill-creator` 的量化評估框架（benchmark viewer + assertion grading + iteration loop）是技能開發的最佳實踐，LLM Wiki 可以借鑒其技能評估方法。

DeerFlow 的子 Agent 架構（parallel fanning + result synthesis）與 Hermes 的 `delegate_task` 工具在概念上相似。

## 連結

- 官網：https://deerflow.tech
- GitHub：https://github.com/bytedance/deer-flow
- 對比：Hermes Gateway（消息通道 + Agent Runtime）、OpenClaw（Debugger Agent）、Cowork（多 Agent 協調）
