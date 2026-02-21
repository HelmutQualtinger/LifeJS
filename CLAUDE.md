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

The entire application lives in a single file: `index.html`. All JS is in a `<script>` block (lines 200–391). There are no modules, imports, or external libraries — only vanilla JS and the HTML5 Canvas API.

**Grid constants** (line 201–203):
- `COLS = 100`, `ROWS = 60`, `CELL = 12` (pixels per cell)

**Key sections:**
- **State** (lines 229–240): `grid` is an array of `Uint8Array` rows; `generation`, `running`, `stepsPerSec` are module-level vars.
- **Logic** (lines 242–266): `nbCount(g, r, c)` counts live neighbors (no toroidal wrapping); `step()` allocates a new grid each generation using standard Conway rules.
- **Rendering** (lines 268–295): `draw()` repaints the canvas — cells are colored by neighbor count using `NEIGHBOR_COLORS[0..8]`. Grid lines are pre-rendered onto an offscreen canvas and composited.
- **Animation loop** (lines 297–309): `loop()` uses `requestAnimationFrame`; limits burst steps to 4 to avoid freezing on tab-switch.
- **Mouse painting** (lines 311–335): mousedown determines paint mode (set vs. erase) based on the clicked cell's current state; mousemove continues painting.

## UI Language

The UI labels are in German (`Lebende Zellen` = live cells, `Generationen` = generations, `Steuerung` = controls, `Geschwindigkeit` = speed, `Nachbarn` = neighbors, `Zufällig` = random). Keep any new labels in German.