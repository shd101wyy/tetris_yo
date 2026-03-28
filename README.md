# tetris_yo

[![Build & Deploy](https://github.com/shd101wyy/tetris_yo/actions/workflows/build-and-deploy.yml/badge.svg)](https://github.com/shd101wyy/tetris_yo/actions/workflows/build-and-deploy.yml)

A classic Tetris game written in [Yo](https://github.com/shd101wyy/Yo) using [raylib_yo](https://github.com/shd101wyy/raylib_yo) bindings.

**[▶ Play in Browser](https://shd101wyy.github.io/tetris_yo/tetris_yo_wasm.html)**

This project demonstrates:
- Using Yo's **build system** with git dependencies
- **C interop** via `c_include` struct bindings
- Game development in Yo with the raylib graphics library

## Screenshot

<img width="800" height="500" alt="Image" src="https://github.com/user-attachments/assets/969ae508-0688-40d7-85e0-ccef5b8fad52" />

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

### Build & Run (Native)

```bash
yo fetch                # Fetch raylib_yo dependency from GitHub
yo build install_native # Build only the native executable
yo build run            # Build and run the game
```

The compiled binary will be at `yo-out/<target>/bin/tetris_yo` (e.g., `yo-out/x86_64-windows-msvc/bin/tetris_yo.exe`).

### Build for WASM (Browser)

The project also builds a WebAssembly version via Emscripten. This requires additional setup:

#### 1. Install Emscripten

Follow the [Emscripten getting started guide](https://emscripten.org/docs/getting_started/downloads.html), or use a package manager:

```bash
# scoop (Windows)
scoop install emscripten

# brew (macOS)
brew install emscripten
```

#### 2. Build raylib for Emscripten

```bash
git clone --depth 1 --branch 5.5 https://github.com/raysan5/raylib.git raylib-wasm-build
cd raylib-wasm-build

# Configure with Emscripten
emcmake cmake -B build -G Ninja -DPLATFORM=Web -DCMAKE_BUILD_TYPE=Release -DBUILD_EXAMPLES=OFF

# Build and install to Emscripten sysroot
emmake ninja -C build
emmake ninja -C build install

cd ..
rm -rf raylib-wasm-build
```

> **Note (Windows):** emcc may install `libraylib.a` to `sysroot/lib/` but search in `sysroot/lib/wasm32-emscripten/`. If you get a linker error, copy the file:
> ```powershell
> $sysroot = (emcc --cflags | Select-String 'sysroot=(.+?)[\s"]' | % { $_.Matches[0].Groups[1].Value })
> Copy-Item "$sysroot\lib\libraylib.a" "$sysroot\lib\wasm32-emscripten\libraylib.a"
> ```

#### 3. Build and serve

```bash
yo build install_wasm           # Build only the WASM target
# or
yo build                        # Build both native and WASM targets
```

The WASM output will be at `yo-out/wasm32-emscripten/bin/`. Serve it with a local HTTP server:

```bash
cd yo-out/wasm32-emscripten/bin
python -m http.server 8080
# Open http://localhost:8080/tetris_yo_wasm.html
```

### Build Steps

| Command | Description |
|---------|-------------|
| `yo build` | Build all targets (native + WASM) |
| `yo build install_native` | Build only the native executable |
| `yo build install_wasm` | Build only the WASM target |
| `yo build run` | Build and run the native game |

## Project Structure

```
tetris_yo/
├── .github/workflows/    # CI/CD — builds WASM and deploys to GitHub Pages
├── build.yo              # Build configuration (native + WASM targets)
├── deps.yo               # Dependencies (managed by `yo install`)
├── yo.lock               # Dependency lock file
├── src/
│   ├── lib.yo            # Library root (empty)
│   └── main.yo           # Game implementation (~600 lines)
├── yo-out/
│   ├── x86_64-windows-msvc/bin/   # Native build output
│   └── wasm32-emscripten/bin/     # WASM build output (.html + .js + .wasm)
└── devenv.nix            # Development environment
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
