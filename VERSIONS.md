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
| 20    | `chimpy-v20`  | Wood grain on the trunks. Fixed the blue bar at the bottom for real. Fixed layout state being wiped on every restart. |

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


## Build 20 in detail

**Grain on the trunks.** Three faint lines per trunk, each wandering on its
own sine phase so none are straight or parallel. Plotted against world y so
it cannot slide along the trunk as the camera moves, and clipped to the
visible span so off-screen trunks cost nothing.

**The blue bar, properly.** The canvas was sized from the wrapper, whose
`height:100%` can resolve shorter than the real screen in standalone once
the home indicator area is involved. It now takes the larger of the wrapper
rect and `window.innerHeight`, which reports the true height under
`viewport-fit=cover`.

**Layout state surviving a restart.** `reset()` preserved W, H and dpr but
dropped safeTop, zoom, ui, land, PW and PH. Since `resize()` early-returns
when the dimensions have not changed, those stayed at their defaults for the
rest of the session — so after the first death the zoom silently reverted to
0.8 and the score crept back under the clock. All viewport-derived state is
now carried across.
