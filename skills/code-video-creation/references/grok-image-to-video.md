# Grok Image to Video — Full Reference (from `grok-image-to-video` skill)

Animate a local image into a short MP4 via xAI Grok Imagine through Hermes Web UI.

## Endpoint

```bash
POST <Hermes Web UI base URL>/api/hermes/media/grok-image-to-video
```

## Resolve Base URL

1. `HERMES_WEB_UI_URL` env var
2. `http://127.0.0.1:${PORT}` if `PORT` set
3. `http://127.0.0.1:8648` (local dev)
4. For Docker Compose: `http://127.0.0.1:6060`

## Authentication

Send Hermes Web UI bearer token resolved from:
1. `AUTH_TOKEN` env var
2. `${HERMES_WEB_UI_HOME}/.token`
3. `${HERMES_WEBUI_STATE_DIR}/.token`
4. `~/.hermes-web-ui/.token`

## JSON Fields

| Field | Required | Description |
|-------|----------|-------------|
| `image_path` | Yes | Local path to png/jpeg/webp image |
| `prompt` | Yes | Motion and style instructions |
| `duration` | No | Seconds, 1-15 (default: 8) |
| `output_path` | No | Where to save MP4 |
| `timeout_ms` | No | Max wait (default: 600000) |

## Example

```bash
curl -sS -X POST "$BASE_URL/api/hermes/media/grok-image-to-video" \
  -H "Authorization: Bearer $TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"image_path": "/path/to/image.png", "prompt": "Slow cinematic push-in", "duration": 8}'
```
