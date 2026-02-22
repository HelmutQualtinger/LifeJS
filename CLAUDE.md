# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

No build step. Open `index.html` directly in a browser or serve it locally:

```sh
python -m http.server
# then open http://localhost:8000
```

There are no tests, no linter, no package manager, and no dependencies.

## Architecture

The entire application lives in a single file: `index.html`. All JS is in a `<script>` block. The simulation runs entirely on the **GPU via WebGL2** — there is no Canvas 2D context. Only vanilla JS, GLSL shaders, and the WebGL2 API are used.

**Grid constants** (top of script):
- `COLS = 100`, `ROWS = 60`, `CELL = 12` (pixels per cell)

**Key sections:**

- **Shaders**: Two GLSL fragment shader programs compiled at startup:
  - `simFS` — simulation shader: reads the current cell texture, applies Conway rules (survive on 2–3, born on 3), writes the next state to the output texture.
  - `renderFS` — render shader: maps pixel coordinates to grid cells, colors live cells by neighbor count using `nc[0..8]` (a hardcoded vec3 palette), draws cell borders and grid lines.

- **Ping-pong textures** (`tex0` / `tex1`, `fbo0` / `fbo1`): The grid state lives in two `COLS × ROWS` GPU textures. Each `step()` renders from `currentTex` into `nextFBO`, then `swap()` flips the pointers. The red channel encodes cell state (255 = alive, 0 = dead).

- **`uploadGrid(grid)`**: Converts a JS `Uint8Array` grid to `texData` (RGBA bytes) and uploads it to `currentTex` via `gl.texSubImage2D`.

- **`downloadGrid()`**: Reads `currentTex` back from the GPU via `gl.readPixels`, returns `{ grid, alive }`. Used for mouse painting and the live-cell counter.

- **`step()`**: Runs `simProg` into `nextFBO` (viewport = `COLS × ROWS`), then calls `swap()` and increments `generation`.

- **`draw()`**: Runs `renderProg` into the default framebuffer (viewport = full canvas), then calls `downloadGrid()` to update the stats display.

- **Animation loop** (`loop(time)`): Uses `requestAnimationFrame`; limits burst steps to 4 to avoid freezing on tab-switch.

- **Mouse painting**: `mousedown` calls `downloadGrid()` to determine paint mode (set vs. erase) based on the clicked cell; `mousemove` continues painting. Each paint event calls `uploadGrid()` + `draw()`.

- **Glider Gun** (`btnGun`): Hardcodes the Gosper Glider Gun pattern and places two mirrored copies on the grid.

## UI Language

The UI labels are in German (`Lebende Zellen` = live cells, `Generationen` = generations, `Steuerung` = controls, `Geschwindigkeit` = speed, `Nachbarn` = neighbors, `Zufällig` = random, `Hilfe` = help). Keep any new labels in German.