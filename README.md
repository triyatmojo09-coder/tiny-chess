[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/triyatmojo09-coder/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/triyatmojo09-coder/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# Tiny Chess — a chess engine in 4 KB of Python

An entry for make.fun's **tiny-chess-01** bounty ("Build the strongest chess
engine in 4 KB of Python"). The whole engine is `main.py`: 4,067 bytes as
committed, Python 3 standard library only. It unpacks itself at startup
(a zlib-packed source plus a compact piece-square table block) and plays
through the environment API at `MAKEFUN_ENV_URL`.

## How it plays

- **Board.** 0x88 mailbox with precomputed knight/king/pawn attack tables and
  sliding rays; incremental make/unmake keeps the evaluation, phase and
  position key up to date on every move.
- **Evaluation.** Tapered PeSTO piece-square tables (midgame and endgame,
  blended by game phase) on top of the classic material values, stored at
  half-centipawn precision in a compressed table block.
- **Search.** Negamax alpha-beta with a transposition table (exact position
  keys), MVV-LVA capture ordering, and a quiescence search that resolves
  captures and promotions and searches all evasions when in check.
- **Determinism.** No wall clock anywhere in the search: each game gets a
  fixed budget of 9.2M nodes, handed out per move as a shrinking share of
  what is left (30k–240k nodes a move), so a trial replays move for move
  and one move can never approach the 10-second limit.

Local practice on the kit's gauntlet (depth-2 through nodes-500k anchors)
rates the engine at Elo ≈1,545 (4 wins, 1 draw, 1 loss in the trial-0
gauntlet), above the house baseline.

## Running it

The engine is driven entirely by the environment:

```
MAKEFUN_ENV_URL=http://127.0.0.1:PORT python3 main.py
```

It answers `POST /game`, computes its move from the position and the game's
move list, and posts it back with `POST /move` until the match is done.

## License

MIT — see `LICENSE`.
