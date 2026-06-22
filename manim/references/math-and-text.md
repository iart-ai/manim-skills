# Math & Text in Manim

LaTeX is required for `Tex`/`MathTex` (install a TeX distribution: TeX Live / MiKTeX). `Text`/`MarkupText` use Pango and need no LaTeX. **Always use raw strings `r"..."`** for TeX so backslashes survive Python.

## Which class

| Class | Engine | Use for |
|---|---|---|
| `Text` | Pango | Plain labels, titles, captions (fonts, weight, slant) |
| `MarkupText` | Pango + markup | Inline styled spans without LaTeX |
| `Tex` | LaTeX (text mode) | Typeset prose with occasional math |
| `MathTex` | LaTeX (math mode, `align*`) | Equations and formulas |

```python
title = Text("Euler's identity", font="Noto Sans", weight=BOLD, font_size=48)
eq    = MathTex(r"e^{i\pi} + 1 = 0", font_size=96)
prose = Tex(r"The area is $\pi r^2$.")
self.play(Write(title))
self.play(Write(eq))
```

## Styling text

```python
Text("RED", color=RED)
Text("Hello", t2c={"[1:-1]": BLUE})            # color by slice
Text("World", t2c={"rl": RED})                  # color by matched chars
Text("Hi", gradient=(RED, BLUE, GREEN))
MarkupText(f'plain <span fgcolor="{YELLOW}">highlight</span>')
```

## Coloring parts of an equation

```python
# A. multiple args, color one
t = MathTex("a^2", "+", "b^2", "=", "c^2")
t.set_color_by_tex("c^2", YELLOW)

# B. isolate substrings inside one string
e = MathTex(r"e^{x} = x^0 + x^1 + \tfrac{1}{2}x^2", substrings_to_isolate="x")
e.set_color_by_tex("x", YELLOW)

# C. {{ }} groups (Manim syntax) — best for matching transforms
eq = MathTex(r"{{a^2}} + {{b^2}} = {{c^2}}")
```

## Morphing one equation into another

`TransformMatchingTex` matches identical sub-TeX between two `MathTex` and morphs only what changed — ideal for algebra steps. Use `{{ }}` groups so terms align.

```python
src = MathTex(r"{{a}}^2 + {{b}}^2 = {{c}}^2")
dst = MathTex(r"{{c}}^2 = {{a}}^2 + {{b}}^2")
self.play(Write(src))
self.play(TransformMatchingTex(src, dst))      # terms fly to new positions
```

For non-TeX shape matching use `TransformMatchingShapes`. For a hard cut between unrelated equations use `ReplacementTransform(eq1, eq2)`.

## Custom LaTeX packages

```python
tmpl = TexTemplate()
tmpl.add_to_preamble(r"\usepackage{mathrsfs}")
MathTex(r"\mathscr{H} \to \mathbb{H}", tex_template=tmpl)
```

## Indexing into rendered glyphs

`MathTex`/`Tex` are `VGroup`s of submobjects; index to animate a single piece: `eq[0][2]` is the 3rd glyph of the 1st arg. `Indicate(eq[0])`, `eq[2].animate.set_color(RED)`. Prefer `{{ }}` groups or `index_labels(eq)` (debug) over guessing indices.
