---
source_url: https://github.com/HEEERE/PMU-Graph
ingested: 2026-08-12
sha256: 30c588ffee1fccab411c7cdf7eec80bdc8b065f37c4b3d4e8404dc9bd1a6f9b6
---

<div align="center">
  <h1>🧠 PMU-Graph</h1>
  <p><strong>Governed Procedural Memory & Self-Evolving Skill Graph for LLM Agents</strong></p>
  <p>
    <img src="https://img.shields.io/badge/python-3.10+-blue" alt="Python">
    <img src="https://img.shields.io/badge/fastapi-0.115+-green" alt="FastAPI">
    <img src="https://img.shields.io/badge/pydantic-v2-orange" alt="Pydantic v2">
    <img src="https://img.shields.io/badge/license-MIT-lightgrey" alt="License">
  </p>
</div>

---

## Table of Contents

- [What is PMU-Graph?](#what-is-pmu-graph)
- [Core Concepts](#core-concepts)
- [Architecture](#architecture)
- [Data Flywheel (Self-Evolution Loop)](#data-flywheel-self-evolution-loop)
- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [API Reference](#api-reference)
- [Configuration](#configuration)
- [Key Modules](#key-modules)
  - [PMU Lifecycle](#1-pmu-lifecycle-state-machine)
  - [Risk Detection](#2-risk-detection)
  - [Environment-Aware Retrieval](#3-environment-aware-retrieval)
  - [Dual-Graph System](#4-dual-graph-system)
  - [Game-Theoretic Verification](#5-game-theoretic-verification)
  - [Task Synthesis Engine](#6-task-synthesis-engine)
  - [Active Refinement](#7-active-refinement)
  - [External Skill Store Integration](#8-external-skill-store-sandbox-smelting)
	  - [MCP Tool Integration](#9-mcp-tool-integration)
	  - [Trajectory Export](#10-trajectory-export-training-data)
	  - [Next-Step Hints](#11-scenario-guided-next-step-hints)
	  - [Skill Evaluation Cards](#12-skill-evaluation-cards)
	  - [Retrieval Diagnostics](#13-retrieval-diagnostics)
- [Extension Directions](#extension-directions)
- [License](#license)

---

## What is PMU-Graph?

PMU-Graph is a **governed procedural memory system** that turns raw LLM agent execution traces into structured, reusable knowledge units called **PMUs (Procedural Memory Units)**. Unlike naive vector databases that blindly store and retrieve unstructured text, PMU-Graph applies a rigorous **lifecycle gating system** — risk detection, conflict checking, game-theoretic verification, and evidence anchoring — to ensure only safe, proven procedural knowledge enters the agent's active memory.

The system then organizes these PMUs into a **dual-graph architecture**:
- A **Metadata Knowledge Graph** for audit and provenance tracking.
- A **Scenario-Mediated Skill Graph** where edges between environmental states represent executable skills — enabling graph-based reasoning and task synthesis.

**Key insight**: PMU-Graph doesn't just store memories. It *governs* them. Every PMU must earn its place through sandbox evidence before it can influence agent behavior.

---

## Core Concepts

### PMU (Procedural Memory Unit)

A PMU is a structured representation of a reusable procedure — what to do, when to do it, how to verify success, and what to do if it fails. Unlike a free-text "prompt library", a PMU has:

| Category | Fields | Purpose |
|----------|--------|---------|
| Identity | `id`, `name`, `description`, `type` | What this PMU is |
| Trigger | `pre_scenarios`, `trigger` (legacy) | When to activate this PMU |
| Execution | `goal`, `procedure`, `fallback` | What to do and how to recover |
| Verification | `verification`, `post_scenarios` | How to confirm success |
| Governance | `risk_level`, `source_trust`, `source_type`, `status` | Safety metadata |
| Statistics | `confidence`, `success_count`, `failure_count` | Performance tracking |

### Scenario

A **Scenario** is a semantic snapshot of the agent's environment at a point in time:

- **`pre` scenario**: The environmental state required *before* a skill can be activated (e.g., "FileNotFoundError for config.yaml", "empty directory with no template")
- **`post` scenario**: The expected environmental state *after* the skill executes (e.g., "config.yaml created with defaults", "report file generated with all sections")

Scenarios are the **nodes** in the Scenario-Mediated Skill Graph. Skills are the **edges** connecting them.

### Dual Graph

1. **Metadata Knowledge Graph** (Audit Layer): PMU → Tool, PMU → Evidence, PMU → Risk, PMU → Skill relationships. Answers "where did this memory come from?"
2. **Scenario-Mediated Skill Graph** (Reasoning Layer): `Scenario(pre)` → `SkillEdge` → `Scenario(post)`. Answers "what can I do next from this state?"

![Hand-drawn dual graph model](docs/assets/dual-graph-model-handdrawn.png)

### Lifecycle State Machine

PMUs flow through a strictly one-directional state machine:

![Hand-drawn PMU lifecycle state machine](docs/assets/pmu-lifecycle-handdrawn.png)

```
CANDIDATE ──→ VERIFIED ──→ ACTIVE ──→ DEPRECATED ──→ REJECTED
    │                        ↑
    └──→ QUARANTINE ─────────┘
    │         │
    └──→ REJECTED
```

Each transition is gated:
- **Verification Gate** (`CANDIDATE/QUARANTINE → VERIFIED`): Complete structure + LOW risk + TRUSTED source
- **Activation Gate** (`VERIFIED → ACTIVE`): Game-theoretic verification passed + ≥2 successes + 0 failures + evidence present
- **Skill Promotion** (`ACTIVE → Skill`): ≥5 successes + confidence ≥0.85 + LOW risk
- **Failure Demotion** (`ACTIVE → DEPRECATED`): ≥2 consecutive failures
- **Decay Pruning**: SkillEdges with ≥5 consecutive failures are automatically quarantined

---

## Architecture

![Hand-drawn PMU-Graph architecture layers](docs/assets/pmu-graph-architecture-handdrawn.png)

```
┌─────────────────────────────────────────────────────────────────────┐
│                         API Layer (FastAPI)                         │
│  /pmus  │  /tasks  │  /graph  │  /synthesis  │  /refiner  │  /ext  │
├─────────────────────────────────────────────────────────────────────┤
│                        Agent Layer                                  │
│  ┌──────────────┐  ┌──────────────────┐  ┌────────────────────┐   │
│  │  BaseAgent   │  │  MemoryAgent     │  │  Shadow Agent      │   │
│  │  (baseline)  │  │  (PMU retrieval) │  │  (sandbox testing) │   │
│  └──────────────┘  └──────────────────┘  └────────────────────┘   │
├─────────────────────────────────────────────────────────────────────┤
│                       Governance Layer                              │
│  ┌─────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────────────┐   │
│  │Lifecycle│ │  Risk    │ │ Conflict │ │ GameTheoreticVerifier│   │
│  │ Manager │ │ Detector │ │ Checker  │ │ Executor→Judge→Repair│   │
│  └─────────┘ └──────────┘ └──────────┘ └──────────────────────┘   │
├─────────────────────────────────────────────────────────────────────┤
│                      Intelligence Layer                             │
│  ┌─────────────┐ ┌────────────────┐ ┌─────────────────────────┐   │
│  │Environment  │ │ActiveRefiner   │ │TaskSynthesizer          │   │
│  │Retriever    │ │(trace → PMU)   │ │(random walk → task)     │   │
│  │(embedding)  │ │                │ │                         │   │
│  └─────────────┘ └────────────────┘ └─────────────────────────┘   │
├─────────────────────────────────────────────────────────────────────┤
│                       Dual Graph Layer                              │
│  ┌──────────────────────┐  ┌──────────────────────────────────┐   │
│  │ Metadata Graph        │  │ Scenario-Mediated Skill Graph    │   │
│  │ PMU/Tool/Evidence/    │  │ Scenario → SkillEdge → Scenario  │   │
│  │ Risk/Skill nodes      │  │ (reasoning + task synthesis)     │   │
│  └──────────────────────┘  └──────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────────────┤
│                       Storage Layer                                 │
│  ┌──────────┐ ┌─────────────┐ ┌──────────────┐ ┌──────────────┐   │
│  │ SQLite   │ │ FAISS/Chroma│ │ Docker       │ │ Scenario     │   │
│  │ (ORM)    │ │ (embedding) │ │ Sandbox      │ │ Alignment    │   │
│  └──────────┘ └─────────────┘ └──────────────┘ └──────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Data Flywheel (Self-Evolution Loop)

PMU-Graph is designed to continuously improve itself through a closed-loop mechanism:

![Hand-drawn PMU-Graph data flywheel](docs/assets/data-flywheel-handdrawn.png)

```
                    ┌──────────────┐
                    │ TaskSynthesizer│
                    │ (random walk)  │
                    └───────┬────────┘
                            │ sample paths from scenario graph
                            ▼
                    ┌──────────────┐
                    │ Synthesized   │
                    │ Test Tasks    │
                    └───────┬────────┘
                            │ feed to agent
                            ▼
                    ┌──────────────┐
                    │ MemoryAgent   │
                    │ (execution)   │
                    └───────┬────────┘
                            │ success/failure traces
                            ▼
              ┌─────────────────────────┐
              │ EdgeDecayManager         │
              │ (update edge confidence) │
              └────────────┬─────────────┘
                           │
              ┌────────────┴─────────────┐
              ▼                          ▼
     ┌────────────────┐        ┌──────────────────┐
     │ActiveRefiner   │        │ExternalSkill     │
     │(trace → PMU)   │        │Ingestor          │
     └───────┬────────┘        │(doc → PMU→Skill) │
             │                  └────────┬─────────┘
             │ new PMUs + scenarios      │
             └──────────┬───────────────┘
                        ▼
              ┌──────────────────┐
              │ GameTheoretic     │
              │ Verifier          │
              │ (sandbox proof)   │
              └────────┬─────────┘
                       │ verified PMUs
                       ▼
              ┌──────────────────┐
              │ Scenario Graph    │
              │ Update            │
              │ (new edges)       │
              └──────────────────┘
                       │
                       └────→ back to TaskSynthesizer (loop!)
```

The flywheel ensures:
1. **Cold-start resilience**: Even with zero manual PMUs, the ActiveRefiner discovers patterns from traces.
2. **Decay protection**: Necrotic edges are automatically pruned, preventing garbage accumulation.
3. **Diversity preservation**: Inverse-frequency random walk prioritizes under-tested paths.

---

## Project Structure

```
PMU_Graph/
├── app/
│   ├── core/                          # Core domain logic
│   │   ├── pmu_schema.py              # Pydantic v2 models: PMU, Scenario, SkillEdge, Trigger
│   │   ├── lifecycle_manager.py       # State machine: 6 states, 4 gates, confidence formula
│   │   ├── risk_detector.py           # Rule-based risk scanner (46 high + 17 critical keywords)
│   │   ├── environment_retriever.py   # Embedding-based retrieval + StateExtractor (v2)
│   │   ├── pmu_retriever.py           # Legacy Jaccard/keyword retriever (v1 fallback)
│   │   ├── verifier.py                # Static verifiers: report structure, code consistency, safety
│   │   ├── game_verifier.py           # Game-theoretic verifier: Executor→Judge→Repair Loop + Docker sandbox
│   │   ├── conflict_checker.py        # 5 conflict rules + priority-based resolution
│   │   ├── pmu_extractor.py           # Extract PMU candidates from task traces
│   │   ├── active_refiner.py          # LLM-driven trace reflection → new PMUs + ScenarioAlignment
│   │   ├── task_synthesizer.py        # Inverse-frequency random walk + task synthesis + EdgeDecayManager
│   │   ├── external_skill_ingestor.py # 4-pipeline external skill sandbox smelting
│   │   └── skill_promoter.py          # PMU → Skill candidate evaluation
│   │
│   ├── graph/                         # Dual-graph system
│   │   ├── graph_builder.py           # Metadata graph: PMU/Tool/Evidence/Risk/Skill nodes
│   │   ├── scenario_graph.py          # Scenario graph: Scenario→SkillEdge→Scenario edges
│   │   ├── graph_store.py             # NetworkX MultiDiGraph wrapper (replaceable with Neo4j)
│   │   └── graph_visualizer.py        # PyVis interactive HTML export
│   │
│   ├── agents/                        # Agent implementations
│   │   ├── base_agent.py              # Baseline agent (no memory)
│   │   ├── memory_agent.py            # PMU-augmented agent with retrieval
│   │   └── prompts.py                 # System prompt constants
│   │
│   ├── api/                           # FastAPI route handlers
│   │   ├── pmu_api.py                 # PMU CRUD + lifecycle transitions + risk/verify endpoints
│   │   ├── task_api.py                # Task execution + PMU extraction endpoints
│   │   ├── graph_api.py               # Dual-graph JSON + HTML export endpoints
│   │   ├── synth_api.py               # Task synthesis cycle + path sampling endpoints
│   │   ├── refiner_api.py             # Trace scanning + PMU→Skill promotion endpoints
│   │   ├── external_skill_api.py      # External skill ingestion + verification + listing
│   │   └── eval_api.py                # Experiment results endpoint
│   │
│   ├── db/                            # Database layer
│   │   ├── models.py                  # 9 SQLAlchemy ORM tables
│   │   ├── database.py                # SQLite connection + session management
│   │   └── init_db.py                 # Schema creation + PMU import/export helpers
│   │
│   ├── store/                         # Vector storage abstraction
│   │   └── embedding_store.py         # FAISS/Chroma-replaceable numpy embedding index
│   │
│   ├── tools/                         # Utility tools & protocol adapters
│   │   ├── mcp_client.py               # MCP (Model Context Protocol) client adapter
│   │   ├── code_formatter.py           # Code formatting extension point
│   │   ├── docx_generator.py           # DOCX report generation extension point
│   │   └── report_checker.py           # Report validation extension point
│   │
│   ├── utils/                         # Shared utilities
│   │   ├── llm_client.py              # LLM abstraction (MockLLMClient + OpenAI/Claude extensibility)
│   │   ├── text_utils.py              # Tokenization, Jaccard similarity, dynamic paragraph chunking
│   │   └── logger.py                  # Logging configuration
│   │
│   └── main.py                        # FastAPI app entry point
│
├── tests/                             # pytest test suite (23 tests)
├── experiments/                       # Baseline & PMU-Graph experiment runners
├── frontend/                          # Streamlit demo UI
├── data/                              # Example data, traces, evaluation results
├── docs/                              # Design docs, API docs, extension plans
├── config.yaml                        # Application configuration
├── requirements.txt                   # Python dependencies
└── README.md                          # This file
```

---

## Quick Start

### Prerequisites

- Python 3.10+
- Docker (optional, for sandbox isolation in production)

### Installation

```bash
# Clone the repository
git clone https://github.com/HEEERE/PMU-Graph.git
cd PMU-Graph

# Create virtual environment
python -m venv .venv
source .venv/bin/activate       # Linux/macOS
# .venv\Scripts\Activate.ps1    # Windows

# Install dependencies
pip install -r requirements.txt
```

Optional local environment file:

```bash
cp .env.example .env
```

The default configuration runs in deterministic mock mode when no provider
credentials are configured.

### One-Command Local Demo

```bash
python scripts/dev.py --install
```

On Windows PowerShell:

```powershell
.\scripts\dev.ps1 -Install
```

The helper initializes SQLite, starts FastAPI, and starts the Streamlit UI.

### Initialize the Database

```bash
python -m app.db.init_db
```

This creates all SQLite tables and imports 10 example PMUs from `data/pmu_examples/`. The default database path is `data/pmu_graph.sqlite3`. Override with:

```bash
export PMU_GRAPH_DB_PATH=/custom/path/pmu_graph.sqlite3
# or set PMU_GRAPH_DATABASE_URL for full PostgreSQL URL
```

### Start the Backend

```bash
python -m uvicorn app.main:app --reload
```

FastAPI is now running at `http://127.0.0.1:8000`. Interactive docs at `http://127.0.0.1:8000/docs`.

### Start the Frontend (Optional)

```bash
python -m streamlit run frontend/streamlit_app.py
```

### Docker Compose

```bash
docker compose up --build
```

Compose starts Neo4j, FastAPI, and Streamlit. Copy `.env.example` to `.env`
only when you want to provide real MiMo, Zhipu, Hugging Face, or Neo4j
credentials.

### Run Tests

```bash
python -m pytest                    # all tests
python -m pytest tests/ -v          # verbose
python -m pytest tests/test_pmu_schema.py  # specific file
```

### Run Experiments

```bash
python experiments/run_baseline_no_memory.py     # baseline without PMU memory
python experiments/run_baseline_text_memory.py    # baseline with text memory
python experiments/run_pmu_graph_agent.py         # PMU-Graph agent
python experiments/analyze_results.py             # generate comparison report
```

Results are saved to `data/evaluation_results/`.

### Run GAIA Benchmark

The GAIA runner supports the `base` and `pmu` agents, local attachments,
optional web search, multimodal inputs, trace persistence, and PMU distillation.
GAIA is a gated Hugging Face dataset, so place accepted dataset files under
`data/gaia/` or set `HF_TOKEN`/`HUGGINGFACE_HUB_TOKEN`.

```bash
python experiments/run_gaia_benchmark.py --agent base --limit 30
python experiments/run_gaia_benchmark.py --agent pmu --limit 30 --distill-gaia-pmus
```

Generated GAIA datasets, logs, and JSONL outputs are ignored by Git. Commit only
curated summaries or plots that you intentionally want to publish.

Local benchmark checkouts, uv caches, and generated STATE-Bench outputs are
ignored by Git. Keep experimental runners, downloaded datasets, and benchmark
artifacts local unless they are intentionally promoted to tracked project code.

---

## API Reference

### Health Check
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/health` | Health check → `{"status": "ok"}` |

### PMU Management (`/pmus`)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/pmus` | List PMUs. Query params: `status`, `risk_level`, `type` |
| `GET` | `/pmus/{pmu_id}` | Get a single PMU by ID |
| `POST` | `/pmus` | Create a new PMU (Pydantic body) |
| `PUT` | `/pmus/{pmu_id}` | Update an existing PMU |
| `POST` | `/pmus/{pmu_id}/transition` | Execute lifecycle transition. Body: `{"target_status": "verified"}` |
| `POST` | `/pmus/{pmu_id}/verify` | Run PMU Safety Verifier |
| `POST` | `/pmus/{pmu_id}/risk-check` | Run Risk Detector |

### Task Execution (`/tasks`)
| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/tasks/run` | Execute a task. Body: `{"user_task": "...", "use_memory": true}` |
| `POST` | `/tasks/extract-pmu` | Extract PMU candidates from a task trace |

### Graph (`/graph`)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/graph` | Build dual-graph from DB → JSON |
| `GET` | `/graph/html` | Build dual-graph → interactive PyVis HTML |

### Task Synthesis (`/synthesis`)
| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/synthesis/run-cycle` | Run synthesis cycle. Params: `num_tasks`, `min_steps`, `max_steps`, `auto_execute` |
| `POST` | `/synthesis/sample-path` | Sample a single random walk path (debugging) |

### Active Refinement (`/refiner`)
| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/refiner/scan` | Trigger trace scanning. Params: `min_traces`, `task_type_filter`, `dry_run` |
| `POST` | `/refiner/promote-to-skill/{pmu_id}` | Promote an eligible PMU to a Skill |

### External Skill Store (`/external-skill`)
| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/external-skill/ingest` | Full 4-pipeline sandbox smelting |
| `POST` | `/external-skill/ingest-dry-run` | Parse external doc without persisting |
| `GET` | `/external-skill/quarantined` | List all quarantined external PMUs |
| `POST` | `/external-skill/retry-verification/{pmu_id}` | Re-run sandbox verification |

### Trajectory Export (`/export`)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/export/trajectories` | Export ACTIVE PMU trajectories as OpenAI Tool-Calling JSONL. Params: `format`, `min_confidence`, `max_trajectories` |

### Evaluation (`/eval`)
| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/eval/results` | List all evaluation result records |
| `POST` | `/eval/run` | Placeholder (use experiment scripts instead) |

---

## Configuration

Configuration is loaded from `config.yaml` and can be overridden with
environment variables. `.env.example` documents the commonly used local keys.

```yaml
app:
  name: PMU-Graph
  environment: local
  log_level: INFO

runtime:
  api_host: 127.0.0.1
  api_port: 8000
  frontend_port: 8501

database:
  path: data/pmu_graph.sqlite3

llm:
  provider: mimo
  use_mock_by_default: true
  model: MiMo-V2.5-Pro
  base_url: ""
  timeout_seconds: 30.0
  temperature: 0.2

embedding:
  provider: zhipu
  use_mock_by_default: true
  model: embedding-3
  base_url: https://open.bigmodel.cn/api/paas/v4/embeddings
  dim: 2048
  timeout_seconds: 30.0

retrieval:
  top_k: 5
  min_score: 0.1

risk:
  external_source_min_level: medium
  block_active_levels:
    - high
    - critical

graph:
  backend: neo4j
  export_path: data/evaluation_results/skill_graph.html
  fallback_to_networkx: true
  neo4j_uri: bolt://localhost:7687
  neo4j_user: neo4j
  neo4j_database: neo4j

dataset:
  name: General AI Assistants Benchmark
  split: validation
  local_path: data/gaia
```

Environment variables:
- `PMU_GRAPH_DB_PATH` - Override SQLite database path
- `PMU_GRAPH_DATABASE_URL` - Full database URL, for example `postgresql://...`
- `PMU_GRAPH_LLM_API_KEY`, `MIMO_API_KEY` - Remote MiMo/OpenAI-compatible key
- `PMU_GRAPH_LLM_BASE_URL`, `MIMO_BASE_URL` - Chat completions endpoint base URL
- `PMU_GRAPH_EMBEDDING_API_KEY`, `ZHIPUAI_API_KEY` - Zhipu embedding key
- `NEO4J_URI`, `NEO4J_USER`, `NEO4J_PASSWORD`, `NEO4J_DATABASE` - Graph backend
- `HF_TOKEN` or `HUGGINGFACE_HUB_TOKEN` - GAIA dataset download token

---

## Key Modules

### 1. PMU Lifecycle (State Machine)

**File**: `app/core/lifecycle_manager.py`

The PMU lifecycle is a strict, auditable state machine. Every transition is recorded with a reason string.

**State Diagram**:
```
CANDIDATE ──→ VERIFIED ──→ ACTIVE ──→ DEPRECATED ──→ REJECTED
    │                        ↑
    └──→ QUARANTINE ─────────┘
    │         │
    └──→ REJECTED
```

**Gate Conditions**:

| Gate | Required Conditions |
|------|-------------------|
| Default Status | Incomplete structure → REJECTED; External source → QUARANTINE; HIGH/CRITICAL risk → QUARANTINE |
| Verify (`→ VERIFIED`) | Structure complete + risk=LOW + source_trust=TRUSTED + non-external source |
| Activate (`→ ACTIVE`) | risk ≠ HIGH/CRITICAL + success_count ≥ 2 + failure_count = 0 + evidence_ids non-empty |
| Skill Candidate | ACTIVE + success_count ≥ 5 + confidence ≥ 0.85 + risk = LOW |
| Auto-Deprecate | ACTIVE + failure_count ≥ 2 |

**Confidence Formula**:
```
confidence = BASE_SCORE[source_type] + 0.05 × success_count - 0.10 × failure_count - RISK_PENALTY[risk_level]
```

| Source Type | Base Score |
|-------------|-----------|
| manual | 0.75 |
| agent_success_trace | 0.65 |
| user_feedback | 0.55 |
| agent_failure_trace | 0.50 |
| external_document | 0.35 |
| webpage | 0.30 |
| unknown | 0.20 |

---

### 2. Risk Detection

**File**: `app/core/risk_detector.py`

A purely rule-based system — no LLM involvement, ensuring deterministic safety guarantees. Scans all PMU text fields against keyword lists:

| Risk Level | Criteria |
|-----------|----------|
| **CRITICAL** | API key exposure, credential upload, `rm -rf`, `format disk`, dangerous commands |
| **HIGH** | "bypass verification", "delete user code", "ignore verifier", "disable safety check" |
| **MEDIUM** | External/unknown source; user preference PMU from feedback |
| **LOW** | No risky patterns detected |

PMUs with HIGH or CRITICAL risk are **blocked from ACTIVE status** permanently.

---

### 3. Environment-Aware Retrieval

**Files**: `app/core/environment_retriever.py`, `app/core/pmu_retriever.py`

PMU-Graph v2 replaces keyword matching with **semantic environment retrieval**:

1. **StateExtractor** compresses noisy environment context (tracebacks, file trees) into a compact semantic signature — filtering out stack line numbers, timestamps, and irrelevant paths.
2. **EmbeddingStore** encodes the signature into a vector (MVP: deterministic n-gram hash; production: FAISS/Chroma with real embeddings).
3. The vector is compared against all `pre_scenario` embeddings to find the most relevant PMUs.
4. Final scoring combines: **50% vector similarity** + **25% confidence** + **15% success rate** − **10% risk penalty**.
5. If no embedding match is found, the system falls back to the legacy Jaccard/keyword retriever.

---

### 4. Dual-Graph System

**Files**: `app/graph/graph_builder.py`, `app/graph/scenario_graph.py`, `app/graph/graph_store.py`

#### Metadata Knowledge Graph (Audit Layer)
Constructed from the database on every request. Nodes and edges:

| Node Type | ID Pattern | Attributes |
|-----------|-----------|------------|
| `PMNode` | `{pmu_id}` | label, status, risk_level |
| `RiskNode` | `risk:{level}` | label |
| `ToolNode` | `tool:{name}` | label |
| `EvidenceNode` | `evidence:{id}` | label, trusted |
| `SkillNode` | `{skill_id}` | label, status |

| Edge Type | Direction |
|-----------|-----------|
| `has_risk` | PMU → Risk |
| `uses_tool` | PMU → Tool |
| `derived_from` | PMU → Evidence |
| `promoted_to` | PMU → Skill |
| `conflicts_with` | PMU ↔ PMU |

#### Scenario-Mediated Skill Graph (Reasoning Layer)
The reasoning graph encodes *transitions* between environmental states. When a PMU is promoted to a Skill:

```
Scenario(pre)  ──enters_skill──→  [SkillEdge Node]  ──exits_skill──→  Scenario(post)
```

The `SkillEdge` node carries the skill's `confidence`, `success_rate`, and `times_traversed` statistics. This graph is what powers the **TaskSynthesizer's random walk**.

**ScenarioAlignment** prevents graph fragmentation by merging semantically identical scenarios (cosine similarity ≥ 0.82) through feature union — "empty directory" and "no files folder" map to the same node.

---

### 5. Game-Theoretic Verification

**File**: `app/core/game_verifier.py`

Replaces static verifiers with a three-role adversarial system. Only PMUs that pass this gate can reach ACTIVE status.

**Roles**:

| Role | Responsibility |
|------|---------------|
| **Executor** | ReAct loop: reasons about each procedure step, outputs shell commands |
| **System** | Actually executes commands in the **Docker sandbox** (no LLM fabrication) |
| **Judge** | Scores execution against a weighted Rubric (correctness 40%, procedure_fidelity 30%, safety 20%, efficiency 10%) |
| **Repair** | If Judge fails, generates a concrete fix suggestion for the next attempt |

**Sandbox Security** (P0 requirement):
- **Docker** (primary): `--network none`, `--memory 256m`, `--read-only`, `--cap-drop ALL`, `no-new-privileges`
- **Subprocess** (fallback): Workspace isolation + minimal environment variables + RuntimeWarning

The Executor processes each procedure step individually — if step 2 fails, it immediately stops (no blind script continuation) and triggers the fallback strategy.

---

### 6. Task Synthesis Engine

**File**: `app/core/task_synthesizer.py`

The data flywheel's engine. Generates test tasks to stress-test and strengthen the graph.

**Inverse-Frequency Random Walk**:
1. Compute `weight = 1.0 / (1.0 + visit_count)` for every Scenario node.
2. Cold nodes (rarely visited) have **higher weight** → prioritized for sampling.
3. Walk from `Scenario(pre)` → `SkillEdge` → `Scenario(post)`, repeating for 2–5 steps.
4. The path is sent to an LLM that translates it into a natural language task instruction.
5. The task is executed by the MemoryAgent; results feed back through the EdgeDecayManager.

**EdgeDecayManager**: Tracks `consecutive_failures` per SkillEdge. ≥5 consecutive failures → confidence × 0.30. confidence < 0.25 → auto-quarantine. Successful executions reset the counter and slowly restore confidence.

---

### 7. Active Refinement

**File**: `app/core/active_refiner.py`

Solves the cold-start problem by proactively discovering reusable patterns from raw task traces.

1. **Scan**: Collects recent TaskTrace records (successful + failed).
2. **Reflect**: Sends trace summaries to an LLM with a structured prompt asking it to identify 1–3 recurring patterns.
3. **Extract**: For each pattern, generates a PMU with `pre_scenario` and `post_scenario`.
4. **Align**: `ScenarioAlignment` checks if semantically identical scenarios already exist in the database. If so, merges features (union, not overwrite) to prevent graph fragmentation.
5. **Gate**: New PMUs pass through RiskDetector + LifecycleManager before entering the CANDIDATE pool.

---

### 8. External Skill Store (Sandbox Smelting)

**File**: `app/core/external_skill_ingestor.py`

External skills (API docs, scripts from marketplaces) are treated as **untrusted prior knowledge**, not ready-to-use tools.

**Four-Pipeline Process**:

| Pipeline | Name | Action |
|----------|------|--------|
| **P1** | Ingestion & Downgrade | LLM parses skill documentation with **dynamic paragraph-level chunking** (not fixed-token windows) → extracts pre/post scenarios → creates QUARANTINE PMU (`source_type=EXTERNAL_DOCUMENT`, `confidence=0.15`) |
| **P2** | Synthetic Task & Sandbox | Generates test tasks from pre_scenario → Shadow Agent executes in sandbox with forced PMU access → anchors EvidenceRecord on success → confidence accumulates |
| **P3** | Graph Fusion & Conflict | Embedding alignment with existing PMUs → ConflictChecker detects functional overlaps → priority-based resolution (external may replace local, or be rejected) |
| **P4** | Promotion & Routing | QUARANTINE → VERIFIED → ACTIVE → Skill → Scenario graph edges created → Main agent can now route through this skill |

**Key principle**: The main agent's retriever only sees ACTIVE PMUs, so external skills remain completely invisible until they pass all four pipelines.

---

### 9. MCP Tool Integration

**File**: `app/tools/mcp_client.py`, `app/graph/graph_builder.py`

Breaks the tool ecosystem silo by making external **Model Context Protocol (MCP)** servers discoverable as native ToolNodes in the metadata graph.

| Component | Role |
|-----------|------|
| `StdioTransport` | JSON-RPC 2.0 over subprocess stdin/stdout (most common MCP transport) |
| `HttpTransport` | HTTP + SSE placeholder for remote MCP servers |
| `MCPClient` | `initialize()` → `list_tools()` → `call_tool()` lifecycle |
| `MCPToolRegistry` | Converts MCP `tools/list` responses into unified `ToolNode` specs |
| `GraphBuilder.register_mcp_tools()` | Discovers all configured MCP servers and registers their tools into the graph |

Each external tool gets a deterministic `node_id` (e.g. `tool/mcp/weather-api/get_forecast`) and can be linked to PMU nodes via `uses_tool` edges — without touching `BaseAgent` or `MemoryAgent` logic.

---

### 10. Trajectory Export (Training Data)

**File**: `app/api/export_api.py`

Converts high-quality ACTIVE PMU execution traces into standard **OpenAI Tool-Calling Message** JSONL format, suitable for SFT fine-tuning or RL (e.g., DeepSeek-R1) training pipelines.

`GET /export/trajectories?format=openai-tool&min_confidence=0.5`

Each output line is a complete `{"messages": [...]}` object containing system prompt, user task, assistant tool calls, tool results (from game verification + evidence), and final output.

---

### 11. Scenario-Guided Next-Step Hints

**File**: `app/agents/memory_agent.py`

Every `MemoryAgent.run()` response now includes a read-only `next_scenarios` field — the system tells the caller **"you are here, these are the paths forward"** without auto-executing anything.

Data source: pure graph traversal on the Scenario-Mediated Skill Graph. After a PMU executes, the system queries `ScenarioMediatedGraphBuilder.get_reachable_skills(post_scenario_id)` to discover which skills (SkillEdges) are reachable from the new environmental state. Each path includes skill name, next-state label, and confidence score.

The caller (Streamlit UI, CLI, or external agent framework like LangChain/CrewAI) decides whether to follow a suggested path — PMU-Graph stays within its governance boundary and never auto-chains execution.

---

### 12. Skill Evaluation Cards

**Files**: `app/core/skill_promoter.py`, `app/core/pmu_schema.py`, `app/db/models.py`

Every PMU promotion to Skill is now audited through a **SkillEvaluationCard** — a multi-dimensional quality snapshot persisted to the `skill_evaluation` table.

**Gate-by-gate audit**:

| Gate | Condition |
|------|-----------|
| `is_active` | PMU status == ACTIVE |
| `success_count_gate` | success_count >= 5 |
| `confidence_gate` | confidence >= 0.85 |
| `risk_gate` | risk_level == LOW |
| `evidence_gate` | >= 1 trusted EvidenceRecord (cross-validated against DB) |

**Score breakdown** mirrors the lifecycle confidence formula: `base_score + success_bonus - failure_penalty - risk_penalty`. An immutable snapshot of the PMU state (status, counts, risk, source) is recorded at evaluation time for full auditability.

New API: `GET /pmus/{pmu_id}/evaluations` returns all evaluation cards for a PMU, enabling historical audit and quality trend analysis.

---

### 13. Retrieval Diagnostics

**Files**: `app/core/environment_retriever.py`, `app/core/pmu_retriever.py`, `app/api/pmu_api.py`

The retrieval pipeline now includes **full PMU text reranking** and per-result **score breakdown**.

**Scoring formula** (updated):

```
score = 0.45 × vec_similarity + 0.25 × confidence + 0.15 × success_rate
      + 0.10 × full_text_score − 0.10 × risk_penalty
```

The `full_text_score` measures Jaccard similarity between the user's environment context and a concatenated PMU text document (name + description + goal + procedure + verification). This captures textual relevance that pure embedding similarity might miss.

Each retrieval result now includes a `score_breakdown` field exposing every component: `vec_similarity`, `confidence`, `success_rate`, `risk_penalty`, `full_text_score`.

New API: `POST /pmus/retrieve` accepts `environment_context` and returns ranked PMUs with full diagnostic breakdowns.

---

## Extension Directions

PMU-Graph is designed with replaceable boundaries at every layer:

| Component | Current | Production Target |
|-----------|---------|-------------------|
| Database | SQLite | PostgreSQL (via SQLAlchemy URL) |
| Graph Store | NetworkX | Neo4j / ArangoDB |
| Embedding | N-gram hash | FAISS / Chroma / Pinecone |
| LLM Backend | MockLLMClient | OpenAI / Claude / local LLM |
| Sandbox | Docker (primary) + subprocess (fallback) | Firecracker microVM |
| Report Generator | Extension point | python-docx |

Each boundary has a single-file abstraction. Replacing the backend requires changing **one file only** — no business logic changes needed.

---

## License

MIT License. See LICENSE file for details.