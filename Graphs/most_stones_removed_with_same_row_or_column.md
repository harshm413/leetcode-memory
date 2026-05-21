## 🪨🗺️ _The Crumbling of Connected Stones: The Most Stones Removed Saga_

> \_"On an infinite 2D plane,
> stones were placed at integer coordinates.
> Each stone occupied a unique position.
>
> A stone could be **removed** if it shared
> a **row** or a **column** with at least one other stone
> that had NOT yet been removed.
>
> The Oracle was commanded:
>
> **'What is the MAXIMUM number of stones
> that can be removed?'**
>
> The Oracle thought deeply.
>
> If two stones share a row or column --
> they are **connected**.
> If stone A connects to stone B,
> and stone B connects to stone C --
> then A, B, C are all in the same **connected component**.
>
> Within a connected component of size `k`,
> we can remove `k - 1` stones.
> Why? Because we can always keep removing stones
> one by one (each shares a row/col with at least one remaining stone)
> until only ONE stone is left -- the last survivor.
>
> **Maximum removals = total stones - number of connected components.**
>
> Each component contributes `size - 1` removals.
> Sum across all components = `n - c`
> (where n = total stones, c = number of components).
>
> The question became:
> **How many connected components exist?**
>
> The Oracle used Union-Find --
> but with a clever twist.
> Instead of unioning stones directly (O(n²) to check all pairs),
> she unioned stones through their **rows and columns**.
>
> Two stones in the same row → union them.
> Two stones in the same column → union them.
>
> To avoid O(n²) pairwise checks,
> she treated rows and columns as **virtual nodes**
> and unioned each stone's row with its column.
>
> This way, all stones sharing a row or column
> were transitively connected through the virtual nodes."\_

---

This is the saga of **Most Stones Removed with Same Row or Column**.

Given `stones[i] = [xi, yi]` on a 2D plane:

-   A stone can be removed if it shares a row or column with another remaining stone.
-   Return the **maximum** number of stones that can be removed.

```
Input:  stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]
Output: 5

Input:  stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]
Output: 3

Input:  stones = [[0,0]]
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Connected Components

**Key insight:** if stones are connected (share row/column, directly or transitively),
they form a connected component. Within a component of size `k`,
we can remove `k - 1` stones (keep one survivor).

```
Maximum removals = total stones - number of connected components
                 = n - c
```

**Why can we always remove k-1 from a component of size k?**

Think of it like a tree. In a connected group,
we can always find an order to remove stones one by one
such that each removed stone still shares a row/col
with at least one remaining stone.
The last stone has no one to share with -- it stays.

---

## 🧠 The Trick -- Union Rows and Columns

**Naive approach:** for every pair of stones, check if they share a row or column.
O(n²) pairs. Works but slow.

**Smart approach:** treat each **row** and each **column** as a virtual node.
For each stone at `(r, c)`:
-   Union row `r` with column `c`.

All stones in the same row are connected through that row node.
All stones in the same column are connected through that column node.
Transitivity handles the rest.

**Problem:** row numbers and column numbers might overlap
(row 0 and column 0 are different things).
**Solution:** offset columns by a large number.
If rows go from 0 to 10000, store columns as `c + 10001`.

```
Time:  O(N × α(N)) ≈ O(N)
Space: O(max_coordinate)
```

---

### 📜 The Scroll of the Crumbling Stones

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🏗️ The Disjoint Set with Dynamic Nodes

```cpp
class DisjointSet {
    unordered_map<int, int> parent, rank;
public:
```

Using a **hash map** instead of a vector because
node IDs can be large (rows up to 10000, columns offset to 10001+).
Only nodes that actually appear get created.

---

### Find with Path Compression

```cpp
    int find(int node) {
        if (parent.find(node) == parent.end()) {
            parent[node] = node;
            rank[node] = 0;
        }
        if (parent[node] != node) {
            parent[node] = find(parent[node]);
        }
        return parent[node];
    }
```

If the node doesn't exist yet → create it as its own parent.
Then standard path compression.

This **lazy initialization** means we only create nodes
for rows and columns that actually have stones.

---

### Union by Rank

```cpp
    void unite(int u, int v) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU == rootV) return;

        if (rank[rootU] < rank[rootV]) swap(rootU, rootV);
        parent[rootV] = rootU;
        if (rank[rootU] == rank[rootV]) rank[rootU]++;
    }
};
```

Standard union by rank. Shorter tree under taller tree.

---

## 🪨 The Main Ritual

```cpp
int removeStones(vector<vector<int>>& stones) {
    DisjointSet ds;
```

---

### 🔗 Union Each Stone's Row with Its Column

```cpp
    for (auto& stone : stones) {
        int row = stone[0];
        int col = stone[1] + 10001;
        ds.unite(row, col);
    }
```

For each stone at position `(r, c)`:
-   Row node = `r` (values 0 to 10000).
-   Column node = `c + 10001` (values 10001 to 20001).

The offset ensures row 5 and column 5 are DIFFERENT nodes.

**What does this union achieve?**

All stones in row 3 will have their row node `3` unioned
with their respective column nodes.
If stone A is at (3, 5) and stone B is at (3, 8):
-   A unions row 3 with col 10006.
-   B unions row 3 with col 10009.
-   Now col 10006 and col 10009 are connected THROUGH row 3.
-   Stones A and B are in the same component.

If stone C is at (7, 5):
-   C unions row 7 with col 10006.
-   Col 10006 was already connected to row 3 (from stone A).
-   Now row 7 is connected to row 3 through col 10006.
-   Stones A, B, C are all in the same component.

> _"The row is the bridge between stones in the same row.
> The column is the bridge between stones in the same column.
> Through these bridges, distant stones become kin."_

---

### 🔢 Count the Number of Unique Components Among Stones

```cpp
    unordered_map<int, int> componentMap;
    for (auto& stone : stones) {
        int root = ds.find(stone[0]);
        componentMap[root] = 1;
    }
    int components = componentMap.size();
```

For each stone, find its root (component leader).
Count how many UNIQUE roots exist among all stones.

We only check `stone[0]` (the row node) because
the row and column of each stone are already unioned --
finding the root of either gives the same component.

---

### 🏁 The Answer

```cpp
    return stones.size() - components;
}
```

Maximum removals = total stones - number of components.

Each component keeps exactly one survivor.
Everything else can be removed.

> _"From each island of connected stones,
> all but one can crumble.
> The total fallen = total stones minus the survivors."_

---

### 🎺 The Trial of the Crumbling Stones

```cpp
int main() {
    vector<vector<int>> s1 = {{0,0},{0,1},{1,0},{1,2},{2,1},{2,2}};
    cout << removeStones(s1) << endl; // expected: 5

    vector<vector<int>> s2 = {{0,0},{0,2},{1,1},{2,0},{2,2}};
    cout << removeStones(s2) << endl; // expected: 3

    vector<vector<int>> s3 = {{0,0}};
    cout << removeStones(s3) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]:**

**Step 1 -- Union row with column for each stone:**

| Stone  | Row node | Col node (offset) | Union          |
| ------ | -------- | ------------------ | -------------- |
| (0,0)  | 0        | 10001              | unite(0, 10001)|
| (0,1)  | 0        | 10002              | unite(0, 10002)|
| (1,0)  | 1        | 10001              | unite(1, 10001)|
| (1,2)  | 1        | 10003              | unite(1, 10003)|
| (2,1)  | 2        | 10002              | unite(2, 10002)|
| (2,2)  | 2        | 10003              | unite(2, 10003)|

**Connections formed:**

- Stone (0,0): row 0 ↔ col 10001
- Stone (0,1): row 0 ↔ col 10002
- Stone (1,0): row 1 ↔ col 10001 → row 1 connects to row 0 (through col 10001)
- Stone (1,2): row 1 ↔ col 10003
- Stone (2,1): row 2 ↔ col 10002 → row 2 connects to row 0 (through col 10002)
- Stone (2,2): row 2 ↔ col 10003 → already connected (row 2 → col 10002 → row 0 → col 10001 → row 1 → col 10003)

**All 6 stones end up in ONE component.**

**Step 2 -- Count components:**

All stones have the same root → 1 component.

**Answer: 6 - 1 = 5** ✓

---

**Trace for stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]:**

| Stone  | Union              | Connection                    |
| ------ | ------------------ | ----------------------------- |
| (0,0)  | unite(0, 10001)    | row 0 ↔ col 0                |
| (0,2)  | unite(0, 10003)    | row 0 ↔ col 2                |
| (1,1)  | unite(1, 10002)    | row 1 ↔ col 1 (isolated)     |
| (2,0)  | unite(2, 10001)    | row 2 ↔ col 0 → connects to row 0 |
| (2,2)  | unite(2, 10003)    | row 2 ↔ col 2 → already connected |

**Components:**
-   Component 1: stones (0,0), (0,2), (2,0), (2,2) — connected through rows 0,2 and cols 0,2.
-   Component 2: stone (1,1) — isolated (row 1 and col 1 have no other stones).

2 components. **Answer: 5 - 2 = 3** ✓

---

**Trace for stones = [[0,0]]:**

1 stone. 1 component. **Answer: 1 - 1 = 0** ✓

Cannot remove the only stone (no other stone shares its row or column).

---

## 🔍 Why Union Row with Column (Not Stone with Stone)?

**Stone-to-stone approach (naive):**
For every pair of stones, check if they share a row or column.
O(n²) comparisons. For n = 1000, that's 1 million checks.

**Row-column virtual node approach:**
For each stone, one union operation (row ↔ column).
O(n) total unions. Much faster.

The virtual nodes (rows and columns) act as **hubs**.
All stones in row 5 connect to the hub "row 5".
Through that hub, they're all connected to each other.

---

## 🔍 Why the Offset?

Row 3 and column 3 are DIFFERENT concepts.
Without an offset, `unite(3, 3)` would be a self-union (useless).

With offset: row 3 = node `3`, column 3 = node `10004`.
Now `unite(3, 10004)` correctly connects them as separate entities.

The offset value (10001) must be larger than the maximum row value
to prevent any overlap between row nodes and column nodes.

---

## 🔍 Why HashMap Instead of Vector?

Node IDs range from 0 to ~20002 (rows 0-10000, columns 10001-20001).
A vector of size 20002 would work but wastes space
when only a few rows/columns actually have stones.

The hash map creates nodes **lazily** -- only for rows and columns
that actually appear. More memory-efficient for sparse inputs.

---

## 🔄 DFS Alternative

Build a graph where stones are nodes.
Two stones are connected if they share a row or column.
Count connected components with DFS.

```cpp
int removeStones(vector<vector<int>>& stones) {
    int n = stones.size();
    vector<vector<int>> graph(n);

    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (stones[i][0] == stones[j][0] || stones[i][1] == stones[j][1]) {
                graph[i].push_back(j);
                graph[j].push_back(i);
            }
        }
    }

    vector<bool> visited(n, false);
    int components = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            components++;
            // DFS to visit entire component
            stack<int> st;
            st.push(i);
            while (!st.empty()) {
                int node = st.top(); st.pop();
                if (visited[node]) continue;
                visited[node] = true;
                for (int nb : graph[node]) {
                    if (!visited[nb]) st.push(nb);
                }
            }
        }
    }
    return n - components;
}
```

This is O(n²) due to the pairwise edge building.
The Union-Find approach with virtual row/column nodes is O(n).

---

### 🧠 Memory of the Crumbling Stones Law

-   **Answer:** `total stones - number of connected components`
-   **Why?** Each component of size k keeps 1 survivor → removes k-1 stones
-   **Connection rule:** two stones are connected if they share a row OR column
-   **Union-Find with virtual nodes:**
    -   Row node = `row` (0 to 10000)
    -   Column node = `col + 10001` (offset to avoid collision)
    -   For each stone: `unite(row, col + 10001)`
-   **Count components:** find unique roots among all stones' row nodes
-   **Lazy initialization:** hash map creates nodes only when needed
-   **DFS alternative:** O(n²) pairwise graph. Union-Find is O(n).
-   **Time:** O(N × α(N)) ≈ O(N)
-   **Space:** O(N) for the hash map
-   **Edge cases:**
    -   Single stone → 0 (can't remove)
    -   All stones in same row → n-1 (one component)
    -   All stones isolated (no shared row/col) → 0 (n components, n-n=0)

Thus is remembered the saga of **Most Stones Removed**,
where the Oracle did not simulate removals one by one
but instead saw the stones as a graph --
connected through shared rows and columns --
and knew that from each connected island,
all but one stone could crumble away,
leaving exactly one survivor per component --
the maximum destruction measured simply as
total stones minus the number of islands. 🪨🗺️✨
