## 🔢🌊 _The Distance to the Nearest Zero: The 01 Matrix Saga_

> \_"In the Grid of Ones and Zeros,
> every cell held either a `0` or a `1`.
>
> The Oracle was commanded:
>
> **'For every cell, find the distance
> to the NEAREST `0` cell.
> Distance is measured in steps --
> up, down, left, right (Manhattan distance).'**
>
> Cells that ARE `0` have distance 0 to themselves.
> Cells that are `1` need to find the closest `0`.
>
> The naive approach: for each `1` cell,
> BFS outward to find the nearest `0`.
> O(cells × grid size) -- too slow.
>
> The Oracle flipped the problem:
>
> **Instead of searching FROM each `1` TO a `0`,
> search FROM all `0`s TO all `1`s simultaneously.**
>
> **Multi-source BFS.**
>
> Start BFS from ALL `0` cells at once.
> Every `0` is at distance 0.
> Their `1` neighbors are at distance 1.
> Their neighbors' neighbors are at distance 2.
> And so on.
>
> The first wave to reach a `1` cell
> is guaranteed to be the shortest distance.
>
> The same pattern as Walls and Gates
> and Rotting Oranges."\_

---

This is the saga of **01 Matrix**.

Given an `m × n` binary matrix `mat`:

-   For each cell, find the distance to the **nearest `0`**.
-   Return a matrix of the same size with the distances.

```
Input:
  [[0,0,0],
   [0,1,0],
   [0,0,0]]
Output:
  [[0,0,0],
   [0,1,0],
   [0,0,0]]

Input:
  [[0,0,0],
   [0,1,0],
   [1,1,1]]
Output:
  [[0,0,0],
   [0,1,0],
   [1,2,1]]
```

---

## 🧠 The Oracle's Core Insight -- Multi-Source BFS from All Zeros

All `0` cells are the sources. Distance from a `0` to itself = 0.

BFS outward from all `0`s simultaneously.
Each wave = one step further.
The first wave to reach a `1` cell sets its distance.

```
1. Create a result matrix. Set 0-cells to 0, 1-cells to -1 (unvisited).
2. Enqueue ALL 0-cells.
3. BFS: for each dequeued cell, check 4 neighbors.
   If neighbor is -1 (unvisited 1-cell):
     Set its distance = current cell's distance + 1.
     Enqueue it.
4. Return the result matrix.
```

```
Time:  O(M × N) -- every cell visited at most once
Space: O(M × N) -- the queue + result matrix
```

---

### 📜 The Scroll of the Distance Grid

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 📦 Step One -- Initialize and Enqueue All Zeros

```cpp
vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
    int rows = mat.size();
    int cols = mat[0].size();
```

Measure the grid.

---

```cpp
    vector<vector<int>> dist(rows, vector<int>(cols, -1));
    queue<pair<int, int>> q;
```

`dist` is the result matrix.
`-1` means "not yet reached" (unvisited `1` cell).
`q` is the BFS queue.

---

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (mat[r][c] == 0) {
                dist[r][c] = 0;
                q.push({r, c});
            }
        }
    }
```

Every `0` cell:
-   Distance = 0 (it IS a zero).
-   Enqueued as a BFS source.

Every `1` cell stays at `-1` -- waiting to be reached.

> _"The flood begins at every zero simultaneously.
> They are the sources. Distance 0.
> The ones wait in darkness for the wave to arrive."_

---

## 🌊 Step Two -- BFS Outward from All Zeros

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

The four cardinal directions.

---

```cpp
    while (!q.empty()) {
        auto [r, c] = q.front();
        q.pop();
```

Dequeue a cell. Its distance is already set correctly.

---

### 🧭 Check All Four Neighbors

```cpp
        for (auto& d : dirs) {
            int nr = r + d[0];
            int nc = c + d[1];
```

Compute each neighbor's position.

---

### 🛡️ Boundary Check

```cpp
            if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) {
                continue;
            }
```

Stay within the grid.

---

### 🔍 Is the Neighbor Unvisited?

```cpp
            if (dist[nr][nc] != -1) {
                continue;
            }
```

If the neighbor already has a distance set (not `-1`) --
it was already reached by an earlier wave.
That earlier distance is shorter or equal. Skip.

---

### 📏 Set the Distance and Enqueue

```cpp
            dist[nr][nc] = dist[r][c] + 1;
            q.push({nr, nc});
        }
    }
    return dist;
}
```

The neighbor was a `1` cell that hadn't been reached yet.

Set its distance = current cell's distance + 1.
Enqueue it for further exploration.

> _"The wave reaches a dark cell.
> Its distance is one step more than the cell that sent the wave.
> It joins the queue to spread the wave further."_

---

### 🎺 The Trial of the Distance Grid

```cpp
int main() {
    vector<vector<int>> mat1 = {{0,0,0},{0,1,0},{0,0,0}};
    auto r1 = updateMatrix(mat1);
    for (auto& row : r1) {
        for (int x : row) cout << x << " ";
        cout << endl;
    }
    // expected:
    // 0 0 0
    // 0 1 0
    // 0 0 0

    vector<vector<int>> mat2 = {{0,0,0},{0,1,0},{1,1,1}};
    auto r2 = updateMatrix(mat2);
    for (auto& row : r2) {
        for (int x : row) cout << x << " ";
        cout << endl;
    }
    // expected:
    // 0 0 0
    // 0 1 0
    // 1 2 1

    return 0;
}
```

---

**Full trace for `[[0,0,0],[0,1,0],[1,1,1]]`:**

```
Initial mat:
  0 0 0
  0 1 0
  1 1 1

After init:
  dist:
   0  0  0
   0 -1  0
  -1 -1 -1

Queue: [(0,0),(0,1),(0,2),(1,0),(1,2)]  (all zeros)
```

**Wave 1 (distance 0 → 1):**

Process all 5 zero-cells. Their unvisited neighbors:

| Dequeue | Unvisited neighbors set to dist=1 |
| ------- | --------------------------------- |
| (0,0)   | none (all neighbors are 0 or OOB) |
| (0,1)   | (1,1) → dist=1                   |
| (0,2)   | none                              |
| (1,0)   | (2,0) → dist=1                   |
| (1,2)   | (2,2) → dist=1                   |

```
  dist after wave 1:
   0  0  0
   0  1  0
   1 -1  1
```

Queue: [(1,1),(2,0),(2,2)]

**Wave 2 (distance 1 → 2):**

| Dequeue | Unvisited neighbors set to dist=2 |
| ------- | --------------------------------- |
| (1,1)   | (2,1) → dist=2                   |
| (2,0)   | none (neighbors already visited)  |
| (2,2)   | none                              |

```
  dist after wave 2:
   0  0  0
   0  1  0
   1  2  1
```

Queue empty. Done.

**Answer matches expected** ✓

---

**Trace for `[[0,0,0],[0,1,0],[0,0,0]]`:**

All `1` cells are surrounded by `0`s.
The center `1` at (1,1) is reached in wave 1 from any of its 4 neighbors.

```
  0 0 0
  0 1 0
  0 0 0
```

**Answer: distance of center = 1.** ✓

---

**Trace for `[[1,1,1],[1,1,1],[1,1,0]]`:**

Only one `0` at (2,2). BFS spreads outward:

```
Wave 0: (2,2) = 0
Wave 1: (2,1) = 1, (1,2) = 1
Wave 2: (2,0) = 2, (1,1) = 2, (0,2) = 2
Wave 3: (1,0) = 3, (0,1) = 3
Wave 4: (0,0) = 4
```

```
  4 3 2
  3 2 1
  2 1 0
```

The farthest cell (0,0) is 4 steps from the only zero. ✓

---

## 🔍 Why Multi-Source BFS and Not Per-Cell BFS?

**Per-cell BFS:** for each `1` cell, BFS to find nearest `0`.
Each BFS is O(M×N). Total: O(M²×N²). Way too slow.

**Multi-source BFS:** start from ALL `0`s at once.
Every cell visited exactly once. Total: O(M×N). Optimal.

The multi-source approach shares work across all cells.
The first wave to reach any `1` cell is always the shortest path.

---

## 🔄 The Multi-Source BFS Family

| Problem              | Sources              | What floods?              |
| -------------------- | -------------------- | ------------------------- |
| 01 Matrix            | All `0` cells        | Distance to nearest 0     |
| Walls and Gates      | All gates (value 0)  | Distance to nearest gate  |
| Rotting Oranges      | All rotten oranges   | Time to rot fresh ones    |
| Shortest Bridge      | All cells of island 1| Distance to island 2      |

Same template every time:
enqueue all sources → BFS outward → first visit = shortest distance.

---

## 🔍 Why `-1` Instead of `INT_MAX`?

Both work. `-1` is simpler:
-   `0` cells get distance 0.
-   `1` cells get `-1` (unvisited).
-   Check `dist[nr][nc] != -1` to skip visited cells.

Using `INT_MAX` also works:
-   `0` cells get 0.
-   `1` cells get `INT_MAX`.
-   Check `dist[nr][nc] == INT_MAX` for unvisited.

`-1` avoids any risk of overflow when doing `dist + 1`.

---

### 🧠 Memory of the Distance Grid Law

-   **Multi-source BFS** from all `0` cells simultaneously
-   **Initialize:** `dist[r][c] = 0` for zeros, `-1` for ones. Enqueue all zeros.
-   **BFS:** for each dequeued cell, check 4 neighbors
    -   Out of bounds → skip
    -   Already visited (`!= -1`) → skip
    -   Unvisited → `dist = current + 1`, enqueue
-   **First visit = shortest distance** -- BFS guarantees this
-   **Time:** O(M × N) -- every cell visited once
-   **Space:** O(M × N) -- queue + result matrix
-   **Edge cases:**
    -   All zeros → all distances are 0
    -   Single zero in corner → distances form a gradient
    -   All ones except one → BFS from that single zero

Thus is remembered the saga of **01 Matrix**,
where the Oracle flooded from every zero at once --
a multi-source BFS that expanded uniformly,
wave by wave, step by step --
the first wave to reach each one-cell
setting its distance to the nearest zero --
until every cell in the grid
knew exactly how far it stood
from the closest zero. 🔢🌊✨
