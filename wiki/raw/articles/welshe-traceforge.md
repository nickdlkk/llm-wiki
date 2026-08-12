---
source_url: https://github.com/welshe/TraceForge
ingested: 2026-08-12
sha256: ed306f7ab9dae42f1aacaa0218e57acb5a675a0ad920be812b99fbccac24b9aa
---

# TraceForge

Every time a developer manually fixes something their AI agent couldn't, that knowledge vanishes. TraceForge captures those moments, distills them into structured skills, and injects them back into the agent so it never makes the same mistake twice.

**Native Dataset Generation for DeepSeek-VL2**: TraceForge doubles as a synthetic data engine for training the next generation of vision-language models. Every debugging session becomes a structured training sample: screen frames paired with normalized coordinates, contrastive failure/success traces, and chain-of-thought reasoning. In DeepSeek mode, the pipeline outputs coordinate-annotated traces in the exact `[0, 999]` format DeepSeek-VL2 expects, making it a direct feed into fine-tuning pipelines.

**Windows-Agent Alignment**: This is the first tool that treats Windows system state as a primary training signal for VLMs. Win32 process exit codes, CPU-time hang detection, keyboard/mouse hooks, active window rects, and PowerShell command traces are all first-class citizens in the data model. Every trace is grounded in actual OS events, not synthetic simulations.

## How It Works

TraceForge runs a Capture-Distill-Inject loop:

**Capture** - A rolling 60s screen buffer, Win32 keyboard/mouse hooks, and a process watcher that detects both crashes and terminal silence (hangs). Frames are cropped to the active window via `GetForegroundWindow` and downsampled to keep VLM token pressure low. Fires when a human takes over from an AI, when a terminal error is detected, or manually.

**Distill** - Sends the captured frames and terminal log to a local VLM via Ollama. Supports Qwen-2.5-VL (default) and DeepSeek-VL2 for advanced reasoning traces with coordinate-annotated visual anchors. The model identifies the trigger error, the exact fix command, and the reasoning behind it. A ReasoningValidator cross-checks the VLM output against actual process events and input logs to flag hallucinations.

**Inject** - Wraps the trace into a `.skill.md` file with OpenClaw-compatible metadata, writes it to `.claw/skills/`, and optionally publishes to ClawHub. Includes a contrastive analysis that diffs the AI's failed attempt against the human's fix, and generates negative traces for on-policy distillation.

**Reinforce** - Before distilling, checks a LanceDB vector store for existing fixes. High-confidence matches short-circuit the pipeline entirely. Search results use recency-biased re-ranking so newer fixes have a slight edge over stale ones.

## Stack

| Layer | Tool | Why |
|---|---|---|
| Screen capture | mss + Pillow | Fast rolling buffer, crops to active window via GetForegroundWindow |
| System hooks | ctypes (Win32) | Low-level keyboard/mouse/process monitoring without external deps |
| VLM | Ollama (Qwen-2.5-VL / DeepSeek-VL2) | Local vision-to-text inference, no data leaves the machine |
| Coordinates | utils.py | Pixel-to-DeepSeek [0, 999] normalization with window-relative anchoring |
| Vector DB | LanceDB | Stores traces with embeddings, supports vector + metadata search |
| CLI | Click + Rich | Clean command interface with formatted output |
| Models | Pydantic v2 | Validated data structures throughout the pipeline |

## Install

```
pip install -e ".[dev]"
```

Requires Python 3.12+, Windows, and Ollama running locally with a vision model:

```
ollama pull qwen2.5-vl:7b
```

## Usage

```bash
# Check that Ollama and LanceDB are reachable
traceforge health

# Record a session. Ctrl+C to stop and distill.
traceforge record

# Record with a specific trigger type
traceforge record -t human_takeover

# Record while tailing the terminal window's log file for output context
traceforge record --terminal-log C:\logs\session.log

# Process a terminal log directly (no screen capture)
traceforge process "npm ERR! code EBUSY" --ai-attempt "npm install"

# Generate a negative trace (on-policy distillation pair)
traceforge negative "npm ERR! code EBUSY" \
  --ai-command "npm install" \
  --ai-thought "Dependencies are corrupted" \
  --terminal "npm ERR! syscall rename" \
  --human-fix "Stop-Process -Name node -Force" \
  --human-reasoning "Port locked by zombie process"

# Search trace memory for similar errors
traceforge lookup "EBUSY"

# Count stored traces
traceforge stats
```

### Triggers

`record` stops automatically when one of these fires:

- **terminal_error** - a watched process exits with a non-zero code (or a code in `error_codes`).
- **human_takeover** - a process hang is detected, then the user starts typing within `takeover_window_seconds`. Requires keyboard hooks enabled. Based on the heuristic that when an AI stalls and a human takes over, the user begins typing within seconds.
- **manual** - Ctrl+C, or `-t` on the command line.

### Terminal output

By default a recording session has no terminal context (the pipeline only sees screenshots). Point `record --terminal-log <file>` at the log file of the session you're watching and TraceForge tails it during capture, so the VLM gets real terminal output. SDK users can also call `recorder.set_terminal_log(text)` before `capture_bundle`.

### Hang monitoring

The process watcher polls system processes once per second to detect crashes (exit codes) and hangs (CPU silence beyond `silence_threshold`). Exit detection requires enumerating all processes, but CPU/hang tracking can be scoped with `[capture.hooks] watch_processes = ["node.exe", "npm.exe"]` to cut polling overhead in half, at the cost of hang detection being limited to those processes.

## VLM Modes

TraceForge ships with two VLM presets. Set `mode` in `[distill]` to switch:

- **qwen** (default) - `qwen2.5-vl:7b`. Good balance of speed and accuracy for screen analysis.
- **deepseek** - `deepseek-vl2:16b`. Chain-of-thought reasoning, visual anchor coordinates, higher accuracy on complex debugging traces. Requires more VRAM.

You can also set `model = "custom:tag"` directly to override the preset entirely.

Pull the model before use:

```
ollama pull qwen2.5-vl:7b
ollama pull deepseek-vl2:16b
```

## Configuration

All settings live in `config/default.toml`:

```toml
[capture]
buffer_seconds = 60
fps = 2

[capture.hooks]
silence_threshold = 10.0
watch_processes = []          # empty = watch all processes for hangs

[capture.triggers]
human_takeover = true
terminal_error = true
takeover_window_seconds = 30.0
min_takeover_keystrokes = 2

[capture.screen]
max_width = 1280
crop_to_active_window = true

[distill]
mode = "qwen"
ollama_url = "http://localhost:11434"
max_retries = 3
backoff_base_seconds = 1.0   # exponential backoff + jitter between VLM retries
backoff_max_seconds = 10.0

[distill.models]
qwen = "qwen2.5-vl:7b"
deepseek = "deepseek-vl2:16b"

[memory.rerank]
recency_weight = 0.15
half_life_days = 90.0
```

## Project Structure

```
src/traceforge/
  capture/
    hooks.py          Win32 keyboard/mouse hooks, process watcher with hang detection
    screen.py         Rolling buffer, active-window crop, frame downsampling
    recorder.py       Orchestrates capture into CaptureBundle
  distill/
    vlm.py            Ollama client with mode-aware inference params
    prompt.py         Mode-specific prompt templates (Qwen flat, DeepSeek chain-of-thought)
    trace.py          Parses VLM output, extracts visual anchors
    validator.py      Cross-checks VLM reasoning against captured events (hallucination detection)
  inject/
    skill.py          Generates .skill.md files with OpenClaw metadata
    clawhub.py        Publish/search on ClawHub API
    reinforcement.py  Checks trace memory before generic fixes
  memory/
    lancedb_store.py  Vector DB with recency-biased re-ranking
    contrastive.py    AI-attempt vs human-fix diff analysis with thought chain extraction
    negative.py       On-policy negative trace generator (paired failure/success traces)
  models/types.py     Pydantic models (Trace, FailureBundle, NegativeTrace, VisualAnchor, etc.)
  utils.py            DeepSeek coordinate normalization, window bounds, pixel-to-[0,999] conversion
  config.py           TOML config loader with mode preset resolution
  pipeline.py         Capture-Distill-Inject orchestrator with validation and negative trace support
  cli.py              Click CLI entrypoint
```

## License

MIT