## 🏝️🔥 _The Burning of the Archipelago: The Number of Islands Saga_

> \_"In the Sea of Grids,
> land masses lay scattered --
> clusters of `1`s surrounded by `0`s (water).
>
> Some islands were large, sprawling across many cells.
> Some were tiny -- a single cell alone.
>
> The Oracle was commanded:
>
> **'Count the number of distinct islands.'**
>
> An island was a group of `1`s connected
> **horizontally or vertically** (not diagonally).
>
> The Oracle devised the **Burning Ritual**:
>
> **Walk the grid cell by cell.
> When you find an unvisited land cell --
> a new island has been discovered.
> Increment the count.
> Then BURN the entire island --
> use DFS to visit every connected land cell,
> marking each as visited so it is never counted again.**
>
> Each DFS call consumed one entire island.
> The number of DFS calls = the number of islands.
>
> Walk. Discover. Burn. Count."\_

---

This is the saga of **Number of Islands**.

Given a 2D grid where:
-   `'1'` = land
-   `'0'` = water

Count the number of **islands** --
connected components of `'1'`s (horizontal/vertical adjacency).

```
Input:
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
Output: 1

Input:
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
Output: 3
```

---

## 🧠 The Oracle's Core Insight -- DFS Flood Fill

The problem is **counting connected components** in a grid graph.

Each land cell is a node.
Two land cells are connected if they are horizontally or vertically adjacent.
An island = one connected component.

The algorithm:

```
count = 0
For each cell (r, c) in the grid:
  If grid[r][c] == '1':
    count++                    (new island discovered)
    DFS(r, c)                  (burn the entire island)

DFS marks every connected '1' as visited
so it won't be counted as a new island later.
```

**How to mark visited?**
Option 1: Change `'1'` to `'0'` in-place (modifies input).
Option 2: Use a separate `visited[][]` array.

We'll use Option 1 -- simpler, no extra space.

```
Time:  O(rows × cols) -- every cell visited at most once
Space: O(rows × cols) -- recursion stack in worst case
```

---

### 📜 The Scroll of the Archipelago

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔥 The DFS Burn -- Consume an Entire Island

```cpp
void dfs(vector<vector<char>>& grid, int r, int c) {
```

The Oracle entered a land cell and began burning outward.

---

### 🛑 Base Case -- Stop If Out of Bounds or Water

```cpp
    int rows = grid.size();
    int cols = grid[0].size();

    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
    if (grid[r][c] == '0') return;
```

**Out of bounds** -- walked off the grid. Stop.

**Water or already visited** -- `'0'` means either water
or a cell we already burned. Stop.

These two checks are the **guard clauses** --
they prevent the DFS from going where it shouldn't.

> _"If you've fallen into the sea
> or stepped on ashes already burned --
> turn back."_

---

### 🔥 Burn This Cell

```cpp
    grid[r][c] = '0';
```

Mark the current cell as visited by changing it to `'0'`.

This is the **sink** -- the cell is consumed.
It will never be counted as land again.

> _"The flame touches the cell.
> It turns from land to ash.
> No future explorer will mistake it for undiscovered land."_

---

### 🧭 Spread to All Four Neighbors

```cpp
    dfs(grid, r - 1, c);  // up
    dfs(grid, r + 1, c);  // down
    dfs(grid, r, c - 1);  // left
    dfs(grid, r, c + 1);  // right
}
```

The fire spread in all four cardinal directions.

Each recursive call either:
-   Hit a guard clause and returned immediately (water/boundary/visited).
-   Found more land, burned it, and spread further.

The recursion naturally explored the entire connected component --
every land cell reachable from the starting cell was burned.

> _"The fire does not choose its path.
> It spreads to every neighbor that is land.
> When no more land remains -- the island is fully consumed."_

---

## 🏝️ The Main Ritual -- Walk, Discover, Burn, Count

```cpp
int numIslands(vector<vector<char>>& grid) {
    int rows = grid.size();
    if (rows == 0) return 0;
    int cols = grid[0].size();
    int count = 0;
```

The Oracle prepared to walk the entire grid.
`count` tracked the number of islands discovered.

---

### 🔁 Scan Every Cell

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
```

Every cell was inspected, row by row, column by column.

---

### 🏝️ New Island Discovered

```cpp
            if (grid[r][c] == '1') {
                count++;
                dfs(grid, r, c);
            }
        }
    }
    return count;
}
```

When an unvisited land cell was found:

1. **`count++`** -- a new island was discovered.
2. **`dfs(grid, r, c)`** -- burn the entire island.
   Every connected `'1'` was turned to `'0'`.

After the DFS, no cell of this island remained as `'1'`.
Future iterations of the loop would skip them all.

The next time the loop found a `'1'` --
it was guaranteed to be a DIFFERENT island.

> _"Each DFS call consumes one island completely.
> The number of DFS calls = the number of islands."_

---

### 🎺 The Trial of the Archipelago

```cpp
int main() {
    vector<vector<char>> grid1 = {
        {'1','1','1','1','0'},
        {'1','1','0','1','0'},
        {'1','1','0','0','0'},
        {'0','0','0','0','0'}
    };
    cout << numIslands(grid1) << endl; // expected: 1

    vector<vector<char>> grid2 = {
        {'1','1','0','0','0'},
        {'1','1','0','0','0'},
        {'0','0','1','0','0'},
        {'0','0','0','1','1'}
    };
    cout << numIslands(grid2) << endl; // expected: 3

    return 0;
}
```

---

**Full trace for grid2 (3 islands):**

```
Grid:
  1 1 0 0 0
  1 1 0 0 0
  0 0 1 0 0
  0 0 0 1 1
```

**Scan cell by cell:**

| Cell (r,c) | Value | Action                                    | count |
| ---------- | ----- | ----------------------------------------- | ----- |
| (0,0)      | '1'   | New island! count++. DFS burns (0,0),(0,1),(1,0),(1,1) | 1 |
| (0,1)      | '0'   | Already burned. Skip.                     | 1     |
| (0,2)-(0,4)| '0'   | Water. Skip.                              | 1     |
| (1,0)-(1,1)| '0'   | Already burned. Skip.                     | 1     |
| (1,2)-(1,4)| '0'   | Water. Skip.                              | 1     |
| (2,0)-(2,1)| '0'   | Water. Skip.                              | 1     |
| (2,2)      | '1'   | New island! count++. DFS burns (2,2) only | 2     |
| (2,3)-(2,4)| '0'   | Water. Skip.                              | 2     |
| (3,0)-(3,2)| '0'   | Water. Skip.                              | 2     |
| (3,3)      | '1'   | New island! count++. DFS burns (3,3),(3,4)| 3     |
| (3,4)      | '0'   | Already burned. Skip.                     | 3     |

**Answer: 3** ✓

Three DFS calls were made → three islands found.

**Island 1:** cells (0,0), (0,1), (1,0), (1,1) -- a 2×2 block.
**Island 2:** cell (2,2) -- a single cell.
**Island 3:** cells (3,3), (3,4) -- two cells in a row.

---

**Trace for grid1 (1 island):**

```
Grid:
  1 1 1 1 0
  1 1 0 1 0
  1 1 0 0 0
  0 0 0 0 0
```

The first `'1'` at (0,0) triggers DFS.
The fire spreads through the entire connected land mass:
(0,0)→(0,1)→(0,2)→(0,3)→(1,3)→(1,0)→(1,1)→(2,0)→(2,1).

All land cells are burned in one DFS call.
No more `'1'`s remain for the loop to find.

**Answer: 1** ✓

---

**Trace for `[['1']]` (single cell):**

(0,0) is `'1'`. count++. DFS burns it. No neighbors.

**Answer: 1** ✓

---

**Trace for `[['0']]` (all water):**

No `'1'` found. count stays 0.

**Answer: 0** ✓

---

## 🔄 BFS Alternative

Instead of DFS (recursion), use BFS (queue):

```cpp
void bfs(vector<vector<char>>& grid, int r, int c) {
    int rows = grid.size(), cols = grid[0].size();
    queue<pair<int,int>> q;
    q.push({r, c});
    grid[r][c] = '0';

    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};

    while (!q.empty()) {
        auto [cr, cc] = q.front(); q.pop();
        for (auto& d : dirs) {
            int nr = cr + d[0], nc = cc + d[1];
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && grid[nr][nc] == '1') {
                grid[nr][nc] = '0';
                q.push({nr, nc});
            }
        }
    }
}
```

BFS explores level by level (like ripples in water).
DFS explores depth-first (like a snake through the island).

Both visit every cell exactly once. Both are O(rows × cols).

**DFS risk:** deep recursion can cause stack overflow on huge grids.
**BFS advantage:** iterative, no stack overflow risk.
**DFS advantage:** simpler code, fewer lines.

For interviews, DFS is usually the first approach shown.
Mention BFS as an alternative to demonstrate breadth of knowledge.

---

## 🔍 Why Modify the Grid Instead of Using Visited?

Modifying `'1'` → `'0'` avoids allocating a `visited[][]` array.
This saves O(rows × cols) space.

But it **destroys the input**. If the caller needs the grid later,
use a separate `visited` array instead:

```cpp
vector<vector<bool>> visited(rows, vector<bool>(cols, false));
// In DFS: check visited[r][c] instead of grid[r][c] == '0'
// Mark visited[r][c] = true instead of grid[r][c] = '0'
```

In interviews, mention this tradeoff.
Most interviewers accept in-place modification.

---

## 🌐 The Connected Components Pattern

This problem is the **template** for all grid-based graph problems:

| Problem              | What changes?                          |
| -------------------- | -------------------------------------- |
| Number of Islands    | Count connected components             |
| Max Area of Island   | Track size during DFS, return max      |
| Island Perimeter     | No DFS needed (just counting)          |
| Surrounded Regions   | DFS from borders, then flip            |
| Pacific Atlantic     | DFS from both oceans, find intersection|
| Rotting Oranges      | Multi-source BFS                       |

Master this DFS flood-fill template
and every grid graph problem becomes a variation.

---

### 🧠 Memory of the Burning Archipelago Law

-   **Walk the grid** cell by cell
-   **When `'1'` found:** new island → `count++` → DFS to burn it
-   **DFS guard clauses:** out of bounds OR `'0'` (water/visited) → return
-   **DFS burn:** `grid[r][c] = '0'` → recurse to 4 neighbors (up/down/left/right)
-   **Number of DFS calls = number of islands**
-   **In-place marking** (`'1'` → `'0'`) avoids a visited array
-   **BFS alternative:** use a queue instead of recursion
-   **Time:** O(rows × cols) -- every cell visited at most once
-   **Space:** O(rows × cols) worst case -- recursion stack for DFS
-   **Edge cases:**
    -   All water → 0
    -   All land → 1
    -   Single cell → 0 or 1
    -   Diagonal neighbors do NOT connect islands

Thus is remembered the saga of **Number of Islands**,
where the Oracle walked the grid of sea and land --
and every time she stepped on undiscovered land,
she lit a fire that consumed the entire island,
spreading through DFS to every connected cell --
turning land to ash so it would never be counted twice --
until the grid was fully walked
and the number of fires lit
was the number of islands found. 🏝️🔥✨
