# Troubleshooting — FowyldAI

Common issues and how to resolve them.

## Crown Engine won't start

**Symptom:** `fowyldai serve` exits immediately or port 8400 is unreachable.

- Check if port 8400 is already in use: `netstat -an | findstr 8400`
- Verify config file syntax: `fowyldai config validate`
- Check logs: `logs/fowyldai.log`
- Ensure Python 3.10+ is active

## Model loading fails

**Symptom:** `Model not found` or `Failed to load model` errors.

- Verify model path in `fowyldai-config.yaml`
- Check disk space — models can be several GB
- For air-gap deployments, ensure the model bundle was extracted correctly

## High memory usage

**Symptom:** OOM errors or system slowdown under load.

- Reduce `workers` count in config
- Use quantized models (Q4/Q8) for lower memory footprint
- Set `max_concurrent_requests` to limit parallel inference

## API returns 401 Unauthorized

- Verify API key is set in request header
- Check `security.api_key_required` config setting
- Ensure the key matches what's configured in your environment

---

!!! tip "Still stuck?"
    Check the FAQ included with your installation or [contact support](mailto:support@melhousensolutions.com).
