# tetris_yo

A classic Tetris game written in [Yo](https://github.com/shd101wyy/Yo) using [raylib_yo](https://github.com/shd101wyy/raylib_yo) bindings.

This project demonstrates:
- Using Yo's **build system** with git dependencies
- **C interop** via `c_include` struct bindings
- Game development in Yo with the raylib graphics library

## Screenshot

<!-- TODO: Add screenshot -->

## How to Play

| Key | Action |
|-----|--------|
| ← → | Move piece left/right |
| ↓ | Soft drop |
| ↑ | Rotate piece |
| Enter | Start game / restart |
| P | Pause / unpause |

## Building

### Prerequisites

- [Yo](https://github.com/shd101wyy/Yo) compiler
- [raylib](https://www.raylib.com/) system library
- A C compiler (clang recommended)
- `pkg-config`

If you use [devenv](https://devenv.sh/), all prerequisites are handled automatically:

```bash
direnv allow .  # One-time setup
```

### Build & Run

```bash
yo fetch         # Fetch raylib_yo dependency from GitHub
yo build         # Build the executable
yo build run     # Build and run the game
```

The compiled binary will be at `yo-out/bin/tetris_yo`.

## Project Structure

```
tetris_yo/
├── build.yo          # Build configuration (dependencies, system libraries)
├── yo.lock           # Dependency lock file
├── src/
│   ├── lib.yo        # Library root (empty)
│   └── main.yo       # Game implementation (~600 lines)
└── devenv.nix        # Development environment
```

## Implementation Notes

The game is a faithful port of the [classic raylib Tetris example](https://github.com/raysan5/raylib-games/blob/master/classics/src/tetris.c), adapted to Yo's syntax and conventions:

- **All mutable state is local** — Yo has no module-level mutable variables, so the game grid, piece state, and timers are all mutable locals in `main`.
- **Flat arrays** — The 12×20 grid is stored as `Array(i32, 240)` with column-major indexing.
- **7 standard tetrominoes** — I, T, L, J, S, Z, O pieces with rotation support.
- **Scoring** — 100 points per cleared line.

## Dependencies

- [raylib_yo](https://github.com/shd101wyy/raylib_yo) — Raylib bindings for Yo (git dependency)
- [raylib](https://www.raylib.com/) — Graphics library (system library via pkg-config)

## License

MIT
