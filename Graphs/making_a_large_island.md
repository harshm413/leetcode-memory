## 🏝️👑 _The One Flip That Unites Kingdoms: The Making a Large Island Saga_

> \_"In the Grid Kingdom,
> islands of `1`s were scattered across a sea of `0`s.
> Each island had a size -- the number of land cells it contained.
>
> The King issued a bold decree:
>
> **'You may change AT MOST ONE `0` to a `1`.
> What is the LARGEST island you can create?'**
>
> The Oracle thought carefully.
>
> If she flipped a `0` to `1`,
> that new land cell could potentially connect
> up to 4 neighboring islands into one massive island.
>
> The brute force: try flipping every `0`,
> run DFS to measure the resulting island.
> O(n² × n²) -- far too slow.
>
> The Oracle devised a two-pass strategy:
>
> **Pass 1 -- Label and measure every island.**
> DFS through the grid. Give each island a unique ID.
> Record the size of each island.
>
> **Pass 2 -- Try flipping every `0`.**
> For each `0` cell, look at its 4 neighbors.
> Collect the UNIQUE island IDs adjacent to it.
> Sum their sizes + 1 (the flipped cell itself).
> Track the maximum.
>
> Two passes. No redundant DFS.
> The largest possible island revealed."\_

---

This is the saga of **Making a Large Island**.

Given an `n × n` binary grid:
-   `1` = land, `0` = water.
-   Change **at most one** `0` to `1`.
-   Return the size of the **largest island**.

```
Input:  [[1,0],[0,1]]
Output: 3

Input:  [[1,1],[1,0]]
Output: 4

Input:  [[1,1],[1,1]]
Output: 4
```

---

## 🧠 The Oracle's Core Insight -- Label Islands, Then Try Flips

**Step 1: Label every island with a unique ID and record its size.**

DFS/BFS through the grid. Each connected component of `1`s
gets a unique label (2, 3, 4, ...) replacing the `1`s.
Store the size of each labeled island in a map.

**Step 2: For every `0` cell, simulate the flip.**

Look at the 4 neighbors. Collect the unique island IDs.
The resulting island size = sum of those islands' sizes + 1.

Use a **set** to avoid counting the same island twice
(a `0` cell might have two neighbors from the same island).

**Step 3: Handle the edge case where the grid is ALL land.**

If no `0` exists, the answer is `n × n` (the entire grid is one island).

```
Time:  O(n²) -- two passes over the grid
Space: O(n²) -- the labels + size map
```

---

### 📜 The Scroll of the United Kingdoms

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <unordered_set>
using namespace std;
```

---

## 🔥 Step One -- DFS to Label and Measure Islands

### The DFS Function

```cpp
int dfs(vector<vector<int>>& grid, int r, int c, int label) {
    int n = grid.size();
    if (r < 0 || r >= n || c < 0 || c >= n) return 0;
    if (grid[r][c] != 1) return 0;
```

Out of bounds → 0. Not an unlabeled land cell → 0.

Only process cells that are `1` (unlabeled land).
Cells already labeled (2, 3, 4...) or water (0) are skipped.

---

```cpp
    grid[r][c] = label;
```

Replace `1` with the island's unique label.
This marks it as visited AND records which island it belongs to.

---

```cpp
    int size = 1;
    size += dfs(grid, r - 1, c, label);
    size += dfs(grid, r + 1, c, label);
    size += dfs(grid, r, c - 1, label);
    size += dfs(grid, r, c + 1, label);
    return size;
}
```

Count this cell (1) + recursively count all connected land cells.
Return the total size of this island.

> _"The DFS paints every cell of the island
> with the same label -- like a flag claiming territory.
> And while painting, it counts every cell it touches."_

---

## 🏝️ The Main Ritual

```cpp
int largestIsland(vector<vector<int>>& grid) {
    int n = grid.size();
    unordered_map<int, int> islandSize;
    int label = 2;
```

`islandSize[id]` = number of cells in island with label `id`.
Labels start at 2 (because 0 = water, 1 = unlabeled land).

---

### 🗺️ Pass 1 -- Label All Islands

```cpp
    for (int r = 0; r < n; r++) {
        for (int c = 0; c < n; c++) {
            if (grid[r][c] == 1) {
                int size = dfs(grid, r, c, label);
                islandSize[label] = size;
                label++;
            }
        }
    }
```

Walk the grid. Every time an unlabeled `1` is found:
-   DFS to label the entire island and get its size.
-   Store the size in the map.
-   Increment the label for the next island.

After this pass, the grid looks like:
```
Before: 1 0 1      After: 2 0 3
        1 0 1              2 0 3
        0 1 1              0 3 3
```

Each island has a unique number. Water stays 0.

---

### 🔄 Pass 2 -- Try Flipping Every `0`

```cpp
    int maxSize = 0;
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

`maxSize` tracks the largest island achievable.

---

```cpp
    for (int r = 0; r < n; r++) {
        for (int c = 0; c < n; c++) {
            if (grid[r][c] == 0) {
```

For every water cell -- simulate flipping it to land.

---

### 🔍 Collect Unique Neighboring Islands

```cpp
                unordered_set<int> neighbors;
                for (auto& d : dirs) {
                    int nr = r + d[0];
                    int nc = c + d[1];
                    if (nr >= 0 && nr < n && nc >= 0 && nc < n
                        && grid[nr][nc] != 0) {
                        neighbors.insert(grid[nr][nc]);
                    }
                }
```

Check all 4 neighbors. If a neighbor is land (label ≠ 0),
add its island label to the set.

**Why a set?** A `0` cell might have two neighbors
from the SAME island (e.g., the island wraps around it).
The set ensures we count each island only once.

> _"The set guards against double-counting.
> If two neighbors belong to the same island,
> we must not add that island's size twice."_

---

### 📐 Sum the Sizes + 1

```cpp
                int total = 1;
                for (int id : neighbors) {
                    total += islandSize[id];
                }
                maxSize = max(maxSize, total);
            }
        }
    }
```

The flipped cell itself contributes 1.
Plus the size of every unique neighboring island.
This is the total island size if we flip this cell.

Track the maximum across all possible flips.

---

### 🛡️ Edge Case -- All Land (No `0` Exists)

```cpp
    if (maxSize == 0) return n * n;
    return maxSize;
}
```

If `maxSize` is still 0, no `0` cell was found.
The entire grid is already land → answer = `n × n`.

Otherwise return the maximum island size found.

> _"If the entire grid is already one island,
> no flip is needed. The answer is the grid itself."_

---

### 🎺 The Trial of the United Kingdoms

```cpp
int main() {
    vector<vector<int>> g1 = {{1,0},{0,1}};
    cout << largestIsland(g1) << endl; // expected: 3

    vector<vector<int>> g2 = {{1,1},{1,0}};
    cout << largestIsland(g2) << endl; // expected: 4

    vector<vector<int>> g3 = {{1,1},{1,1}};
    cout << largestIsland(g3) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for grid = [[1,0],[0,1]]:**

**Pass 1 -- Label islands:**

```
Original:     After labeling:
  1 0           2 0
  0 1           0 3

islandSize = {2: 1, 3: 1}
```

Two islands, each of size 1.

**Pass 2 -- Try flipping each `0`:**

**Cell (0,1):**
-   Neighbors: up OOB, down (1,1)=3, left (0,0)=2, right OOB.
-   Unique islands: {2, 3}.
-   Total = 1 + size[2] + size[3] = 1 + 1 + 1 = 3.

**Cell (1,0):**
-   Neighbors: up (0,0)=2, down OOB, left OOB, right (1,1)=3.
-   Unique islands: {2, 3}.
-   Total = 1 + 1 + 1 = 3.

**maxSize = 3.**

**Answer: 3** ✓

Flipping either (0,1) or (1,0) connects both islands → size 3.

---

**Full trace for grid = [[1,1],[1,0]]:**

**Pass 1:**

```
Original:     After labeling:
  1 1           2 2
  1 0           2 0

islandSize = {2: 3}
```

One island of size 3.

**Pass 2:**

**Cell (1,1):**
-   Neighbors: up (0,1)=2, left (1,0)=2.
-   Unique islands: {2}.
-   Total = 1 + size[2] = 1 + 3 = 4.

**maxSize = 4.**

**Answer: 4** ✓

Flipping (1,1) extends the existing island by 1.

---

**Trace for grid = [[1,1],[1,1]]:**

**Pass 1:** All cells labeled 2. islandSize = {2: 4}.

**Pass 2:** No `0` cells found. maxSize stays 0.

**Edge case:** return `n × n = 4`.

**Answer: 4** ✓

---

**Trace for a more complex grid:**

```
Grid:
  1 1 0 1 1
  1 0 0 0 1
  0 0 1 0 0
  1 0 0 0 1
  1 1 0 1 1
```

After labeling: 4 separate islands with various sizes.
Flipping the right `0` cell could connect 2 or 3 islands.
The set ensures each island is counted once even if
multiple neighbors belong to the same island.

---

## 🔍 Why Label Instead of DFS Per Flip?

**Brute force:** for each `0`, flip it, DFS to measure the island, flip back.
Each DFS is O(n²). With O(n²) zeros → O(n⁴) total. Way too slow.

**Label approach:** one DFS pass to label everything (O(n²)).
Then for each `0`, just look up sizes in O(1) per neighbor.
Total: O(n²). Optimal.

---

## 🔍 Why Labels Start at 2?

-   `0` = water.
-   `1` = unlabeled land (before Pass 1).
-   `2, 3, 4, ...` = labeled islands.

If we used `1` as a label, we couldn't distinguish
"unlabeled land" from "island with label 1."
Starting at 2 avoids this ambiguity.

---

## 🔍 Why the Set Is Critical

Consider:
```
  0 2
  2 2
```

Cell (0,0) has neighbors: right = 2, down = 2.
Both belong to island 2.

Without a set: total = 1 + size[2] + size[2] = 1 + 3 + 3 = 7. **WRONG.**
With a set: unique = {2}. total = 1 + size[2] = 1 + 3 = 4. **CORRECT.**

The set prevents double-counting the same island.

---

### 🧠 Memory of the United Kingdoms Law

-   **Pass 1:** DFS to label each island with unique ID (2, 3, 4...) + record sizes
-   **Pass 2:** for each `0` cell, collect unique neighbor island IDs (set), sum sizes + 1
-   **Answer:** max across all flips. Edge case: all land → n×n.
-   **Labels start at 2** (0=water, 1=unlabeled land)
-   **Set prevents double-counting** same island from multiple neighbors
-   **DFS labels in-place** (replaces 1 with island ID → serves as visited marker)
-   **Time:** O(n²) -- two passes over the grid
-   **Space:** O(n²) -- labels stored in grid + size map
-   **Edge cases:**
    -   All land → n×n (no flip needed)
    -   All water → 1 (flip one cell)
    -   Single cell connecting 4 different islands → sum all 4 + 1
    -   Two neighbors from same island → set handles it

Thus is remembered the saga of **Making a Large Island**,
where the Oracle did not blindly flip every zero and recount --
but instead labeled every island with a unique flag,
measured each one's territory,
then stood at every water cell and asked:
"If I rise here, which kingdoms do I unite?" --
collecting unique neighbors, summing their sizes,
adding one for herself --
until the greatest possible union was found. 🏝️👑✨
