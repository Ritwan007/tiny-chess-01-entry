# tiny-chess-01 entry

A chess engine in at most 4,096 bytes of Python, entered in make.fun's
"Build the strongest chess engine in 4 KB of Python" bounty.

main.py is the whole engine: a negamax alpha-beta search with iterative
deepening under a fixed node budget (deterministic, never clock-driven),
a transposition table, quiescence search over captures, promotions and
check evasions, MVV-LVA and killer move ordering, null-move pruning, and
a material + piece-square-table evaluation with bishop-pair and endgame
king tables. It plays the env's gauntlet over HTTP (POST /game, /move).

The readable source is minified and packed as an lzma payload with its
unpacker inside main.py's byte budget, as the competition's size rule
allows.

## Run it

Started by the make.fun harness as `python3 -P -s /bot/main.py` with
MAKEFUN_ENV_URL set; it plays the trial's 8 games and exits.
