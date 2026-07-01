# Remotion — Full Reference (from `remotion` skill)

Create editable video projects as React components, rendered to MP4.

## Setup

```bash
hermes skills install skills-sh/google-labs-code/stitch-skills/remotion
npx create-video@latest --yes --blank --no-tailwind my-video
```

## Workflow

1. Create production brief: purpose, audience, duration, aspect ratio, style
2. Use defaults: 1080x1920 vertical, 1920x1080 horizontal, 30 fps, MP4
3. Build React components using Remotion primitives: `Composition`, `Sequence`, `AbsoluteFill`, `useCurrentFrame`, `interpolate`, `spring`
4. Preview: `npx remotion studio`
5. Still frame check: `npx remotion still <composition-id> --scale=0.25 --frame=30`
6. Render: `npx remotion render <composition-id> out/final.mp4`

## Guidelines

- Keep copy, scene timing, colors in clear constants/data arrays
- Make captions readable on mobile: high contrast, generous line height
- Use frame-based timing, not browser timers
- Separate components for scenes, captions, overlays, lower thirds
- When adding voiceover/audio, keep timing explicit and adjustable

## Delivery

A Remotion task is complete when the project is editable AND the MP4 is rendered.
