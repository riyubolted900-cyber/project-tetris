# Flappy Bird Clone — SPEC.md

## Project Overview
- **Type:** Single HTML5 canvas game, no build step, zero dependencies
- **File:** `games/flappy-bird/index.html`
- **Feel:** Classic Flappy Bird — tight, responsive, instant retry

---

## Game Mechanics

### Physics
| Parameter       | Value         |
|----------------|---------------|
| Gravity        | 0.45 px/frame² |
| Flap impulse   | -7.5 px/frame (upward) |
| Max fall speed | 10 px/frame   |
| Bird hitbox    | ~28×20 px (elliptical) |

### Pipe System
| Parameter         | Value              |
|-------------------|--------------------|
| Pipe width        | 52 px              |
| Gap height        | 145 px             |
| Horizontal spacing | 200 px            |
| Scroll speed      | 2.8 px/frame       |
| Pipe cap style    | 10 px tall lip on each end |

### Collision Detection
- **Bird vs Pipe:** AABB with reduced hitbox (2 px inset on each side)
- **Bird vs Ground:** y > canvas height - ground height (50 px)
- **Bird vs Ceiling:** y < 0 (bird can reach top but bounces slightly)

### Scoring
- +1 point when bird's x passes the trailing edge of a pipe pair
- Score displayed center-top, large font, white with black stroke

---

## Visual Spec

### Canvas
- **Size:** 288 × 512 px (classic portrait ratio, scaled to fit viewport)
- **Renderer:** HTML5 Canvas 2D API, 60fps via `requestAnimationFrame`

### Background — Day Sky
- Solid sky color: `#70c5ce`
- No parallax (single layer for simplicity)

### Ground
- Height: 50 px
- Color: `#ded895` (sand/tan)
- Top border: dark green stripe `#535e3a`, 10 px

### Bird
- **Size:** ~28 × 20 px (drawn as ellipse)
- **Colors:**
  - Body: `#f7dc6f` (yellow)
  - Wing: `#e8b30f` (dark yellow/orange)
  - Eye: white circle + black pupil
  - Beak: `#e74c3c` (red-orange) upper + `#f39c12` (orange) lower
- **Animation:** 3-frame wing flap cycle (up / mid / down), cycling at ~12 fps

### Pipes
- **Body color:** `#73bf2e` (classic green)
- **Cap color:** `#558b2f` (darker green) — 10 px tall lip on each end
- **Cap width:** extends 3 px on each side (total cap = pipe_width + 6)

### Score Display
- Font: bold sans-serif, ~48 px
- Color: white with 2 px black stroke
- Position: top-center, 60 px from top

### Ready Screen (Initial State)
- Title: "Flappy Bird" in bold, dark color
- Subtitle: "Click or press SPACE to start"
- Bird gently bobbing (sinusoidal, no pipes)

### Game Over Screen
- Semi-transparent dark overlay
- "GAME OVER" text center
- Score panel: current score + best score (persisted to `localStorage` key `flappyBest`)
- "Play Again" / "Restart" prompt

---

## Controls

| Input              | Action              |
|--------------------|---------------------|
| Spacebar           | Flap                |
| Click / Tap        | Flap (mobile)       |
| Touch (touchstart) | Flap (mobile)       |

- All inputs work during all game states (playing, game-over restart)

---

## Game States

```
READY → (input) → PLAYING → (collision) → GAME_OVER → (input) → PLAYING
```

### READY
- Bird bobs up/down
- No pipes spawning
- No gravity acting (bird floating)

### PLAYING
- Gravity pulls bird down
- Pipes scroll right-to-left
- Scoring active

### GAME_OVER
- Bird falls (gravity continues)
- All pipe movement stops
- Overlay shown
- Best score updated in localStorage

---

## Game Feel

- **Flap responsiveness:** Flap instantly zeroes out vertical velocity (or sets it to flap impulse), no animation delay
- **Death animation:** Bird tumbles (rotates) as it falls off-screen; brief screen flash on collision (white overlay, 50ms)
- **Sound:** None (no audio dependencies)

---

## File Structure

```
games/
  flappy-bird/
    SPEC.md        ← this file
    index.html     ← the game (single file)
```

---

## Acceptance Criteria

1. Game renders at 288×512, scales to fit viewport
2. Bird responds to spacebar, click, and touch with immediate flap
3. Pipes generate at correct intervals with correct gap
4. Collision with pipe or ground triggers game over
5. Score increments correctly per pipe passed
6. Best score persists across sessions via localStorage
7. Game restarts cleanly on input after game over
8. Wing flap animation cycles while alive
9. Death tumble animation plays
10. Works on mobile (touch events, viewport scaling)
