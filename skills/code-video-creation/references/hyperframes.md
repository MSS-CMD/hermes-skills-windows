# HyperFrames — Full Reference (from `hyperframes` skill)

Create videos from HTML/CSS/JS compositions using `npx hyperframes`.

## Setup

```bash
hermes skills install official/creative/hyperframes
npx hyperframes doctor  # check prerequisites
```

## Workflow

1. Convert the user's request into a production brief: duration, aspect ratio, platform, style
2. Create project: `npx hyperframes init my-video --non-interactive`
3. Write composition in HTML/CSS/JS (root with `data-composition-id`, `data-width`, `data-height`)
4. Validate: `npx hyperframes lint && npx hyperframes inspect --samples 15`
5. Preview: `npx hyperframes preview`
6. Render: `npx hyperframes render --output final.mp4 --quality standard`

## Composition Rules

- Use `data-start`, `data-duration`, and `data-track-index` for timed clips
- Register GSAP timelines synchronously on `window.__timelines`
- Use CSS as the final layout state, then animate from or to that state
- Avoid `Math.random()` or `Date.now()` — use seeded generators
- No infinite repeats — calculate finite counts from composition duration
- Check text/UI stays inside frame on every inspected timestamp

## Delivery

A HyperFrames task is only complete after `lint`, `inspect`, and render to MP4.
