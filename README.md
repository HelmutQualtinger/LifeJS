# Conway's Game of Life

A minimal, dependency-free implementation of [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) in vanilla JavaScript and HTML5 Canvas.

## Demo

Open `index.html` in any modern browser — no build step, no install, no server required.

## Features

- **100 × 60 grid** rendered on an HTML5 Canvas
- **Draw mode** — click and drag to set or erase cells
- **Color-coded cells** — live cells are colored by their neighbor count (0–8)
- **Controls** — Run, Stop, single Step, Clear, and Random seed
- **Adjustable speed** — 1 to 30 generations per second via slider
- **Tab-safe loop** — catches up at most 4 steps after a tab switch to avoid freezing

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

The application uses **WebGL2 GPGPU** to accelerate the simulation. The Game of Life rules are implemented in a fragment shader, using ping-pong textures to update the grid state entirely on the GPU. This allows for smooth performance even with much larger grids. The rendering also happens in WebGL, calculating neighbor counts on the fly for dynamic coloring. Grid state is read back to the CPU only for UI updates and user interaction.

## License

MIT