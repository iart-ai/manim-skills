# 3D Scenes & Moving Camera

## ThreeDScene basics

```python
class Surf(ThreeDScene):
    def construct(self):
        self.set_camera_orientation(phi=70 * DEGREES, theta=-45 * DEGREES)
        axes = ThreeDAxes()
        surface = Surface(
            lambda u, v: axes.c2p(u, v, np.sin(u) * np.cos(v)),
            u_range=[-3, 3], v_range=[-3, 3],
            resolution=(30, 30),
        )
        surface.set_style(fill_opacity=0.7)
        self.add(axes)
        self.play(Create(surface))
        self.wait()
```

- `set_camera_orientation(phi=, theta=, zoom=, focal_distance=)` — `phi` tilts from the z-axis, `theta` spins around it. Use `DEGREES`.
- `ThreeDAxes`, `Sphere`, `Cube`, `Prism`, `Surface` (parametric via `c2p`), `ParametricFunction` for 3D curves.
- `resolution=(n, m)` controls Surface mesh density (higher = smoother but slower).

## Ambient + animated camera (3D)

```python
self.begin_ambient_camera_rotation(rate=0.2)   # slow continuous orbit
self.wait(5)
self.stop_ambient_camera_rotation()

# or animate camera explicitly
self.move_camera(phi=45 * DEGREES, theta=30 * DEGREES, run_time=3)
```

`add_fixed_in_frame_mobjects(label)` keeps a 2D label (title, caption) pinned to the screen instead of floating in 3D space.

## Moving camera (2D)

`MovingCameraScene` exposes `self.camera.frame` as an animatable mobject — push in, pan, follow.

```python
class Follow(MovingCameraScene):
    def construct(self):
        self.camera.frame.save_state()
        dot = Dot()
        self.add(dot)
        # zoom in and recenter on the dot
        self.play(self.camera.frame.animate.scale(0.5).move_to(dot))
        # follow a moving dot with an updater
        self.camera.frame.add_updater(lambda f: f.move_to(dot))
        self.play(dot.animate.shift(RIGHT * 4))
        self.camera.frame.clear_updaters()
        self.play(Restore(self.camera.frame))   # back to the saved full view
```

- `self.camera.frame.animate.scale(0.5)` zooms in (smaller frame = closer); `.move_to(x)` pans.
- `save_state()` / `Restore(self.camera.frame)` — snapshot and return to a framing.
- Follow a target by adding an updater that moves the frame to it each frame; `clear_updaters()` to release.

## Tips

- 3D renders are slower — iterate with `-ql` and a low Surface `resolution`, raise both only for the final `-qh` encode.
- Verify a 3D shot with `manim -sql ... -n a,b` (save a frame) to check the camera angle and that nothing is behind the camera before the full render.
- Keep ambient rotation slow (`rate` ~0.1–0.3); fast orbits read as disorienting, not explanatory.

---
Move the camera and trackers with intent and a Manim scene teaches the math. Built by **[iart.ai](https://iart.ai/?utm_source=github&utm_medium=readme&utm_campaign=manim-skills&utm_content=skill_footer&utm_term=manim)** — the AI motion agent for editable, on-brand motion graphics.
