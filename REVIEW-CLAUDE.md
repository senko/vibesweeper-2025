# Minesweeper AI Implementation Code Review

## 1. gpt-oss (GPT Open Source)

**Strengths:**
- Clean, minimal code
- Good CSS with number colors
- Responsive design

**Issues:**
- **No first-click protection** - Mines are placed before gameplay starts, so first click can hit a mine
- **Recursive flood-fill without stack overflow protection** - Large empty areas could cause issues
- **No mobile touch support for flagging** - Right-click only works on desktop
- **Timer doesn't start** - No timer implementation visible in the status area

---

## 2. gemini

**Strengths:**
- First-click safety zone (3x3 area)
- Timer with proper formatting
- Polished dark UI with animations
- Difficulty presets

**Issues:**
- **Potential infinite loop** when placing mines on hard difficulty - if safe zone is large and board is small, placement loop could stall
- **No mobile long-press support** for flagging
- **Flag limit not enforced** - `flagsLeft` can go negative if not properly checked

---

## 3. deepseek

**Strengths:**
- Feature-rich - has hint button, difficulty presets, mobile long-press support, nice win/lose modal

**Issues:**
- **First-click only protects single cell** (not surrounding cells) - still risky
- **Re-renders entire grid on every action** (`renderGrid()`) - inefficient DOM manipulation
- **`touchstart` prevents default** - may break scrolling or other touch behaviors
- **Hint button reveals random safe cell** - trivializes the game
- **External CDN dependency** (Font Awesome) - won't work offline

---

## 4. qwen

**Strengths:**
- Clean state management
- First-click protection (single cell only)
- Timer

**Issues:**
- **Mines placed at init, not on first click** - Comment says "except on first click position" but mines are placed in `createBoard`, before any click
- **Re-renders entire board after every cell click** - very inefficient
- **No mobile flagging support**
- **Timer doesn't stop on game loss** - keeps checking `gameOver` in interval but timer continues if not won

---

## 5. codex

**Strengths:**
- First-click safety with 3x3 zone
- Good difficulty presets
- Chord reveal (double-click/shift-click)
- Custom board size support
- Clean modular code

**Issues:**
- **Mines input allows up to 667** but max cells on hard is 480 (16×30) - no validation
- **`floodReveal` modifies queue while iterating** - `queue.shift()` then pushes to same queue via `revealCell` - works but could be cleaner
- **Status message "Getting close..." triggers on flag count** - misleading when flags don't equal mines

---

## 6. kimi-k2

**Strengths:**
- First-click protection (single cell)
- Clean, minimal code

**Issues:**
- **Incorrect neighbor calculation** - The formula `i + x + y * gridSize` doesn't properly handle row wrapping. Cells at row edges will incorrectly check cells on adjacent rows
- **Uses `alert()` for game over** - poor UX, blocks the thread
- **No first-click timer start** - timer never starts
- **No visual timer display** - completely missing
- **Square-only boards** - Hard mode (30×16) is treated as 30×30

---

## 7. llama

**Strengths:**
- First-click protection (single cell)
- Difficulty presets
- Game over modal with animation

**Issues:**
- **Win condition requires all mines to be flagged** - non-standard, you should win by revealing all safe cells regardless of flags
- **No mobile flagging support**
- **Uses `parseInt` on dataset values** - works but `+` would be cleaner

---

## 8. claude

**Strengths:**
- First-click 3x3 safe zone
- Face button with expressions
- Good animations
- Proper win condition (reveal all non-mines)

**Issues:**
- **Fixed cell size (36px)** breaks layout on hard mode (16×30) - board overflows container
- **No chord support** (middle-click to reveal around satisfied numbers)
- **No mobile flagging support**

---

## 9. devstral

**Strengths:**
- First-click protection (single cell)
- Good classic Minesweeper styling

**Issues:**
- **Difficulty settings don't update gameState** - `setDifficulty` resets to 10×10 in `initGame()`, ignoring the difficulty button selection (lines 291-295 override switch statement values)
- **Custom font `Digital-7`** uses invalid URL - won't load
- **Flag limit prevents flagging** when count equals mines even if some flags are wrong
- **No mobile flagging support**
- **Timer can exceed 999** - no max cap

---

## Summary Table

| Agent | First-Click Safe | Mobile Flag | Efficient DOM | Timer | Major Bug |
|-------|------------------|-------------|---------------|-------|-----------|
| gpt-oss | :x: | :x: | :white_check_mark: | :x: | First click can die |
| gemini | :white_check_mark: (3x3) | :x: | :white_check_mark: | :white_check_mark: | Potential infinite loop |
| deepseek | :warning: (1 cell) | :white_check_mark: | :x: | :white_check_mark: | Full re-render |
| qwen | :x: | :x: | :x: | :warning: | Mines placed at init |
| codex | :white_check_mark: (3x3) | :x: | :white_check_mark: | :white_check_mark: | Minor validation |
| kimi-k2 | :warning: (1 cell) | :x: | :white_check_mark: | :x: | **Broken neighbor calc** |
| llama | :warning: (1 cell) | :x: | :white_check_mark: | :white_check_mark: | Wrong win condition |
| claude | :white_check_mark: (3x3) | :x: | :white_check_mark: | :white_check_mark: | Layout overflow |
| devstral | :warning: (1 cell) | :x: | :white_check_mark: | :white_check_mark: | **Difficulty broken** |

---

## Conclusions

**Best implementations:** **codex** and **gemini** - fewest critical bugs, most complete features.

**Most problematic:** **kimi-k2** (broken neighbor logic) and **devstral** (difficulty selection doesn't work).
