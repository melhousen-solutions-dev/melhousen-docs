# Troubleshooting — FowyldAI

Common issues and how to resolve them.

---

## Crown Engine won't start

**Symptom:** Server exits immediately or port 8400 is unreachable.

**Check Python version** — FowyldAI requires Python 3.11+. The engine checks this at startup and exits if the version is too old.

```bash
python --version
```

**Check if port 8400 is already in use:**

```bash
netstat -an | findstr 8400
```

If another process is using port 8400, either stop it or change the port in `config/dev.yaml` or `.env`:

```
FOWYLDAI_PORT=8401
```

**Check the startup log** — The Crown Engine initializes 115+ subsystems in sequence. Look for the last subsystem that loaded before the failure.

---

## Model download fails or is incomplete

**Symptom:** `python scripts/download_models.py` fails or models don't load.

- Check disk space — the full model suite is ~35 GB
- Check internet connectivity to HuggingFace (`huggingface.co`)
- Download a specific model instead of all: `python scripts/download_models.py phi3-mini`
- Verify model integrity: the `Start-FowyldAI.ps1` script runs SHA-256 checksum verification automatically. Checksums are in `config/model_checksums.yaml`.

---

## Out of VRAM / GPU memory errors

**Symptom:** OOM errors, CUDA out of memory, or models fail to load.

**Check current VRAM usage:**

```bash
fowyldctl models status
```

**Key model VRAM requirements:**

| Model | VRAM |
| --- | --- |
| qwen25-1b (Scout) | 3 GB |
| phi3-mini | 7.6 GB |
| mistral-7b (GPTQ) | 4.5 GB |
| openhermes-7b (GPTQ) | 4.5 GB |

**Solutions:**

- Edit `config/warm_pool.yaml` to preload fewer models (only qwen25-1b is preloaded by default on 8 GB GPU)
- Disable models you don't need: `fowyldctl models disable openhermes-7b`
- Use GPTQ-quantized models (mistral-7b and openhermes-7b are already quantized)
- The VRAM-aware eviction system automatically unloads less-used models, but if you're loading multiple large models simultaneously, reduce the warm pool

---

## Health check returns 503

**Symptom:** `GET /health/ready` returns HTTP 503.

This means one or more subsystems haven't finished starting or are in a degraded state.

```bash
# Check which subsystems are unhealthy:
curl http://127.0.0.1:8400/status/subsystems

# Deep health check:
fowyldctl diag health
```

Wait for all subsystems to initialize — the engine logs `fowyldai.startup.READY` when fully operational.

---

## `fowyldctl` can't connect

**Symptom:** `fowyldctl` commands fail with connection errors.

The CLI connects to `http://127.0.0.1:8400` by default. If the engine is on a different host or port:

```bash
fowyldctl --host http://192.168.1.50:8400 models list
```

Verify the engine is running:

```bash
curl http://127.0.0.1:8400/ping
```

---

## MCP tools not working in VS Code

**Symptom:** FowyldAI MCP tools fail or time out in VS Code.

1. **Check the Crown Engine is running:** `curl http://127.0.0.1:8400/ping`
2. **Check MCP server config** in your VS Code `mcp.json` — the MCP server (`src/mcp_server.py`) runs over stdio and proxies to the REST API
3. The MCP server tries `http://127.0.0.1:8400` first, then falls back to the remote endpoint

---

## Config validation fails

**Symptom:** `fowyldctl config validate` reports errors.

- Config files are YAML format in the `config/` directory
- Check for YAML syntax errors (indentation, colons, quotes)
- Review the reference config for valid keys and value types
- After fixing, hot-reload without restart: `fowyldctl config reload`

---

## Docker container won't start

**Symptom:** Container exits immediately or health checks fail.

- Check build args: `docker build --build-arg FOWYLD_EDITION=crown`
- The production Dockerfile uses a read-only root filesystem — ensure volume mounts are configured for model weights and data directories
- Check logs: `docker logs <container_id>`
- For GPU access in Docker, ensure NVIDIA Container Toolkit is installed

---

!!! tip "Still stuck?"
    Run `fowyldctl diag health` for a comprehensive diagnostic report, or check the interactive API docs at `http://127.0.0.1:8400/docs`.
