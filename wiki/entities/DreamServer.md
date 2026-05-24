---
title: DreamServer
type: entity
created: 2026-05-24
updated: 2026-05-24
sources:
  - https://github.com/Light-Heart-Labs/DreamServer
tags:
  - local-ai
  - self-hosted
  - llm
  - docker
  - ai-stack
  - voice
  - rag
  - workflow-automation
---

# DreamServer

Local-first AI full-stack platform — LLM inference, chat UI, voice (STT/TTS), agents, workflows, RAG, and image generation. One-command install, auto-detects GPU, bootstraps in <2 min, hot-swaps model. No cloud, no subscriptions. Apache 2.0, Shell + Docker. 1660 stars.

## Core Philosophy

DreamServer's mission: make local AI sovereign. One command deploys a complete AI stack on local hardware — inference, chat, agents, voice, RAG, image generation, workflow automation. No API costs, no data leaves the machine.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     DreamServer Stack                        │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │  Hermes  │  │  Open    │  │   n8n    │  │  ComfyUI │  │
│  │  Agent   │  │  WebUI   │  │ Workflow │  │  Image   │  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘  │
│  ┌────┴─────────────┴─────────────┴─────────────┴────┐    │
│  │              LiteLLM (API Gateway)                 │    │
│  │     local / cloud / hybrid modes                   │    │
│  └────┬────────────────┬──────────────────┬──────────┘    │
│  ┌────┴────┐  ┌───────┴────┐  ┌────────┴──────────┐     │
│  │llama-   │  │ Whisper   │  │  TEI Embeddings   │     │
│  │server   │  │   (STT)   │  │    + Qdrant       │     │
│  │(llama.cpp)  │ Kokoro    │  │     (RAG)        │     │
│  │         │  │   (TTS)   │  │                   │     │
│  └────┬────┘  └───────────┘  └───────────────────┘     │
│  ┌────┴───────────────────────────────────────────┐    │
│  │          GPU: NVIDIA / AMD / Apple Silicon / Intel Arc │ │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

## Service Stack

### Chat & Inference
- **Open WebUI** — chat interface with conversation history, web search, document upload, 30+ languages
- **llama-server** — llama.cpp inference, continuous batching, GPU-accelerated
- **LiteLLM** — API gateway, local/cloud/hybrid mode switching
- **TEI** — text embedding for RAG and search

### Voice
- **Whisper** — speech-to-text
- **Kokoro** — text-to-speech

### Agents & Automation
- **Hermes Agent** — default local-first autonomous/browser agent (memory, skills, magic-link proxy)
- **n8n** — workflow automation, 400+ integrations (Slack, email, databases, APIs)
- **APE** — Agent Policy Engine for auditing autonomous tool calls
- **OpenCode** — browser-based AI coding assistant

### Knowledge & Search
- **Qdrant** — vector database for RAG
- **SearXNG** — self-hosted privacy-respecting web search
- **Perplexica** — deep research engine

### Creative & Privacy
- **ComfyUI** — node-based image generation
- **Privacy Shield** — PII scrubbing proxy for API calls
- **Token Spy** — token usage monitor
- **Langfuse** — optional LLM observability/tracing

## Hardware Auto-Detection

The installer detects GPU and assigns a deterministic hardware tier, then picks the best GGUF model for the detected memory envelope.

### NVIDIA Tiers

| Tier | GPU RAM | Default Model | Context | Hardware |
|------|--------|--------------|---------|----------|
| T0 | 8 GB CPU | Qwen3.5 2B | 8K | CPU fallback |
| T1 | 8 GB | Qwen3.5 9B | 32K | RTX 4060, RTX 3060 12GB |
| T2 | 12 GB | Phi-4 14B | 16K | RTX 4070-class |
| T3 | 24 GB | Qwen3.5 27B | 32K | RTX 4090, A6000 |
| T4 | 48 GB | DeepSeek R1 Distill Llama 70B | 32K | A6000 Ada, L40S |
| NV_ULTRA | 90+ GB | Qwen3 Coder Next | 128K | Multi-GPU A100/H100 |

### AMD Strix Halo (Unified Memory)

| Tier | Unified RAM | Default Model | Context |
|------|-----------|--------------|---------|
| SH_COMPACT | 64 GB | Qwen3.6 35B-A3B | 128K |
| SH_LARGE | 96 GB | DeepSeek R1 Distill Llama 70B | 32K |

### Apple Silicon (Metal)

| Tier | Unified RAM | Default Model | Context |
|------|-----------|--------------|---------|
| M1 | 8 GB | Phi-4 Mini | 128K |
| M2 | 16 GB | Qwen3.5 9B | 32K |
| M3 | 32 GB | Phi-4 14B | 16K |
| M4 | 48 GB | Qwen3.5 27B | 32K |
| M5+ | 64+ GB | Qwen3.6 35B-A3B | 128K |

Model selection uses `dream-server/scripts/select-model.py` reading `dream-server/config/model-library.json`. Override with `MODEL_PROFILE=gemma4` or `MODEL_PROFILE=auto`.

## Bootstrap Mode

Default behavior — no waiting for large downloads:

1. Downloads a 1.5B model in under 1 minute
2. User starts chatting immediately
3. Full model downloads in background
4. Hot-swap to full model with zero downtime

Skip bootstrap: `./install.sh --no-bootstrap`

## dream CLI

```bash
# Status & management
dream status              # Health checks + GPU status
dream list                # All services and their state
dream logs llm            # Tail logs
dream restart [service]   # Restart one or all
dream start / stop        # Stack control

# Model switching
dream model current       # What's running?
dream model list          # Available tiers
dream model swap T3       # Switch tier

# Mode switching
dream mode cloud         # LiteLLM → cloud APIs
dream mode local         # Local inference
dream mode hybrid        # Local primary, cloud fallback

# Extensions
dream enable n8n         # Enable service
dream disable whisper     # Disable service
dream list               # See everything

# Config
dream config show        # View .env (secrets masked)
dream preset save gaming  # Snapshot config
dream preset load gaming  # Restore
```

## Extension System

Every service is an extension — folder with `manifest.yaml` + `compose.yaml`:

```
extensions/services/
  my-service/
    manifest.yaml      # name, port, health endpoint, GPU backends
    compose.yaml       # Docker Compose fragment (auto-merged)
```

```bash
dream enable my-service     # Enable
dream disable my-service   # Disable
```

The installer itself is modular: 19 library modules, shared service registry, 13 ordered phases.

## Deployment

```bash
# Linux/macOS (one-liner)
curl -fsSL https://raw.githubusercontent.com/Light-Heart-Labs/DreamServer/main/dream-server/get-dream-server.sh | bash

# Cloud mode (no GPU)
./install.sh --cloud

# Manual
git clone https://github.com/Light-Heart-Labs/DreamServer.git
cd DreamServer/dream-server
./install.sh

# Windows
# Requires Docker Desktop with WSL2
powershell -ExecutionPolicy Bypass -Command "git clone https://github.com/Light-Heart-Labs/DreamServer.git; cd DreamServer; .\install.ps1"
```

Open http://localhost:3000 (Open WebUI). llm-server API: `localhost:11434` (Linux Docker) or `localhost:8080` (macOS/Windows native).

## Comparison

| | DreamServer | Ollama + Open WebUI | LocalAI |
|---|:---:|:---:|:---:|
| Scope | Full AI stack | LLM + chat | LLM only |
| One-command install | Everything | LLM + chat only | LLM only |
| Hardware auto-detect | All GPUs | No | No |
| Agents | Hermes + OpenCode | No | No |
| Workflow automation | n8n | No | No |
| Voice (STT+TTS) | Whisper + Kokoro | No | No |
| Image generation | ComfyUI | No | No |
| RAG pipeline | Qdrant | No | No |
| Extension system | Manifest-based | No | No |

## Key Engineering Decisions

1. **llama-server over Ollama** — llama.cpp server mode gives more control over batching, context, and GPU utilization
2. **LiteLLM as gateway** — unified API surface for local/cloud/hybrid mode switching without changing client code
3. **Docker-only services** — all services except llama-server (macOS/Metal) run in Docker for portability
4. **Extension manifest schema** — `manifest.yaml` + `compose.yaml` convention enables dashboard, CLI, health checks to discover services automatically
5. **Bootstrap model at 64K** — Hermes requires 64K minimum context; bootstrap model ensures agent works during full model download
6. **Tier-based model selection** — deterministic mapping from GPU VRAM/RAM to GGUF model and context size

## Meta

- **License**: Apache 2.0
- **Language**: Shell + Docker + Python (dashboard-api)
- **Stars**: 1660 (as of 2026-05-24)
- **Created**: 2026-02-09
- **Platforms**: Linux (NVIDIA/AMD/Intel Arc), Windows (NVIDIA/AMD via WSL2), macOS (Apple Silicon)
