# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Games

No build step or server required. Open any `.html` file directly in Chrome or Firefox:

```bash
start chrome shooter.html
start chrome tictactoe.html
```

## Git & GitHub Workflow

After every change:
1. Update `README.md` to reflect what changed
2. Commit with a clean, descriptive message (imperative mood)
3. Push to origin

```bash
git add <files>
git commit -m "description"
git push
```

The `gh` CLI is installed at `C:\Program Files\GitHub CLI\gh.exe` (not on PATH — call directly if needed).
Remote: `https://github.com/sdsdsdasa/browser-games` (public).

## Architecture

Each game is a **single self-contained HTML file** with three sections:
- `<style>` — page/canvas layout (dark background, centered canvas)
- `<canvas>` — 800×600 rendering surface
- `<script>` — all game logic (no external dependencies, no modules)

### shooter.html structure

The script is organized top-to-bottom as:
1. **Constants** — speeds, radii, cooldowns, `LEVEL_DATA` array, `ENEMY_DEFS` map
2. **State variables** — `state` string (`menu | playing | levelComplete | gameOver`), score, level, wave counters
3. **Input handlers** — `keys` object (keydown/keyup), `mouse` object (mousemove/mousedown)
4. **Screen shake** — `triggerShake(amt, dur)` modulates a canvas `ctx.translate` each frame
5. **Web Audio** — lazy `AudioContext` init; `beep(freq, dur, vol, type)` for all SFX
6. **Player object** — single mutable object with `reset()` method
7. **Collections** — `bullets`, `enemyBullets`, `enemies`, `particles`, `muzzleFlashes`, `floatingTexts` (plain arrays, filtered each frame)
8. **Draw functions** — `drawBackground`, `drawPlayer`, `drawEnemy`, `drawBullet`, `drawHUD`, overlay screens
9. **Update functions** — `updatePlayer`, `updateBullets`, `updateEnemies`, `checkCollisions`, `updateWaveLogic`, `updateParticles`
10. **Game flow** — `startGame`, `startLevel`, `nextLevel`, `spawnWave`, `spawnEnemy`, `hitPlayer`
11. **Main loop** — single `requestAnimationFrame` loop; delta-time capped at 50ms; shake applied via `ctx.save/translate/restore`

All rendering uses Canvas 2D API primitives only (arcs, rects, fills) — no images or sprites.

Enemy behavior is type-switched inline inside `updateEnemies`. Adding a new enemy type requires: an entry in `ENEMY_DEFS`, a draw branch in `drawEnemy`, and a movement branch in `updateEnemies`.
