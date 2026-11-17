# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no build, no tests, no linter. UI text and README are in Spanish.

## Run

- `open index.html`, or serve statically: `python3 -m http.server 8000` → http://localhost:8000

## Architecture

Three files: `index.html` (DOM + canvases `#board` 300x600, `#next-canvas` 120x120, `#overlay`), `style.css`, `game.js` (all logic, loaded as a classic script, no modules).

`game.js` notes:
- Game state lives in module-level `let` globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, ...), reset by `init()`. Restart button calls `init()`.
- Board is `ROWS x COLS` matrix; cell values 1–7 index into both `COLORS` and `PIECES` (0 = empty). Piece shape cells carry their own type index.
- Loop: `requestAnimationFrame` → `loop(ts)` accumulates `dropAccum` against `dropInterval`; gravity is inlined in `loop`, separate from `softDrop()` (which adds score).
- Piece lifecycle: `lockPiece()` = `merge()` → `clearLines()` → `spawn()`; `spawn()` ends the game if the new piece collides. `clearLines()` also updates level and `dropInterval` (`max(100, 1000 - (level-1)*90)`).
- Rotation: `rotateCW` + `tryRotate` wall kicks of x offsets `[0,-1,1,-2,2]` only (no SRS, no y kicks).
- Pause/game-over reuse the same `#overlay`; pause cancels the rAF and resumes by resetting `lastTime`.
- Piece selection is pure `Math.random()` (no 7-bag).
