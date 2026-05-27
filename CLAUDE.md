# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the app

Open `index.html` directly in a browser — no build step, no server required. There are no dependencies, no package manager, and no test suite.

## Architecture

The entire application lives in a single `index.html` file with three sections:

**CSS (lines 7–177):** Dark purple theme. Cell highlight states (`.selected`, `.peer`, `.same-val`, `.error`, `.given`, `.player`) drive visual feedback. The 3×3 box borders are achieved with CSS `:nth-child` selectors, not JS.

**HTML (lines 179–224):** Static markup for toolbar (difficulty select, New Game, Hint), stats bar (timer, mistakes), the `#grid` div (filled entirely by JS), numpad buttons, and a win overlay.

**JavaScript (lines 226–539):** No frameworks. Structured in layers:
- *Sudoku engine* — `isValid`, `solve` (backtracking, optionally randomized), `countSolutions` (used to enforce unique solutions), `generatePuzzle` (removes cells while verifying uniqueness).
- *State* — a single `state` object holds `puzzle` (given clues), `solution` (the full answer), `player` (user entries), `notes` (per-cell `Set`), selection, mistake count, timer, and `solved` flag.
- *Rendering* — `render()` rebuilds the entire grid from scratch on every state change; no partial updates.
- *Interaction* — `enterNumber(n)` handles both note mode and normal entry, increments mistakes on wrong answers, clears peer notes on correct ones. Keyboard (digits, Backspace, `N`, arrow keys) and numpad clicks both route here.
- *Game lifecycle* — `newGame()` resets state and calls `generatePuzzle`; `startTimer()` runs `setInterval`; `checkWin()` compares `player` against `solution`.

**Key constants:** `CLUES = { easy: 36, medium: 30, hard: 24 }` controls how many givens remain per difficulty (more clues = easier).
