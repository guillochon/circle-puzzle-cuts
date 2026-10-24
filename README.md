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
- **Following a piece.** Click a piece to select it: the camera rides with it through every turn (it stays at
  the same pixel even at ×10⁵ zoom, n = 200), and every slot it can reach (its orbit under the two turns) is
  outlined. An inset shows both disks, which one is turning, the view and the selected piece; click it to jump.
- **Fractals.** Above the critical radius the cuts never stop. With pieces on, the closure is followed to 400k
  cuts and the pieces of its first complete generations (about 80k cuts, half a million pieces or so) are traced
  and coloured, with the cuts drawn to the same depth; "cuts by generation" follows the cuts to the full budget.
- **Deep zoom.** When a turn would carry content more than 1.5 screens, it is shown as a crossfade: the cut pattern
  maps onto itself, so each slot takes on the colour of the piece that arrives (and, when riding with a piece, its
  own disk turns rigidly while the outside crossfades).
- **Colours.** Every piece is filled with its own shade, from three families: pieces that start in the left disk only,
  the right disk only, or the lens they share. A turn carries each colour to where its piece goes, so the families
  mix. "Cuts by generation" instead colours each cut by the number of turns it took to make it.

## How it works

Everything runs in the browser, in one file (`index.html`); the computation runs in a Web Worker.

- **Exact closure.** Every cut is an arc of a radius-r circle whose centre lies in Z[ζ_n]. Centres are integer
  vectors modulo the cyclotomic polynomial (their coefficients stay below 20 up to n = 200, so JavaScript numbers are
  exact), identified by value, not by tolerance. An arc end is stored as "where this circle crosses that one", so a
  turn maps ends to ends and every angle is recomputed from exact centres; no round-off accumulates. It is a port of
  `ExactPuzzle` in `attacks/circle_puzzles/twodisk.py` and reproduces its circle and arc counts exactly
  (n = 200: 39,604 circles, 78,805 arcs, about 1.5 s).
- **Pieces.** The worker also finds every crossing (sub-arcs on a grid), joins coincident points, and traces every
  face of the arrangement (half-edges sorted by direction and curvature, as in `twodisk.py`). The piece count is
  therefore computed live and independently of the Python table: n = 200 gives 1,094,205 = a(200) in about 6 s. A
  turn maps pieces to pieces; the page finds that permutation by matching one edge of each piece (off the turning
  disk's rim) to its rotated image, and checks it is a bijection.
- **Speed.** Pieces and cuts are indexed by size (a wide view visits only those at least ~half a pixel across) and on a
  grid (a zoomed view visits only those near the screen). The screen shows a cached exact render; panning and zooming
  move the cached image and the exact render follows when the view settles. A turn renders the still part and the
  turning disk once, as layers, and each frame only moves those images; the end of the turn is rendered exactly.
  No <code>closePath</code>: in Chrome it makes a large Path2D quadratic to build (n = 7 at its critical radius: 81 s
  per full-view render with it, under 1 s without, identical pixels). n = 200 renders in about 20 ms, a deep zoom in
  under 15 ms.
- **Drawing.** Canvas 2D, with the view transform done in double precision and each arc clipped to the view and
  sampled so the chord stays within a quarter pixel, so lines stay sharp at any zoom.

The piece counts a(n) come from the Python program (Euler's formula on the arc arrangement, checked by tracing every
piece and summing areas). Made by James Guillochon with Claude. The source lives in the oeis-sniper project (`viz/circle_puzzles/`, computation in
`attacks/circle_puzzles/`); this repo holds the published copy.
