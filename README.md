# gjbh

## Prerequisites

- [Emscripten SDK](https://emscripten.org/docs/getting_started/downloads.html)
- CMake ≥ 3.13
- Git (for the raylib submodule)

## Setup

```bash
# Initialize the raylib submodule
git submodule update --init --recursive
```

## Build

### Debug (with DWARF debug symbols + source maps)

```bash
mkdir -p build-debug && cd build-debug
emcmake cmake -DCMAKE_BUILD_TYPE=Debug ..
emmake make
```

### Release

```bash
mkdir -p build-release && cd build-release
emcmake cmake -DCMAKE_BUILD_TYPE=Release ..
emmake make
```

## Serve & Run

```bash
cd build-debug   # or build-release
python3 -m http.server 8080
# Open http://localhost:8080/app.html in a browser
```

## IDE Setup (VSCode + clangd)

CMake generates `compile_commands.json` in the build directory. Tell clangd about it:

```bash
# Create a symlink in the project root pointing to the build directory
ln -sf build-debug/compile_commands.json compile_commands.json
```

Then restart the language server: `Ctrl+Shift+P` → `clangd: Restart language server`.

## Debugging in Chrome

1. Build in **Debug** mode (see above)
2. Serve the files via HTTP
3. Open `app.html` in Chrome, open DevTools (`F12`)
4. Go to **Sources** → right-click file tree → **Add folder to workspace** → select your `src/` folder
5. Enable the **C/C++ DevTools Support (DWARF)** extension
6. Set breakpoints in `src/main.c`, reload the page

## Updating raylib

```bash
git submodule update --remote third_party/raylib
```
