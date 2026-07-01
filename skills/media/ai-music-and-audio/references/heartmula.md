# HeartMuLa — Full Reference (from `heartmula` skill)

Open-source Suno-like music generation from lyrics + tags. Apache-2.0.

## Quick Start

```bash
cd ~/
git clone https://github.com/HeartMuLa/heartlib.git
cd heartlib
uv venv --python 3.10 .venv
. .venv/bin/activate
uv pip install -e .
uv pip install --upgrade datasets transformers

# Patch source (see heartmula skill in .archive/ for details)
# Download models
hf download --local-dir './ckpt' 'HeartMuLa/HeartMuLaGen'
hf download --local-dir './ckpt/HeartMuLa-oss-3B' 'HeartMuLa/HeartMuLa-oss-3B-happy-new-year'
hf download --local-dir './ckpt/HeartCodec-oss' 'HeartMuLa/HeartCodec-oss-20260123'

# Generate
python ./examples/run_music_generation.py \
  --model_path=./ckpt --version="3B" \
  --lyrics=./assets/lyrics.txt --tags=./assets/tags.txt \
  --save_path=./assets/output.mp3 --lazy_load true
```

## Hardware Requirements

- Minimum: 8GB VRAM with `--lazy_load true`
- Recommended: 16GB+ VRAM
- CPU: possible but extremely slow (~30-60 min per song vs ~4 min on GPU)

## Key Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `--max_audio_length_ms` | 240000 | Max length in ms |
| `--topk` | 50 | Top-k sampling |
| `--temperature` | 1.0 | Sampling temperature |
| `--cfg_scale` | 1.5 | Classifier-free guidance scale |
| `--lazy_load` | false | Load/unload models on demand (saves VRAM) |

## Pitfalls

1. Do NOT use bf16 for HeartCodec — degrades quality. Use fp32.
2. Tags may be ignored (known issue #90) — lyrics tend to dominate.
3. Triton not available on macOS.
4. Dependency pin conflicts require the manual upgrades and patches.
5. Full patch details preserved in `.archive/heartmula/SKILL.md`.
