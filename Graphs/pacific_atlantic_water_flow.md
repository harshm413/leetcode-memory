## 🌊🌏 _The Two Oceans and the Blessed Peaks: The Pacific Atlantic Water Flow Saga_

> \_"The Kingdom's island was bordered by two great oceans:
>
> **The Pacific** touched the top edge and the left edge.
> **The Atlantic** touched the bottom edge and the right edge.
>
> Rain fell on every cell of the island.
> Water flowed downhill -- from a cell to any neighbor
> with **equal or lower** height.
>
> The Oracle was commanded:
>
> **'Find every cell from which water can flow
> to BOTH the Pacific AND the Atlantic.'**
>
> The naive approach: for each cell,
> DFS/BFS outward to see if it reaches both oceans.
> O(N² × M²) -- far too slow.
>
> The Oracle flipped the problem:
>
> **Instead of flowing water DOWNHILL from each cell,
> flow water UPHILL from each ocean.**
>
> Start DFS from every Pacific border cell.
> Mark every cell reachable by flowing UPHILL.
> These are cells that can drain to the Pacific.
>
> Start DFS from every Atlantic border cell.
> Mark every cell reachable by flowing UPHILL.
> These are cells that can drain to the Atlantic.
>
> **The answer = cells marked by BOTH oceans.**
>
> Two DFS passes. One intersection.
> The blessed peaks revealed."\_

---

This is the saga of **Pacific Atlantic Water Flow**.

Given an `m × n` matrix `heights`:

-   Pacific ocean touches the **top** and **left** edges.
-   Atlantic ocean touches the **bottom** and **right** edges.
-   Water flows to neighbors with **equal or lower** height.
-   Return all cells that can reach **both** oceans.

```
Input:
  [[1,2,2,3,5],
   [3,2,3,4,4],
   [2,4,5,3,1],
   [6,7,1,4,5],
   [5,1,1,2,4]]

Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```

---

## 🧠 The Oracle's Core Insight -- Reverse the Flow

**Forward approach (slow):**
For each cell → DFS downhill → check if it reaches both oceans.
Each DFS is O(M×N). Total: O(M²×N²).

**Reverse approach (fast):**
From each ocean → DFS UPHILL → mark reachable cells.
Two DFS passes, each O(M×N). Total: O(M×N).

**Why uphill?**

If water flows downhill from cell A to the ocean,
then flowing uphill from the ocean reaches cell A.
The reachability is the same -- just reversed.

By starting from the ocean borders and going uphill,
we find ALL cells that can drain to that ocean in one pass.

```
Time:  O(M × N) -- two DFS passes, each visits every cell at most once
Space: O(M × N) -- two visited matrices
```

---

### 📜 The Scroll of the Two Oceans

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🌊 The DFS -- Flow Uphill from the Ocean

```cpp
void dfs(vector<vector<int>>& heights,
         vector<vector<bool>>& reachable,
         int r, int c) {
```

The Oracle entered a cell and tried to flow **uphill**
to all neighbors -- marking every cell she could reach.

`reachable` was the visited matrix for one specific ocean.

---

### 🏔️ Mark This Cell as Reachable

```cpp
    int rows = heights.size();
    int cols = heights[0].size();

    reachable[r][c] = true;
```

This cell could drain to the ocean we started from.
Mark it.

---

### 🧭 Define the Four Directions

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

Up, down, left, right.

---

### 🔁 Try to Flow Uphill to Each Neighbor

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

### 🔍 Already Visited?

```cpp
        if (reachable[nr][nc]) {
            continue;
        }
```

If this neighbor was already marked reachable --
no need to visit again. Skip.

---

### 📈 Can Water Flow Uphill to the Neighbor?

```cpp
        if (heights[nr][nc] < heights[r][c]) {
            continue;
        }
```

**This is the reversed flow condition.**

Normal flow: water goes from high to low (`neighbor <= current`).
Reversed flow: we go from low to high (`neighbor >= current`).

If the neighbor is **lower** than the current cell --
water cannot flow uphill to it. Skip.

If the neighbor is **equal or higher** --
water CAN flow from the neighbor down to us,
which means the neighbor can also drain to this ocean.

> _"We are walking uphill from the ocean.
> If the neighbor is at least as high as us --
> water could flow from them down to us,
> so they too can reach the ocean."_

---

### 🔥 Recurse -- Continue Flowing Uphill

```cpp
        dfs(heights, reachable, nr, nc);
    }
}
```

The neighbor was valid, unvisited, and at least as high.
Recurse into it -- continue the uphill exploration.

---

## 🌏 The Main Ritual -- Two Oceans, One Intersection

```cpp
vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
    int rows = heights.size();
    int cols = heights[0].size();
```

The Oracle measured the island.

---

### 🗺️ Create Two Reachability Matrices

```cpp
    vector<vector<bool>> pacific(rows, vector<bool>(cols, false));
    vector<vector<bool>> atlantic(rows, vector<bool>(cols, false));
```

Two separate matrices:

-   `pacific[r][c] = true` means cell (r,c) can drain to the Pacific.
-   `atlantic[r][c] = true` means cell (r,c) can drain to the Atlantic.

Both started as all `false` -- no cell proven reachable yet.

---

### 🌊 DFS from Pacific Borders (Top Edge and Left Edge)

```cpp
    for (int c = 0; c < cols; c++) {
        dfs(heights, pacific, 0, c);
    }
```

The **top edge** (row 0) borders the Pacific.
DFS uphill from every cell in the top row.

---

```cpp
    for (int r = 0; r < rows; r++) {
        dfs(heights, pacific, r, 0);
    }
```

The **left edge** (column 0) borders the Pacific.
DFS uphill from every cell in the left column.

> _"The Pacific touches the top and the left.
> From every border cell, the Oracle flows uphill,
> marking every cell that can drain down to the Pacific."_

---

### 🌊 DFS from Atlantic Borders (Bottom Edge and Right Edge)

```cpp
    for (int c = 0; c < cols; c++) {
        dfs(heights, atlantic, rows - 1, c);
    }
```

The **bottom edge** (last row) borders the Atlantic.
DFS uphill from every cell in the bottom row.

---

```cpp
    for (int r = 0; r < rows; r++) {
        dfs(heights, atlantic, r, cols - 1);
    }
```

The **right edge** (last column) borders the Atlantic.
DFS uphill from every cell in the right column.

> _"The Atlantic touches the bottom and the right.
> The same uphill flood, marking every cell
> that can drain down to the Atlantic."_

---

### 🏔️ Find the Intersection -- Cells That Reach Both

```cpp
    vector<vector<int>> result;

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (pacific[r][c] && atlantic[r][c]) {
                result.push_back({r, c});
            }
        }
    }
    return result;
}
```

The Oracle walked every cell one final time.

If a cell was marked reachable by **both** oceans --
it was a blessed peak. Water from it could flow to both.

These cells were collected and returned.

> _"Only the cells blessed by both oceans
> make the final list.
> Reachable by the Pacific AND the Atlantic --
> water from these peaks flows to both edges of the world."_

---

### 🎺 The Trial of the Two Oceans

```cpp
int main() {
    vector<vector<int>> heights = {
        {1,2,2,3,5},
        {3,2,3,4,4},
        {2,4,5,3,1},
        {6,7,1,4,5},
        {5,1,1,2,4}
    };

    auto result = pacificAtlantic(heights);
    for (auto& cell : result) {
        cout << "[" << cell[0] << "," << cell[1] << "] ";
    }
    cout << endl;
    // expected: [0,4] [1,3] [1,4] [2,2] [3,0] [3,1] [4,0]
    return 0;
}
```

---

**Visualization of the grid:**

```
Heights:
  1  2  2  3  5
  3  2  3  4  4
  2  4  5  3  1
  6  7  1  4  5
  5  1  1  2  4

Pacific borders: top row + left column
Atlantic borders: bottom row + right column
```

**Pacific reachability (after DFS from top + left):**

```
  P  P  P  P  P
  P  P  P  P  P
  P  P  P  .  .
  P  P  .  .  .
  P  .  .  .  .
```

Cells marked `P` can drain to the Pacific.

**Atlantic reachability (after DFS from bottom + right):**

```
  .  .  .  .  A
  .  .  .  A  A
  .  .  A  .  A
  A  A  .  A  A
  A  A  A  A  A
```

Cells marked `A` can drain to the Atlantic.

**Intersection (both P and A):**

```
  .  .  .  .  ★
  .  .  .  ★  ★
  .  .  ★  .  .
  ★  ★  .  .  .
  ★  .  .  .  .
```

Stars = cells that reach both oceans.
`[0,4], [1,3], [1,4], [2,2], [3,0], [3,1], [4,0]` ✓

---

**Trace for a simple 2×2 grid:**

```
Heights:
  1  1
  1  1
```

Pacific borders: (0,0), (0,1), (1,0).
Atlantic borders: (1,0), (1,1), (0,1).

All cells are equal height → DFS reaches everywhere from every border.
Pacific = all cells. Atlantic = all cells.
Intersection = all cells.

**Answer: [[0,0],[0,1],[1,0],[1,1]]** ✓

---

**Trace for a 1×1 grid `[[5]]`:**

Single cell borders both oceans.
Pacific = true. Atlantic = true.

**Answer: [[0,0]]** ✓

---

## 🔍 Why Reverse the Flow?

**Forward (from each cell to ocean):**
-   For each of M×N cells, DFS to check if it reaches Pacific.
-   For each of M×N cells, DFS to check if it reaches Atlantic.
-   Each DFS is O(M×N).
-   Total: O(M²×N²). Way too slow.

**Reverse (from ocean to cells):**
-   DFS from ~M+N Pacific border cells. Total work: O(M×N).
-   DFS from ~M+N Atlantic border cells. Total work: O(M×N).
-   Total: O(M×N). Optimal.

The reverse approach does the same work
but shares it across all cells instead of repeating it.

---

## 🔄 BFS Alternative

Replace DFS with BFS -- enqueue all border cells of one ocean,
BFS uphill, mark reachable. Same logic, iterative.

```cpp
void bfs(vector<vector<int>>& heights,
         vector<vector<bool>>& reachable,
         queue<pair<int,int>>& q) {
    int rows = heights.size(), cols = heights[0].size();
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};

    while (!q.empty()) {
        auto [r, c] = q.front(); q.pop();
        for (auto& d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && !reachable[nr][nc]
                && heights[nr][nc] >= heights[r][c]) {
                reachable[nr][nc] = true;
                q.push({nr, nc});
            }
        }
    }
}
```

Enqueue all Pacific border cells → BFS → mark pacific matrix.
Enqueue all Atlantic border cells → BFS → mark atlantic matrix.
Same O(M×N) complexity. No recursion stack risk.

---

### 🧠 Memory of the Two Oceans Law

-   **Reverse the flow:** DFS UPHILL from ocean borders, not downhill from cells
-   **Two separate DFS passes:**
    -   Pacific: DFS from top edge + left edge
    -   Atlantic: DFS from bottom edge + right edge
-   **Uphill condition:** `heights[neighbor] >= heights[current]`
-   **Two boolean matrices:** `pacific[][]` and `atlantic[][]`
-   **Answer:** cells where `pacific[r][c] && atlantic[r][c]`
-   **DFS guard clauses:** out of bounds, already visited, neighbor is lower
-   **BFS alternative:** enqueue all border cells, BFS uphill
-   **Time:** O(M × N) -- two passes, each visits every cell at most once
-   **Space:** O(M × N) -- two boolean matrices + recursion stack
-   **Edge cases:**
    -   1×1 grid → that cell reaches both oceans
    -   All equal heights → every cell reaches both
    -   Strictly decreasing from corner → only corner cells

Thus is remembered the saga of **Pacific Atlantic Water Flow**,
where the Oracle did not chase water downhill from every cell
but instead reversed the flow --
climbing uphill from every ocean border,
marking every cell the Pacific could reach,
marking every cell the Atlantic could reach --
and where both marks overlapped,
the blessed peaks stood revealed --
cells from which water could flow
to both edges of the world. 🌊🌏✨
