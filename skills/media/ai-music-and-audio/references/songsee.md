# songsee — Full Reference (from `songsee` skill)

Generate spectrograms and multi-panel audio feature visualizations.

## Quick Start

```bash
go install github.com/steipete/songsee/cmd/songsee@latest

# Basic spectrogram
songsee track.mp3

# Multi-panel visualization
songsee track.mp3 --viz spectrogram,mel,chroma,hpss,selfsim,loudness,tempogram,mfcc,flux

# Time slice
songsee track.mp3 --start 12.5 --duration 8 -o slice.jpg
```

## Visualization Types

| Type | Description |
|------|-------------|
| `spectrogram` | Standard frequency spectrogram |
| `mel` | Mel-scaled spectrogram |
| `chroma` | Pitch class distribution |
| `hpss` | Harmonic/percussive separation |
| `selfsim` | Self-similarity matrix |
| `loudness` | Loudness over time |
| `tempogram` | Tempo estimation |
| `mfcc` | Mel-frequency cepstral coefficients |
| `flux` | Spectral flux (onset detection) |

## Flags

`--style` (classic/magma/inferno/viridis/gray), `--width/--height`, `--window/--hop`, `--min-freq/--max-freq`, `--format` (jpg/png).

Output images can be inspected with `vision_analyze` for automated audio analysis.
