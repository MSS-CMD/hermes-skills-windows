---
name: code-video-creation
description: "Create videos programmatically: HyperFrames (HTML/CSS/JS), Remotion (React), or Grok Imagine (xAI image-to-video). Use for short video intros, trailers, promos, animations, and motion graphics."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [video, animation, hyperframes, remotion, grok, mp4, motion-graphics]
    related_skills: [p5js, manim-video]
---

# Code-Based Video Creation

Three approaches for creating videos programmatically. Choose based on the use case:

| Approach | Best For | Tech Stack | File Size |
|----------|----------|------------|-----------|
| **HyperFrames** (HTML/CSS/JS) | Short intros, cinematic trailers, HUD visuals, motion graphics | HTML + CSS + JS + npx | 87 lines |
| **Remotion** (React) | Editable video projects, product demos, tutorials, feed ads | React + npx | 98 lines |
| **Grok Imagine** (xAI) | Animating a single image into a short video | curl + Hermes Web UI | 112 lines |

## HyperFrames — see `references/hyperframes.md`

HTML compositions → MP4. Uses `npx hyperframes` CLI.

**Quick start:** `npx hyperframes init my-video`, write HTML/CSS/JS composition, `npx hyperframes render --output final.mp4`.

## Remotion — see `references/remotion.md`

React components → MP4. Uses `npx create-video` and `npx remotion` CLI.

**Quick start:** `npx create-video@latest --yes --blank my-video`, build React components, `npx remotion render <composition-id> out/final.mp4`.

## Grok Imagine — see `references/grok-image-to-video.md`

Animate a local image into short MP4 via xAI Grok API through Hermes Web UI.

**Quick start:** POST to `/api/hermes/media/grok-image-to-video` with image_path and prompt.

## Common Rules

1. Do not stop after writing code — render must complete successfully.
2. Report the output MP4 path and any assumptions made.
3. For non-trivial layouts, render at least one still frame first to catch issues.
