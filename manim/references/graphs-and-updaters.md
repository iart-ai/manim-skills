# Graphs, Plotting & Updaters

## Axes and plotting a function

```python
ax = Axes(
    x_range=[0, 10, 1], y_range=[0, 100, 10],
    axis_config={"include_tip": True},
)
labels = ax.get_axis_labels(x_label="x", y_label="f(x)")
curve  = ax.plot(lambda x: 2 * (x - 5) ** 2, color=MAROON)

self.play(Create(ax), Write(labels))
self.play(Create(curve))
```

- `ax.plot(fn, x_range=[a,b])` — graph a function; `ax.plot_line_graph(x_values, y_values)` for discrete data.
- `ax.c2p(x, y)` (coords→point) and `ax.p2c(point)` (point→coords) — **always** place objects on axes via `c2p`, never raw screen coords.
- `ax.get_area(curve, x_range=[a,b])` — shade under a curve; `ax.get_riemann_rectangles(curve, dx=0.5)` — Riemann sum.
- `NumberPlane()` for a full grid; `NumberLine()` for 1D.

## Moving a dot along the curve (ValueTracker)

`ValueTracker` holds an animatable scalar; updaters recompute dependent mobjects every frame.

```python
t = ValueTracker(0)
dot = always_redraw(
    lambda: Dot(ax.c2p(t.get_value(), (t.get_value() - 5) ** 2 * 2), color=YELLOW)
)
self.add(dot)
self.play(t.animate.set_value(10), run_time=4, rate_func=linear)
```

- `always_redraw(fn)` — rebuild the mobject from scratch each frame (simplest; fn returns a fresh mobject).
- `mob.add_updater(lambda m: m.move_to(ax.c2p(t.get_value(), ...)))` — mutate an existing mobject each frame.
- `mob.clear_updaters()` when finished, or later animations fight the updater.

## Animated counter / readout

```python
num = always_redraw(
    lambda: DecimalNumber(t.get_value(), num_decimal_places=1).next_to(dot, UR)
)
# or a value-driven label via t.get_value()
self.play(t.animate.set_value(10))
```

## Tangent / secant, derivatives

```python
tangent = always_redraw(lambda: ax.get_secant_slope_group(
    x=t.get_value(), graph=curve, dx=0.01, secant_line_color=GREEN
))
```

## Common patterns

- Reveal axes → write labels → `Create` the curve → run a `ValueTracker` to trace/scan.
- Pair `always_redraw` (recompute geometry) with `ValueTracker` (the single source of truth) — drive everything off one tracker so the scene stays consistent.
- For multiple curves, `VGroup(c1, c2)` and animate together; `ax.plot(...).set_color_by_gradient(...)`.
