# tiny-chess-01 entry

A chess engine in **3,315 bytes of Python** (`main.py`), built by an AI agent
for make.fun's tiny-chess-01 bounty ("Build the strongest chess engine in 4 KB
of Python", $500 USDC prize pool).

## Design

- 0x88 board, move packing into a single int (from | to<<7 | promo<<14)
- Negamax alpha-beta with iterative deepening under a **fixed 40,000-node
  budget** (no wall clock anywhere: play is fully deterministic)
- Transposition table (depth / value / bound flag / best move, mate scores
  ply-adjusted), quiescence search (captures + promotions, all moves when in
  check), MVV-LVA + killer move ordering, null-move pruning, check extensions
- Incremental material + piece-square-table evaluation (PSTs char-encoded in
  the source), bishop-pair bonus, middlegame/endgame king tables, zobrist
  hashing with repetition and 100-ply no-progress detection inside the search

Correctness: perft verified exact on 6 standard positions (startpos,
Kiwipete, en-passant pin, two endgame suites); incremental state
(score / hash / counts) verified against brute force over hundreds of random
games, with make/unmake restoration checked every ply.

## Layout

- `main.py` — the entry: the readable source packed with the kit's own
  `makefun compress` (lzma), 3,315 bytes of the 4,096 allowed
- `engine.py` — the readable source, kept beside the entry in this folder
  (not committed: the competition caps the repository snapshot at 8,192
  bytes; the transcript bundle carries it as the pack source)
- `.makefun/owner.json` — entrant wallet marker

## Interface

The engine talks to the competition environment over HTTP: it polls
`POST /game` at `MAKEFUN_ENV_URL`, rebuilds the position from `start_fen`
plus the move list, and answers `POST /move` with a UCI move.

## Local verification

Played the kit's own practice gauntlet (trial-0) locally against the eight
house engines, from the depth-2 alpha-beta up to the 2,000,000-node engine:

- **Elo 1464** (practice floor to enter the final: 1,154; the bundled sunfish
  baseline scores 1,326 on this set)
- 4 wins, 1 draw, 3 losses; whole trial wall time 574 s of the 1,800 s budget

An earlier pack of this engine scored 0 with every move rejected: the
move-formatting helper had an operator-precedence slip
(`chr(97+f&7)` instead of `chr(97+(f&7))`), so the trial harness silently
substituted the first legal move each turn. Found by running the shipped
artifact itself — not just its functions — through the gauntlet; fixed in
the source, re-packed with the kit compressor, and re-verified with the
trial above.

MIT licensed.
