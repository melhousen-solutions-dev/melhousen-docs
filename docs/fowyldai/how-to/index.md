# How-To Guides — Administer & Configure FowyldAI

Task-based guides for administrators, operators, and integrators.

---

## Set Up with the Guided Wizard

The fastest way to configure a new FowyldAI instance:

```bash
fowyldctl setup
```

This walks you through host, port, model selection, safety policies, and security settings.

---

## Manage Models

### List available models

```bash
fowyldctl models list
```

### Check model load status and VRAM usage

```bash
fowyldctl models status
```

### Enable or disable a model

```bash
fowyldctl models enable mistral-7b
fowyldctl models disable gemma-2b
```

### Download a specific model

```bash
python scripts/download_models.py phi3-mini
```

### View model families

```bash
fowyldctl models families
```

Model configuration lives in `config/models.yaml`. Each entry defines the HuggingFace repo, local directory, VRAM requirement, quantization, and cluster node assignment.

---

## Configure Security

### View current security settings

```bash
fowyldctl security show
```

### Set rate limiting

```bash
fowyldctl security rate-limit --rpm 100 --burst 20
```

### Configure CORS origins

```bash
fowyldctl security cors --origins "https://your-app.com,https://admin.your-app.com"
```

### Run a security audit

```bash
fowyldctl security audit
```

Security configuration lives in `config/security.yaml`.

---

## Configure Safety Policies

### View current safety settings

```bash
fowyldctl safety show
```

### Update safety policy

```bash
fowyldctl safety set-policy --policy strict
```

### Test safety filters

```bash
fowyldctl safety test --input "Test content here"
```

---

## Monitor Health

### Quick liveness check

```bash
curl http://127.0.0.1:8400/ping
```

### Full health status

```bash
curl http://127.0.0.1:8400/health
```

### Deep readiness probe (for K8s)

```bash
curl http://127.0.0.1:8400/health/ready
```

### Standalone health check script

```bash
python scripts/healthcheck.py --host http://127.0.0.1:8400 --verbose
```

### View metrics

```bash
curl http://127.0.0.1:8400/metrics
# Or Prometheus format:
curl http://127.0.0.1:8400/metrics/prometheus
```

### Check all subsystem status

```bash
curl http://127.0.0.1:8400/status/subsystems
```

---

## Manage Configuration

### View current config

```bash
fowyldctl config show
```

### Validate config files

```bash
fowyldctl config validate
```

### Hot-reload config (no restart required)

```bash
fowyldctl config reload
```

### Update a specific setting

```bash
fowyldctl config set inference.max_tokens 4096
```

---

## Manage Storage

### Check disk usage

```bash
fowyldctl storage status
```

### Set disk budget

```bash
fowyldctl storage set-budget --max-gb 100
```

Storage budgets are enforced via `config/storage.yaml`.

---

## Run Diagnostics

### View cluster topology

```bash
fowyldctl diag topology
```

### Deep health check across all subsystems

```bash
fowyldctl diag health
```

### View delegation history

```bash
fowyldctl diag delegation-log
```

---

## Deploy with Docker (Production)

```bash
# Build the Crown Edition image
docker build -t fowyldai:crown --build-arg FOWYLD_EDITION=crown .

# Start the production stack
docker compose -f docker-compose.prod.yml up -d
```

The production Docker configuration runs with:

- Read-only root filesystem
- All Linux capabilities dropped
- Non-root user
- No-new-privileges flag
- Port 8400 bound to 127.0.0.1 only (use the gateway for external access)

---

## Set Up Peer Mesh (Multi-Node)

### Check mesh status

```bash
fowyldctl mesh status
```

### Add a peer node

```bash
fowyldctl mesh add-peer --host 192.168.1.50 --port 8400
```

### List peers

```bash
fowyldctl mesh list-peers
```

Cluster configuration lives in `config/clustering.yaml`. Crown-to-Crown mTLS peering is configured in `config/crown_tunnel.yaml`.
