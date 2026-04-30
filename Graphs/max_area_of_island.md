## 🏝️👑 _The Mightiest Land Mass: The Max Area of Island Saga_

> \_"In the Sea of Grids,
> the Oracle had learned to COUNT islands --
> lighting a fire on each new land mass,
> burning it entirely with DFS.
>
> But now the King demanded more:
>
> **'I do not care how many islands exist.
> I want to know the size of the LARGEST one --
> the island with the most land cells.'**
>
> The Oracle adjusted her ritual:
>
> **Instead of just counting DFS calls,
> make each DFS RETURN the area it consumed.**
>
> Every time the fire spread to a new cell,
> the area grew by 1.
> When the fire died out,
> the total area of that island was known.
>
> Track the maximum area across all islands.
>
> Same DFS flood fill.
> One extra return value.
> The mightiest land mass revealed."\_

---

This is the saga of **Max Area of Island**.

Given a 2D grid where:
-   `1` = land
-   `0` = water

Return the **area** of the largest island
(the number of `1`s in the largest connected component).

If there is no island, return `0`.

```
Input:
  [[0,0,1,0,0,0,0,1,0,0,0,0,0],
   [0,0,0,0,0,0,0,1,1,1,0,0,0],
   [0,1,1,0,1,0,0,0,0,0,0,0,0],
   [0,1,0,0,1,1,0,0,1,0,1,0,0],
   [0,1,0,0,1,1,0,0,1,1,1,0,0],
   [0,0,0,0,0,0,0,0,0,0,1,0,0],
   [0,0,0,0,0,0,0,1,1,1,0,0,0],
   [0,0,0,0,0,0,0,1,1,0,0,0,0]]
Output: 6

Input:
  [[0,0,0,0,0,0,0,0]]
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- DFS Returns Area

This is **Number of Islands** with one twist:

Instead of `void dfs(...)`, use `int dfs(...)` that **returns**
the number of cells consumed.

```
For each cell (r, c):
  If grid[r][c] == 1:
    area = dfs(r, c)          (burn the island, get its size)
    maxArea = max(maxArea, area)

DFS returns:
  0 if out of bounds or water
  1 + sum of DFS on all 4 neighbors (this cell + everything it connects to)
```

```
Time:  O(rows × cols) -- every cell visited at most once
Space: O(rows × cols) -- recursion stack worst case
```

---

### 📜 The Scroll of the Mightiest Land

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔥 The DFS Burn -- Returns the Area Consumed

```cpp
int dfs(vector<vector<int>>& grid, int r, int c) {
```

The Oracle entered a cell and began burning outward --
but this time, she counted every cell she touched.

---

### 🛑 Base Case -- Out of Bounds or Water

```cpp
    int rows = grid.size();
    int cols = grid[0].size();

    if (r < 0 || r >= rows || c < 0 || c >= cols) return 0;
    if (grid[r][c] == 0) return 0;
```

**Out of bounds** → no land here. Return area 0.

**Water or already visited** → `0` means water or burned cell.
Return area 0.

> _"If you've fallen into the sea
> or stepped on ashes already burned --
> you contribute nothing to the island's size."_

---

### 🔥 Burn This Cell and Count It

```cpp
    grid[r][c] = 0;
```

Mark the cell as visited by sinking it to `0`.
This cell is now consumed -- it will never be counted again.

---

### 🧭 Spread and Accumulate Area

```cpp
    int area = 1;
```

This cell itself contributes 1 to the area.

```cpp
    area += dfs(grid, r - 1, c);  // up
    area += dfs(grid, r + 1, c);  // down
    area += dfs(grid, r, c - 1);  // left
    area += dfs(grid, r, c + 1);  // right
```

The fire spread in all four directions.
Each recursive call returned the area it consumed.
All areas were accumulated into `area`.

> _"This cell is 1.
> Plus everything reachable above.
> Plus everything reachable below.
> Plus everything reachable left.
> Plus everything reachable right.
> The sum is the total area of the island
> reachable from this starting cell."_

---

### 📐 Return the Total Area

```cpp
    return area;
}
```

The total area of the connected component
reachable from this cell was returned to the caller.

---

## 🏝️ The Main Ritual -- Walk, Burn, Track the Maximum

```cpp
int maxAreaOfIsland(vector<vector<int>>& grid) {
    int rows = grid.size();
    int cols = grid[0].size();
    int maxArea = 0;
```

`maxArea` tracked the largest island found so far.

---

### 🔁 Scan Every Cell

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
```

Every cell inspected, row by row.

---

### 🏝️ Found Land -- Burn and Measure

```cpp
            if (grid[r][c] == 1) {
                int area = dfs(grid, r, c);
                maxArea = max(maxArea, area);
            }
        }
    }
    return maxArea;
}
```

When unvisited land was found:

1. **`dfs(grid, r, c)`** -- burn the entire island, get its area.
2. **`max(maxArea, area)`** -- update the record if this island was the largest.

After the full scan, `maxArea` held the answer.

> _"Every island is burned and measured.
> Only the mightiest measurement survives."_

---

### 🎺 The Trial of the Mightiest Land

```cpp
int main() {
    vector<vector<int>> grid1 = {
        {0,0,1,0,0,0,0,1,0,0,0,0,0},
        {0,0,0,0,0,0,0,1,1,1,0,0,0},
        {0,1,1,0,1,0,0,0,0,0,0,0,0},
        {0,1,0,0,1,1,0,0,1,0,1,0,0},
        {0,1,0,0,1,1,0,0,1,1,1,0,0},
        {0,0,0,0,0,0,0,0,0,0,1,0,0},
        {0,0,0,0,0,0,0,1,1,1,0,0,0},
        {0,0,0,0,0,0,0,1,1,0,0,0,0}
    };
    cout << maxAreaOfIsland(grid1) << endl; // expected: 6

    vector<vector<int>> grid2 = {{0,0,0,0,0,0,0,0}};
    cout << maxAreaOfIsland(grid2) << endl; // expected: 0

    return 0;
}
```

---

**Trace for a simpler grid:**

```
Grid:
  1 1 0 0
  1 1 0 0
  0 0 1 1
  0 0 1 0
```

**Scan cell by cell:**

| Cell (r,c) | Value | Action                                          | area | maxArea |
| ---------- | ----- | ----------------------------------------------- | ---- | ------- |
| (0,0)      | 1     | DFS burns (0,0),(0,1),(1,0),(1,1)               | 4    | 4       |
| (0,1)      | 0     | Burned. Skip.                                   | --   | 4       |
| (1,0)-(1,1)| 0     | Burned. Skip.                                   | --   | 4       |
| (2,2)      | 1     | DFS burns (2,2),(2,3),(3,2)                     | 3    | 4       |
| (2,3)      | 0     | Burned. Skip.                                   | --   | 4       |
| (3,2)      | 0     | Burned. Skip.                                   | --   | 4       |

**Answer: 4** ✓

Island 1 (top-left 2×2 block) had area 4.
Island 2 (bottom-right L-shape) had area 3.
Maximum = 4.

---

**DFS call tree for island at (2,2):**

```
dfs(2,2) → grid[2][2]=0, area=1
  + dfs(1,2) → grid[1][2]=0 → return 0
  + dfs(3,2) → grid[3][2]=0, area=1
      + dfs(2,2) → already 0 → return 0
      + dfs(4,2) → out of bounds → return 0
      + dfs(3,1) → grid[3][1]=0 → return 0
      + dfs(3,3) → grid[3][3]=0 → return 0
      return 1
  + dfs(2,1) → grid[2][1]=0 → return 0
  + dfs(2,3) → grid[2][3]=0, area=1
      + all neighbors return 0
      return 1
  area = 1 + 0 + 1 + 0 + 1 = 3
  return 3
```

The DFS naturally accumulated the area as it unwound.

---

**Trace for all water `[[0,0,0]]`:**

No `1` found. `maxArea` stays 0.

**Answer: 0** ✓

---

**Trace for single cell `[[1]]`:**

DFS at (0,0): burns it, area = 1. All neighbors out of bounds.

**Answer: 1** ✓

---

## 🔍 The Key Difference from Number of Islands

| Number of Islands              | Max Area of Island              |
| ------------------------------ | ------------------------------- |
| `void dfs(...)` -- just burn   | `int dfs(...)` -- burn AND count|
| Count the number of DFS calls  | Track the max area returned     |
| `count++` per island           | `maxArea = max(maxArea, area)`  |

The DFS structure is identical.
The only change: DFS returns `int` instead of `void`,
and each cell contributes `1` to the running total.

---

## 🔄 BFS Alternative

Replace DFS with BFS using a queue:

```cpp
int bfs(vector<vector<int>>& grid, int r, int c) {
    int rows = grid.size(), cols = grid[0].size();
    queue<pair<int,int>> q;
    q.push({r, c});
    grid[r][c] = 0;
    int area = 0;

    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};

    while (!q.empty()) {
        auto [cr, cc] = q.front(); q.pop();
        area++;
        for (auto& d : dirs) {
            int nr = cr + d[0], nc = cc + d[1];
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && grid[nr][nc] == 1) {
                grid[nr][nc] = 0;
                q.push({nr, nc});
            }
        }
    }
    return area;
}
```

Each dequeued cell adds 1 to the area.
Same result, iterative instead of recursive.

---

### 🧠 Memory of the Mightiest Land Law

-   **Same as Number of Islands** but DFS returns `int` (area)
-   **DFS base case:** out of bounds or `0` → return 0
-   **DFS recursive case:** sink cell → `area = 1 + dfs(up) + dfs(down) + dfs(left) + dfs(right)`
-   **Main loop:** for each `1` found → `area = dfs(r,c)` → `maxArea = max(maxArea, area)`
-   **In-place marking:** `grid[r][c] = 0` to avoid revisiting
-   **BFS alternative:** queue-based, count each dequeued cell
-   **Time:** O(rows × cols)
-   **Space:** O(rows × cols) -- recursion stack or queue
-   **Edge cases:**
    -   All water → 0
    -   All land → rows × cols
    -   Single cell → 0 or 1
    -   Many small islands → max is 1

Thus is remembered the saga of **Max Area of Island**,
where the Oracle walked the grid of sea and land --
and every time she stepped on undiscovered land,
she lit a fire that consumed the entire island,
counting every cell it touched --
and when the fire died,
the area was measured against the record --
until the grid was fully walked
and the mightiest land mass was known. 🏝️👑✨
