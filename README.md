# Nabla Forge 🔺

**A node-based computational workshop for mathematics & 3D visualization — entirely in your browser.**

Nabla Forge is a single-file web application inspired by Mathematica, Blender's shader editor, and TouchDesigner. You build a graph of computation nodes on a canvas; each wire carries a *scalar field* (a function of `x, y, z, u, v, t`); render nodes turn those fields into interactive 3D geometry, 2D maps, flowing particle systems — or a standalone shareable web page.
No installation, no account, no server. Everything runs locally: the graph compiles your formulas down to native JavaScript functions, so even dense animated scenes stay at 60 fps.

---

## ✨ Features

| Area | What you get |
|---|---|
| **Node editor** | 20+ node types, drag-and-drop, bezier wires, cycle detection, rubber-band selection, multi-move, Ctrl+D duplicate (copies internal links) |
| **Fields** | Free-form expressions with implicit multiplication (`2x`, `x(x-1)`), 40+ built-in functions, fBm noise, domain warping |
| **8 renderers** | Surfaces, parametric surfaces, 3D curves, vector fields, 2D contours, streamlines, advected particles, HTML exporter |
| **8 colormaps** | viridis, magma, plasma, inferno, cividis, ocean, sunset, mono |
| **Animation** | Time node, animated fields, self-running particle systems |
| **Pupitre** | Floating panel aggregating *every* slider of *every* node — your custom Manipulate |
| **History** | Full Undo/Redo (Ctrl+Z / Ctrl+Y), 60 steps |
| **Persistence** | Auto-save to localStorage, JSON project files |
| **Exports** | PNG snapshot, OBJ geometry (3D-printable), standalone animated HTML page |

---

## 🚀 Getting started

1. Download `index.html` (the whole app is one file).
2. Open it in any modern browser. That's it.
3. The welcome graph loads immediately — a glowing particle vortex, already animating.

Try the **Exemples** menu for 21 curated scenes: fractal terrain, Möbius strip, Klein bottle, phase portraits, curl-noise ink, and more.

> **Requirements:** any recent browser (Chrome, Firefox, Edge, Safari). Internet needed on first load only (Three.js and KaTeX come from CDN). The exported HTML pages also need internet to load Three.js.

---

## 🧩 Core concept: everything is a field

Every wire transports a **scalar field** — a real-valued function of six variables:

| Variable | Meaning |
|---|---|
| `x`, `y` | position in the plane |
| `z` | third spatial dimension (vector fields, 3D probes) |
| `u`, `v` | parametric coordinates (surfaces, curves) |
| `t` | time in seconds (animation) |

Nodes transform fields, and render nodes consume them. For example:

```text
[Coordinates]──r──▶[Wave]──────────▶[Surface 3D]
[Time]───phase─────┘
```

…produces an animated radial wave surface. The graph evaluates lazily, topologically, with cycle detection — and each formula is compiled once into a native JS function via `new Function`, so sampling millions of points per frame stays fast.

### Expression syntax

```text
sin(x*1.4) + 0.6*cos(y*2.1)     // functions: sin, cos, tan, exp, log, sqrt,
2x + 3y                         // implicit multiplication is allowed
x(x-1)                          // variable followed by ( = product
noise2(0.4x, 0.4y, 4)           // 2D fBm value noise, 4 octaves
hypot(x-2, y)                   // vector length helper
2^3^2                           // right-associative: = 512
-pi                             // unary minus, constants: pi, tau, e, phi, degree
```

Comments: `(* like this *)`.

---

## 📚 The node library

### Sources

| Node | Outputs | Description |
|---|---|---|
| **Coordinates** | `x, y, r, θ` | Planar position, radius, angle |
| **UV Params** | `u, v` | Parametric coordinates |
| **Time** | `phase, sec` | Cyclic phase (0→1) and elapsed seconds |
| **Constant** | `val` | A number |
| **Expression** | `f` | Free-form formula (see syntax above) |

### Operations

`Add, Subtract, Multiply, Divide, Power, Min, Max, Mod` — pointwise combination of two fields.

### Functions

| Node | Options |
|---|---|
| **Trigonometry** | `sin, cos, tan` |
| **Function** | `exp, log, sqrt, abs, sign, floor, fract` |
| **Wave** | `amp·sin(2π·freq·A + φ)` — wire Time into φ to animate |
| **Noise** | 2D value-noise fBm at coordinates (U, V); scale, offset, octaves (1–6) |

### Domain

| Node | Effect |
|---|---|
| **Remap** | Rescale field from interval [a,b] to [c,d] |
| **Warp** | Evaluate F at a displaced domain — the classic shader "domain warping" smoke effect |

### Render nodes

| Node | Geometry | Notes |
|---|---|---|
| **Surface 3D** | `z = f(x, y)` | Color-mapped heightfield; OBJ export |
| **Parametric Surface** | `(X, Y, Z)(u, v)` | Tori, Möbius, Klein bottles, seashells… |
| **Curve 3D** | `(X, Y, Z)(u)` | Smooth tube (Catmull-Rom + TubeGeometry) |
| **Vector Field** | arrows `(VX, VY, VZ)` | Cube [min,max]³, colored by magnitude, 98th-percentile normalized |
| **Contours 2D** | heatmap + isolines | Marching squares — a ContourPlot; top-down camera |
| **Streamlines** | integrated `(VX, VY)` | RK4 seeds, colored by speed — phase portraits |
| **Particles** | advected points | Hundreds to thousands, glowing trails, **self-animating** |
| **HTML Page** | *(no geometry)* | Branch any renderer's `scène` output into it, then generate a standalone page |

---

## 🎬 Animation

- Wire a **Time** node into any input that feeds the shape (e.g. Wave φ, or use `t` directly inside an Expression).
- Press ▶ in the toolbar (or just let **Particles** run — they animate on their own).
- Animated surfaces, curves and fields resample every frame; the status bar shows live FPS.

---

## 🎛 The Pupitre (Manipulate)

Click the sliders icon in the toolbar: a floating panel lists every numeric parameter of every node, grouped and color-coded. Tweak your whole scene from one place — ideal while it's animating. Draggable, closable, live.

---

## ⌨️ Keyboard & mouse reference

### Graph editing

| Gesture | Action |
|---|---|
| Right-click on background | Add-node menu at cursor |
| Drag from library | Add node |
| Drag output socket → input | Connect (cycles rejected) |
| Click a connected input | Unplug |
| Click a wire | Delete link |
| Drag on background | Rubber-band selection |
| Ctrl/Shift + click | Add/remove node from selection |
| Drag a selected node | Move the whole selection |
| Ctrl + D | Duplicate selection (internal links copied) |
| Delete / Backspace | Delete selection |
| Ctrl + A | Select all |
| F | Frame selection (or whole graph) |
| Space / Alt / Middle-drag | Pan the canvas |
| Mouse wheel | Zoom |

### Workflow

| Shortcut | Action |
|---|---|
| Ctrl + Z / Ctrl + Y | Undo / Redo (60 steps) |
| Ctrl + Enter | Force graph evaluation |
| Ctrl + S | Save graph as JSON |
| Escape | Cancel (link, menu, help, panel, selection) |

### Viewport

| Gesture | Action |
|---|---|
| Drag | Orbit |
| Wheel | Zoom |
| Toolbar buttons | Wireframe · Reframe · Export OBJ · Export PNG |

---

## 📤 Sharing your work

| Format | What it is | How |
|---|---|---|
| **JSON** | The editable graph — reopen it anywhere | `Fichier → Enregistrer` |
| **HTML page** | A standalone, orbitable, animated scene of one renderer — formulas compiled and embedded | Add an **HTML Page** node, branch a renderer's `scène` output into it, set title & auto-rotation, click *Generate* in the inspector |
| **OBJ** | The displayed mesh, Y-up, importable in Blender / slicers | Cube button in the viewport |
| **PNG** | Snapshot of the current view | Arrow button in the viewport |

The generated HTML is a single ~30 KB file that rotates your Klein bottle by itself. Three.js loads from CDN, so recipients need internet to open it — everything else is embedded.

---

## 🗂 Project structure (single file)

```text
index.html
├── §0  utilities (icons, toast, status)
├── §1  AST (constructors, LaTeX printing, substitution)
├── §2  expression parser (tokenizer + Pratt parser)
├── §3  compiler: AST → native JS function
├── §4  node definitions (sources, ops, functions, domain, renders)
├── §5  graph state, serialization, undo history
├── §6  selection (rubber band, multi-edit)
├── §7  node DOM, wires, pan/zoom, connections
├── §8  lazy topological evaluation with error propagation
├── §9  3D viewport (Three.js) + 7 scene builders
├── §10 standalone HTML exporter
├── §11 Pupitre (global control panel)
├── §12 inspector (properties + KaTeX symbolic preview)
├── §13 node library UI
├── §14 menus, toolbar, 21 examples
├── §15 keyboard shortcuts
└── §16 help modal, resizable panels, startup
```

**Stack:** Three.js r160 (WebGL), KaTeX (symbolic previews), vanilla ES modules. No build step, no framework, no dependencies to install.

---

## 🧠 Under the hood — a few highlights

- **Symbolic composition**: every wire carries an AST; nodes compose them algebraically, and the inspector renders the *composed expression* of any node in KaTeX — you can literally read what your graph computes.
- **Compiled evaluation**: `toJS()` turns the AST into a JS source string; `new Function` compiles it once; sampling a 200×200 grid becomes a tight native loop.
- **Robust rendering**: NaN-holes in surfaces, singular vector fields (1/r²) normalized by 98th percentile, zero-length arrows degenerated safely, per-vertex validity masks on parametric surfaces.
- **RK4 everywhere**: streamlines and particles integrate with Runge-Kutta 4, step-limited and clamped to the domain.
- **Camera memory**: each render node remembers its own camera; switching nodes or re-evaluating never yanks your view.

---

## 🛣 Roadmap ideas

- [ ] Particle trajectories in 3D vector fields
- [ ] Lorenz / ODE iterator node (continuous dynamical systems)
- [ ] WebM video recording of animations
- [ ] STL export for direct 3D printing
- [ ] Pinned personal example gallery
- [ ] Exact rational arithmetic (BigInt): `1/3 + 1/6 = 1/2`

---

## 📄 License

MIT — do whatever you want, attribution appreciated.

*Nabla Forge v2.4 — forge your fields.* 🔺
```

Copy everything between the fences into a file named `README.md` and push it next to your `index.html` — GitHub will render the tables, emojis and everything automatically.
