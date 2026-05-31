---
title: Local Deep Research
type: entity
created: 2026-05-19
updated: 2026-05-19
sources:
  - https://github.com/LearningCircuit/local-deep-research
  - https://docs.localdeepresearch.com
tags:
  - research
  - agentic
  - self-hosted
  - langgraph
  - flask
  - local-llm
---

# Local Deep Research

**Local Deep Research**（LDR）是开源的本地深度研究 Agent，支持完全离线运行（单卡 RTX 3090 + Qwen3.6-27B）。核心理念：**数据主权 + 可审计 + 自建知识库**。

**GitHub**: [LearningCircuit/local-deep-research](https://github.com/LearningCircuit/local-deep-research) · ★7,827 · MIT

## 核心成绩

| 基准 | 成绩 | 硬件 |
|------|------|------|
| SimpleQA (n=500) | **~95%** | RTX 3090 + Qwen3.6-27B |
| xbench-DeepSearch (n=100) | **77%** | RTX 3090 + Qwen3.6-27B |

首个在单卡消费级 GPU 上达到 ~95% SimpleQA 的开源项目。

## 技术架构

```
┌─────────────────────────────────────────────────────────────┐
│  User Interface (Browser → localhost:5000)                  │
└──────────────────────────┬────────────────────────────────┘
                           │
┌──────────────────────────▼────────────────────────────────┐
│  Flask Backend (REST API + WebSocket)                    │
│  ├── Authentication (CSRF Protection)                     │
│  ├── Research Routes                                      │
│  └── Strategy Selector (20+ strategies)                   │
└──────┬──────────────┬──────────────┬─────────────────────┘
       │              │              │
┌──────▼──────┐ ┌────▼────┐ ┌──────▼──────┐
│  Question   │ │  Search  │ │   Report    │
│  Generator  │ │  Executor│ │  Synthesizer│
└──────┬──────┘ └────┬────┘ └──────┬──────┘
       │              │              │
┌──────▼──────────────▼──────────────▼──────┐
│           LLM Providers                     │
│  Local: Ollama · LM Studio                │
│  Cloud: OpenAI · Anthropic · Gemini ·     │
│         OpenRouter (100+ models)          │
└────────────────────────────────────────────┘

┌────────────────────────────────────────────┐
│           Search Engines                    │
│  Web:   SearXNG · Tavily · Brave · DDG    │
│  Aca:   ArXiv · PubMed · Semantic Scholar  │
│  Local: Document Library · Elasticsearch    │
└────────────────────────────────────────────┘

┌────────────────────────────────────────────┐
│  Storage Layer                             │
│  SQLCipher (AES-256 加密) · Vector Store   │
│  File Storage (PDFs & Docs)               │
└────────────────────────────────────────────┘
```

## 研究流程

```
用户查询
    ↓
┌────────────────────────────────┐
│  Iterative Research Loop        │
│  Generate Questions →           │
│  Parallel Search →              │
│  Analyze Results →              │
│  (loop until satisfied)        │
└────────────┬───────────────────┘
             ↓
┌────────────────────────────────┐
│  Synthesis                      │
│  Generate Report →             │
│  Add Citations                  │
└────────────┬───────────────────┘
             ↓
    Final Report (引用完整)
```

## 两种研究模式

| 模式 | 说明 |
|------|------|
| **Pipeline（默认）** | 20+ 固定策略：Quick Summary → Detailed Report → Academic 等 |
| **LangGraph Agent（推荐）** | 自主决策：LLM 自己决定搜什么、切换哪个引擎、什么时候合成。~95% SimpleQA 成绩即来自此模式 |

## 知识库构建

```
Research → Download Sources → Library
    ↑                              ↓
    └──── Index & Embed ←──────────┘
```

- 研究会话中找到的优质来源直接下载到加密知识库
- 支持 ArXiv、PubMed、网页 PDF
- 自动提取文本、索引、向量化
- 下次研究时同时搜索本地文档 + 实时网络

## 部署模式

| 模式 | 组件 | 隐私级别 |
|------|------|---------|
| **Fully Local** | LDR + Ollama + SearXNG | 最高（完全离线）|
| **Hybrid** | LDR + Ollama + Cloud APIs | 平衡 |
| **Cloud-Powered** | LDR + OpenAI/Claude + Tavily | 最快 |

### Docker 一键部署（GPU）

```bash
# Step 1: Ollama
docker run -d -p 11434:11434 --name ollama ollama/ollama
docker exec ollama ollama pull gpt-oss:20b

# Step 2: SearXNG
docker run -d -p 8080:8080 --name searxng searxng/searxng

# Step 3: Local Deep Research
docker run -d -p 5000:5000 --network host \
  --name local-deep-research \
  --volume "deep-research:/data" \
  -e LDR_DATA_DIR=/data \
  localdeepresearch/local-deep-research
```

### pip 安装

```bash
pip install local-deep-research
python -m local_deep_research.web.app   # http://localhost:5000
```

## 技术栈

| 层 | 技术 |
|----|------|
| 前端 | React |
| 后端 | Python · Flask |
| Agent 框架 | LangGraph |
| ORM | Drizzle |
| 数据库 | SQLite + SQLCipher（AES-256 加密）|
| 向量存储 | Embeddings 支持 |
| 包管理 | pnpm（monorepo）|
| 容器 | Docker |
| 安全扫描 | 20+ CI 工具（CodeQL/Semgrep/OWASP ZAP/OSV-Scanner/Hadolint 等）|

## 支持的搜索源

| 类别 | 引擎 |
|------|------|
| 网络搜索 | SearXNG · Tavily · Brave · DuckDuckGo |
| 学术 | ArXiv · PubMed · Semantic Scholar |
| 本地 | Document Library · Elasticsearch |

## 输出格式

Markdown · PDF · LaTeX · Quarto · RIS/BibTeX

## 安全实践

项目在 CI 中集成了 20+ 安全扫描工具，涵盖：
- **代码静态分析**：CodeQL、Semgrep、DevSkim、Bearer、pre-commit
- **依赖扫描**：OSV-Scanner、npm-audit、Retire.js
- **容器安全**：Container Security、Dockle、Hadolint、Checkov
- **运行时安全**：Zizmor、OWASP ZAP、Security Tests
- **供应链**：OpenSSF Scorecard

## 与同类对比

| 系统 | 范式 | 本地运行 | 数据加密 | 知识库 | 学术搜索 | Agent 自主决策 |
|------|------|---------|---------|--------|---------|--------------|
| **Local Deep Research** | LangGraph Agent | 单卡 3090 | AES-256 | 内置 | ArXiv/PubMed | 完全自主 |
| Open Deep Research | Pipeline | 否 | 否 | 部分 | 部分 | 无 |
| Perplexity | 云端 API | 否 | 否 | 否 | 否 | 部分 |
| ChatGPT Deep Research | 云端 API | 否 | 否 | 否 | 是 | 部分 |

LDR 的核心差异化：**完全本地 + 加密 + 知识库 + 自主 Agent 决策**。

## 相关项目

- [LangGraph](https://langchain-ai.github.io/langgraph/) — Agent 编排框架
- [[OpenViking]] — 字节火山引擎上下文数据库（同样文件系统范式）
- [[Supermemory (mem0)]] — 云端记忆 + RAG
