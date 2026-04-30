## 🏝️📏 _The Walls of the Lonely Island: The Island Perimeter Saga_

> \_"In the Sea of Grids,
> a single island lay surrounded by water --
> its shape carved from `1`s in a grid of `0`s.
>
> The King commanded:
>
> **'Measure the perimeter of this island --
> the total length of its outer walls,
> where land meets water or land meets the edge of the grid.'**
>
> The Oracle studied the grid.
>
> She realized she did not need DFS.
> She did not need BFS.
> She did not need flood fill or visited arrays.
>
> She needed only one simple truth:
>
> **Every land cell contributes 4 walls.
> But every shared edge between two adjacent land cells
> removes 2 walls (one from each cell).**
>
> Count the land cells. Count the shared edges.
>
> `perimeter = 4 × land - 2 × sharedEdges`
>
> One pass through the grid.
> No recursion. No graph traversal.
> Pure counting."\_

---

This is the saga of **Island Perimeter**.

Given a 2D grid where:
-   `1` = land
-   `0` = water

The grid contains **exactly one island** (one connected component of land).
There are **no lakes** (no water enclosed by land).

Return the **perimeter** of the island.

```
Input:
  [[0,1,0,0],
   [1,1,1,0],
   [0,1,0,0],
   [1,1,0,0]]
Output: 16

Input:
  [[1]]
Output: 4

Input:
  [[1,0]]
Output: 4
```

---

## 🧠 The Oracle's Core Insight -- Walls Minus Shared Edges

Every land cell is a unit square with **4 walls**.

But when two land cells are adjacent (share an edge),
that shared edge is an **interior wall** -- not part of the perimeter.
Each shared edge removes **2 walls** from the total
(one from each cell).

```
perimeter = 4 × (number of land cells) - 2 × (number of shared edges)
```

To count shared edges, we only need to check
**right** and **down** neighbors for each cell.
This avoids double-counting --
if we checked all 4 directions, each shared edge
would be counted twice.

```
Time:  O(rows × cols) -- single pass through the grid
Space: O(1) -- only counters
```

---

### 📜 The Scroll of the Island Walls

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Wall-Counting Ritual

```cpp
int islandPerimeter(vector<vector<int>>& grid) {
    int rows = grid.size();
    int cols = grid[0].size();
    int land = 0;
    int sharedEdges = 0;
```

The Oracle prepared two counters:

-   `land` -- the total number of land cells.
-   `sharedEdges` -- the number of edges shared between adjacent land cells.

---

## 🔁 Walk Every Cell in the Grid

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
```

Every cell was inspected, row by row, column by column.

---

### 🏝️ Found a Land Cell

```cpp
            if (grid[r][c] == 1) {
                land++;
```

A land cell was found. It contributed 4 potential walls.
The counter incremented.

---

### ➡️ Check the Right Neighbor

```cpp
                if (c + 1 < cols && grid[r][c + 1] == 1) {
                    sharedEdges++;
                }
```

If the cell to the **right** was also land --
they shared an edge. One shared edge counted.

The boundary check `c + 1 < cols` prevented going off the grid.

---

### ⬇️ Check the Down Neighbor

```cpp
                if (r + 1 < rows && grid[r + 1][c] == 1) {
                    sharedEdges++;
                }
            }
        }
    }
```

If the cell **below** was also land --
another shared edge counted.

> _"We only check RIGHT and DOWN --
> never left or up.
> This ensures every shared edge is counted exactly once.
> If we checked all four directions,
> every edge would be counted twice."_

---

## 📐 Compute the Perimeter

```cpp
    return 4 * land - 2 * sharedEdges;
}
```

Each land cell started with 4 walls.
Each shared edge removed 2 walls (one from each side).

The formula gave the exact perimeter.

> _"4 walls per cell.
> Minus 2 for every handshake between neighbors.
> What remains is the outer boundary --
> the perimeter."_

---

### 🎺 The Trial of the Island Walls

```cpp
int main() {
    vector<vector<int>> grid1 = {
        {0,1,0,0},
        {1,1,1,0},
        {0,1,0,0},
        {1,1,0,0}
    };
    cout << islandPerimeter(grid1) << endl; // expected: 16

    vector<vector<int>> grid2 = {{1}};
    cout << islandPerimeter(grid2) << endl; // expected: 4

    vector<vector<int>> grid3 = {{1,0}};
    cout << islandPerimeter(grid3) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for the main example:**

```
Grid:
  0 1 0 0
  1 1 1 0
  0 1 0 0
  1 1 0 0
```

**Counting land cells and shared edges:**

| Cell (r,c) | Land? | Right neighbor land? | Down neighbor land? | land | sharedEdges |
| ---------- | ----- | -------------------- | ------------------- | ---- | ----------- |
| (0,0)      | 0     | --                   | --                  | 0    | 0           |
| (0,1)      | 1     | No (0)               | Yes (1)             | 1    | 1           |
| (0,2)      | 0     | --                   | --                  | 1    | 1           |
| (0,3)      | 0     | --                   | --                  | 1    | 1           |
| (1,0)      | 1     | Yes (1)              | No (0)              | 2    | 2           |
| (1,1)      | 1     | Yes (1)              | Yes (1)             | 3    | 4           |
| (1,2)      | 1     | No (0)               | No (0)              | 4    | 4           |
| (1,3)      | 0     | --                   | --                  | 4    | 4           |
| (2,0)      | 0     | --                   | --                  | 4    | 4           |
| (2,1)      | 1     | No (0)               | Yes (1)             | 5    | 5           |
| (2,2)      | 0     | --                   | --                  | 5    | 5           |
| (2,3)      | 0     | --                   | --                  | 5    | 5           |
| (3,0)      | 1     | Yes (1)              | No (edge)           | 6    | 6           |
| (3,1)      | 1     | No (0)               | No (edge)           | 7    | 6           |
| (3,2)      | 0     | --                   | --                  | 7    | 6           |
| (3,3)      | 0     | --                   | --                  | 7    | 6           |

**Final:** land = 7, sharedEdges = 6.

`perimeter = 4 × 7 - 2 × 6 = 28 - 12 = 16` ✓

---

**Trace for `[[1]]`:**

land = 1, sharedEdges = 0.
`perimeter = 4 × 1 - 2 × 0 = 4` ✓

A single cell has 4 walls, no neighbors.

---

**Trace for `[[1, 1]]`:**

land = 2, sharedEdges = 1 (right neighbor).
`perimeter = 4 × 2 - 2 × 1 = 8 - 2 = 6` ✓

Two cells side by side: 4+4 = 8 walls, minus 2 for the shared edge.

---

**Trace for a 2×2 square `[[1,1],[1,1]]`:**

land = 4.
Shared edges: (0,0)→right, (0,0)→down, (0,1)→down, (1,0)→right = 4.
`perimeter = 4 × 4 - 2 × 4 = 16 - 8 = 8` ✓

A 2×2 square has perimeter 8 -- like a real square of side 2.

---

## 🔄 Alternative -- Count Exposed Walls Per Cell

Instead of the formula, count walls directly:

```cpp
int islandPerimeter(vector<vector<int>>& grid) {
    int rows = grid.size(), cols = grid[0].size();
    int perimeter = 0;

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == 1) {
```

For each land cell, check all 4 neighbors.
Every neighbor that is water or out of bounds = 1 wall.

```cpp
                if (r == 0 || grid[r-1][c] == 0) perimeter++;  // top
                if (r == rows-1 || grid[r+1][c] == 0) perimeter++;  // bottom
                if (c == 0 || grid[r][c-1] == 0) perimeter++;  // left
                if (c == cols-1 || grid[r][c+1] == 0) perimeter++;  // right
            }
        }
    }
    return perimeter;
}
```

Each land cell contributes 0 to 4 walls depending on its neighbors.

**Top wall exists if:** row is 0 (grid edge) OR cell above is water.
**Bottom wall exists if:** row is last OR cell below is water.
**Left wall exists if:** col is 0 OR cell left is water.
**Right wall exists if:** col is last OR cell right is water.

Same O(rows × cols) time. More intuitive for some people.
The formula approach is more elegant. Both are correct.

---

## 🔍 Why No DFS/BFS Needed?

This problem has **no connectivity requirement** to solve.
We don't need to "find" the island or "traverse" it.

We just need to count land cells and shared edges --
a simple grid scan. No visited array. No recursion.

DFS/BFS would work (traverse the island, count exposed walls),
but it's overkill. The counting approach is simpler and faster.

The problem guarantees exactly one island with no lakes,
so every land cell is part of the same island.
No need to distinguish components.

---

## 🌐 Connection to Other Island Problems

| Problem              | Needs DFS/BFS? | Why?                              |
| -------------------- | -------------- | --------------------------------- |
| Island Perimeter     | No             | Just count cells and edges        |
| Number of Islands    | Yes            | Must identify separate components |
| Max Area of Island   | Yes            | Must traverse each island fully   |
| Surrounded Regions   | Yes            | Must find connected boundary cells|

Island Perimeter is the simplest island problem --
a warm-up before the real graph traversal challenges.

---

### 🧠 Memory of the Island Wall Law

-   **Formula:** `perimeter = 4 × land - 2 × sharedEdges`
-   **Count land cells:** every `1` in the grid
-   **Count shared edges:** check only RIGHT and DOWN neighbors (avoids double-counting)
-   **Alternative:** for each land cell, count neighbors that are water or out-of-bounds
-   **No DFS/BFS needed** -- pure grid counting
-   **No visited array** -- each cell checked independently
-   **Time:** O(rows × cols) -- single pass
-   **Space:** O(1) -- only counters
-   **Edge cases:**
    -   Single cell → perimeter = 4
    -   Straight line of cells → perimeter = 2 × length + 2
    -   Square block → perimeter = 4 × side length
    -   L-shaped island → formula handles it naturally

Thus is remembered the saga of **Island Perimeter**,
where the Oracle did not traverse the island
but simply counted its land cells and shared edges --
knowing that every cell brought 4 walls
and every neighborly handshake removed 2 --
until the formula revealed the exact perimeter
of the lonely island in the sea of grids. 🏝️📏✨
