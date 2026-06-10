# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Git workflow

After completing any meaningful unit of work, commit and push to GitHub so progress is never lost:

```powershell
git add <changed files>
git commit -m "short imperative summary of what changed and why"
git push
```

Commit at logical checkpoints — a new feature, a bug fix, a meaningful refactor — not after every line. Commit messages should be concise and describe the change, not the task ("add wrap-around movement" not "update shooter.html").

## Running

Open either HTML file directly in a browser — no build step or server needed.

```powershell
Start-Process tictactoe.html
Start-Process shooter.html
```

## Architecture

Both games are self-contained single-file apps (HTML + CSS + JS in one file). There is no build system, no dependencies, and no module boundaries.

### tictactoe.html

Straightforward DOM-based game.

- `WINS` — 8 hardcoded win-combination index arrays, checked on every move
- State lives in three variables: `board` (9-element array), `current` ('X'/'O'), `over` (boolean), plus a `scores` object (`{X, O, D}`)
- `init()` resets board and DOM; `checkWin()` returns the winning triplet or undefined; `handleClick()` drives the full turn sequence
- Scoreboard is in-memory only — it resets on page refresh

### shooter.html

Canvas game (800×600 internal resolution, `image-rendering: pixelated`).

**State machine** — `gameState` string drives everything: `MENU → PLAYING → LEVEL_COMPLETE → GAME_OVER → SCORE_SCREEN`. `setState(s)` is the only function that transitions between states and triggers side effects (level init, high-score save).

**Game loop** — `requestAnimationFrame(loop)` calls `update(now)` then `draw(now)` every frame. `dt` is clamped to max 3× a 16.67 ms frame so a tab in the background can't cause a physics explosion on re-focus.

**Entity pools** — `enemies`, `bullets`, and `particles` are plain arrays; dead entries are marked `active: false` and filtered out each frame.

**Enemy types** (defined in `LEVELS` array, spawned via `spawnWave`):
- `chaser` — always moves toward the player
- `sniper` — chases until within 200 px, then stops and fires after a 1.5 s aim delay
- `tank` — slow, high-HP (6), enrages at ≤2 HP (speed boost + color change)

**Particles** — four named presets (`spark`, `flash`, `blood`, `explosion`) defined in `P_CFG`; drawn on two layers (0 = behind entities, 1 = in front) to control draw order without a z-sort.

**High score** — persisted to `localStorage` under the key `retroShooterHiScore`. Written only on game-over/completion via `saveHighScore()`.

**Input** — keyboard state in a `keys` object (set/cleared on keydown/keyup); mouse position scaled from CSS pixels to the 800×600 canvas coordinate space on every `mousemove`.
