# Concepts — How FowyldAI Works

Understand FowyldAI's architecture before you configure or deploy it.

---

## Crown Engine

The Crown Engine is the core runtime — a FastAPI server (`src/main.py`) that initializes **115+ subsystems** in a specific startup sequence via an async `lifespan()` context manager.

Startup order includes:

1. **Observability** — logging, metrics collection, Prometheus
2. **Safety** — content filters, guardrails
3. **Compliance** — GDPR, AI Act, SOC 2 checks
4. **Inference Queue** — request queuing and prioritization
5. **Model Registry** — loads `config/models.yaml`, maps models to nodes
6. **Warm Pool** — preloads configured models into GPU VRAM (see `config/warm_pool.yaml`)
7. **Routing** — request-to-model routing logic
8. **Sovereign Brain** — reasoning and planning intelligence
9. **Agent Orchestrator** — multi-step agent and council execution
10. **Peer Mesh** — cluster node discovery, heartbeat, delegation
11. **Gateway** — reverse proxy coordination
12. **Crown Tunnel** — mTLS peering between Crown instances

When all subsystems are ready, the engine logs `fowyldai.startup.READY` and begins accepting requests on port 8400.

---

## Sovereign Brain

The Sovereign Brain is the intelligence layer that handles reasoning, planning, and memory. It provides:

- **`/brain/reason`** — Primary reasoning endpoint, routes to the best available model
- **`/brain/plan`** — Decomposes a goal into sub-tasks
- **`/brain/remember`** — Stores facts in institutional memory (vector store)
- **`/brain/recall`** — Semantic search over stored memories
- **`/brain/compress`** — Extractive summarization for token savings
- **`/brain/cached_answer`** — Checks semantic cache before running inference

The Brain works with the Adaptive Intelligence Agent (AIA) to learn from interactions and improve routing over time.

---

## Model Routing

FowyldAI doesn't use a single model — it routes requests to the **best model for the task**:

1. **`/classify`** — The Scout model (qwen25-1b, 3 GB VRAM) classifies what kind of task is being requested
2. **`/auto`** — Auto-routing uses the classification to pick a reasoning tier:
    - **Light** (`/reason/light`) — phi3-mini: fast, low-VRAM tasks
    - **Deep** (`/reason/deep`) — mistral-7b GPTQ: complex analysis
    - **Heavy** (`/reason/heavy`) — openhermes-7b GPTQ: multi-step reasoning
3. **VRAM-Aware Eviction** — When GPU memory is tight, the warm pool evicts less-used models to make room

The model registry in `config/models.yaml` defines available models, their HuggingFace repos, VRAM requirements, quantization settings, and which cluster node they run on.

---

## Council Protocol

For high-stakes decisions, the Council Protocol runs **multi-model deliberation**:

1. The same prompt is sent to multiple models simultaneously
2. Each model produces an independent response
3. Responses are synthesized, conflicts are identified
4. A consensus answer is produced with confidence scores

Access via `POST /agent/council`.

---

## Editions

FowyldAI ships in two editions:

| Edition | Environment Variable | Description |
| --- | --- | --- |
| **Crown** | `FOWYLD_EDITION=crown` | Full-featured, enterprise subsystems enabled |
| **Ranger** | `FOWYLD_EDITION=ranger` | Lightweight, base product pack |

Set the edition in `.env` or pass it as a Docker build arg.

---

## MCP Server (VS Code Integration)

FowyldAI exposes an **MCP (Model Context Protocol) server** (`src/mcp_server.py`) that integrates with VS Code and other MCP-compatible tools.

The MCP server runs over **stdio** using `FastMCP("FowyldAI")` and exposes 20+ tools:

| Tool | Purpose |
| --- | --- |
| `classify` | Task type classification |
| `reason` | Sovereign reasoning |
| `plan` | Goal decomposition |
| `remember` / `recall` | Memory storage and retrieval |
| `compress` | Token-saving summarization |
| `cached_answer` | Semantic cache lookup |
| `code_review` | Code review analysis |
| `explain_code` | Code explanation |
| `generate_code` | Code generation |
| `write_tests` | Test generation |
| `find_bugs` | Bug detection |
| `refactor` | Refactoring suggestions |
| `commit_message` | Commit message generation |
| `generate_docstring` | Docstring generation |
| `auto_route` | Smart model auto-routing |
| `eval_quality` | Output quality checking |

Each MCP tool is an HTTP proxy that calls the FowyldAI REST API. It connects to `http://127.0.0.1:8400` (local Crown Engine) first, with fallback to a remote endpoint.

---

## Gateway

FowyldAI includes a **mesh-aware reverse proxy** (`gateway/app.py`) — a separate FastAPI application that sits in front of one or more Crown Engine instances.

Two routing modes:

- **FAILOVER** (default) — Routes to on-premise GPU, fails over to a cloud CPU engine
- **COOPERATIVE** — Routes GPU-heavy tasks (image/video) to GPU nodes, CPU tasks to CPU nodes

---

## Peer Mesh

For multi-node deployments, the **Peer Mesh** handles:

- Node discovery and heartbeat (`/peer/heartbeat`)
- Task delegation to remote nodes (`/peer/delegate`)
- Consensus and quorum protocols (`/peer/consensus`, `/peer/quorum`)
- Crown-to-Crown communication via mTLS tunnel (`config/crown_tunnel.yaml`)
