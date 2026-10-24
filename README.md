# Circle Puzzle Cuts

**https://guillochon.github.io/circle-puzzle-cuts/**

Two overlapping disks of radius r, centred at ±1, each turn by 1/n of a full turn (the compound symmetry groups
GG_n of Hearn, Kretschmer, Rokicki, Streeter and Vergo, [arXiv:2302.12950](https://arxiv.org/abs/2302.12950)).
The page draws every cut the puzzle ever makes, for any n from 3 to 200 and any radius: a finite puzzle below the
critical radius, a fractal (drawn to a budget) above it.

- **Presets.** "Plateau" picks a radius just past r = sec(π/n), where the piece count a(n) is constant over a wide
  range of r; "critical radius" uses the paper's table (n = 5 to 100).
- **Zoom.** Scroll, pinch, double-click or the +/− buttons, down to ×10¹². "Zoom to the smallest cut" jumps to the
  shortest arc (about 2.5·10⁻⁶ at n = 200).
- **Animation.** "Turn the disks" alternates turns of the two disks; on a finite puzzle each turn maps the cuts onto
  themselves while the generation colours travel. "Grow by generation" reveals the cuts in the order they are made.
- **Colours.** A cut's generation is the number of turns it took to make it from the disk boundaries; colours are
  quantiles of the generation.

## How it works

Everything runs in the browser, in one file (`index.html`); the computation runs in a Web Worker.

- **Exact closure.** Every cut is an arc of a radius-r circle whose centre lies in Z[ζ_n]. Centres are integer
  vectors modulo the cyclotomic polynomial (their coefficients stay below 20 up to n = 200, so JavaScript numbers are
  exact), identified by value, not by tolerance. An arc end is stored as "where this circle crosses that one", so a
  turn maps ends to ends and every angle is recomputed from exact centres; no round-off accumulates. It is a port of
  `ExactPuzzle` in `attacks/circle_puzzles/twodisk.py` and reproduces its circle and arc counts exactly
  (n = 200: 39,604 circles, 78,805 arcs, about 1.5 s).
- **Drawing.** Canvas 2D, with the view transform done in double precision and each arc clipped to the view and
  sampled so the chord stays within a quarter pixel, so lines stay sharp at any zoom.

The piece counts a(n) come from the Python program (Euler's formula on the arc arrangement, checked by tracing every
piece and summing areas). Made by James Guillochon with Claude. The source lives in the oeis-sniper project (`viz/circle_puzzles/`, computation in
`attacks/circle_puzzles/`); this repo holds the published copy.
