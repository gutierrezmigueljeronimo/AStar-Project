# AStarCity — Pathfinding Visualiser

Interactive pathfinding visualiser built with Python and Pygame. Place buildings, configure traffic zones and watch **A\*** and **Dijkstra** find the optimal route through a weighted city grid — step by step, with live metrics.

![Demo search animation](readme_assets/demo_search.gif)

---

## What it does

- **Interactive grid editor** — paint buildings, clear roads, medium-traffic and jam zones with the mouse before running any search.
- **A\* with octile heuristic** — correct for 8-direction movement with weighted terrain. The heuristic is scaled by the minimum walkable weight to stay admissible across all terrain types.
- **Dijkstra** — guarantees the optimal path by exhaustive expansion; used as the reference baseline.
- **Side-by-side comparison** — run both algorithms on the same map in a single keypress and compare path cost, nodes expanded and execution time.
- **Step-by-step animation** — watch the search frontier expand node by node before the final path is drawn.
- **Corner-cutting prevention** — diagonal moves are blocked when either adjacent cell is a building, preventing the agent from squeezing between obstacles.
- **Standalone executable** — distributed as a Windows x64 binary; no Python installation required.

---

## Screenshots

| Map editor & terrain types | A\* vs Dijkstra comparison |
|---|---|
| ![Map editor](readme_assets/screenshot_main.png) | ![Comparison](readme_assets/screenshot_comparison.png) |

---

## Terrain types

| Type | Weight | Colour |
|---|---|---|
| Clear road | 1.0 | White |
| Medium traffic | 3.0 | Light blue |
| Traffic jam | 7.0 | Light red |
| Building | — | Dark grey (impassable) |

Move costs scale with terrain weight. A diagonal step costs `√2 × weight`; a cardinal step costs `1.0 × weight`. A\* accounts for this in both the edge cost and the heuristic.

---

## Controls

| Key / Action | Description |
|---|---|
| **Left click** | Paint selected terrain |
| **Right click** | Erase cell (reset to clear road) |
| **S** | Set start position (click a cell after pressing S) |
| **G** | Set goal position (click a cell after pressing G) |
| **1 / 2 / 3 / 4** | Select terrain: clear / medium traffic / jam / building |
| **A** | Run A\* with step-by-step animation |
| **D** | Run Dijkstra with step-by-step animation |
| **C** | Run both and display the comparison panel |
| **R** | Reset the grid |
| **ESC** | Quit |

---

## Project structure

```
AStarCity/
├── run_game.py                  # Entry point
├── src/
│   └── astar_city/
│       ├── astar.py             # A* implementation
│       ├── astar_stepper.py     # A* as a Python generator (drives the animation)
│       ├── constants.py         # Grid dimensions, cell size, FPS and animation speeds
│       ├── dijkstra.py          # Dijkstra implementation
│       ├── grid.py              # Grid, neighbour expansion, weight lookup
│       ├── heuristics.py        # Octile distance heuristic
│       ├── path.py              # Path reconstruction from came_from map
│       ├── search_result.py     # SearchResult dataclass (path, expanded nodes, cost)
│       └── terrain.py           # TerrainType enum and TerrainSpec configuration
├── ui/
│   └── pygame_app.py            # Pygame interface, rendering and event loop
├── tests/
│   ├── test_astar_basic.py      # Basic pathfinding correctness tests
│   └── test_astar_weighted.py   # Weighted terrain tests
├── tools/
│   └── demo_neighbors.py        # Development utility — neighbour expansion debugger
├── assets/                      # Sprite assets
└── readme_assets/               # Screenshots and demo GIF
```

---

## Technical notes

**Why octile distance?** Manhattan distance assumes only 4-directional movement and would overestimate the true cost of diagonal moves, breaking A\*'s optimality guarantee. Octile distance is the exact minimum cost across an unweighted 8-connected grid, and scaling it by the minimum terrain weight keeps the heuristic admissible even when cells have different costs.

**Why a generator for the animation?** `astar_stepper.py` implements A\* as a Python generator that yields one expansion snapshot per step — open set, closed set, current node. This decouples the algorithm from the rendering loop entirely: the UI can consume steps at any frame rate without modifying the algorithm itself.

**Tie-breaking in the heap:** when two nodes share the same f-score, a monotonically increasing counter `tie` is used as a secondary sort key, avoiding direct comparison of `Coord` tuples and potential type errors.

---

## Running from source

**Requirements:** Python 3.10+, Pygame 2.x.

```bash
git clone https://github.com/gutierrezmigueljeronimo/AStar-Project.git
cd AStar-Project
pip install pygame
python run_game.py
```

---

## Running the executable (Windows x64)

Download the latest release and extract the ZIP. Run `AStarCity.exe` directly — no Python installation needed.

```
AStarCity-windows-x64/
└── dist/
    └── AStarCity/
        ├── AStarCity.exe
        └── internal/
```

> The executable was built with PyInstaller. Windows Defender may show a warning on first launch — this is expected for unsigned PyInstaller binaries.

---

## Academic context

Built as an individual assignment for the *AI and Big Data* specialisation course at EUSA — Cámara de Comercio de Sevilla (2026).
