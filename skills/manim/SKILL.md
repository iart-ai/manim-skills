---
name: manim
description: This skill should be used when the user asks to "make a math animation", "animate an equation", "use Manim", "make a 3Blue1Brown-style video", "plot or animate a graph/function", "visualize a math/physics/CS concept", "morph one formula into another", "animate a geometric proof", or "render a math explainer". Covers Manim Community Edition — Scenes, Mobjects, animations, LaTeX/MathTex, graphs, updaters/ValueTracker, 3D, and rendering to MP4/GIF via the CLI.
version: 0.1.0
---

# Manim (Programmatic Math Animation)

Manim (Mathematical Animation Engine, Community Edition) renders explanatory math/physics/CS videos from Python — the engine behind 3Blue1Brown-style motion. Every scene is declarative: you build vector objects, play timed animations over them, and render a deterministic MP4/GIF from the CLI. Best-in-class for moving equations, transforming formulas, animated graphs, and geometric intuition.

## When to use

- Animate an equation: write it on, transform it term-by-term, highlight substitutions.
- Visualize a function/graph: axes, plotted curves, a dot tracing the curve, area under it.
- Geometric or conceptual explainers: a proof unfolding, vectors, transformations, number lines.
- 3D math: surfaces, parametric curves, a camera orbiting a shape.
- Any "show the math moving" educational clip rendered to MP4/GIF.

> Not the tool for UI motion, social/marketing video, or data-from-CSV business charts — use the web-animation or Remotion video skills for those. Manim's edge is *mathematical* typesetting and intuition.

## Setup & render

```bash
pip install manim          # needs Python 3.9+, ffmpeg, and a LaTeX distribution (for Tex/MathTex)
manim -pql scene.py SquareToCircle   # render one Scene class, preview at low quality
```

Render flags (the ones that matter):
- `-q l|m|h|k` — quality: low (fast iteration) → 4K. `-ql` while building, `-qh` to ship.
- `-p` — play when done; `-o name` — output filename; `--format=gif` — render a GIF (README demos).
- `-s` — **save the last frame as a PNG** (no video encode — the verification lever, see below).
- `-n a,b` — render only animation index range `a..b` (stop early to inspect a moment).
- Output lands in `media/videos/<file>/<quality>/<Scene>.mp4` (and `media/images/...` for `-s`).

## The three building blocks

Everything is **Scenes** (orchestration) acting on **Mobjects** (objects) via **Animations** (timed change).

```python
from manim import *

class SquareToCircle(Scene):
    def construct(self):                 # ALL animation code lives in construct()
        sq = Square().set_fill(BLUE, opacity=0.5)
        self.play(Create(sq))            # animate it drawing on
        self.play(sq.animate.rotate(PI / 4))
        self.play(Transform(sq, Circle().set_fill(PINK, opacity=0.5)))
        self.wait(0.5)                   # hold
```

**Mobjects** — vector objects (`Circle`, `Square`, `Rectangle`, `Line`, `Dot`, `Polygon`, `Axes`, `Text`, `MathTex`).
- Position: `.shift(UP*2)` (relative), `.move_to(ORIGIN)` (absolute), `.next_to(other, RIGHT, buff=0.5)`, `.align_to(other, LEFT)`. Directions are constants: `UP DOWN LEFT RIGHT ORIGIN UL UR DL DR`.
- Style: `.set_fill(color, opacity)`, `.set_stroke(color, width)`, `.set_color(YELLOW)`. Colors: `BLUE RED GREEN YELLOW WHITE` + `BLUE_E`, etc.
- `self.add(m)` shows instantly (no animation); `self.remove(m)`. Later-added objects render on top.

**Animations** — interpolate a mobject's state over `run_time` seconds (default 1s).
- Construction: `Create`, `Write` (for text/Tex), `DrawBorderThenFill`, `FadeIn`, `FadeOut`, `GrowFromCenter`.
- Change: `Transform(a, b)` (morph a into b, keep a), `ReplacementTransform(a, b)` (swap a for b), `Rotate`, `Indicate`, `Circumscribe`, `Wiggle`.
- **`.animate` syntax** — animate any method call: `self.play(sq.animate.shift(UP).set_fill(RED))`. Chains apply together.
- Play several at once: `self.play(Create(a), FadeIn(b), run_time=2)`.

**Timing & easing** — `run_time=` sets duration; `rate_func=` sets the easing curve (`smooth` is the default — ease-in-out; also `linear`, `rush_into`, `rush_from`, `there_and_back`, `ease_out_bounce`). Stagger a group with `LaggedStart(*anims, lag_ratio=0.2)`. The motion-craft rules from the `motion-design-skills` pack (`animation-principles`: the eye forgives a slow *end* less than a slow start; reserve `linear` for loops; stagger groups) apply here — Manim just expresses them as `rate_func` + `LaggedStart`.

## Math, text & equations

`Text`/`MarkupText` (Pango, no LaTeX) for labels; `Tex`/`MathTex` (LaTeX, raw strings) for typeset math. Animate equations with `Write` and morph between them with `TransformMatchingTex`. Full recipes — substring coloring, `{{ }}` isolation, term-by-term transforms, LaTeX preamble — in `references/math-and-text.md`.

## Graphs, plotting & updaters

`Axes` + `ax.plot(lambda x: ...)` for functions; `ax.c2p(x, y)` maps math coords → screen. Drive live values with `ValueTracker` + `always_redraw`/`add_updater` so dependent mobjects recompute every frame (a dot riding a curve, a counter, a moving tangent). See `references/graphs-and-updaters.md`.

## 3D & moving camera

`ThreeDScene` + `set_camera_orientation(phi=, theta=)`, `ThreeDAxes`, `Surface`, `begin_ambient_camera_rotation()`; `MovingCameraScene` + `self.camera.frame.animate...` for 2D camera pushes/follows. See `references/3d-and-camera.md`.

## Deliver & verify (rendered frame → MP4)

> **Packaged helper** (`scripts/`): tile your stills with `scripts/contact-sheet.sh sheet.png f-hook.png f-mid.png f-end.png`, then assert the encode with `scripts/probe-mp4.sh out.mp4 [WxH] [fps]`. See `scripts/README.md`.

Manim is deterministic — the same scene renders the same frames — so verify by rendering **frames** (PNG, no video encode) before committing to the full video. Use this whenever the output is a math clip MP4/GIF.

**Output contract:**
- A `.py` with one `Scene` (or `ThreeDScene`) subclass per clip; all motion inside `construct()`. Deliverable = the rendered `media/videos/.../<Scene>.mp4` plus the script (so it re-renders).
- Raw strings (`r"..."`) for every `Tex`/`MathTex`; LaTeX installed.

**Verify loop — render frames → inspect → encode video:**
```bash
# 1. Save the final frame as a PNG, low quality — fast, no encode
manim -sql scene.py MyScene
# 2. Inspect a MID moment: stop after animation N and save that frame
manim -sql scene.py MyScene -n 0,3        # frame after the first 3 animations
# 3. Check each PNG: fidelity (equation correct, axes/labels right, nothing
#    overlapping or off the 16:9 frame) + LaTeX actually compiled (no blank/box glyphs).
# 4. Only once frames look right, encode the video:
manim -qh scene.py MyScene                # full MP4 at high quality
manim -qm scene.py MyScene --format=gif   # GIF for a README demo
```

**Before you finish:**
1. `manim -sql ... -n a,b` renders cleanly at start / a mid range / end — no LaTeX errors, no missing-glyph boxes.
2. Equations/labels are mathematically correct and fully inside the frame (nothing clipped at edges).
3. No overlap between mobjects at the inspected frames; `next_to`/`arrange` used instead of hand-guessed coords.
4. Timing reads well — `run_time`/`rate_func` intentional, `self.wait()` holds on key states, groups staggered not simultaneous.
5. Full MP4 encodes and plays; (optional) GIF rendered for the README.

## Quick reference

| Need | Use |
|---|---|
| New animation | `class S(Scene): def construct(self): ...` |
| Show instantly / animate on | `self.add(m)` / `self.play(Create(m))` |
| Animate a method | `self.play(m.animate.shift(UP))` |
| Morph / replace | `Transform(a,b)` / `ReplacementTransform(a,b)` |
| Typeset math | `MathTex(r"e^{i\pi}+1=0")`, animate with `Write` |
| Morph equations | `TransformMatchingTex(eq1, eq2)` |
| Plot a function | `ax=Axes(...); ax.plot(lambda x: x**2)` |
| Live/linked value | `ValueTracker` + `always_redraw(...)` |
| Position relative | `m.next_to(other, RIGHT, buff=0.3)` |
| Duration / easing | `self.play(a, run_time=2, rate_func=smooth)` |
| Stagger a group | `LaggedStart(*anims, lag_ratio=0.2)` |
| Render / inspect / gif | `manim -qh f.py S` / `-sql -n a,b` / `--format=gif` |

## Gotchas

- **LaTeX is required** for `Tex`/`MathTex` (not for `Text`). A missing-glyph box or compile error = LaTeX not installed or a bad TeX string. Always use raw strings `r"..."`.
- `Transform(a, b)` mutates `a` to look like `b` (and `b` is not added); `ReplacementTransform(a, b)` leaves `b` on screen — pick based on which object you keep referencing after.
- `.animate` interpolates start→end states, so it can take odd paths for rotations/some transforms; use a dedicated animation (`Rotate`) when the path matters.
- Updaters added with `add_updater` keep running — `mob.clear_updaters()` (or `remove_updater`) when done, or later animations fight the updater.
- Don't hand-guess coordinates; compose with `next_to`, `arrange`, `VGroup(...).arrange(DOWN)`, and `ax.c2p()` so layout survives edits.
- `construct()` runs once to *script* the timeline; there is no realtime loop — never use wall-clock time or randomness without a seed.

## Reference files

- `references/math-and-text.md` — Text vs MarkupText vs Tex vs MathTex, raw strings, substring coloring (`set_color_by_tex`, `substrings_to_isolate`, `{{ }}`), `TransformMatchingTex` term morphs, and LaTeX preamble/packages.
- `references/graphs-and-updaters.md` — `Axes`/`NumberPlane`, `plot`, `get_area`, `c2p`/`p2c`, `ValueTracker`, `always_redraw`, `add_updater`, a dot tracing a curve, and animated counters.
- `references/3d-and-camera.md` — `ThreeDScene`, `set_camera_orientation`, `ThreeDAxes`, `Surface`/parametric, ambient camera rotation, and `MovingCameraScene` pushes/follows.
