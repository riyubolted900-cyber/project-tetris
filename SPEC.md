# TETRIS-001 — Standalone HTML Tetris Game

## 1. Project Overview

- **Type:** Single HTML file, zero dependencies, no build step
- **File:** `index.html` — self-contained Canvas-based game
- **GitHub:** https://github.com/riyubolted900-cyber/project-tetris
- **Target:** Desktop browsers (Chrome, Firefox, Safari)

---

## 2. Game Mechanics

### 2.1 Tetrominoes (7 Standard Pieces)

| Piece | Shape (SRS spawn) | Color |
|-------|-------------------|-------|
| **I** | `[[0,0,0,0],[1,1,1,1],[0,0,0,0],[0,0,0,0]]` | Cyan `#00CED1` |
| **O** | `[[1,1],[1,1]]` | Yellow `#FFD700` |
| **T** | `[[0,1,0],[1,1,1],[0,0,0]]` | Purple `#9370DB` |
| **S** | `[[0,1,1],[1,1,0],[0,0,0]]` | Green `#32CD32` |
| **Z** | `[[1,1,0],[0,1,1],[0,0,0]]` | Red `#FF4500` |
| **J** | `[[1,0,0],[1,1,1],[0,0,0]]` | Blue `#4169E1` |
| **L** | `[[0,0,1],[1,1,1],[0,0,0]]` | Orange `#FFA500` |

### 2.2 Super Rotation System (SRS)

- Rotation states: 0 (spawn), 1 (90° CW), 2 (180°), 3 (270° CW)
- Wall kick data (SRS standard, T/S/I/L/J/Z):
  ```
  L|J|S|Z|T: [ [[0,0],[-1,0],[-1,-1],[0,2],[-1,2]], ... ] per rotation
  I:          [ [[0,0],[-2,0],[1,0],[-2,1],[1,-2]], ... ] per rotation
  ```
- O piece does not rotate (centered 2×2 square)

### 2.3 Scoring Table (Guideline)

| Action | Points |
|--------|--------|
| 1 line (Single) | 100 × level |
| 2 lines (Double) | 300 × level |
| 3 lines (Triple) | 500 × level |
| 4 lines (Tetris) | 800 × level |
| Soft drop (per cell) | 1 |
| Hard drop (per cell) | 2 |

### 2.4 Level Progression

- Start at **Level 1**
- Level up every **10 lines cleared**
- Drop interval = `Math.max(100, 1000 - (level - 1) * 100)` ms
  - Level 1: 1000ms, Level 5: 600ms, Level 10: 100ms

---

## 3. Visual Layout

```
┌──────────────────────────────────────────────┐
│  TETRIS                                      │
│  ┌────────────────┐  ┌──────────────┐         │
│  │                │  │  NEXT        │         │
│  │                │  │  ┌────────┐  │         │
│  │   10×20 GRID   │  │  │ piece  │  │         │
│  │   (main board) │  │  └────────┘  │         │
│  │                │  │              │         │
│  │                │  │  SCORE       │         │
│  │                │  │  000000      │         │
│  │                │  │              │         │
│  │                │  │  LEVEL       │         │
│  │                │  │  01          │         │
│  │                │  │              │         │
│  │                │  │  LINES       │         │
│  │                │  │  000         │         │
│  └────────────────┘  └──────────────┘         │
│                                              │
│  ← → ↓ : Move  |  ↑ : Rotate  |  SPACE: Drop │
│  P : Pause  |  R : Restart                   │
└──────────────────────────────────────────────┘
```

- **Main board canvas:** 10 cols × 20 rows, 30px per cell = 300×600px
- **Preview canvas:** 4×4 grid, 25px per cell = 100×100px
- **Stats panel:** Score, Level, Lines — monospace font
- **Controls hint:** Displayed below canvas
- **Background:** Dark `#111`, board background `#1a1a2e`, grid lines `#333`

---

## 4. UX Details

### 4.1 Controls

| Key | Action |
|-----|--------|
| `←` / `→` | Move piece left/right |
| `↓` | Soft drop (accelerated fall) |
| `↑` | Rotate clockwise |
| `Space` | Hard drop (instant placement) |
| `P` | Toggle pause |
| `R` | Restart game (resets score/board) |

### 4.2 Pause Screen

- Overlay with semi-transparent dark layer
- Text: **"PAUSED"** centered on board
- Resume on `P` key

### 4.3 Game Over Screen

- Triggered when a new piece cannot spawn (top of board occupied)
- Overlay with text: **"GAME OVER"** + final score
- Prompt: **"Press R to restart"**
- Board remains visible underneath

### 4.4 Ghost Piece

- Displayed as outline/ghost showing where the piece will land
- Same shape as current piece, rendered at drop-destination row

---

## 5. Technical Approach

- **Single `index.html`** — all CSS and JS inline
- **Rendering:** HTML5 Canvas 2D API, 60fps game loop via `requestAnimationFrame`
- **Input:** `keydown` event listener on `window`
- **State:** Plain JS objects (board array, current piece, next piece, score)
- **No external dependencies** — no CDN, no frameworks, no build tools

---

## 6. Acceptance Criteria Summary

| Issue | Feature |
|-------|---------|
| #1 | Game loop + canvas rendering |
| #2 | Tetromino generation + SRS rotation |
| #3 | Collision detection + line clearing + scoring |
| #4 | Level progression + speed increase |
| #5 | Game over, pause, restart, controls |
