# Tic Tac Toe

A single-file browser game. All HTML, CSS, and JS live in `tictactoe.html`.

## Running

Open `tictactoe.html` directly in a browser — no build step or server needed.

On Windows you can also launch it from the terminal:

```powershell
Start-Process tictactoe.html
```

## Structure

- `tictactoe.html` — the entire app (markup, styles, game logic)
- 3×3 grid, two-player local, win detection via 8 hardcoded win combinations
- Scoreboard persists across rounds until the page is refreshed
