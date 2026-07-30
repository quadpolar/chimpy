# Chimpy version history

One continuous counter. It does not reset for renames. The running build
shows its number in the game, faint, under BEST.

| Build | Cache name    | What changed |
|-------|---------------|--------------|
| 13    | `thread-v13`  | The build liked on day one. RAMP 38, opening 238→58. |
| 14    | `chimpy-v1`   | Same game logic, renamed from Thread to Chimpy. |
| 15    | `chimpy-v2`   | Gangly character with the frown. Fixed a hard line across the ground and removed the full-screen flash when clearing a gap. |
| 16    | `chimpy-v3`   | New audio — sine only, filtered, reverb. Fixed the blue bar at the bottom (the canvas was pinning its own size and could never grow). Score moved clear of the status bar. |
| 17    | `chimpy-v4`   | Portrait only: the scene rotates when the phone does, since iOS has no orientation lock for web apps. **Runs capped around 31.** |
| 18    | `chimpy-v18`  | Difficulty rework. Numbering switched to one continuous counter here. |
| 19    | `chimpy-v19`  | Removed the aim dots in front of the monkey — a leftover from when the release point had to be judged rather than aimed at a visible gap. |

Builds 14–17 used a second counter that restarted at 1 after the rename,
which made them impossible to place against build 13. That is why 18
follows 17 rather than continuing the chimpy-vN sequence.

## Build 18 in detail

**Fixed genuinely impossible gaps.** A swing carries the energy it arrived
with, clamped to ±13% of nominal, but generation only ever validated gaps
against the nominal speed. Arrive slow and every trajectory falls short — so
at maximum difficulty 22.7% of gaps were unclearable, and 4.5% at gap 31.
Generation now tests each candidate against the weakest allowed arrival.
Measured result: 0.0% impossible at every difficulty.

**Ramp stretched** from 38 to 85 gaps, floor opening 58px → 88px.

**Difficulty breathes** instead of closing in a straight line: a ±16% wave on
top of the ramp, an outright breather at 1.3× width every sixth gap, and hard
segments gated so a double, split lane or spike is always followed by two
plain ones. Verified: zero back-to-back hard segments across 400.

**Tight spikes** on about 11% of segments — the opening drops to 62% of the
ramp value. Still validated against a weak arrival, so always passable.

**Openings aim high and low.** Candidate selection now scores on opening
height as well as branch height. Spread went from a narrow mid band to
288–607 with sd 62.


## Build 23 — the bar at the bottom, measured

Three previous attempts failed because each fixed a plausible cause rather
than the actual one. Measuring the screenshot settled it: the bar was
exactly 47 CSS points tall and its colour was (185, 204, 209), which is
`#B9C9D6` — the page background colour. So the canvas really was 47pt short
of the screen, and nothing about the drawing was involved.

The cause was in the layout chain: `body` carried `position:fixed; inset:0`
**and** `height:100%`. On a fixed element an explicit height overrides
`bottom:0`, so when iOS reported a layout viewport shorter than the screen,
`#stage` and the canvas inherited the shortfall.

- `width`/`height:100%` removed from html, body and the canvas, so `inset:0`
  governs and cannot come up short.
- `min-height:100dvh` added, covering the case where the dynamic viewport is
  taller than the layout viewport.
- The backing store now takes the tallest of the wrapper rect,
  `window.innerHeight` and `documentElement.clientHeight`. These can disagree
  by tens of points in standalone and the smallest one was winning.
- The page background was set to the game's own colour at the bottom of the
  screen, so even a one-pixel seam during rotation is invisible.
