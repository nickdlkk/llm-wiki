# TencentDB Agent Memory

> **记忆不是让 AI 囤积一切，而是让人不必重复一切。**

## 概述

**TencentDB Agent Memory** 是腾讯开发的一个四层记忆系统插件，为 AI Agent 提供长期记忆和短期上下文压缩能力。

核心理念：**拒绝扁平向量堆砌，通过符号化短期记忆和分层长期记忆，让 Agent 记住该记的，让人专注判断、创造和真正有价值的工作。**

**集成效果**（对比裸 OpenClaw）：
- Token 消耗最高节省 **61.38%**（WideSearch 场景）
- 任务通过率相对提升 **51.52%**
- PersonaMem 准确率从 48% 提升到 **76%**

**GitHub**: [Tencent/TencentDB-Agent-Memory](https://github.com/Tencent/TencentDB-Agent-Memory)
**npm**: `[@tencentdb-agent-memory/memory-tencentdb](https://www.npmjs.com/package/@tencentdb-agent-memory/memory-tencentdb)`
**支持平台**: OpenClaw ≥ 2026.3.13 · Hermes ≥ 0.3.4 · Node.js ≥ 22.16

## 核心架构：分层 + 符号化

### 双核心设计哲学

1. **记忆分层（Memory Layering）** — 不论长期知识还是短期任务，记忆的生成和召回都必须有层次
2. **符号化记忆（Symbolic Memory）** — 用 Mermaid 符号图谱替代冗长日志，最大语义最小 Token

### 长期记忆：L0→L3 语义金字塔

```
                    ┌──────────────────────────────────────┐
         L3        │         Persona（用户画像）            │  高层：判断力 + 方向感
                    │  日常偏好、沟通风格、长期目标          │
                    ├──────────────────────────────────────┤
         L2        │         Scenario（场景块）              │  中层：场景化知识块
                    │  项目背景、SOP、工作流程模式           │
                    ├──────────────────────────────────────┤
         L1        │         Atom（原子事实）                │  低层：可引用的事实单元
                    │  具体日期、技术选型、工具习惯           │
                    ├──────────────────────────────────────┤
         L0        │      Conversation（原始对话）           │  底层：100% 原文保留
                    │  完整对话记录 + 工具调用日志           │
                    └──────────────────────────────────────┘
```

**每条信息 100% 可溯源**：Persona → Scenario → Atom → Conversation 链路无损回溯，无黑盒摘要。

### 短期记忆：Mermaid 符号压缩

```
┌─────────────────────┐     1. Offload      ┌─────────────────┐
│ Verbose Logs        │ ──────────────────→  │ External FS      │
│ (成百上千 Token)     │                     │ (refs/*.md)     │
└─────────────────────┘     2. Extract    ┌─────────────────┐
        │                      ──────────→  │ Mermaid Canvas  │
        │                         (带 node_id) │ (几百 Token)   │
        │                                      └─────────────────┘
        │                                         │
        │                              3. Light injection
        ↓                                         ↓
┌─────────────────────────────────────────────────────────────┐
│                    Agent Context (轻量)                       │
└─────────────────────────────────────────────────────────────┘
        ↑
        │ 4. Recall via node_id (grep 瞬间找回原文)
        │
┌─────────────────┐
│ External FS     │
│ (refs/*.md)    │
└─────────────────┘
```

## 四层管道（L0→L1→L2→L3）

| 层级 | 名称 | 处理内容 | 输出 |
|------|------|----------|------|
| **L0** | 对话记录 | 原始对话 + 工具调用结果 | JSONL 文件 |
| **L1** | 记忆提取 | LLM 抽取原子事实 + 向量去重 | 结构化 Atoms |
| **L2** | 场景归纳 | LLM 从多个 Session 归纳场景块 | Markdown Scenario |
| **L3** | 用户画像 | LLM 从场景块合成用户画像 | persona.md |

**存储策略**：底层（事实/日志/轨迹）→ 数据库（全文检索）；高层（画像/场景/画布）→ 文件系统（Markdown，白盒可读）

## 技术架构

### 源码结构

```
src/
├── core/
│   ├── tdai-core.ts          # Host-neutral 核心门面（TdaiCore 类）
│   ├── types.ts              # 抽象接口定义（HostAdapter, LLMRunner, RuntimeContext）
│   ├── conversation/
│   │   └── l0-recorder.ts    # L0 对话记录器
│   ├── record/
│   │   └── l1-extractor.ts   # L1 记忆提取（LLM + 去重）
│   ├── scene/
│   │   └── scene-extractor.ts # L2 场景归纳
│   ├── persona/
│   │   ├── persona-generator.ts  # L3 画像生成
│   │   └── persona-trigger.ts    # 画像触发器
│   ├── profile/
│   │   └── profile-sync.ts   # L2/L3 本地 ↔ DB 同步
│   ├── store/
│   │   ├── factory.ts        # 存储后端工厂（SQLite / TCVDB）
│   │   ├── embedding.ts      # Embedding 服务抽象
│   │   └── types.ts          # IMemoryStore 接口
│   └── report/
│       └── reporter.ts       # 指标上报
├── adapters/
│   ├── openclaw/
│   │   └── host-adapter.ts   # OpenClaw 主机适配器
│   └── standalone/
│       └── llm-runner.ts     # 独立 LLM 运行器（StandaloneLLMRunnerFactory）
├── utils/
│   ├── pipeline-factory.ts   # 管道工厂（创建 L1/L2/L3 运行器）
│   ├── pipeline-manager.ts   # 管道调度器（定时触发 L1→L2→L3）
│   ├── checkpoint.ts         # Session 状态检查点（断点续捕）
│   ├── session-filter.ts     # Session 过滤器（排除 benchmark/内部 session）
│   └── memory-cleaner.ts    # 记忆自动清理器
└── offload/
    └── index.ts              # 上下文卸载模块（short-term compression）
```

### 核心抽象：Host-Neutral 设计

```
┌─────────────────────────────────────────────────┐
│               TdaiCore (Host-Neutral)            │
│  唯一入口：recall / capture / search / pipeline  │
└───────────────────────┬─────────────────────────┘
                        │
         ┌──────────────┼──────────────┐
         ↓              ↓              ↓
  ┌────────────┐ ┌────────────┐ ┌──────────────┐
  │OpenClaw    │ │Hermes      │ │Standalone    │
  │HostAdapter │ │HostAdapter │ │LLMRunner     │
  └────────────┘ └────────────┘ └──────────────┘
```

- **TdaiCore**：纯内存逻辑，不依赖具体主机
- **HostAdapter**：每个主机平台自己的适配器实现
- **LLMRunnerFactory**：抽象 LLM 调用，支持 OpenClaw 内置模型或独立 API

### 存储后端

```
SQLite + sqlite-vec（默认，本地开箱即用）
    或
腾讯云向量数据库（TCVDB，生产级向量检索）
```

**混合检索**：BM25 关键词 + 向量相似度 + RRF 融合排序（`recall.strategy=hybrid`）

## 集成方式

### OpenClaw 插件（推荐）

```bash
openclaw plugins install @tencentdb-agent-memory/memory-tencentdb
openclaw gateway restart
```

```jsonc
// ~/.openclaw/openclaw.json
{
  "memory-tencentdb": {
    "enabled": true,
    "config": {
      "offload": { "enabled": true }   // 短期压缩（可选）
    }
  },
  "plugins": {
    "slots": {
      "contextEngine": "openclaw-context-offload"  // 注册上下文卸载槽位
    }
  }
}
```

### Hermes Gateway（Docker）

```bash
docker build -f Dockerfile.hermes -t hermes-memory .
docker run -d --name hermes-memory -p 8420:8420 \
  -e MODEL_API_KEY="***" \
  -e MODEL_BASE_URL="https://api.lkeap.cloud.tencent.com/v1" \
  -e MODEL_NAME="deepseek-v3.2" \
  -v hermes_data:/opt/data \
  hermes-memory
```

## 核心参数

| 层级 | 参数 | 默认 | 说明 |
|------|------|------|------|
| L0 | `capture.excludeAgents` | `[]` | 排除特定 Agent glob 模式 |
| L1 | `pipeline.everyNConversations` | `5` | 每 N 轮触发一次 L1 提取 |
| L1 | `extraction.maxMemoriesPerSession` | `20` | 单次 L1 最大记忆条数 |
| L2 | `pipeline.l1IdleTimeoutSeconds` | `600` | L1 触发空闲超时（秒） |
| L3 | `persona.triggerEveryN` | `50` | 每 N 条新记忆触发画像生成 |
| 召回 | `recall.strategy` | `hybrid` | `keyword` / `embedding` / `hybrid`（RRF） |
| 卸载 | `offload.enabled` | `false` | 启用 Mermaid 符号压缩 |

## 技术亮点

1. **白盒可调试** — 所有中间结果为人类可读 Markdown，非黑盒向量分值
2. **无损压缩** — Mermaid 符号图谱 + `node_id` 溯源，Token 降低 61% 同时保留 100% 可追溯性
3. **主机解耦** — TdaiCore 与 OpenClaw/Hermes/CLI 完全解耦，新主机只需实现 HostAdapter
4. **渐进式唤醒** — Warm-up 模式：新 session 从 1 轮开始触发 L1，每次翻倍（1→2→4→…），加速早期记忆沉淀
5. **Benchmark 真实性** — SWE-bench 每个 Session 连续执行 50 个任务，模拟真实长程 Agent 压力

## 路线图

- [x] 长期个性化记忆（L0→L3）
- [x] 短期上下文压缩（Context Offload + Mermaid 画布）
- [x] SQLite 本地后端 + 腾讯云向量数据库后端
- [x] OpenClaw 插件 + Hermes Gateway 集成
- [ ] **跨 Agent / 跨框架 / 跨设备**的记忆迁移（Portable Memory）
- [ ] 自动 Skill 生成（L2→可复用 Skill/SOP）
- [ ] 可视化调试和记忆可观测 Dashboard

## 相关项目

- [OpenClaw](https://github.com/openclaw/openclaw) — Agent 执行框架，TencentDB Agent Memory 的主要集成目标
- [Hermes Agent](https://github.com/NousResearch/hermes-agent) — Nous Research 的 AI Agent，也支持此记忆系统
