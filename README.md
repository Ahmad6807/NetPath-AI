# NetPath AI — Graph Algorithm Visualiser

NetPath AI is a browser-based, canvas-driven application for creating weighted graphs and visualising the execution of classic search/pathfinding algorithms.

It includes:
- A fully interactive graph editor (nodes + weighted edges)
- Start/goal selection
- Step-through playback with colour-coded frontier/visited/expanded/path states
- A suite of search & optimisation algorithms (BFS, DFS, UCS/Dijkstra, A* variants, Greedy, Beam, Bidirectional UCS, Hill Climbing, Simulated Annealing, Genetic Algorithm, Minimax + Alpha-Beta demo)
- Local authentication (demo-style) with a protected app experience
- Save/load of user graphs (localStorage)
- Export graph as PNG or JSON

> Project is implemented with plain HTML/CSS/JavaScript (no build step).

---

## Relevent Screenshots
<img width="1858" height="920" alt="image" src="https://github.com/user-attachments/assets/8c70fceb-177e-4404-8753-e299c54ca46a" />

---

## Table of Contents
1. Features
2. Algorithm Visualisation (How it works)
3. Project Structure
4. User Authentication (Local demo)
5. Graph Editor (Controls & Shortcuts)
6. Running Algorithms (What you can run)
7. Save / Load / Export
8. Data Formats
9. Extending the Project
10. Notes, Limitations & Security

---

## Features

### Graph editor (canvas)
- Add nodes by clicking the canvas (Node mode)
- Add weighted edges by selecting a source node then a target node (Edge mode)
- Drag nodes to reposition
- Pan + zoom the viewport (mouse wheel + drag empty space)
- Set **Start** (green) and **Goal** (red)
- Edit edge weights (double-click edge in Edit mode, or right-click edge → Edit weight)
- Delete nodes/edges (Delete key or right-click context menu)
- Congestion simulation:
  - Right-click edge → **Double weight (congestion)**

### Algorithm visualisation
- Play / pause animation
- Step forward/backward
- Slider for scrubbing through recorded history
- Metrics panel updates after each run:
  - Status, Algorithm name
  - Path cost, Path length
  - Nodes expanded
  - Runtime (ms)

### Algorithms
- **Uninformed**: BFS, DFS, IDDFS, UCS (Dijkstra)
- **Informed**: A* (admissible), A* (non-admissible), Weighted A*, Greedy Best-First, Beam Search
- **Bidirectional**: Bidirectional UCS
- **Local search / optimisation**: Hill Climbing (load balance), Simulated Annealing (load balance)
- **Metaheuristic**: Genetic Algorithm
- **Adversarial (demo)**: Minimax + Alpha-Beta

---

## Algorithm Visualisation (How it works)

Each algorithm generates a `history[]` list of “snapshots” during search/optimisation.
- The canvas renderer uses the current snapshot to draw:
  - expanded/current node (white pulse)
  - visited/closed set (purple)
  - frontier/open set (yellow)
  - current best path (cyan)
  - congested edges (orange)

Playback is controlled by `Animator` (`js/animator.js`), which steps through `history` and asks `GraphRenderer` (`js/graph.js`) to render the correct state.

---

## Project Structure

```
.
├─ README.md
├─ PRD.txt
├─ TODO.md
├─ index.html
├─ app.html
├─ js/
│  ├─ auth.js
│  ├─ app.js
│  ├─ algorithms.js
│  ├─ animator.js
│  └─ graph.js
└─ css/
   ├─ main.css
   ├─ app.css
   └─ auth.css
```

### Key files
- `index.html` — Sign in / Create account UI
- `app.html` — Main application layout (graph editor + controls)
- `js/auth.js` — Local authentication + session handling (demo)
- `js/app.js` — UI orchestration, run logic, save/load/export, keyboard shortcuts
- `js/graph.js` — Canvas graph renderer + editor interactions
- `js/algorithms.js` — Search/optimisation algorithm implementations
- `js/animator.js` — Step-through animation controller

---

## User Authentication (Local demo)

Authentication is implemented purely in the browser using `localStorage`.

### What it does
- Signup: stores a user record locally
- Login: validates email + password (using a simple demo hash)
- Session: stored in `localStorage` with an expiry (7 days)
- Protected app: `app.js` checks the session and blocks access when invalid

### Important limitation
This is **not** production-grade security.
- Passwords are not hashed with bcrypt/argon2.
- This is for demonstration/assignment purposes only.

Files involved:
- `js/auth.js`
- Session keys:
  - `netpath_users`
  - `netpath_session`

---

## Graph Editor (Controls & Shortcuts)

### Editor modes (left sidebar)
- **Edit** (`ESC`) — default canvas interaction
- **Node** (`N`) — click canvas to add a node
- **Edge** (`E`) — click source node, then target node
- **Start** (`S`) — click a node to set start
- **Goal** (`G`) — click a node to set goal

### Keyboard shortcuts
- `N` — Node mode
- `E` — Edge mode
- `S` — Start mode
- `G` — Goal mode
- `ESC` — return to Edit mode / cancel edge selection
- `Del` / `Backspace` — delete hovered node/edge
- `Space` — Play/Pause animation
- `→` — Next step
- `←` — Previous step
- `Ctrl+R` — Run selected algorithm

### Mouse interactions
- Drag node — reposition
- Drag empty space — pan
- Mouse wheel — zoom
- Right-click:
  - node: delete, set as Start, set as Goal
  - edge: delete, congestion (double weight), edit weight
- Double-click edge (Edit mode): edit weight

### Congestion mode
The UI implements congestion by doubling an edge weight.
- Congested edges are treated as:
  - `weight >= 15` and/or
  - edges marked as congested in the algorithm state

---

## Running Algorithms (What you can run)

### Selecting an algorithm
- Use the **Algorithm** dropdown in the left sidebar.
- The description updates in the panel below the dropdown.

### Running
- Ensure graph has:
  - at least one node
  - a selected **Start** and **Goal**
- Click **Run Algorithm**

### Heuristic input prompt (for A* / Greedy / Beam / Weighted A*)
For heuristic-based algorithms, the app prompts the user to enter a value for each node:
- `h(node)` for every node is collected before running.
- Inputs are validated as non-negative numbers.

### Algorithm categories and availability
The algorithm registry is defined in `js/algorithms.js` under `ALGORITHMS`.

Supported keys in the registry include:
- `bfs`, `dfs`, `iddfs`, `ucs`
- `astar` (admissible), `astar_na` (non-admissible)
- `astar_w` (Weighted A*), requires `w`
- `greedy` (Greedy Best-First)
- `beam` (Beam Search), requires `beamWidth`
- `biucs` (Bidirectional UCS)
- `hill` (Hill Climbing load balance)
- `sa` (Simulated Annealing load balance)
- `ga` (Genetic Algorithm)
- `minimax` (Minimax + Alpha-Beta demo)

---

## Save / Load / Export

### Save Graph
- Click **Save** in the navbar
- Enter a graph name in the prompt
- Graph is stored in `localStorage`

### Saved Graphs list
- Shown in the left sidebar
- Click an item to load it back
- Click the `×` button to delete a saved graph

### Export
In the navbar:
- **PNG**: `exportPNG()` downloads `netpath_graph.png`
- **JSON**: `exportJSON()` downloads `netpath_graph.json`

### Import
- **Import** button opens a file picker
- Validates JSON shape (`nodes` + `edges`)
- Loads into the canvas

---

## Data Formats

### Graph JSON shape (exported)

```json
{
  "nodes": [
    { "id": "n1", "x": 120, "y": 200, "label": "A" }
  ],
  "edges": [
    { "id": "e1", "source": "n1", "target": "n2", "weight": 5 }
  ],
  "startNode": "n1",
  "goalNode": "n10"
}
```

### localStorage keys
- Users: `netpath_users`
- Session: `netpath_session`
- Saved graphs: `netpath_saved_graphs` (scoped by current session user)

---

## Extending the Project

### Add a new algorithm
1. Implement the algorithm function in `js/algorithms.js`.
2. Add it to the `ALGORITHMS` registry with:
   - `fn` (function)
   - `name`, `category`, `desc`
   - optional `hasParam` to show parameters in the UI
3. Ensure the function returns the standard result object:
   - `path`, `cost`, `nodesExpanded`, `elapsedMs`, `history`, `found`, `algorithmName`, `heuristicUsed`

### Ensure it supports visualisation
- Your algorithm should push snapshots into `history[]` using the provided helper (`snap`), including:
  - `expanded`, `visited`, `frontier`, `pathSoFar`, `costSoFar`

### Heuristic-based algorithms
If your algorithm uses heuristic `h(n)`, accept an optional `heuristics` map and fallback to Euclidean values when missing.

---

## Notes, Limitations & Security

- Authentication is **client-side demo** only.
- Password hashing uses a `simpleHash()` function rather than bcrypt.
- Graph algorithms run on the main thread (no Web Worker currently).
- The step timeline is visualised via CSS; the renderer uses internal `history`.

---

## Repository PRD and TODO

- PRD: `PRD.txt`
- Current tracked items: `TODO.md`

---

## Quick Start (Run locally)

Because this project has no build step, the simplest way to run it is:
1. Double-click `index.html` to open it in your browser.
2. Sign up (creates a local account).
3. Sign in and open the app.
4. Add nodes/edges, set start/goal, then run an algorithm.

> Note: Some browsers may block local `prompt`/storage behaviour in certain contexts. For consistent results, open files from a local web server.



