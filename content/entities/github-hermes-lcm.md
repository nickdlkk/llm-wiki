---
title: hermes-lcm
type: entity
created: 2026-04-17
updated: 2026-04-17
sources:
  - https://github.com/stephenschoettler/hermes-lcm
  - https://papers.voltropy.com/LCM
tags:
  - hermes-plugin
  - context-management
  - lossless
  - dag
  - sqlite
  - open-source
---

# hermes-lcm

> **L**ossless **C**ontext **M**anagement — Hermes Agent 的無損上下文管理插件
> MIT License · stephenschoettler
> Inspired by [lossless-claw](https://github.com/martian-engineering/lossless-claw) for OpenClaw
> 基於 [LCM paper](https://papers.voltropy.com/LCM) (Ehrlich & Blackman, Voltropy PBC, Feb 2026)

## 核心問題

傳統的上下文壓縮：當 context 滿了，Agent 用**有損摘要**替換對話——細節丟失、模型自信地錯誤記憶、無法回溯。

hermes-lcm 的解決方案：**Nothing is ever lost.** 一切皆可檢索。

## 架構

```
Messages (immutable SQLite)
    ↓ compact
DAG Summary (D0 → D1 → D2 → D3+)
    ↓ retrieve
Agent Tools: lcm_grep / lcm_describe / lcm_expand / lcm_expand_query
    ↓ assemble
Active Context = system prompt + highest-depth summaries + fresh tail
```

### DAG 深度語義

| Depth | 名稱 | 時間尺度 | 內容 |
|-------|------|----------|------|
| D0 | Leaf summaries | 分鐘級 | 原始消息的摘要 |
| D1 | D0 condensation | 小時級 | D0 節點的壓縮 |
| D2 | D1 condensation | 天級 | D1 節點的進一步壓縮 |
| D3+ | Further condensation | 週/月級 | 繼續向上收斂 |

### 三層 Escalation（保證收斂）

```
L1: LLM detailed summary
    ↓ if summary_tokens ≥ source_tokens
L2: LLM bullet points (50% token budget)
    ↓ if bullet_tokens ≥ source_tokens
L3: Deterministic truncate (40% head + [...] + 40% tail)
    → 永遠保證 output < input
```

**核心不變量**：無論哪層，最終輸出 token 數 < 源輸入 token 數。

## 核心機制

### Immutable Store

- 每條消息完整寫入 SQLite，**永不修改**
- 配合 FTS5 全文索引支持 `lcm_grep`
- `earliest_at` / `latest_at` 追蹤時間窗口

### 摘要節點（SummaryNode）

```python
@dataclass
class SummaryNode:
    node_id: int
    session_id: str
    depth: int              # D0/D1/D2/D3+
    summary: str
    token_count: int
    source_token_count: int  # 源材料總 token 數
    source_ids: List[int]  # 指向 store_ids 或 node_ids
    source_type: str       # "messages" or "nodes"
    created_at: float
    earliest_at: float | None
    latest_at: float | None
    expand_hint: str       # "Expand for details about: ..."
```

### 總結 Always Smaller Than Source

每層 escalation 都做 `count_tokens(output) < count_tokens(input)` 檢查——如果摘要比原文還長，立即升級到下一層。這是**硬約束**，不是建議。

### L1 Prompt 深度指導

```python
depth_guidance = {
    0: "Preserve decisions, rationale, constraints, active tasks, file paths, commands, and specific values.",
    1: "Distill into arc-level outcomes: what evolved, what was decided, current state. Drop per-turn detail.",
    2: "Capture durable narrative: decisions in effect, completed milestones, timeline. Drop process detail.",
}
```

## Agent Tools（6 個）

| Tool | 功能 |
|------|------|
| `lcm_grep` | FTS5 語法搜索原始消息和所有深度的摘要 |
| `lcm_describe` | 查看 DAG 結構（token 數、children、expand hints） |
| `lcm_expand` | 從摘要節點恢覆原始內容（token 預算內） |
| `lcm_expand_query` | 用擴展 LCM 上下文回答問題（可指定 node_ids） |
| `lcm_status` | 健康概覽：壓縮次數、store 大小、DAG 深度分佈、context 使用率 |
| `lcm_doctor` | 診斷：SQLite 完整性、FTS 索引同步、孤立節點、配置驗證、context 壓力 |

## 關鍵配置參數

| 參數 | 預設值 | 說明 |
|------|--------|------|
| `LCM_FRESH_TAIL_COUNT` | `64` | 保護不被壓縮的最新消息數 |
| `LCM_LEAF_CHUNK_TOKENS` | `20000` | 觸發 leaf 壓縮的 token 閾值 |
| `LCM_CONTEXT_THRESHOLD` | `0.75` | 觸發壓縮的 context window 比例 |
| `LCM_INCREMENTAL_MAX_DEPTH` | `1` | 單次 condensation 最大深度（`-1`=無限制） |
| `LCM_CONDENSATION_FANIN` | `4` | 觸發 condensation 需要的同深度節點數 |
| `LCM_IGNORE_SESSION_PATTERNS` | - | 完全排除的 session（glob 模式） |
| `LCM_STATELESS_SESSION_PATTERNS` | - | 設為只讀的 session |
| `LCM_SUMMARY_MODEL` | auxiliary | 摘要模型（預設用 Hermes auxiliary LLM） |
| `LCM_NEW_SESSION_RETAIN_DEPTH` | `2` | `/new` 後保留的 DAG 深度 |

## 與 Hermes 的集成

### Plugin Slot（PR #7464）

hermes-lcm 使用 Hermes Agent 的**可插拔 context engine 插槽**（`ContextEngine` ABC）：

```
context:
  engine: lcm   # ← 激活 hermes-lcm
```

插件目錄：`~/.hermes/hermes-agent/plugins/context_engine/lcm/`

### `/lcm` Slash Commands

| 命令 | 功能 |
|------|------|
| `/lcm` / `/lcm status` | 當前 session/runtime 狀態 |
| `/lcm doctor` | SQLite + FTS 健康檢查 |
| `/lcm doctor clean` | 識別噪音 session |
| `/lcm backup` | 建立時間戳 SQLite 備份 |

## 與 LLM Wiki 的關係

LLM Wiki 採用 **Hot Cache + Origin Provenance** 機制實現無損記憶，與 hermes-lcm 的思路高度契合：

| 機制 | hermes-lcm | LLM Wiki |
|------|-----------|---------|
| 持久化 | SQLite（每消息完整） | 文件系統（entity + concept 頁） |
| 壓縮 | DAG 摘要（多層） | Hot Cache（20 latest entities） |
| 檢索 | lcm_grep / lcm_expand_query | 知識庫搜索 |
| 收斂保證 | L1→L2→L3 三層 escalation | 定時蒸餾到冷存儲 |

hermes-lcm 的 **三層 escalation 機制**（L1 detailed → L2 bullets → L3 deterministic truncate）是 LLM Wiki 實現可靠的上下文淘汰策略的良好參考。

## 連結

- GitHub：https://github.com/stephenschoettler/hermes-lcm
- LCM Paper：https://papers.voltropy.com/LCM
- lossless-claw（OpenClaw 類似實現）：https://github.com/martian-engineering/lossless-claw
