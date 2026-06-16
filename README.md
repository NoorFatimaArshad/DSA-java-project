# 🐍 Snake Game DSA

<div align="center">

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Swing](https://img.shields.io/badge/Java_Swing-GUI-007396?style=for-the-badge&logo=java&logoColor=white)
![DSA](https://img.shields.io/badge/DSA-Linked_List_%7C_Queue_%7C_HashMap_%7C_A*-3fef89?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey?style=for-the-badge)
![JDK](https://img.shields.io/badge/JDK-8%2B-orange?style=for-the-badge&logo=openjdk)

**A feature-rich Snake Game built entirely with Java Swing — no game engine, no external libraries.**  
Custom data structures power every mechanic: a hand-rolled Linked List for the snake body, a buffered Queue for direction input, a HashMap for score persistence, and a full A\* pathfinder with a custom Min-Heap for AI-assisted hints.

[▶ Getting Started](#-getting-started) · [🧠 DSA Features](#-data-structures--algorithms) · [🎮 Gameplay](#-gameplay) · [📁 Project Structure](#-project-structure) · [🔬 Architecture](#-architecture-overview)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features at a Glance](#-features-at-a-glance)
- [Getting Started](#-getting-started)
- [Gameplay](#-gameplay)
- [Data Structures & Algorithms](#-data-structures--algorithms)
- [Architecture Overview](#-architecture-overview)
- [Class Diagram](#-class-diagram)
- [A\* Pathfinding Deep Dive](#-a-pathfinding-deep-dive)
- [Project Structure](#-project-structure)
- [File Persistence](#-file-persistence)
- [UI & Theming](#-ui--theming)
- [Controls Reference](#-controls-reference)
- [Difficulty Modes](#-difficulty-modes)
- [Multiplayer Rules](#-multiplayer-collision-rules)
- [Known Limitations](#-known-limitations)

---

## 🌟 Overview

This project demonstrates how classical **Data Structures and Algorithms** directly power a real-time game. Every core game mechanic maps to a specific DSA concept — the snake *is* a linked list, direction presses *are* a queue, and the optional AI hint runs a genuine graph search on the board.

The GUI is written entirely in **Java Swing** with custom painting: gradient backgrounds, rounded glass-style cards, animated snake previews, glowing food, HUD cards, and modern hover-animated buttons — all without a single external dependency.

---

## ✨ Features at a Glance

| Category | Feature |
|----------|---------|
| 🎮 Game Modes | Single Player · Two Player (split controls) |
| 🧠 DSA | Custom Linked List · Custom Queue · HashMap · A\* + Min-Heap |
| 🎨 GUI | Animated main menu · Gradient theme · Rounded cards · Glowing food |
| 💾 Persistence | High scores (`scores.txt`) · Full match history (`history.txt`) |
| 🤖 AI Hint | Live A\* path visualisation toggled with `H` |
| 🏆 Leaderboard | Sorted high score table with player search |
| 📜 History | Per-game log with player name, score, date/time, and result |
| ⚙️ Difficulty | Easy · Medium · Hard (speed increases dynamically) |
| 🪟 One-Click Run | `Run_Snake_Game.bat` for Windows |

---

## 🚀 Getting Started

### Prerequisites

- **Java JDK 8 or newer** installed and on `PATH`
- Any OS (Windows / macOS / Linux)

Verify your installation:
```bash
java -version
javac -version
```

### Windows — One Click

Double-click **`Run_Snake_Game.bat`** in the project root.  
The script compiles all `.java` files and launches the game automatically.

### Manual (All Platforms)

```bash
# 1. Navigate to the src directory
cd SnakeGameDSA/src

# 2. Compile every source file
javac *.java

# 3. Run the entry point
java Main
```

The game opens an 900 × 700 window. Two data files are created automatically on first run:

| File | Purpose |
|------|---------|
| `scores.txt` | Persistent high-score records (CSV: `name,score`) |
| `history.txt` | Full match history (CSV: `name,score,datetime,result`) |

---

## 🎮 Gameplay

### Single Player

1. Select **▶ Single Player** from the main menu.
2. Enter your name and choose a difficulty.
3. Guide the snake to eat the glowing food.
4. Each food item scores **+10 points** and grows the snake by one segment.
5. The snake speeds up by 5 ms after every food eaten (floor: 45 ms).
6. The game ends on a **wall hit** or **self-collision**.
7. High scores are saved automatically.

### Two Player

1. Select **⚔ Two Player Mode** from the main menu.
2. Enter names for both players and choose difficulty.
3. Both snakes share the same food and board.
4. Collision rules determine the winner (see [Multiplayer Collision Rules](#-multiplayer-collision-rules)).

---

## 🧠 Data Structures & Algorithms

The five DSA pillars of the project:

---

### 1 · Custom Singly Linked List — `SnakeLinkedList`

The snake body is a **manually implemented singly linked list**. Java's built-in `LinkedList` is not used.

```
HEAD ──► [7,10] ──► [6,10] ──► [5,10] ──► null
  ▲                                          ▲
(new head added each move)          (tail removed unless food eaten)
```

**Key operations:**

| Method | Complexity | Purpose |
|--------|-----------|---------|
| `addHead(x, y)` | O(1) | Snake moves forward |
| `removeTail()` | O(n) | Snake doesn't grow after a normal move |
| `contains(x, y)` | O(n) | Collision detection |
| `headCollidesWithBody()` | O(n) | Self-hit detection |
| `toPointList()` | O(n) | Rendering |

**Why a linked list?** Prepend is O(1) — perfect for the constant head-insertion of snake movement. Arrays would require O(n) shifting on every tick.

---

### 2 · Custom Queue — `DirectionQueue`

Player key presses are buffered in a **manually implemented singly-linked queue**. Java's `Queue` is not used.

```
FRONT                        REAR
  │                            │
[RIGHT] ──► [UP] ──► [LEFT] ──► null
 dequeued                   new presses enqueued here
```

**Key behaviours:**

- Maximum buffer size of **3** directions prevents runaway input.
- Reverse-direction presses (`UP`↔`DOWN`, `LEFT`↔`RIGHT`) are silently dropped — at both the incoming keypress *and* the comparison against the buffered rear — eliminating instant self-collision from rapid double-taps.

```
enqueue("DOWN", "UP")  →  rejected (reverse)
enqueue("RIGHT", "RIGHT")  →  rejected (duplicate)
enqueue("UP", "RIGHT")  →  accepted
```

---

### 3 · HashMap — `ScoreManager`

Player high scores are stored in a `HashMap<String, Integer>`:

```
{ "Alice" → 240, "Bob" → 180, "Carol" → 310 }
```

**Operations:**

| Method | Complexity | Purpose |
|--------|-----------|---------|
| `updateHighScore(name, score)` | O(1) | Only saves if new score is higher |
| `getHighScore(name)` | O(1) | Look up personal best |
| `searchPlayerScore(name)` | O(1) | In-game search |
| `getSortedScoresText()` | O(n log n) | Sorted leaderboard |

All changes are flushed to `scores.txt` immediately after every game.

---

### 4 · A\* Pathfinding — `AStarPathFinder`

The board is modelled as an **unweighted grid graph** where each tile is a vertex with up to 4 neighbours. A\* finds the shortest safe path from the snake's head to the food, avoiding the snake body.

**Heuristic:** Manhattan distance — `|x1−x2| + |y1−y2|`

**f(n) = g(n) + h(n)**
- `g(n)` — actual cost from start (tile hops)
- `h(n)` — estimated cost to goal (Manhattan distance)

The open set is managed by a **custom Min-Heap** (not `PriorityQueue`) ordered by `fCost`, with `hCost` as a tie-breaker.

Press **`H`** during gameplay to see the computed path drawn in yellow.

---

### 5 · Custom Min-Heap — `AStarPathFinder.MinHeap`

An array-backed binary min-heap used as the A\* open set priority queue.

```
           [f=4]
          /      \
      [f=6]    [f=7]
      /    \
  [f=8]  [f=9]
```

**Operations:**

| Operation | Complexity |
|-----------|-----------|
| `insert(node)` | O(log n) |
| `extractMin()` | O(log n) |
| `heapifyUp()` / `heapifyDown()` | O(log n) |
| Capacity growth (`grow()`) | O(n) amortised |

---

## 🔬 Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                        Main.java                        │
│           Creates JFrame + bootstraps managers          │
└──────────────────────┬──────────────────────────────────┘
                       │
          ┌────────────▼────────────┐
          │       MainMenu.java     │  ← Navigation hub
          │  (CardLayout screens)   │
          └─┬──────┬───────┬───────┘
            │      │       │
   ┌────────▼─┐  ┌─▼──┐  ┌─▼──────────────────┐
   │GamePanel │  │Game│  │MultiplayerGamePanel │
   │(1-player)│  │Over│  │   (2-player)        │
   └────┬─────┘  │Panel│  └──────────┬──────────┘
        │        └────┘              │
        │   ┌────────────────────────┘
        ▼   ▼
   ┌─────────────────────────────────────────────┐
   │            Core DSA Layer                   │
   │  SnakeLinkedList  DirectionQueue             │
   │  AStarPathFinder  (MinHeap inside)           │
   └──────────────────────────┬──────────────────┘
                              │
   ┌──────────────────────────▼──────────────────┐
   │            Persistence Layer                │
   │      ScoreManager        PlayerHistory      │
   │      (scores.txt)        Manager            │
   │      HashMap<String,Int> (history.txt)      │
   └─────────────────────────────────────────────┘
        │
   ┌────▼────────────────────────────────────────┐
   │             UI / Theme Layer                │
   │  UITheme · RoundedPanel · ModernButton      │
   │  GradientPanel · AnimatedSnakePanel         │
   └─────────────────────────────────────────────┘
```

---

## 📐 Class Diagram

```
┌──────────────┐        ┌──────────────────┐
│  SnakeNode   │◄───────│  SnakeLinkedList  │
│──────────────│  next  │──────────────────│
│ int x        │        │ + addHead(x,y)   │
│ int y        │        │ + removeTail()   │
│ SnakeNode nxt│        │ + contains(x,y)  │
└──────────────┘        │ + headCollides() │
                        │ + toPointList()  │
                        └────────┬─────────┘
                                 │ uses
                    ┌────────────▼─────────────┐
                    │       GamePanel           │
                    │ ─────────────────────── │
                    │ SnakeLinkedList snake    │
                    │ DirectionQueue dirQueue  │
                    │ AStarPathFinder finder   │
                    │ + moveSnake()            │
                    │ + checkCollisions()      │
                    │ + drawSnake()            │
                    └──────────────────────────┘

┌─────────────────────┐      ┌───────────────────────┐
│  DirectionQueue     │      │   AStarPathFinder      │
│─────────────────────│      │───────────────────────│
│ DirectionNode front │      │ int cols, rows         │
│ DirectionNode rear  │      │ + findPath(…)          │
│ int size (max 3)    │      │ - buildBlockedGrid()   │
│ + enqueue(dir, cur) │      │ - heuristic()          │
│ + dequeue()         │      │ - reconstructPath()    │
│ + clear()           │      │                        │
└─────────────────────┘      │  ┌───────────────┐    │
                             │  │   PathNode    │    │
┌─────────────────────┐      │  │ int gCost,hCst│    │
│    ScoreManager     │      │  │ PathNode prent│    │
│─────────────────────│      │  └───────────────┘    │
│ HashMap scores      │      │  ┌───────────────┐    │
│ File scoreFile      │      │  │   MinHeap     │    │
│ + updateHighScore() │      │  │ PathNode[] hp │    │
│ + getHighScore()    │      │  │ + insert()    │    │
│ + getSortedText()   │      │  │ + extractMin()│    │
└─────────────────────┘      │  └───────────────┘    │
                             └───────────────────────┘
```

---

## 🔍 A\* Pathfinding Deep Dive

```
Board state example (32 × 22 grid):

  S = Snake head    s = Snake body    F = Food    . = empty

  . . . . . . . . . . . . . . .
  . . . s s s . . . . . . . . .
  . . . s S → → → → F . . . . .
  . . . . . . . . . . . . . . .
        ↑
  Path computed by A* (yellow dots in-game)
```

**Algorithm flow:**

```
1. Build blocked[][] grid from snake body positions
2. Insert start node (head) into MinHeap with g=0, h=manhattan(head,food)
3. While heap not empty:
     current = heap.extractMin()
     if current == food → reconstruct & return path
     for each of 4 neighbours (UP/DOWN/LEFT/RIGHT):
         if in-bounds AND not blocked AND not closed:
             newG = current.g + 1
             if newG < neighbour.g:
                 neighbour.g = newG
                 neighbour.h = manhattan(neighbour, food)
                 neighbour.parent = current
                 heap.insert(neighbour)
4. Return empty list (no path exists)
```

**Why Manhattan distance?** The grid has no diagonal moves, so Manhattan distance is both admissible (never overestimates) and consistent — guaranteeing optimal paths.

**Toggle during play:**  
Press **`H`** to overlay the computed path as a yellow dotted trail. The path is recomputed every game tick when the hint is active.

---

## 📁 Project Structure

```
SnakeGameDSA/
├── src/
│   ├── Main.java                  # Entry point — creates JFrame, managers
│   ├── MainMenu.java              # All screen navigation (CardLayout)
│   ├── GamePanel.java             # Single-player game loop & rendering
│   ├── MultiplayerGamePanel.java  # Two-player game loop & rendering
│   ├── GameOverPanel.java         # Game-over screen with score summary
│   │
│   ├── SnakeNode.java             # DSA: single linked-list node
│   ├── SnakeLinkedList.java       # DSA: custom singly linked list
│   ├── DirectionQueue.java        # DSA: custom FIFO queue (max 3)
│   ├── AStarPathFinder.java       # DSA: A* search + custom MinHeap
│   │
│   ├── ScoreManager.java          # Persistence: HashMap + scores.txt
│   ├── PlayerHistoryManager.java  # Persistence: history.txt
│   │
│   └── UITheme.java               # UI: colours, fonts, helpers,
│                                  #     RoundedPanel, ModernButton,
│                                  #     GradientPanel, AnimatedSnakePanel
│
├── Run_Snake_Game.bat             # Windows one-click compile + run
├── scores.txt                     # Auto-created: high scores
├── history.txt                    # Auto-created: match history
└── README.md
```

---

## 💾 File Persistence

### `scores.txt` — High Scores

Stores one record per player. Only updated when a new personal best is achieved.

```
Alice,240
Bob,180
Carol,310
```

### `history.txt` — Match History

Appended after every single-player game.

```
Alice,240,2024-06-10 03:45 PM,Hit Wall
Bob,130,2024-06-10 03:52 PM,Hit Own Body
Alice,310,2024-06-10 04:01 PM,Hit Wall
```

**History summary shown in-app:**

```
Total Games Played: 3
Highest Score Achieved: 310
Last Played: 2024-06-10 04:01 PM

Player: Alice | Score: 240 | Date/Time: 2024-06-10 03:45 PM | Result: Hit Wall
...
```

---

## 🎨 UI & Theming

All UI is handled by `UITheme.java` and related inner classes with zero external dependencies.

### Colour Palette

| Token | Hex | Use |
|-------|-----|-----|
| `PRIMARY` | `#3FEE89` | Snake head, borders, highlights |
| `PRIMARY_DARK` | `#158E53` | Snake body gradient |
| `ACCENT` | `#00C6FF` | Player 2 snake, secondary highlights |
| `WARNING` | `#FF5858` | Game over title, danger buttons |
| `TEXT` | `#EEF8FF` | Main text |
| `MUTED` | `#9DB0BE` | Labels, legends |
| `BG_TOP` | `#070B18` | Gradient background top |
| `BG_BOTTOM` | `#10202B` | Gradient background bottom |

### Custom Components

| Component | Description |
|-----------|-------------|
| `RoundedPanel` | Glass-style rounded card with drop shadow |
| `ModernButton` | Hover/press animation via Swing Timer, glow effect |
| `GradientPanel` | Full-bleed gradient background panel |
| `AnimatedSnakePanel` | Main-menu background with wave-animated snakes |

### Game Rendering Highlights

- **Board:** Checkerboard texture overlay, rounded border with green neon glow, inner cyan accent ring
- **Food:** Triple-layer glow (outer halo → mid glow → core gradient) with white specular highlight
- **Snake head:** Green glow aura, gradient fill, direction-aware eyes with pupils
- **Snake body:** Per-segment colour fade (brightness decreases toward tail)
- **A\* path hint:** Yellow dashed line with circular waypoints overlaid on the board
- **HUD cards:** Dark glass cards with gradient fill, label in muted text, value in large white type
- **Pause overlay:** Semi-transparent dark blur over board with centred PAUSED text

---

## 🕹️ Controls Reference

### Single Player

| Key | Action |
|-----|--------|
| `↑ ↓ ← →` | Move snake |
| `P` | Pause / Resume |
| `R` | Restart game |
| `H` | Toggle A\* path hint |

### Two Player

| Key | Player 1 | Player 2 |
|-----|----------|----------|
| Move Up | `↑` | `W` |
| Move Down | `↓` | `S` |
| Move Left | `←` | `A` |
| Move Right | `→` | `D` |
| Pause | `P` | `P` |
| Restart | `R` | `R` |

> Key bindings use `WHEN_IN_FOCUSED_WINDOW` input maps — they remain active even after clicking HUD buttons, which would otherwise steal keyboard focus.

---

## ⚡ Difficulty Modes

| Difficulty | Initial Delay | Min Delay | Speed Increment |
|-----------|--------------|-----------|-----------------|
| Easy | 170 ms | 45 ms | −5 ms per food |
| Medium | 120 ms | 45 ms | −5 ms per food |
| Hard | 80 ms | 45 ms | −5 ms per food |

Speed increases dynamically as the score grows — the snake never exceeds 45 ms/tick (~22 FPS equivalent in movement).

---

## ⚔️ Multiplayer Collision Rules

Priority is evaluated top-to-bottom on every game tick:

```
┌──────────────────────────────────────────────────────┐
│ Priority 1 — Wall Hit                                │
│   • Snake hits boundary → that player loses          │
│   • Both hit wall simultaneously → score decides     │
├──────────────────────────────────────────────────────┤
│ Priority 2 — Self Collision (≥ 4 segments)           │
│   • Snake head enters own body → that player loses   │
│   • Both self-collide → score decides                │
├──────────────────────────────────────────────────────┤
│ Priority 3 — Head-to-Head                            │
│   • Both heads meet at same tile → score decides     │
├──────────────────────────────────────────────────────┤
│ Priority 4 — Head into Other Snake Body              │
│   • Head enters opponent body → that player loses    │
│   • Both hit each other's body → score decides       │
├──────────────────────────────────────────────────────┤
│ Score Tiebreaker                                     │
│   • Higher score wins                                │
│   • Equal score → Draw                               │
└──────────────────────────────────────────────────────┘
```

> **Self-collision guard:** Snakes shorter than 4 segments cannot trigger a self-hit — prevents false positives from rapid input at game start.

---

## ⚠️ Known Limitations

- **Sound:** A placeholder `playEatSoundPlaceholder()` method exists in `GamePanel`; audio is not implemented.
- **No diagonal movement:** The grid enforces four cardinal directions only, consistent with the Manhattan heuristic.
- **Single shared food (multiplayer):** Both players target the same food item simultaneously.
- **No AI opponent:** A\* provides a *hint* for the human player; it does not control the snake automatically.
- **`removeTail()` is O(n):** Because the list is singly-linked, finding the node before the tail requires full traversal. For the board sizes used (32 × 22) this is imperceptible.

---

## 🛠️ Built With

- **Java SE 8+** — core language
- **Java Swing** — all GUI rendering
- **No external libraries or game engines**

---

## 📊 DSA Concept Summary

```
Game Feature              DSA Concept              Class
─────────────────────────────────────────────────────────────────
Snake body                Custom Singly Linked List  SnakeLinkedList
                          + Linked List Node          SnakeNode
Direction buffering       Custom FIFO Queue           DirectionQueue
High score storage        HashMap (O(1) lookup)       ScoreManager
A* path search            Graph traversal + heuristic AStarPathFinder
A* open set               Custom Min-Heap             AStarPathFinder
                                                      .MinHeap
Leaderboard sorting       Comparison sort (O(n log n))ScoreManager
Match log persistence     File I/O (append)           PlayerHistoryManager
Score persistence         File I/O (rewrite)          ScoreManager
```

---

<div align="center">

Made with ❤️ using Java · No external dependencies · Pure DSA

</div>
