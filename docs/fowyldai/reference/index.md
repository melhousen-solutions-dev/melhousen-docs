# Reference — API & Configuration

Technical reference for FowyldAI APIs, CLI, and configuration.

## Core API Endpoints

### Health & Status

| Endpoint | Method | Description |
| --- | --- | --- |
| `/ping` | GET | Lightweight liveness check — returns `{"status": "ok"}` |
| `/health` | GET | Version + loaded model list |
| `/health/ready` | GET | Deep readiness probe (K8s) — returns 503 if degraded |
| `/health/live` | GET | Liveness probe (PID check) |
| `/deployment/info` | GET | Environment, tier, GPU info |
| `/metrics` | GET | Uptime, request count, latency, GPU stats |
| `/metrics/prometheus` | GET | Prometheus scrape format |
| `/status/subsystems` | GET | All subsystem health |

### Inference & Reasoning

| Endpoint | Method | Description |
| --- | --- | --- |
| `/auto` | POST | Auto-route to best model via Sovereign Brain |
| `/classify` | POST | Task type classification |
| `/reason/light` | POST | phi3-mini reasoning |
| `/reason/deep` | POST | mistral-7b reasoning |
| `/reason/heavy` | POST | openhermes-7b reasoning |
| `/reason/light/stream` | POST | SSE streaming (phi3) |
| `/reason/deep/stream` | POST | SSE streaming (mistral) |
| `/reason/heavy/stream` | POST | SSE streaming (openhermes) |
| `/image` | POST | Image generation (SD 1.5/SDXL) |

### OpenAI-Compatible (`/v1`)

Drop-in replacements for OpenAI API calls:

| Endpoint | Method | Description |
| --- | --- | --- |
| `/v1/chat/completions` | POST | Chat completions |
| `/v1/embeddings` | POST | Embeddings |
| `/v1/models` | GET | Model listing |
| `/v1/images/generations` | POST | Image generation |
| `/v1/summarize` | POST | Summarization |
| `/v1/estimate` | POST | Token estimation |

### Sovereign Brain

| Endpoint | Method | Description |
| --- | --- | --- |
| `/brain/reason` | POST | Primary intelligence endpoint |
| `/brain/plan` | POST | Goal decomposition |
| `/brain/remember` | POST | Store to institutional memory |
| `/brain/recall` | POST | Semantic memory search |
| `/brain/compress` | POST | Extractive summarization |
| `/brain/cached_answer` | POST | Semantic cache lookup |

### Agent System

| Endpoint | Method | Description |
| --- | --- | --- |
| `/agent/run` | POST | Agent execution |
| `/agent/council` | POST | Multi-model deliberation |
| `/agent/tools` | GET | Available tools list |
| `/v1/agent/run` | POST | Governed multi-step agent run |

### Enterprise

| Prefix | Description |
| --- | --- |
| `/compliance/*` | GDPR, AI Act, SOC 2, FTC compliance checks |
| `/billing/*` | Usage metering, tier status |
| `/admin/*` | SOC2 readiness, incident runbook |
| `/auth/sso/*` | SSO login, callback, userinfo |
| `/knowledge/*` | Document scan, query, stats |
| `/research/*` | Web search, deep research |
| `/batch/*` | Batch job scheduler |
| `/stream/*` | SSE event subscriptions |
| `/interop/*` | MCP server/client, A2A protocol |
| `/peer/*` | Cluster heartbeat, delegation, consensus |

!!! info "Interactive API docs"
    Swagger UI is available at `http://127.0.0.1:8400/docs` and ReDoc at `http://127.0.0.1:8400/redoc` when the Crown Engine is running in dev mode.

---

## Configuration Files

All configs live in the `config/` directory (YAML format):

| File | Purpose |
| --- | --- |
| `config/dev.yaml` | App config: host, port, logging, inference, safety, brain |
| `config/prod.yaml` | Production overrides |
| `config/models.yaml` | Model registry: HF repos, VRAM requirements, quantization, node assignment |
| `config/warm_pool.yaml` | Models preloaded in GPU VRAM at startup |
| `config/security.yaml` | Rate limiting, CORS origins, alerting, encryption |
| `config/billing.yaml` | Metering and tier definitions |
| `config/gateway.yaml` | Gateway routing rules |
| `config/clustering.yaml` | Multi-node cluster config |
| `config/sovereign_brain.yaml` | Brain reasoning configuration |
| `config/doctrine.yaml` | Governance doctrine rules |
| `config/retention.yaml` | Data lifecycle and purge policies |
| `config/storage.yaml` | Disk budget enforcement |
| `config/trade.yaml` | Trading Post (cloud AI provider fallback) |
| `config/embeddings.yaml` | Embedding model config |

Environment variables are loaded from `.env` (see `.env.example` for all options).

---

## CLI Commands

### `fowyldai`

Starts the Crown Engine server. Equivalent to `python -m uvicorn src.main:app`.

### `fowyldctl` — Customer Admin CLI

```bash
fowyldctl [--host http://127.0.0.1:8400] [--format text|json] [--quiet] <command>
```

| Command | Description |
| --- | --- |
| `fowyldctl setup` | Guided setup wizard |
| `fowyldctl models list` | List available models |
| `fowyldctl models status` | Model load status and VRAM usage |
| `fowyldctl models enable <name>` | Enable a model |
| `fowyldctl models disable <name>` | Disable a model |
| `fowyldctl models families` | Show model family intelligence brief |
| `fowyldctl config show` | Display current configuration |
| `fowyldctl config validate` | Validate config files |
| `fowyldctl config reload` | Hot-reload configuration |
| `fowyldctl config set <key> <value>` | Update a config value |
| `fowyldctl security show` | Display security config |
| `fowyldctl security rate-limit` | Configure rate limiting |
| `fowyldctl security cors` | Configure CORS |
| `fowyldctl security audit` | Run security audit |
| `fowyldctl storage status` | Disk usage and budget |
| `fowyldctl storage set-budget` | Set disk budget |
| `fowyldctl safety show` | Safety policy status |
| `fowyldctl safety set-policy` | Update safety policy |
| `fowyldctl safety test` | Test safety filters |
| `fowyldctl diag topology` | Show cluster topology |
| `fowyldctl diag health` | Deep health check |
| `fowyldctl diag delegation-log` | View delegation history |
| `fowyldctl mesh status` | Cluster mesh status |
| `fowyldctl mesh add-peer` | Add a peer node |
| `fowyldctl mesh list-peers` | List peer nodes |
| `fowyldctl compliance` | Compliance operations |

### `fowyld-admin`

Internal admin operations script (`scripts/fowyld_admin.py`).

---

## Model Registry

Core models configured in `config/models.yaml`:

| Model | Provider | Role | VRAM | Quantization |
| --- | --- | --- | --- | --- |
| qwen25-1b | Alibaba | Classification (Scout) | 3 GB | — |
| phi3-mini | Microsoft | Light reasoning | 7.6 GB | — |
| mistral-7b | Mistral AI | Deep reasoning | 4.5 GB | GPTQ |
| openhermes-7b | Nous Research | Heavy reasoning | 4.5 GB | GPTQ |
| llama31-8b | Meta | Sovereign reasoning | 16 GB | — |
| gemma-2b | Google | Fast reasoning | — | — |
| embedding | sentence-transformers | RAG embeddings | — | — |
| sd15 / sdxl | Stability AI | Image generation | — | — |

Download models: `python scripts/download_models.py [model_name]`
