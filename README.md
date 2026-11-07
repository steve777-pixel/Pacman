# Self-Made Pacman Project 1.0

A full-featured Pacman game built with C++ and SFML. The project aims to simulate the original Pacman game by integrating Object-Oriented Programming, SFML, graphic interfaces and automated testing.

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Main Objectives](#2-main-objectives)
3. [Technology Stack](#3-technology-stack)
4. [Features](#4-features)
5. [Architecture](#5-architecture)
6. [Project Structure](#6-project-structure)
7. [Getting Started](#7-getting-started)
8. [Controls](#8-controls)
9. [Level Format](#9-level-format)
10. [Testing](#10-testing)
11. [Roadmap](#11-roadmap)
12. [Documentation](#12-documentation)
13. [Assets and Credits](#13-assets-and-credits)
14. [License](#14-license)

## 1. Project Overview

Self-Made Pacman Project 1.0 is a game designed to simulate the original game logic of Pacman.

Unlike a basic OOP exercise, this project focuses on building a complete game with multiple interconnected components and a maintainable architecture.

## 2. Main Objectives

- Apply OOP principles in a real-world application.
- Develop a layered software architecture.
- Develop a graphical game interface.
- Implement strict game logic.
- Practice unit, integration, and system testing.
- Apply software engineering principles and best practices.

## 3. Technology Stack

| Component | Technology |
|---|---|
| Programming Language | C++17 |
| Graphics, Audio, Window | SFML 2.6 |
| Build System | CMake (3.20+) |
| Compiler | Any C++17 compiler (Apple Clang, GCC, Clang, MSVC) |
| Dependency Manager | vcpkg (manifest mode) |
| Version Control | Git + GitHub |
| Map/Level Editing | Plain text files (ASCII grid) |
| Sprites | Custom or free pixel art (16x16 frames) |
| Testing | GoogleTest + CTest |
| Debugging | VS Code debugger |
| IDE | VS Code |

## 4. Features

Implemented and planned features:

- Tile-based maze loaded from an ASCII level file
- Pac-Man movement with input buffering (turns queue up until possible)
- Four ghosts (Blinky, Pinky, Inky, Clyde), each with its own targeting rule
- Ghost modes: scatter, chase, frightened, and eaten
- Pellets, power pellets, fruit bonuses, and warp tunnels
- Score, lives, extra life, and persistent high score
- Level progression with increasing difficulty
- Game states: menu, play, pause, and game over
- Sound effects and a HUD (score, lives, level)

## 5. Architecture

The code is split into layers so that game logic never depends on rendering. This is what makes the logic testable without opening a window.

| Layer | Responsibility |
|---|---|
| `core` | Main loop, fixed timestep, shared constants and basic types |
| `world` | Maze grid, tiles, level loading |
| `entities` | Pac-Man, ghosts, fruit (position, direction, speed) |
| `systems` | Collision, ghost AI, scoring, level difficulty |
| `states` | State pattern: menu, play, pause, game over |
| `graphics` | Renderer, animation, sprite sheet (SFML-dependent) |
| `audio` | Sound playback (SFML-dependent) |
| `ui` | HUD |

Key design decisions:

- **Logic is independent of SFML.** `world`, `entities` (logic part), and `systems` use the project's own `Vec2i` and `Direction` types from `core/Types.h`.
- **Shared ghost base class.** `Ghost` holds movement and the mode state machine. `Blinky`, `Pinky`, `Inky`, and `Clyde` only override their chase-target calculation.
- **State pattern** for screens, so the main loop only talks to the abstract `GameState` interface.
- **Static library layout.** All code except `main.cpp` is built into `pacman_core`, which both the game and the tests link against.

## 6. Project Structure

```
pacman/
├── CMakeLists.txt        # Root build config
├── vcpkg.json            # Dependency manifest (SFML, GoogleTest)
├── assets/               # Sprites, sounds, fonts, level files
├── include/              # Headers, grouped by layer
│   ├── core/  world/  entities/  systems/
│   └── states/  graphics/  audio/  ui/
├── src/                  # Implementation files, mirroring include/
│   └── main.cpp          # Entry point
├── tests/                # GoogleTest unit tests
└── docs/
    └── design_notes.md   # Ghost behavior rules, speed tables
```

## 7. Getting Started

### Prerequisites

- A C++17 compiler (Apple Clang via Xcode Command Line Tools, GCC, Clang, or MSVC)
- [CMake](https://cmake.org/) 3.20 or newer
- [vcpkg](https://github.com/microsoft/vcpkg), with the `VCPKG_ROOT` environment variable set
- Git

On macOS you may also need: `brew install cmake ninja pkg-config`

### Install vcpkg (once)

```bash
git clone https://github.com/microsoft/vcpkg.git ~/vcpkg
~/vcpkg/bootstrap-vcpkg.sh -disableMetrics
export VCPKG_ROOT="$HOME/vcpkg"   # add this line to ~/.zshrc or ~/.bashrc
```

### Build

```bash
git clone <your-repository-url>
cd pacman

cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake
cmake --build build
```

The first configuration downloads and compiles SFML and GoogleTest, which can take several minutes. Later builds are fast.

### Run

```bash
./build/pacman
```

The `assets/` folder is copied next to the executable after each build, so relative asset paths work.

## 8. Controls

Planned controls:

| Key | Action |
|---|---|
| Arrow keys / WASD | Move Pac-Man |
| P | Pause / resume |
| Enter | Confirm (menu) |
| Esc | Back / quit |

## 9. Level Format

Levels are plain text files in `assets/levels/`. Each character is one tile:

| Char | Meaning |
|---|---|
| `#` | Wall |
| `.` | Pellet |
| `o` | Power pellet |
| ` ` (space) | Empty path |
| `-` | Ghost house door |
| `P` | Pac-Man start |
| `B` `I` `N` `C` | Ghost start positions (Blinky, Inky, Pinky, Clyde) |
| `T` | Tunnel / warp tile |

Example:

```
#######
#P....#
#.###.#
#o...o#
#######
```

Adjust this table if your loader uses different symbols; this section should always match `LevelLoader`.

## 10. Testing

Unit tests use GoogleTest and run through CTest:

```bash
ctest --test-dir build --output-on-failure
```

Or run the test executable directly:

```bash
./build/tests/pacman_tests
```

Only logic that does not need a window or audio device is tested:

| Test file | Covers |
|---|---|
| `test_maze.cpp` | Grid loading, walls, pellets, tunnels |
| `test_level_loader.cpp` | Parsing the ASCII file, invalid input |
| `test_collision.cpp` | Entity-vs-entity collision |
| `test_ghost_ai.cpp` | The four targeting rules, mode switching |
| `test_pacman.cpp` | Movement, turn buffering, wall stopping |
| `test_score_manager.cpp` | Points, extra life, lives, high score |

Rendering, audio, and state classes are verified manually by playing the game.

## 11. Roadmap

- [x] Project structure and README
- [x] Build system (CMake + vcpkg)
- [x] Test setup (GoogleTest)
- [ ] Core types and constants
- [ ] Maze and level loader
- [ ] Pac-Man movement
- [ ] Rendering with placeholder shapes
- [ ] Ghost base class and state machine
- [ ] Ghost targeting for all four ghosts
- [ ] Collision, scoring, and lives
- [ ] Game states (menu, pause, game over)
- [ ] Sprites and animation
- [ ] Sound effects
- [ ] HUD and high score persistence
- [ ] Level progression and fruit
- [ ] Polish, bug fixes, and release 1.0

## 12. Documentation

Detailed ghost behavior rules and speed tables are in [`docs/design_notes.md`](docs/design_notes.md).

## 13. Assets and Credits

- Sprites: custom or freely licensed pixel art (add source and license here)
- Sounds: add source and license here
- Font: add source and license here
- Libraries: [SFML](https://www.sfml-dev.org/) and [GoogleTest](https://github.com/google/googletest)
- Game design reference: the original *Pac-Man* by Namco. This project is a non-commercial learning exercise and is not affiliated with or endorsed by Namco.

## 14. License

Choose a license for your code (for example MIT) and add a `LICENSE` file. Until then, all rights are reserved by the author.

---

Author: *your name* · Repository: *your GitHub link*
