# AudioCraft — MusicGen & AudioGen (Full Reference)

Full reference for Meta's AudioCraft, formerly at `skills/mlops/models/audiocraft/`.

## When to use AudioCraft

- User wants to generate music or audio from text descriptions
- User wants melody-conditioned music generation
- User asks about MusicGen, AudioGen, or EnCodec
- Adding background music or sound effects to projects
- Quick prototyping of audio content

## Quick Start

```bash
# Install
pip install 'torch>=2.0.0' 'transformers>=4.30.0' audiocraft

# Generate music from text
python -m audiocraft.musicgen --model=melody --prompt="happy piano" --output=output.wav

# Extended generation (longer audio)
python -m audiocraft.musicgen --model=large --prompt="ambient electronic" --duration=30 --output=ambient.wav
```

## Models

| Model | Size | Description |
|-------|------|-------------|
| `small` | 300M | Fast, decent quality, good for quick demos |
| `medium` | 1.5B | Good balance of quality and speed (default) |
| `melody` | 1.5B | Same as medium but supports melody conditioning |
| `large` | 3.3B | Best quality, slowest, requires more VRAM |

## Key flags

| Flag | Description |
|------|-------------|
| `--model` | Model size: small/medium/melody/large |
| `--prompt` | Text description |
| `--duration` | Output duration in seconds (default: 8) |
| `--output` | Output file path |
| `--melody` | Melody conditioning file (model=melody only) |
| `--top-k` / `--top-p` | Sampling parameters |
| `--temperature` | Generation temperature |
| `--cfg-coef` | Classifier-free guidance coefficient |

## AudioGen (Sound Effects)

```bash
# Generate sound effects
python -m audiocraft.audiogen --model= --prompt="dog barking" --output=bark.wav
python -m audiocraft.audiogen --model= --prompt="rain on window" --output=rain.wav
```
