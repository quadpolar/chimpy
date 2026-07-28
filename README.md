# Chimpy

Swing on a vine, let go at the right moment, fly through the gap. One tap.

Game logic is the build previously tagged thread-v13.
Fingerprints: G = 606, RAMP = 38, gap lerp(238, 58).

## Install on iPhone
1. Open the GitHub Pages URL in Safari
2. Share -> Add to Home Screen
3. Fullscreen, portrait, works offline

## Physics
Branches sit AHEAD of your flight path, so you catch each vine from behind
and below and sweep about 95 degrees. Period 3.69s, amplitude 71 degrees.
Energy is conserved during a swing but carried from your entry, so the vine
can never pump you.

## Difficulty
Opening 238px -> 58px over 38 gaps. Release window 627ms -> 272ms.
Layout variation widens as you go: entry angle 47-53 deg -> 33-67,
branch height spread sd 24 -> 78. Stops getting harder at 38.
