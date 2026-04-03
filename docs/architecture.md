# SNAKE-001 — Standalone HTML5 Snake Game

## 1. Project Overview

- **Type:** Single HTML file, zero dependencies, no build step
- **File:** `index.html` — self-contained Canvas-based game
- **Repo:** https://github.com/riyubolted900-cyber/games
- **Target:** Desktop browsers (Chrome, Firefox, Safari)

---

## 2. Game Mechanics

### 2.1 Grid & Movement

| Parameter | Value |
|-----------|-------|
| Grid size | 20×20 cells |
| Cell size | 20 px |
| Canvas size | 400×400 px |
| Initial snake length | 3 cells |
| Initial direction | Right |
| Initial speed | 120 ms/tick |
| Speed increase | −2 ms per food eaten (min 60 ms) |

### 2.2 Snake Movement

- Snake is an array of `{x, y}` cell coordinates
- Head moves to new cell each tick; tail is removed unless growth pending
- **Growth:** eating food appends 1 cell to snake (skip tail removal for 1 tick)
- Movement wraps **not** enabled — wall collision = death

### 2.3 Food

- Spawns at random unoccupied cell
- Always exactly 1 food on screen during PLAYING state
- Collision = head overlaps food cell → score +1, grow snake, spawn new food, speed up

### 2.4 Collision & Death

- **Wall collision:** head x < 0 OR head x ≥ gridWidth OR head y < 0 OR head y ≥ gridHeight
- **Self collision:** head overlaps any body segment
- Either collision → GAME_OVER state

### 2.5 Scoring

| Event | Points |
|-------|--------|
| Eat food | +10 |
| High score | Persisted to `localStorage` key `snakeBest` |

---

## 3. Game States

```
START → (input) → PLAYING → (collision) → GAME_OVER → (input) → PLAYING
```

### 3.1 START Screen
- Title: "SNAKE" in bold retro font
- Subtitle: "Press SPACE or click to start"
- High score displayed if > 0
- Snake logo/graphic (optional decorative snake)

### 3.2 PLAYING State
- Game loop runs at `requestAnimationFrame`, tick-gated by speed interval
- Snake moves one cell per tick
- Food visible
- Score displayed top-right or above canvas

### 3.3 GAME_OVER State
- Overlay on top of frozen game board
- Text: "GAME OVER" centered
- Current score + high score displayed
- "Press SPACE or click to restart" prompt

---

## 4. Visual Layout

```
┌──────────────────────────────────┐
│  SCORE: 0          BEST: 42     │
│ ┌────────────────────────────────┐
│ │                                │  ← Canvas 400×400
│ │     Snake + Food rendering     │
│ │                                │
│ └────────────────────────────────┘
│  ← ↑ ↓ → or WASD to move         │
└──────────────────────────────────┘
```

### 4.1 Color Palette

| Element | Color |
|---------|-------|
| Background (page) | `#0f0f23` (deep navy) |
| Canvas background | `#1a1a2e` (dark blue-gray) |
| Grid lines | `#252540` (subtle) |
| Snake head | `#4ade80` (bright green) |
| Snake body | `#22c55e` (green) |
| Snake body gradient | darker toward tail |
| Food | `#ef4444` (red) with pulse animation |
| Text | `#e2e8f0` (off-white) |
| Accent | `#fbbf24` (amber, for score highlights) |

### 4.2 Typography

- Font: `"Courier New", Courier, monospace` (retro terminal feel)
- Title: bold, ~36px
- Score: ~20px
- Controls hint: ~14px, muted color

### 4.3 Snake Rendering

- Each cell drawn as a rounded rectangle
- Head: brighter green + small "eyes" (2 dots) facing direction of travel
- Body: slightly darker green, rounded corners
- Tail: slightly different shade to distinguish from body

### 4.4 Food Rendering

- Red circle or rounded square
- Subtle pulse/glow animation (scale or shadow oscillation)

---

## 5. Controls

| Input | Action |
|-------|--------|
| Arrow keys (←↑↓→) | Change direction |
| WASD | Alternative direction controls |
| Spacebar | Start / Restart |
| Click / Tap | Start / Restart (mobile) |

### 5.1 Direction Lock

- Cannot reverse direction (180° turn) — prevents instant self-collision
- Buffer the next direction input during the tick to prevent missed inputs on fast play

---

## 6. Component Breakdown

### 6.1 Game State Machine
```
state: 'start' | 'playing' | 'gameover'
- Manages transitions
- Controls what renders and what inputs are accepted
```

### 6.2 Snake Entity
```
snake: Array<{x: number, y: number}>   // head at index 0
direction: 'up' | 'down' | 'left' | 'right'
nextDirection: queued direction change
growthPending: number   // cells to grow by
```

### 6.3 Food Entity
```
food: {x: number, y: number}
```

### 6.4 Game Loop
```
lastTick: timestamp
tickInterval: ms (starts at 120, decreases)

loop():
  now = timestamp
  if now - lastTick >= tickInterval:
    applyDirection()
    moveSnake()
    checkCollisions()
    checkFood()
    lastTick = now
  render()
  requestAnimationFrame(loop)
```

### 6.5 Renderer
- Clears canvas
- Draws grid (optional, subtle lines)
- Draws food (with pulse)
- Draws snake (head with eyes, body gradient, tail)
- Draws HUD (score, high score)

### 6.6 Input Handler
- `keydown` listener for arrows, WASD, spacebar
- `click` / `touchstart` for start/restart
- Queues direction change (applied at next tick, not immediately)

### 6.7 Collision Detector
- `isWallCollision(head, gridWidth, gridHeight)` → bool
- `isSelfCollision(head, body[])` → bool

### 6.8 Score Manager
- `currentScore: number`
- `highScore: number` — loaded/saved via localStorage
- Updates on food eaten

---

## 7. Game Feel

- Snake head always visible, eyes animate direction
- Brief screen shake on death (optional, CSS transform on canvas wrapper)
- Food has a subtle bounce-in spawn animation
- Score flashes briefly on increment
- Game over overlay fades in (CSS transition)

---

## 8. File Structure

```
games/
  snake/
    index.html   ← the game (single file, all inline CSS/JS)
```

---

## 9. Acceptance Criteria Summary

| Issue | Feature |
|-------|---------|
| #1 | Canvas rendering + game loop |
| #2 | Snake movement + direction input |
| #3 | Food spawning + eating + growth |
| #4 | Collision detection (walls + self) |
| #5 | Scoring + high score persistence (localStorage) |
| #6 | Game states: start, playing, gameover |
| #7 | Controls: arrows, WASD, space, click/touch |
| #8 | Game over screen + restart |
