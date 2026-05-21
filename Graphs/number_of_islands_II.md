## 🏝️⚡ _The Rising Islands: The Number of Islands II Saga_

> \_"In the Sea of Grids,
> the world began as all water --
> an empty `m × n` grid of zeros.
>
> Then, one by one, land cells rose from the sea.
> After each new land cell appeared,
> the Oracle was asked:
>
> **'How many islands exist NOW?'**
>
> An island was a group of land cells
> connected horizontally or vertically.
>
> The Oracle could not afford to run
> a full DFS/BFS after every addition --
> that would be O(positions × m × n). Too slow.
>
> She needed a **dynamic** solution --
> one that could handle additions incrementally.
>
> **Union-Find.**
>
> Each time a new land cell appeared:
> 1. Create it as a new island (components++).
> 2. Check its 4 neighbors.
> 3. If a neighbor is already land → union them (components--).
> 4. Record the current component count.
>
> Each addition was O(α) ≈ O(1).
> The entire sequence was O(positions × α).
> The dynamic graph was tamed."\_

---

This is the saga of **Number of Islands II**.

Given an `m × n` grid initially filled with water (`0`):

-   `positions[i] = [ri, ci]` -- turn cell `(ri, ci)` into land.
-   After each addition, return the number of islands.

Return an array of counts, one per addition.

```
Input:  m = 3, n = 3
        positions = [[0,0],[0,1],[1,2],[2,1]]
Output: [1, 1, 2, 3]

Input:  m = 1, n = 1
        positions = [[0,0]]
Output: [1]
```

---

## 🧠 The Oracle's Core Insight -- Incremental Union-Find

This is **Number of Islands** but **online** (dynamic).
Land cells are added one at a time. We need the count after each.

**Why not DFS/BFS each time?**
Each DFS is O(m×n). With `k` additions, total = O(k × m × n). Too slow.

**Union-Find handles dynamic connectivity in near O(1) per operation.**

For each new land cell:
1. **Create** it as a new component → `count++`.
2. **Check 4 neighbors** (up, down, left, right).
3. If a neighbor is **already land** AND in a **different component** → union them → `count--`.
4. Record `count`.

```
Time:  O(k × α(m×n)) ≈ O(k) where k = number of positions
Space: O(m × n)
```

---

### 📜 The Scroll of the Rising Islands

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏗️ The Disjoint Set for a 2D Grid

```cpp
class DisjointSet {
    vector<int> parent, rank;
public:
    DisjointSet(int n) : parent(n, -1), rank(n, 0) {}
```

`parent[i] = -1` means cell `i` is **water** (not yet created).
Once a cell becomes land, its parent is set to itself (or a root).

This is the key difference from standard Union-Find:
**-1 means "does not exist yet."**

---

### Find with Path Compression

```cpp
    int find(int node) {
        if (parent[node] != node) {
            parent[node] = find(parent[node]);
        }
        return parent[node];
    }
```

Standard path compression. Only called on nodes that exist (parent ≠ -1).

---

### Union by Rank -- Returns True If Merged

```cpp
    bool unite(int u, int v) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU == rootV) return false;

        if (rank[rootU] < rank[rootV]) swap(rootU, rootV);
        parent[rootV] = rootU;
        if (rank[rootU] == rank[rootV]) rank[rootU]++;
        return true;
    }
```

Returns `true` if two different components were merged.
Returns `false` if already in the same component.

---

### Check If a Cell Is Land

```cpp
    bool isLand(int node) {
        return parent[node] != -1;
    }
```

A cell is land if its parent has been initialized (not -1).

---

### Create a New Land Cell

```cpp
    void addLand(int node) {
        parent[node] = node;
    }
};
```

Set the cell's parent to itself -- it's now its own component.

---

## 🏝️ The Main Ritual

```cpp
vector<int> numIslands2(int m, int n, vector<vector<int>>& positions) {
    DisjointSet ds(m * n);
    vector<int> result;
    int count = 0;
```

Grid has `m × n` cells. All start as water (parent = -1).
`count` tracks the current number of islands.

---

### 🧭 The 4 Directions

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

Up, down, left, right.

---

### 🔁 Process Each Position

```cpp
    for (auto& pos : positions) {
        int r = pos[0];
        int c = pos[1];
        int id = r * n + c;
```

Convert 2D coordinate `(r, c)` to a 1D index: `id = r * n + c`.
This is the standard grid-to-index mapping.

---

### 🛑 Already Land? Skip (Duplicate Position)

```cpp
        if (ds.isLand(id)) {
            result.push_back(count);
            continue;
        }
```

If this cell was already turned into land in a previous step,
the count doesn't change. Just record and move on.

---

### 🏝️ Create New Island

```cpp
        ds.addLand(id);
        count++;
```

A new land cell rises from the sea.
It starts as its own island → `count++`.

> _"A new island is born.
> It stands alone in the sea --
> until it finds a neighbor."_

---

### 🔗 Check 4 Neighbors -- Union If Already Land

```cpp
        for (auto& d : dirs) {
            int nr = r + d[0];
            int nc = c + d[1];
            int neighborId = nr * n + nc;
```

Compute each neighbor's position and 1D index.

---

```cpp
            if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;
            if (!ds.isLand(neighborId)) continue;
```

Skip if out of bounds.
Skip if the neighbor is still water.

---

```cpp
            if (ds.unite(id, neighborId)) {
                count--;
            }
        }
```

If the neighbor IS land AND in a different component →
union them. Two islands merge into one → `count--`.

If they're already in the same component → `unite` returns false.
No change to count.

> _"The new island reaches out to its neighbors.
> Every neighbor that is already land
> and belongs to a different island --
> they merge. Two become one.
> The count drops for each successful union."_

---

### 📝 Record the Current Count

```cpp
        result.push_back(count);
    }
    return result;
}
```

After processing this position (creation + all possible merges),
record the current island count.

---

### 🎺 The Trial of the Rising Islands

```cpp
int main() {
    vector<vector<int>> pos1 = {{0,0},{0,1},{1,2},{2,1}};
    auto r1 = numIslands2(3, 3, pos1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 1 1 2 3

    vector<vector<int>> pos2 = {{0,0}};
    auto r2 = numIslands2(1, 1, pos2);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 1

    return 0;
}
```

---

**Full trace for m=3, n=3, positions=[[0,0],[0,1],[1,2],[2,1]]:**

```
Grid (3×3), all water initially.
1D mapping: (r,c) → r*3 + c

  (0,0)=0  (0,1)=1  (0,2)=2
  (1,0)=3  (1,1)=4  (1,2)=5
  (2,0)=6  (2,1)=7  (2,2)=8
```

**Position [0,0] (id=0):**
-   Create land at 0. count = 1.
-   Neighbors: up(-1,0) OOB, down(1,0) water, left(0,-1) OOB, right(0,1) water.
-   No merges.
-   **Result: [1]**

```
Grid:
  1 0 0
  0 0 0
  0 0 0
```

**Position [0,1] (id=1):**
-   Create land at 1. count = 2.
-   Neighbors: up OOB, down(1,1) water, left(0,0) **LAND!**
    -   unite(1, 0) → different roots → merge. count = 1.
-   right(0,2) water.
-   **Result: [1, 1]**

```
Grid:
  1 1 0
  0 0 0
  0 0 0
```

**Position [1,2] (id=5):**
-   Create land at 5. count = 2.
-   Neighbors: up(0,2) water, down(2,2) water, left(1,1) water, right OOB.
-   No merges.
-   **Result: [1, 1, 2]**

```
Grid:
  1 1 0
  0 0 1
  0 0 0
```

**Position [2,1] (id=7):**
-   Create land at 7. count = 3.
-   Neighbors: up(1,1) water, down OOB, left(2,0) water, right(2,2) water.
-   No merges.
-   **Result: [1, 1, 2, 3]**

```
Grid:
  1 1 0
  0 0 1
  0 1 0
```

**Answer: [1, 1, 2, 3]** ✓

---

**Trace for a merging scenario:**

```
m=2, n=2, positions = [[0,0],[1,1],[0,1],[1,0]]
```

| Position | Create | Neighbors (land) | Merges | count | Result |
| -------- | ------ | ----------------- | ------ | ----- | ------ |
| (0,0)    | count=1| none              | 0      | 1     | [1]    |
| (1,1)    | count=2| none              | 0      | 2     | [1,2]  |
| (0,1)    | count=3| (0,0)=land, (1,1)=land | 2 merges | 1 | [1,2,1] |
| (1,0)    | count=2| (0,0)=land, (1,1)=land | already same component | 1 | [1,2,1,1] |

At position (0,1): the new cell connects to (0,0) on the left AND (1,1) below.
Two merges happen → count goes from 3 to 1. All four cells become one island.

At position (1,0): neighbors (0,0) and (1,1) are already in the same component.
`unite` returns false for the second one. count stays 1.

---

## 🔍 Why Union-Find and Not DFS?

| DFS (recount every time)          | Union-Find (incremental)          |
| --------------------------------- | --------------------------------- |
| O(k × m × n) total               | O(k × α(m×n)) ≈ O(k) total      |
| Recomputes everything each step   | Only processes the new cell + neighbors |
| Simple but slow                   | Fast and elegant                  |

Union-Find is the natural tool for **dynamic connectivity** --
when the graph changes over time and you need answers after each change.

---

## 🔍 The 2D-to-1D Mapping

```
id = row * numCols + col
```

This maps every `(r, c)` to a unique integer in `[0, m*n)`.
Standard technique for using 1D arrays/Union-Find on 2D grids.

---

## 🔍 Handling Duplicate Positions

The problem may add the same position twice.
The `isLand` check handles this:

```cpp
if (ds.isLand(id)) {
    result.push_back(count);
    continue;
}
```

If already land → count doesn't change. Skip.

---

### 🧠 Memory of the Rising Islands Law

-   **Dynamic Number of Islands** → Union-Find (not DFS)
-   **For each new land cell:**
    1. Create it → `count++`
    2. Check 4 neighbors
    3. If neighbor is land AND different component → union → `count--`
    4. Record `count`
-   **parent[i] = -1** means water (cell not yet created)
-   **2D to 1D:** `id = r * n + c`
-   **Duplicate positions:** check `isLand` before processing
-   **A new cell can merge up to 4 separate islands** into one (count drops by up to 4... wait, max 3 merges since the cell itself is 1 and connects to at most 4 neighbors in 4 different components → count drops by up to 4 but started at count+1, so net can drop by 3)
-   **Time:** O(k × α(m×n)) ≈ O(k) per query
-   **Space:** O(m × n)
-   **Edge cases:**
    -   Duplicate position → count unchanged
    -   Single cell → 1
    -   All cells added → converges to standard Number of Islands answer
    -   New cell merges multiple islands at once → count drops by number of successful unions

Thus is remembered the saga of **Number of Islands II**,
where the Oracle did not recount the islands from scratch
after every new land cell rose from the sea --
but instead used Union-Find to track connectivity dynamically,
creating each new cell as its own island,
then merging it with any neighboring land --
so that after every addition,
the exact number of islands was known
in nearly constant time. 🏝️⚡✨
