# Reference — API & Configuration

Technical reference for FowyldAI APIs, CLI, and configuration.

## API Endpoints

| Endpoint | Method | Description |
| --- | --- | --- |
| `/health` | GET | Health check |
| `/v1/inference` | POST | Run inference |
| `/v1/models` | GET | List available models |
| `/v1/council/invoke` | POST | Invoke council workflow |
| `/v1/memory/query` | POST | Query memory store |

## Configuration Reference

See the `fowyldai-config.reference.yaml` file included with your installation for the complete configuration schema.

## CLI Commands

| Command | Description |
| --- | --- |
| `fowyldai serve` | Start the Crown Engine |
| `fowyldai config validate` | Validate configuration file |
| `fowyldai models list` | List installed models |
| `fowyldai models pull` | Download a model |
| `fowyldai health` | Check engine status |

## SDK

- Python SDK Guide (included with your FowyldAI installation)

---

!!! info "Full API docs"
    OpenAPI/Swagger documentation is available at `http://localhost:8400/docs` when the Crown Engine is running.
