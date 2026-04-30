## 🗺️🏃 _The Shortest Road Through the Grid: The Shortest Path in Binary Matrix Saga_

> \_"In the Grid Kingdom,
> a traveler stood at the top-left corner `(0, 0)`
> and needed to reach the bottom-right corner `(n-1, n-1)`.
>
> The grid was filled with `0`s (open roads) and `1`s (walls).
> The traveler could move in **8 directions** --
> up, down, left, right, AND all four diagonals.
>
> The Oracle was commanded:
>
> **'Find the shortest path from top-left to bottom-right,
> moving only through `0` cells.
> Return the LENGTH of the path (number of cells visited).
> If no path exists -- return -1.'**
>
> The Oracle recognized this instantly:
>
> **Unweighted graph → BFS.**
>
> Each cell is a node.
> Each move to an adjacent `0` cell is an edge (cost 1).
> BFS finds the shortest path in an unweighted graph.
>
> The only twist: **8 directions** instead of the usual 4."\_

---

This is the saga of **Shortest Path in Binary Matrix**.

Given an `n × n` binary grid:
-   `0` = open cell (can walk through).
-   `1` = blocked cell (wall).

Find the shortest path from `(0, 0)` to `(n-1, n-1)`,
moving in **8 directions** (including diagonals).

Return the **length** of the path (number of cells visited).
If no path exists, return `-1`.

```
Input:
  [[0,1],
   [1,0]]
Output: 2   (path: (0,0) → (1,1))

Input:
  [[0,0,0],
   [1,1,0],
   [1,1,0]]
Output: 4   (path: (0,0) → (0,1) → (0,2) → (1,2) → (2,2))

Input:
  [[1,0,0],
   [1,1,0],
   [1,1,0]]
Output: -1  (start cell is blocked)
```

---

## 🧠 The Oracle's Core Insight -- BFS with 8 Directions

This is standard grid BFS with two differences from typical island problems:

1. **8 directions** instead of 4 (diagonals allowed).
2. **Path length** = number of cells visited (not number of edges).

BFS guarantees shortest path in an unweighted graph.
The first time BFS reaches `(n-1, n-1)` = shortest path.

```
Time:  O(N²) -- every cell visited at most once
Space: O(N²) -- the queue + visited matrix
```

---

### 📜 The Scroll of the Grid Road

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏃 The Oracle's BFS Ritual

```cpp
int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
    int n = grid.size();
```

Measure the grid. It's `n × n`.

---

### 🛑 Edge Case -- Start or End Is Blocked

```cpp
    if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) {
        return -1;
    }
```

If the starting cell or the ending cell is a wall --
no path is possible. Return `-1` immediately.

---

### 🛑 Edge Case -- Single Cell Grid

```cpp
    if (n == 1) return 1;
```

A 1×1 grid with `grid[0][0] = 0`.
The start IS the end. Path length = 1 (just that cell).

---

### 📦 Prepare the BFS

```cpp
    queue<pair<int, int>> q;
    q.push({0, 0});
    grid[0][0] = 1;
```

Enqueue the starting cell.
Mark it as visited by setting it to `1` (wall).

Using the grid itself as the visited marker saves space --
no separate `visited` array needed.

> _"The traveler steps onto the first cell.
> It turns to stone behind them --
> no one will walk here again."_

---

### ⏱️ Initialize Path Length

```cpp
    int pathLength = 1;
```

The path length starts at 1 --
the starting cell itself counts as part of the path.

---

### 🧭 The 8 Directions

```cpp
    int dirs[8][2] = {
        {-1,0},{1,0},{0,-1},{0,1},
        {-1,-1},{-1,1},{1,-1},{1,1}
    };
```

Up, down, left, right -- the usual 4.
Plus the 4 diagonals: up-left, up-right, down-left, down-right.

---

## 🌊 BFS -- Level by Level

```cpp
    while (!q.empty()) {
        int size = q.size();
        pathLength++;
```

`size` captures the current level.
`pathLength++` because we're about to explore the NEXT level --
cells one step further from the start.

---

### 🔁 Process Every Cell in This Level

```cpp
        for (int i = 0; i < size; i++) {
            auto [r, c] = q.front();
            q.pop();
```

Dequeue one cell from the current level.

---

### 🧭 Check All 8 Neighbors

```cpp
            for (auto& d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];
```

Compute each of the 8 neighbors.

---

### 🛡️ Boundary Check

```cpp
                if (nr < 0 || nr >= n || nc < 0 || nc >= n) {
                    continue;
                }
```

Stay within the grid.

---

### 🧱 Wall or Already Visited

```cpp
                if (grid[nr][nc] == 1) {
                    continue;
                }
```

The cell is a wall (`1`) or already visited (we set visited cells to `1`).
Either way -- can't go there.

---

### 🎯 Reached the Destination?

```cpp
                if (nr == n - 1 && nc == n - 1) {
                    return pathLength;
                }
```

We've reached the bottom-right corner!
Return `pathLength` -- the shortest path length.

BFS guarantees this is the first (and therefore shortest) time
we reach the destination.

> _"The first wave to touch the destination
> is the shortest path.
> BFS never lies about distance."_

---

### 📥 Mark Visited and Enqueue

```cpp
                grid[nr][nc] = 1;
                q.push({nr, nc});
            }
        }
    }
```

The cell was open and unvisited.
Mark it as visited (`1`). Enqueue for the next level.

---

### 🏁 Destination Unreachable

```cpp
    return -1;
}
```

BFS exhausted all reachable cells without reaching `(n-1, n-1)`.
No path exists.

---

### 🎺 The Trial of the Grid Road

```cpp
int main() {
    vector<vector<int>> g1 = {{0,1},{1,0}};
    cout << shortestPathBinaryMatrix(g1) << endl; // expected: 2

    vector<vector<int>> g2 = {{0,0,0},{1,1,0},{1,1,0}};
    cout << shortestPathBinaryMatrix(g2) << endl; // expected: 4

    vector<vector<int>> g3 = {{1,0,0},{1,1,0},{1,1,0}};
    cout << shortestPathBinaryMatrix(g3) << endl; // expected: -1

    return 0;
}
```

---

**Full trace for `[[0,1],[1,0]]`:**

```
Grid:
  0 1
  1 0

Start: (0,0). End: (1,1).
```

pathLength starts at 1. Enqueue (0,0). Mark visited.

**Level 1 (pathLength → 2):**
Process (0,0). Check 8 neighbors:
-   (0,1) = 1 (wall). Skip.
-   (1,0) = 1 (wall). Skip.
-   (1,1) = 0 → **DESTINATION!** Return pathLength = 2.

**Answer: 2** ✓

The diagonal move (0,0) → (1,1) is the shortest path.

---

**Full trace for `[[0,0,0],[1,1,0],[1,1,0]]`:**

```
Grid:
  0 0 0
  1 1 0
  1 1 0

Start: (0,0). End: (2,2).
```

pathLength = 1. Enqueue (0,0).

**Level 1 (pathLength → 2):**
Process (0,0). Neighbors:
-   (0,1) = 0 → enqueue. Mark visited.
-   (1,0) = 1. Skip.
-   (1,1) = 1. Skip.

**Level 2 (pathLength → 3):**
Process (0,1). Neighbors:
-   (0,2) = 0 → enqueue.
-   (1,2) = 0 → enqueue.
-   Others: walls or visited.

**Level 3 (pathLength → 4):**
Process (0,2). Neighbors:
-   (1,2) already visited.
Process (1,2). Neighbors:
-   (2,2) = 0 → **DESTINATION!** Return pathLength = 4.

**Answer: 4** ✓

Path: (0,0) → (0,1) → (0,2) → (1,2) → (2,2). Length = 4 cells... wait.

Actually pathLength counts levels. Let me recount:
-   Level 0: (0,0) → pathLength = 1.
-   Level 1: (0,1) → pathLength = 2.
-   Level 2: (0,2), (1,2) → pathLength = 3.
-   Level 3: (2,2) found → pathLength = 4.

**Answer: 4** ✓ (4 cells in the path).

---

**Trace for `[[1,0,0],[1,1,0],[1,1,0]]`:**

`grid[0][0] = 1` → start is blocked → return `-1`.

**Answer: -1** ✓

---

**Trace for `[[0]]`:**

`n = 1`. Start IS the end. Return 1.

**Answer: 1** ✓

---

## 🔍 Why 8 Directions and Not 4?

The problem explicitly allows diagonal movement.
This means the shortest path can cut diagonally --
often shorter than a 4-directional path.

```
4-direction path (0,0) to (2,2):
  (0,0)→(0,1)→(0,2)→(1,2)→(2,2) = 5 cells

8-direction path:
  (0,0)→(1,1)→(2,2) = 3 cells
```

Diagonals are powerful. Always check the problem statement
for whether 4 or 8 directions are allowed.

---

## 🔍 Why Path Length = Number of Cells, Not Edges?

Most shortest path problems count **edges** (steps).
This problem counts **cells** (nodes visited).

`pathLength = edges + 1`.

A path of 3 cells has 2 edges (2 moves).
The problem asks for 3 (cells), not 2 (moves).

That's why `pathLength` starts at 1 (the starting cell)
and increments at each BFS level.

---

## 🔍 Why Modify the Grid Instead of Using Visited?

Setting `grid[nr][nc] = 1` marks it as visited
without needing a separate `visited[][]` array.

This saves O(N²) space but **destroys the input**.
If the caller needs the grid later, use a separate visited array.

---

### 🧠 Memory of the Grid Road Law

-   **BFS** on a grid with **8 directions** (including diagonals)
-   **Edge cases:** start or end blocked → -1. Single cell → 1.
-   **Mark visited** by setting `grid[nr][nc] = 1` (in-place)
-   **Level-by-level BFS:** `size = q.size()`, `pathLength++` per level
-   **Destination check** on each neighbor → return `pathLength` immediately
-   **Path length = cells visited** (not edges), so starts at 1
-   **Time:** O(N²)
-   **Space:** O(N²)
-   **Edge cases:**
    -   Start blocked → -1
    -   End blocked → -1
    -   1×1 grid → 1
    -   No path → -1
    -   Diagonal shortcut → 8 directions find it

Thus is remembered the saga of **Shortest Path in Binary Matrix**,
where the Oracle sent a BFS wave from the top-left corner --
spreading in 8 directions through every open cell,
level by level, step by step --
until the wave touched the bottom-right corner
and the shortest path was known --
or the wave died out
and the destination was declared unreachable. 🗺️🏃✨
