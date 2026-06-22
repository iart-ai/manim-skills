# Manim

Build math/physics/CS animations in Python with Manim Community Edition — the engine behind 3Blue1Brown-style motion. Declarative scenes: build vector objects, play timed animations, render a deterministic MP4/GIF from the CLI. For educators and explainer makers who want equations that move and graphs that draw themselves, with a render-a-frame-first verify loop.

## When it activates

- "Make a math animation" / "animate this equation" / "use Manim."
- "Make a 3Blue1Brown-style video" or "visualize this concept."
- "Plot and animate a function / graph" or "trace a dot along a curve."
- "Morph one formula into another" / "animate a geometric proof."
- "Render a 3D surface with an orbiting camera."

## Example prompts

- "Animate the Pythagorean theorem with the squares rearranging into the proof."
- "Plot f(x)=sin(x), then sweep a dot along it while a counter shows the y-value."
- "Morph the quadratic formula into its completed-square form, term by term."
- "Render a rotating 3D surface of z = sin(x)·cos(y) with labeled axes."

## What's inside

- `SKILL.md` — Scenes & the `construct()` method, Mobjects (create/position/style), animations (`Create`, `Transform`, `.animate`, `run_time`/`rate_func`), the render CLI, and the deliver-and-verify loop (render a frame → inspect → encode MP4/GIF).
- `references/math-and-text.md` — `Text`/`MarkupText`/`Tex`/`MathTex`, raw strings, substring coloring, `TransformMatchingTex` equation morphs, LaTeX preamble.
- `references/graphs-and-updaters.md` — `Axes`/`plot`/`c2p`, `get_area`, `ValueTracker` + `always_redraw`/`add_updater`, a dot tracing a curve, animated counters.
- `references/3d-and-camera.md` — `ThreeDScene`, `set_camera_orientation`, `ThreeDAxes`/`Surface`, ambient camera rotation, and `MovingCameraScene`.

---
Part of **[Manim Skills](../)** · Built by **[iart.ai](https://iart.ai)** — controllable Motion Graphics MP4 from a prompt or data.
