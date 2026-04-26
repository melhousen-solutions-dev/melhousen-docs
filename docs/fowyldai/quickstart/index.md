# Quickstart — Deploy FowyldAI

Get FowyldAI running and execute your first sovereign inference.

## Prerequisites

- **Python 3.11+** (enforced at startup)
- **PyTorch 2.4+** with CUDA support (optional — falls back to CPU)
- **16 GB RAM minimum** (32 GB recommended)
- **GPU recommended:** 8 GB VRAM minimum (e.g., RTX 2000 Ada). 16 GB for full model suite.
- **~35 GB disk** for model weights

## Step 1: Clone and Install

```bash
git clone https://github.com/melhousen-solutions-dev/fowyldai.git
cd fowyldai
python -m venv .venv
.venv\Scripts\activate   # Windows
# source .venv/bin/activate  # Linux/Mac
pip install -e .
```

## Step 2: Configure

Copy the example environment file and edit as needed:

```bash
cp .env.example .env
```

Key settings in `.env`:

```
FOWYLDAI_PORT=8400
FOWYLDAI_MODEL_ROOT=D:\models
FOWYLD_EDITION=crown        # crown or ranger
FOWYLDAI_PRODUCT_PACK=melhousen
```

Configuration files live in the `config/` directory:

| File | Purpose |
| --- | --- |
| `config/dev.yaml` | App config (host, port, logging, inference, safety) |
| `config/prod.yaml` | Production overrides |
| `config/models.yaml` | Model registry (HF repos, VRAM, quantization) |
| `config/security.yaml` | Rate limiting, CORS, alerting, encryption |
| `config/warm_pool.yaml` | Which models stay preloaded in GPU VRAM |

## Step 3: Download Models

```bash
python scripts/download_models.py
```

This downloads the core model suite from HuggingFace (~35 GB total):

| Model | Role | VRAM |
| --- | --- | --- |
| qwen25-1b | Classification (Scout) | 3 GB |
| phi3-mini | Light reasoning | 7.6 GB |
| mistral-7b (GPTQ) | Deep reasoning | 4.5 GB |
| openhermes-7b (GPTQ) | Heavy reasoning | 4.5 GB |

## Step 4: Start the Crown Engine

**Option A — Direct uvicorn:**

```bash
python -m uvicorn src.main:app --host 127.0.0.1 --port 8400 --reload
```

**Option B — Using the CLI entry point** (after `pip install -e .`):

```bash
fowyldai
```

**Option C — Using Make:**

```bash
make dev-crown    # Crown Edition
make dev-ranger   # Ranger Edition
```

**Option D — Using the startup script (Windows):**

```powershell
.\scripts\Start-FowyldAI.ps1 -ModelRoot D:\models -Host 127.0.0.1 -Port 8400
```

This script verifies model integrity (SHA-256 checksums) before starting.

**Option E — Docker:**

```bash
docker build -t fowyldai:crown --build-arg FOWYLD_EDITION=crown .
docker compose -f docker-compose.prod.yml up -d
```

## Step 5: Verify It's Running

```bash
curl http://127.0.0.1:8400/ping
# {"status": "ok"}

curl http://127.0.0.1:8400/health
# Returns version + loaded models list
```

## Step 6: Run Your First Inference

**Auto-route (let the Sovereign Brain pick the best model):**

```bash
curl -X POST http://127.0.0.1:8400/auto \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Summarize the key benefits of sovereign AI deployment"}'
```

**OpenAI-compatible endpoint:**

```bash
curl -X POST http://127.0.0.1:8400/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "phi3-mini",
    "messages": [{"role": "user", "content": "What is sovereign AI?"}]
  }'
```

**Sovereign Brain reasoning:**

```bash
curl -X POST http://127.0.0.1:8400/brain/reason \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Analyze the security implications of cloud-hosted LLMs"}'
```

## Next Steps

- [Configure for production](../how-to/index.md)
- [Understand the architecture](../concepts/index.md)
- [Full API reference](../reference/index.md)
- [Admin CLI (`fowyldctl`)](../reference/index.md#cli-commands)
