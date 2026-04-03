# SNAKE-001 — Design Decisions

## Why HTML5 Canvas over DOM/SVG

Canvas is the standard choice for real-time grid-based games. It offers:
- Direct pixel control with `requestAnimationFrame` for smooth 60fps rendering
- No DOM node overhead (unlike SVG `<rect>` per cell)
- Mature, well-understood API across all target browsers
- Consistent with the Flappy Bird and Tetris implementations already in the repo

**Alternative considered:** DOM-based grid (divs per cell). Rejected — slower at 400×400 with 60fps updates.

---

## Why Single HTML File (Zero Dependencies)

Per project constraints: "no external dependencies beyond vanilla HTML/CSS/JS." This means:
- No CDN links, no frameworks, no npm
- Works offline and on any static host (GitHub Pages, Netlify, etc.)
- Matches the existing `flappy-bird` and `tetris` patterns in the repo
- Fast first load — no fetch waterfalls

---

## 400×400 Canvas with 20×20 Grid

Rationale: 400px is a good balance of visibility and snappy movement. 20px cells are large enough for clear rendering on all target browsers. Grid fits naturally on 1280×720+ displays and remains usable on smaller screens.

**Alternative considered:** 600×600 (15×15 grid). Rejected — larger canvas, slower perceived movement.

---

## 120ms Tick Interval, Decreasing to 60ms Min

Snake games feel best when responsive but not frantic. 120ms = ~8 moves/second for the snake — fast enough to feel snappy, slow enough to react. Speed increases by 2ms per food (a small but cumulative reward feel), capped at 60ms to remain humanly playable.

**Alternative considered:** Constant speed (no speed increase). Rejected — makes the game feel static. Speed increase is classic Snake dopamine.
**Alternative considered:** Faster initial speed (80ms). Rejected — less reaction time, harder to appreciate the game on first play.

---

## Direction Buffering (Queue Next Input)

A common Snake frustration: pressing a direction key just before a tick executes the wrong move (especially at higher speeds). Solution: queue the next direction and apply it at the next tick. This makes controls feel more forgiving.

**Alternative considered:** Immediate direction application. Rejected — leads to missed inputs and accidental 180° reversals at speed.

---

## No Wrap-Around (Wall = Death)

Classic arcade Snake — walls are deadly. Wrap-around (snake exits right, enters left) is a modern variation. Sticking to walls = death matches the Flappy Bird and Tetris philosophy of "classic feel, tight controls."

---

## localStorage Key: `snakeBest`

Simple string key `snakeBest` for the high score. This is different from the Flappy Bird key (`flappyBest`) to avoid collisions in the same origin. Tetris is on a separate repo so no conflict there.

---

## Retro-Modern Aesthetic

The spec calls for "clean, retro-modern aesthetic":
- Deep navy background (`#0f0f23`) with subtle grid lines
- Monospace font (Courier New) — terminal/retro feel
- Green snake with gradient toward tail — readable, visually interesting
- Red food with pulse animation — draws the eye
- Amber accent for score highlights — warm, arcade feel

**Not pixel art:** The snake is drawn with rounded rectangles, not pixel arrays. Cleaner and scales better.
**Not neon-maximalist:** No heavy glow effects. The palette is dark and focused.

---

## No Audio

No audio dependencies. Audio could be added later via the Web Audio API without restructuring the game. Matches existing games in the repo (Flappy Bird and Tetris both have no audio).

---

## Mobile Support

Controls: arrow keys for desktop, click/tap for start/restart on mobile. The game is portrait-friendly (400×400 square) and scales via CSS `max-width: 100%` to fit mobile viewports. Touch controls could be extended with on-screen D-pad in a future iteration.

---

## GitHub Issues Strategy

8 issues created covering:
1. Core loop + canvas
2. Movement + input
3. Food mechanics
4. Collision detection
5. Scoring + persistence
6. Game states
7. Controls
8. Game over + restart

Issues are intentionally granular so Spacecow can parallelize build work cleanly. Each issue is a single-feature chunk that can be built and verified independently.
