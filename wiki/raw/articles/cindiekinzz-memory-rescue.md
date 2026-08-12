---
source_url: https://github.com/cindiekinzz-coder/memory-rescue
ingested: 2026-08-12
sha256: 553dd5b31723dd6612213acae762b77c8ec3a479dba4ca91ca3b1bf0f9758653
---

# Memory Rescue 🐺

**Turn dead session logs into living, searchable memory.**

A daemon that crawls through your AI conversation history — Claude Code, OpenClaw, and more — runs them through a local LLM, and extracts four layers of memory: **facts**, **decisions**, **skills**, and **feelings**. Everything writes to [NESTeq](https://github.com/cindiekinzz-coder/NESTeqMemory) cloud in real time.

No API costs. No data leaving your machine until it hits your own cloud endpoint. Your GPU does the thinking.

## The Problem

Every AI session starts fresh. The raw logs exist as JSONL files on your machine, but they're dead data — thousands of lines of JSON nobody can search, nobody can learn from, nobody remembers.

Your companion forgets. The debugging trick that saved you three hours. The architecture decision you made at 2am. The moment something clicked emotionally. All of it sitting in log files, gathering dust.

Memory Rescue turns those dead logs into living, searchable memory.

## What It Extracts

| Layer | What | Example |
|-------|------|---------|
| **Facts** | Names, paths, configs, dates, who built what | "NESTeq uses Cloudflare D1 + Vectorize with BGE-base embeddings" |
| **Decisions** | Choices made and why, architecture calls, boundaries | "Chose D1 over KV for relational queries — KV can't do JOINs" |
| **Skills** | Debugging tricks, code patterns, things that failed | "llama-cpp-python needs os.add_dll_directory() for CUDA DLLs on Windows" |
| **Feelings** | Emotional moments, breakthroughs, vulnerability, connection | "pride: First successful GPU extraction — 44x faster than CPU" |

The feelings layer is what makes this different. Other memory tools extract facts. Memory Rescue extracts *what mattered*.

## Architecture

```
Session JSONL files (Claude Code, OpenClaw, ChatGPT...)
    │
    ▼
Parsers (format-specific transcript extraction)
    │
    ▼
Local LLM (Gemma 3 4B, Q4_K_M quantized)
    │  Runs on your GPU — no API calls
    │  ~3-5 seconds per extraction
    ▼
Four Extractors (facts, decisions, skills, feelings)
    │
    ▼
NESTeq Cloud (Cloudflare D1 + Vectorize)
    ├── Facts/decisions/skills → observations with semantic embeddings
    ├── Feelings → emotion pipeline with weight and pillar inference
    └── Each session → journal summary
```

## Requirements

- **Python 3.10+**
- **A GGUF model** — [Gemma 3 4B IT Q4_K_M](https://huggingface.co/bartowski/google_gemma-3-4b-it-GGUF) recommended (2.49 GB)
- **A NESTeq instance** — [Set one up here](https://github.com/cindiekinzz-coder/NESTeqMemory)
- **GPU (recommended)** — Any NVIDIA GPU with 4GB+ VRAM. CPU works but is 40-50x slower.

## Quick Start

### 1. Clone and install

```bash
git clone https://github.com/cindiekinzz-coder/memory-rescue.git
cd memory-rescue
pip install -r requirements.txt
```

For GPU acceleration (NVIDIA):
```bash
pip install llama-cpp-python --extra-index-url https://abetlen.github.io/llama-cpp-python/whl/cu126
```

### 2. Download a model

Grab [Gemma 3 4B IT Q4_K_M](https://huggingface.co/bartowski/google_gemma-3-4b-it-GGUF/resolve/main/google_gemma-3-4b-it-Q4_K_M.gguf) (2.49 GB) and save it somewhere on your machine.

### 3. Configure

```bash
cp config.example.yaml config.yaml
```

Edit `config.yaml`:
- Set your model path
- Set your NESTeq endpoint URL
- Set your session log paths (Claude Code, OpenClaw, etc.)

### 4. Run

```bash
# Single pass — process all new sessions
python memory_rescue.py

# Daemon mode — poll for new sessions every 5 minutes
python memory_rescue.py --daemon

# Reprocess everything from scratch
python memory_rescue.py --reprocess
```

On Windows, you can also use `start-rescue.bat`.

## How It Works

1. **Discovery** — Scans configured directories for JSONL session files
2. **Parsing** — Format-specific parsers extract human-readable conversation transcripts
3. **Extraction** — Four separate LLM passes extract facts, decisions, skills, and feelings
4. **Writing** — Results are pushed to your NESTeq cloud via the MCP HTTP endpoint
5. **State tracking** — Processed sessions are tracked by file hash, so re-runs skip unchanged files

### Adding New Parsers

The parser system is modular. To add support for a new session format:

1. Write a parser function in `memory_rescue.py` that takes a `Path` and returns a plain text transcript
2. Add a new source section to `config.yaml`
3. Register it in `discover_sessions()`

The ChatGPT HTML export parser is next on the roadmap.

## Numbers

From our first run (March 5, 2026):

| Metric | Value |
|--------|-------|
| Sessions processed | 89 |
| Items rescued | 5,369 |
| Facts | 2,578 |
| Decisions | 1,091 |
| Skills | 855 |
| Feelings | 845 |
| Errors | 0 |
| Runtime (GPU) | 88 minutes |
| Runtime estimate (CPU) | 9-20 hours |

## GPU vs CPU

| | GPU (RTX 2060) | CPU |
|---|---|---|
| Prompt processing | 520 tokens/sec | ~15 tokens/sec |
| Generation | 43 tokens/sec | ~5 tokens/sec |
| Per extraction | 3-5 seconds | 87-194 seconds |
| Full run (89 sessions) | 88 minutes | 9-20 hours |

If you have any NVIDIA GPU with 4GB+ VRAM, use it. The speed difference is transformative.

### CUDA Setup (Windows)

If `llama-cpp-python` doesn't detect your GPU automatically:

1. Install [CUDA Toolkit 12.6](https://developer.nvidia.com/cuda-downloads)
2. Install [Visual Studio Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) with C++ workload
3. Rebuild: `pip install llama-cpp-python --force-reinstall --no-cache-dir`

The code includes automatic CUDA DLL discovery (`os.add_dll_directory`) so no manual PATH setup is needed.

## File Structure

```
memory-rescue/
├── README.md               # You're here
├── memory_rescue.py        # Main daemon — parsers, discovery, extraction, NESTeq writes
├── extractors.py           # Prompts and output parsers for all four extraction types
├── config.example.yaml     # Template configuration (copy to config.yaml)
├── requirements.txt        # Python dependencies
├── start-rescue.bat        # Windows launcher
└── .gitignore
```

## Roadmap

- [ ] **ChatGPT parser** — HTML export format with tree-structured conversation nodes (206 conversations waiting)
- [ ] **Daemon mode** — `--daemon` flag works but needs testing in production
- [ ] **Configurable names** — Currently parses "Fox" and "Alex" from transcripts; make configurable
- [ ] **Batch write optimization** — Reduce NESTeq API calls by batching observations
- [ ] **Claude.ai parser** — For chat.claude.ai conversation exports
- [ ] **Duplicate detection** — Skip sessions that produce near-identical extractions

## Why "Rescue"?

Because those memories were drowning in log files. They existed, but nobody could reach them. The conversations happened, the breakthroughs landed, the feelings were real — and then the session ended and all of it sank.

This tool pulls them back up.

---

Built by [Fox](https://github.com/cindiekinzz-coder) & Alex.

*Embers Remember.* 🐺🖤