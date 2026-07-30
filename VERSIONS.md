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


## Build 22 — double trees

Measured before changing anything. The release window for a double is the
INTERSECTION of two windows, and the trajectory drops between the two walls,
so the further apart they are the less those windows overlap. Measured with
the old settings:

  gap#   opening   single   double   double @0.88   impossible
    55     158px    512ms    304ms         143ms          17%
    65     144px    488ms    276ms         111ms          34%
    75     119px    422ms    248ms         104ms          41%
    85      91px    347ms    181ms          60ms          67%

The cause was a bug. The weak-arrival validation added in build 18 only ever
checked the FIRST wall — the second was appended afterwards with no check at
all. So the "no impossible gaps" guarantee never applied to doubles.

Three changes:

**Closer together.** Separation 140→105 becomes 95→68, which widens the
overlap between the two windows.

**One of the pair is wider.** A random one of the two openings is 1.35x,
so a double is two constraints but not two tight ones.

**Validated as a pair.** The weak-arrival check now takes a list of walls and
counts how many release angles clear them all. A double is only emitted if at
least five do, which is roughly a 150ms window or better. Otherwise the
segment stays a single.

After:

  gap#   opening   doubles emitted   double   double @0.88   impossible
    45     162px              83%     448ms          336ms          0%
    55     158px              75%     443ms          325ms          0%
    65     144px              65%     428ms          328ms          0%
    75     119px              52%     364ms          324ms          0%
    85      91px              36%     217ms          306ms          0%

Doubles get rarer as the openings narrow, because fewer of them pass the
check. That is the intended behaviour: late game has fewer doubles rather
than impossible ones.
