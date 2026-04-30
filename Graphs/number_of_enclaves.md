## 🏝️🔒 _The Trapped Lands: The Number of Enclaves Saga_

> \_"In the Grid of Land and Sea,
> some land cells touched the border --
> from them, a person could walk off the grid
> and escape into the ocean.
>
> Other land cells were trapped --
> completely surrounded by water or other trapped land,
> with no path to the border.
>
> The Oracle was commanded:
>
> **'Count the number of land cells
> from which you CANNOT walk off the grid.'**
>
> The Oracle recognized this as the same pattern
> as **Surrounded Regions** --
> but instead of flipping trapped cells,
> she just needed to COUNT them.
>
> **Step 1 -- Burn all border-connected land.**
> DFS from every land cell on the border.
> Mark every reachable land cell as visited (sink it to 0).
> These cells CAN reach the border -- they are NOT enclaves.
>
> **Step 2 -- Count the survivors.**
> Walk the grid. Every remaining `1` is an enclave --
> a land cell that could NOT reach the border.
>
> Burn the escapees. Count the trapped."\_

---

This is the saga of **Number of Enclaves**.

Given an `m × n` binary matrix `grid`:
-   `1` = land
-   `0` = sea

A land cell is an **enclave** if you cannot walk from it
to any border cell through land cells (4-directional movement).

Return the **number of enclave cells**.

```
Input:
  [[0,0,0,0],
   [1,0,1,0],
   [0,1,1,0],
   [0,0,0,0]]
Output: 3   (the three 1s in the middle that can't reach the border)

Input:
  [[0,1,1,0],
   [0,0,1,0],
   [0,0,1,0],
   [0,0,0,0]]
Output: 0   (all land cells are connected to the border)
```

---

## 🧠 The Oracle's Core Insight -- Burn Border Land, Count the Rest

Same logic as Surrounded Regions:

> **Land connected to the border can escape → NOT an enclave.**
> **Land NOT connected to the border is trapped → IS an enclave.**

Instead of finding enclaves directly (hard),
find the NON-enclaves (easy -- DFS from borders) and remove them.
Whatever land remains = enclaves.

```
Step 1: DFS from every border land cell → sink to 0 (mark as non-enclave).
Step 2: Count remaining 1s → these are the enclaves.
```

```
Time:  O(M × N) -- DFS + one grid scan
Space: O(M × N) -- recursion stack worst case
```

---

### 📜 The Scroll of the Trapped Lands

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔥 The DFS -- Burn Border-Connected Land

```cpp
void dfs(vector<vector<int>>& grid, int r, int c) {
```

The Oracle entered a border-connected land cell
and began sinking it and all its connected land.

---

### 🛑 Out of Bounds

```cpp
    int rows = grid.size();
    int cols = grid[0].size();

    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
```

Off the grid. Stop.

---

### 🛑 Not Land

```cpp
    if (grid[r][c] != 1) return;
```

Sea (`0`) or already sunk. Stop.

---

### 🌊 Sink This Cell

```cpp
    grid[r][c] = 0;
```

This land cell was connected to the border.
It is NOT an enclave. Sink it to `0`.

This also marks it as visited --
`0` won't match the `!= 1` check, preventing revisits.

> _"You can reach the border.
> You are free. You are not an enclave.
> Sink into the sea so you are never counted."_

---

### 🧭 Spread to All Four Neighbors

```cpp
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

The sinking spread to every connected land cell.
The entire border-connected component was consumed.

---

## 🏝️ Step One -- DFS from Every Border Land Cell

```cpp
int numEnclaves(vector<vector<int>>& grid) {
    int rows = grid.size();
    int cols = grid[0].size();
```

Measure the grid.

---

### 🔝🔻 Top and Bottom Edges

```cpp
    for (int c = 0; c < cols; c++) {
        if (grid[0][c] == 1) dfs(grid, 0, c);
        if (grid[rows - 1][c] == 1) dfs(grid, rows - 1, c);
    }
```

Walk the top row and bottom row.
Every land cell on these edges triggered a DFS --
sinking it and all connected land.

---

### ◀️▶️ Left and Right Edges

```cpp
    for (int r = 0; r < rows; r++) {
        if (grid[r][0] == 1) dfs(grid, r, 0);
        if (grid[r][cols - 1] == 1) dfs(grid, r, cols - 1);
    }
```

Walk the left column and right column. Same logic.

After this step, every land cell reachable from the border
has been sunk to `0`. Only enclaves remain as `1`.

> _"The border has been walked.
> Every land cell with an escape route has been drowned.
> What remains as `1` is truly trapped."_

---

## 🔢 Step Two -- Count the Remaining Land Cells

```cpp
    int count = 0;

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == 1) {
                count++;
            }
        }
    }
    return count;
}
```

Walk the entire grid one final time.
Every `1` that survived the border DFS is an enclave.
Count them.

---

### 🎺 The Trial of the Trapped Lands

```cpp
int main() {
    vector<vector<int>> g1 = {
        {0,0,0,0},
        {1,0,1,0},
        {0,1,1,0},
        {0,0,0,0}
    };
    cout << numEnclaves(g1) << endl; // expected: 3

    vector<vector<int>> g2 = {
        {0,1,1,0},
        {0,0,1,0},
        {0,0,1,0},
        {0,0,0,0}
    };
    cout << numEnclaves(g2) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for grid1:**

```
Initial:
  0 0 0 0
  1 0 1 0
  0 1 1 0
  0 0 0 0
```

**Step 1 -- DFS from border land cells:**

Scan borders:
-   Top row: all `0`. No DFS.
-   Bottom row: all `0`. No DFS.
-   Left column: (1,0) is `1` → DFS!
-   Right column: all `0`. No DFS.

DFS from (1,0):
-   (1,0) = `1` → sink to `0`.
-   Neighbors: (0,0)=0, (2,0)=0, (1,1)=0. All sea. No spreading.

```
After Step 1:
  0 0 0 0
  0 0 1 0
  0 1 1 0
  0 0 0 0
```

Only (1,0) was border-connected. It was sunk.
The three `1`s at (1,2), (2,1), (2,2) are NOT connected to any border.

**Step 2 -- Count remaining 1s:**

(1,2) = 1. (2,1) = 1. (2,2) = 1. Count = 3.

**Answer: 3** ✓

---

**Full trace for grid2:**

```
Initial:
  0 1 1 0
  0 0 1 0
  0 0 1 0
  0 0 0 0
```

**Step 1 -- DFS from border land cells:**

Scan borders:
-   Top row: (0,1) is `1` → DFS!

DFS from (0,1):
-   (0,1) = `1` → sink. Spread to (0,2).
-   (0,2) = `1` → sink. Spread to (1,2).
-   (1,2) = `1` → sink. Spread to (2,2).
-   (2,2) = `1` → sink. No more `1` neighbors.

```
After Step 1:
  0 0 0 0
  0 0 0 0
  0 0 0 0
  0 0 0 0
```

ALL land was connected to the border through (0,1).
Everything sunk.

**Step 2 -- Count remaining 1s:** 0.

**Answer: 0** ✓

---

**Trace for all-land grid `[[1,1],[1,1]]`:**

Every cell is on the border (2×2 grid).
DFS from every border cell sinks everything.
Count = 0.

**Answer: 0** ✓ -- in a 2×2 grid, every cell IS a border cell.

---

**Trace for `[[0,0,0],[0,1,0],[0,0,0]]`:**

Single `1` at (1,1). Not on any border.
No border land cells → no DFS runs.
Count remaining `1`s = 1.

**Answer: 1** ✓ -- the center cell is an enclave.

---

## 🔍 How This Relates to Surrounded Regions

| Surrounded Regions                | Number of Enclaves                |
| --------------------------------- | --------------------------------- |
| Flip trapped `O`s to `X`         | Count trapped `1`s                |
| Three states: O, S, X            | Two states: 0, 1 (sink in-place) |
| DFS from border → mark safe      | DFS from border → sink non-enclaves |
| Final pass: flip O→X, S→O        | Final pass: count remaining 1s    |

Same "protect the border, capture the rest" pattern.
Enclaves is simpler -- just count instead of flip.

---

## 🔄 BFS Alternative

Replace DFS with BFS -- enqueue all border land cells,
BFS outward sinking everything reachable:

```cpp
queue<pair<int,int>> q;
for (int c = 0; c < cols; c++) {
    if (grid[0][c] == 1) { grid[0][c] = 0; q.push({0, c}); }
    if (grid[rows-1][c] == 1) { grid[rows-1][c] = 0; q.push({rows-1, c}); }
}
for (int r = 0; r < rows; r++) {
    if (grid[r][0] == 1) { grid[r][0] = 0; q.push({r, 0}); }
    if (grid[r][cols-1] == 1) { grid[r][cols-1] = 0; q.push({r, cols-1}); }
}
// BFS sinks all border-connected land
while (!q.empty()) {
    auto [r, c] = q.front(); q.pop();
    for (auto& d : dirs) {
        int nr = r + d[0], nc = c + d[1];
        if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == 1) {
            grid[nr][nc] = 0;
            q.push({nr, nc});
        }
    }
}
```

Same result. No recursion stack risk.

---

### 🧠 Memory of the Trapped Lands Law

-   **Step 1:** DFS from every border land cell → sink to `0` (non-enclave)
-   **Step 2:** count remaining `1`s → these are enclaves
-   **DFS guard clauses:** out of bounds or not `1` → return
-   **Sinking = visiting:** `grid[r][c] = 0` prevents revisits
-   **Same pattern as Surrounded Regions** but count instead of flip
-   **BFS alternative:** enqueue all border land, BFS outward
-   **Time:** O(M × N)
-   **Space:** O(M × N) -- recursion stack or queue
-   **Edge cases:**
    -   All land on border → 0 enclaves
    -   No land at all → 0 enclaves
    -   Single interior land cell → 1 enclave
    -   2×2 grid → every cell is a border cell → 0 enclaves

Thus is remembered the saga of **Number of Enclaves**,
where the Oracle walked the border of the grid
and drowned every land cell that could escape --
DFS-ing from every border `1`,
sinking it and all its connected land to sea --
then counting the survivors,
the trapped `1`s that could never reach the edge,
the true enclaves of the grid. 🏝️🔒✨
