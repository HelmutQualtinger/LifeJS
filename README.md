# Conway's Game of Life

A dependency-free implementation of [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) in vanilla JavaScript and **WebGL2** — the simulation runs entirely on the GPU.

## Demo

Open `index.html` in any modern browser — no build step, no install, no server required.

## Features

- **200 × 120 grid** rendered via WebGL2 — simulation and rendering run on the GPU
- **Toroidal universe** — edges wrap around (top ↔ bottom, left ↔ right)
- **Draw mode** — click and drag to set or erase cells
- **Color-coded cells** — live cells are colored by their neighbor count (0–8)
- **Controls** — Run, Stop, single Step, Clear, Random seed, Gosper Glider Gun, Save/Load
- **Save & Load** — export/import patterns and rules as JSON files
- **Adjustable speed** — 1 to 480 generations per second via slider
- **Tab-safe loop** — catches up at most 4 steps after a tab switch to avoid freezing
- **German UI** — all labels and controls are in German

## Usage

```sh
# Option A: open directly
open index.html

# Option B: serve locally
python -m http.server
# then visit http://localhost:8000
```

Click or drag on the canvas to paint cells. Use the panel on the right to control the simulation.

## Color Legend

Cells are colored by the number of live neighbors:

| Neighbors | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|-----------|---|---|---|---|---|---|---|---|---|
| Color | light grey | cyan | teal | green | yellow | orange | salmon | red | dark red |

## Implementation Notes

The simulation uses a **ping-pong texture** approach: the grid state lives in two `200 × 120` GPU textures (`tex0` / `tex1`). Each generation, a fragment shader (`simFS`) reads from the current texture, applies Conway's rules per texel, and writes the result to the other texture. The toroidal universe is implemented via modulo wrapping in the shader neighbor-checking loop. A second shader (`renderFS`) then colors each pixel on screen based on cell state and neighbor count. Grid state is only read back to the CPU (`gl.readPixels`) for the live-cell counter and for mouse interaction. Patterns and rules can be saved as JSON and imported later.

## License

MIT
