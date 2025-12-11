# Minesweeper Implementations – Review

## Claude (`claude/index.html`)
- Flood fill uses unbounded recursion; large empty areas can overflow the call stack and freeze the page.
- Flags are unlimited; the mines-left counter can go negative and does not reflect actual mine count, which misleads players.

## Codex (`codex/index.html`)
- Empty-cell expansion is broken: the flood routine never propagates because the neighbor queue is ignored, so only the clicked region opens.
- First-click safety can silently reduce the requested mine count when space is tight, altering custom settings without feedback.

## DeepSeek (`deepseek/index.html`)
- Difficulty clicks call `changeDifficulty` without passing the event, yet the handler uses `event.target`; in strict mode this can fail or ignore selection.
- Mine placement loop lacks a cap; requesting too many mines can spin indefinitely.

## DevStral (`devstral/index.html`)
- Difficulty changes do not stick: `setDifficulty` sets rows/cols/mines then `initGame` resets to 10×10×10.
- Right-click is blocked until after the first left-click, preventing pre-flagging suspected mines.

## Gemini (`gemini/index.html`)
- Right-clicks don’t suppress the browser context menu, making flagging unreliable on desktop.
- Flood fill is fully recursive; large openings risk stack overflows and hangs.

## GPT-OSS (`gpt-oss/index.html`)
- No first-click protection; the opening move can detonate a mine.
- Recursive flood fill can overflow the call stack on large empty regions.

## Kimi-K2 (`kimi-k2/index.html`)
- Win check requires every mine to be flagged; clearing all safe cells without flagging all mines never wins (nonstandard behavior).
- Flags are unrestricted, so over-flagging can prevent win detection; first-click safety only avoids the clicked cell, not its neighbors.

## Llama (`llama/index.html`)
- Neighbor counts use linear indexing without column bounds, causing wrapped counts/reveals across rows (logic incorrect).
- “Hard” advertises 30×16 but code uses a 30×30 grid; no first-click protection.

## Qwen (`qwen/index.html`)
- Mines are placed during init rather than after the first click; the opening move can hit a mine despite the comment.
- Timer interval is never cleared on win/loss, so it keeps running.
- Flags are unbounded; “mines left” can go negative.
