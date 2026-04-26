# Quickstart — Deploy FowyldAI

Get FowyldAI running and execute your first sovereign inference in under 10 minutes.

## Prerequisites

- Python 3.10+
- Docker (for containerized deployment) or bare-metal Linux/Windows
- 16 GB RAM minimum (32 GB recommended)
- GPU optional but recommended for production workloads

## Step 1: Install

```bash
pip install fowyldai
```

Or pull the Docker image:

```bash
docker pull melhousensolutions/fowyldai:latest
```

## Step 2: Configure

Create a `fowyldai-config.yaml`:

```yaml
engine:
  host: 0.0.0.0
  port: 8400
  workers: 2

models:
  default: sovereign-brain
  path: ./models

security:
  api_key_required: true
  cors_origins: ["https://your-app.com"]
```

## Step 3: Start the Crown Engine

```bash
fowyldai serve --config fowyldai-config.yaml
```

## Step 4: Run Your First Inference

```bash
curl -X POST http://localhost:8400/v1/inference \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{"prompt": "Summarize this document", "model": "sovereign-brain"}'
```

## Next Steps

- [Configure for production](../how-to/index.md)
- [Understand the architecture](../concepts/index.md)
- [API reference](../reference/index.md)
