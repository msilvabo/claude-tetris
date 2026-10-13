# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Classic Tetris in vanilla JS + HTML5 Canvas + CSS. **No build step, no dependencies, no package.json, no tests.** Three files only: `index.html`, `style.css`, `game.js`.

## Run / debug

- Open `index.html` directly in a browser, or serve statically (`npx serve .`, `python -m http.server 8000`).
- Debug with browser devtools console; there is no lint/test/build command.

## Architecture (all logic in `game.js`, ~300 lines)

- **Board model**: `ROWS × COLS` (20×10) matrix; `0` = empty, `1–7` = piece color index into `COLORS`. Pieces are self-identifying square matrices in `PIECES` (cells carry their own color index, so any matrix can be merged/drawn directly).
- **Game loop**: `requestAnimationFrame` in `loop()`. Time accumulates in `dropAccum`; when it exceeds `dropInterval` the piece drops one row or locks. Speed: `max(100, 1000 - (level-1)*90)` ms; level = `floor(lines/10)+1`.
- **State**: module-level `let` variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, …) all reset by `init()` — the single entry point, also bound to the restart button.
- **Key functions**: `collide` (bounds + overlap), `rotateCW` (transpose + reverse), `tryRotate` (wall kicks `[0, -1, 1, -2, 2]`), `clearLines` (scans bottom-up, `splice`+`unshift`; score = `LINE_SCORES[cleared] * level`, hard drop +2/cell, soft drop +1/row), `ghostY` (projected landing row, drawn at alpha 0.2), `spawn` (collision at spawn → `endGame`).
- **Input**: single `keydown` listener on `document` using `e.code` (ArrowLeft/Right/Down, ArrowUp or KeyX rotate, Space hard drop — has `preventDefault`, KeyP pause).
- **Rendering**: Canvas 2D only. `draw()` redraws everything each frame: grid → board cells → ghost → current piece. Separate small canvas (`next-canvas`) for next-piece preview. HUD is plain DOM (`textContent`). Pause/Game-Over are a CSS overlay div toggled with a `hidden` class.

## Constraints when editing

- Changing `COLS`/`ROWS`/`BLOCK` requires also changing the `<canvas id="board">` `width`/`height` in `index.html` to match (`COLS*BLOCK` × `ROWS*BLOCK`).
- Merge assumes the piece is fully on the board (rows/fills must be validated by `collide` before lock).
- Keep it dependency-free and build-free; add no package.json unless explicitly asked.
