## Pac-Man Multiplayer

### 1. Introduction

Pac-Man Multiplayer is a high-performance, cross-platform recreation of the classic arcade maze chase game, engineered from the ground up using modern C++.

The primary goal of this project was to implement a robust real-time game engine without relying on heavy game development frameworks (like Unity or Unreal). Instead, it utilizes a lightweight multimedia library to handle low-level graphics and audio, allowing for full control over the game loop, memory management, and AI behavior.

**Core Problem Solved:**
This application demonstrates how to manage complex state interactions between autonomous agents (ghosts) and a player-controlled entity in real-time. It solves the challenge of efficient collision detection, pathfinding on a grid-based map, and state management (e.g., transitioning agents from aggressive to evasive behaviors) within a strict frame-time budget.

---

### 2. Architecture Overview

High-Level Design (HLD)

The system follows a classic Game Loop architecture, ensuring distinct phases for input processing, game logic updates, and rendering. This decoupling allows the simulation to run deterministically while the rendering interpolates frames for smoothness.

**Text-Based System Flow:**

```
graph TD
    A[Input System] -->|Keyboard Events| B(Game Loop)
    B --> C{Update Phase}
    C -->|Update Physics/Grid Pos| D[Player Controller]
    C -->|Calc Pathfinding/FSM| E[AI System / Ghosts]
    C -->|Check Collisions| F[Game State Manager]
    B --> G{Render Phase}
    G -->|Draw Sprites/UI| H[SFML RenderWindow]
    F -->|Trigger Events| I[Audio Engine]
```

---

### 3. Low-Level Design (LLD)

The codebase uses Object-Oriented Programming (OOP) with a strong focus on polymorphism to manage game entities.

**Game Engine (Game Class):**

* Acts as the central controller.
* Manages the lifecycle of the `sf::RenderWindow` and high-level resources like audio and texture maps.
* Executes the `pollEvents()`, `update()`, and `render()` cycle.

**Entity Hierarchy (GameTile Interface):**

* `GameTile`: Base abstract class for all grid objects.
* `Movable`: Inherits from `GameTile`. Handles velocity, direction, and grid alignment. Used for both the Player and Ghosts.
* `Wall / Edible`: Static entities representing map barriers and collectibles (Dots, Power Pellets).

**AI Architecture (Ghost Class):**

* Implements a Finite State Machine (FSM).
* States: Chase, Scatter, Frightened, Dead.
* Pathfinding: Uses a target-tile evaluation system (rankMove) to determine the optimal path through the maze dynamically.

---

### 4. Use Cases / Real-World Applications

While primarily a game, the underlying engine and logic have broader applications in technical fields:

* **Autonomous Agent Simulation:** The pathfinding algorithms and state switching logic mirror simple robotics problems where agents must navigate a static environment while reacting to dynamic obstacles.
* **Embedded Systems UI:** The efficient, low-overhead rendering loop is applicable to creating graphical user interfaces (GUIs) on resource-constrained embedded devices (e.g., kiosk displays or automotive dashboards).
* **Algorithm Education:** Serves as a visual testbed for implementing and debugging search algorithms (like A* or BFS) and behavioral trees.

---

### 5. Technical Details

**Tech Stack**

* Language: C++17 (Utilizing modern features for resource safety and type deduction).
* Graphics & Audio: SFML 2.5 (Simple and Fast Multimedia Library).
* Build System: CMake 3.20+.
* Platform: Windows, macOS, Linux.

**Key Design Patterns & Choices**

* **Finite State Machine (FSM):** The ghosts utilize an FSM to switch behaviors. For example, when a Power Pellet is consumed, all ghosts atomically switch to the Frightened state, overriding their current target logic.
* **Component-Based Inheritance:** The `Movable` class abstracts shared movement logic, reducing code duplication between the player and enemies.
* **Separation of Concerns:** The `WindowTitle` class handles UI updates independently of the game logic, ensuring that score tracking does not interfere with the physics calculation.

---

### 6. Setup & Running the Project

**Prerequisites**

* C++ Compiler: GCC, Clang, or MSVC supporting C++17.
* CMake: Version 3.20 or higher.
* SFML: Version 2.5 (Development headers and libraries).

**Installation Steps**

1. **Clone the Repository:**

```bash
git clone https://github.com/your-username/arcade-maze-pursuit.git
cd arcade-maze-pursuit
```

2. **Build the Project:**

```bash
mkdir build && cd build
cmake ..
# For Linux/Mac:
make
# For Windows (using MSVC):
cmake --build . --config Release
```

3. **Run the Game:**

* Linux/Mac: `./bin/pacman`
* Windows: Execute the generated `.exe` in the `bin` or `Debug/Release` folder.

*Note:* Ensure the `res` folder is in the same directory as the executable if it is not automatically copied.

---

### 7. Additional Enhancements

* **A Pathfinding:** Currently, the ghost AI ranks moves based on Euclidean distance to the target. Implementing A* (A-Star) would provide strictly optimal pathing around complex wall structures.
* **Scriptable Levels:** Abstracting the map layout into JSON or Lua scripts to allow for user-generated content without recompiling the code.
* **Networked Multiplayer:** Decoupling the input handling to support a second player controlling a ghost via TCP/UDP sockets.

**Notes for Contributors**

* Code Style: The project enforces standard C++ conventions. Please ensure RAII (Resource Acquisition Is Initialization) is used for all memory management.
* Performance: Avoid heavy computations inside the `render()` loop. All logic should remain in `update()`.

---
