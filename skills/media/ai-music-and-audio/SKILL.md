---
name: ai-music-and-audio
description: "AI music and audio tools: music generation (AudioCraft, HeartMuLa), audio visualization (songsee), and songwriting craft (lyrics, structure, Suno prompts)."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [music, audio, generation, songwriting, spectrogram, heartmula, audiocraft, songsee, suno]
    related_skills: []
---

# AI Music and Audio

Consolidated umbrella covering AI music generation, audio analysis, and songwriting craft. Four sub-areas from the former `audiocraft-audio-generation`, `heartmula`, `songsee`, and `songwriting-and-ai-music` skills.

## Music Generation — AudioCraft (Meta)

Text-to-music (MusicGen) and text-to-sound (AudioGen). See `references/audiocraft.md`.

**Quick start:** `python3 -m audiocraft.musicgen --model=melody --prompt="happy piano" --output=output.wav`

## Music Generation — HeartMuLa (Open Source)

Suno-like song generation from lyrics + tags using open-weight models. See `references/heartmula.md`.

**Quick start:** After install: `python ./examples/run_music_generation.py --model_path=./ckpt --lyrics=lyrics.txt --tags=tags.txt --save_path=output.mp3`

## Audio Visualization — songsee

Generate spectrograms and multi-panel audio feature visualizations. See `references/songsee.md`.

**Quick start:** `songsee track.mp3 --viz spectrogram,mel,chroma,mfcc -o analysis.png`

## Songwriting Craft

Guidance on song structure, rhyme, meter, lyrics, and Suno AI music prompts. See `references/songwriting.md`.

## Choosing Between Generators

| Tool | Best For | Requirements |
|------|----------|-------------|
| **AudioCraft (MusicGen)** | Quick text-to-music, melody conditioning | Python + torch, no GPU required (but slow) |
| **HeartMuLa** | Full songs from lyrics+tags, Suno-like output | 8GB+ VRAM, Python 3.10, ~6GB model downloads |
| **songwriting craft** | Writing lyrics, structure, Suno prompts | No technical requirements |
