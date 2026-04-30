## 📚🔗 _The Ancestry of Every Scholar: The Course Schedule IV Saga_

> \_"The Oracle had learned to detect cycles (Course Schedule I)
> and produce orderings (Course Schedule II).
>
> Now the King posed a deeper question:
>
> **'Given a list of queries [u, v],
> for each query tell me:
> is course u a PREREQUISITE of course v?
> Not just a direct prerequisite --
> but possibly through a chain of dependencies.'**
>
> `u` is a prerequisite of `v` if there exists
> ANY path from `u` to `v` in the prerequisite graph.
>
> The Oracle needed to answer many queries efficiently.
> Running a DFS/BFS per query would be too slow.
>
> She devised a precomputation:
>
> **Build a reachability matrix.**
>
> `isReachable[i][j] = true` if there is a path from `i` to `j`.
>
> Process nodes in **reverse topological order** --
> leaves first, sources last.
> When processing node `u`, for every neighbor `v`:
> -   `v` is reachable from `u` (direct edge).
> -   Everything reachable from `v` is also reachable from `u`.
>
> **Why reverse?** Because `v` (a descendant) is processed BEFORE `u`.
> So `v`'s reachability row is already COMPLETE
> when `u` copies it.
>
> Then answer every query in O(1)."\_

---

This is the saga of **Course Schedule IV**.

Given:
-   `numCourses` courses labeled `0` to `n-1`.
-   `prerequisites` -- pairs `[a, b]` meaning `a` must come before `b`.
-   `queries` -- pairs `[u, v]` asking: is `u` a prerequisite of `v`?

Return a boolean array answering each query.

```
Input:  numCourses = 5, prerequisites = [[0,1],[1,2],[2,3],[3,4]]
        queries = [[0,4],[4,0],[1,3],[3,0]]
Output: [true, false, true, false]

Input:  numCourses = 2, prerequisites = [[1,0]], queries = [[0,1],[1,0]]
Output: [false, true]
```

---

## 🧠 The Oracle's Core Insight -- Reverse Topo Order + Reachability

**Naive approach:** for each query, BFS/DFS from `u` to check if `v` is reachable.
O(Q × (V + E)) -- too slow for many queries.

**Precomputation approach:**

```
1. Build graph + compute topological order (Kahn's).
2. Create boolean matrix isReachable[n][n].
3. Process nodes in REVERSE topological order.
   For each node u and each neighbor v:
     - isReachable[u][v] = true  (direct edge)
     - Copy v's entire reachability row to u (transitivity)
4. Answer each query in O(1).
```

**Why reverse topological order?**

In topo order: `[0, 1, 2, 3, 4]` for a chain `0→1→2→3→4`.

If we process forward (0 first), when we look at 0's neighbor 1,
node 1 hasn't been processed yet -- its reachability row is empty.
We'd miss `0 → 1 → 2 → 3 → 4`.

If we process reverse (4 first, then 3, 2, 1, 0):
when we process node 1, node 2's row already has `{3: true, 4: true}`.
So we correctly copy: `isReachable[1][2] = true`, plus `isReachable[1][3] = true`,
`isReachable[1][4] = true`.

**Descendants are processed before ancestors.**
Their reachability is complete when ancestors need it.

```
Time:  O(V² + V×E + Q)
Space: O(V²)
```

---

### 📜 The Scroll of the Ancestry Web

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏗️ Build the Graph and In-Degree Array

```cpp
vector<bool> checkIfPrerequisite(int numCourses,
    vector<vector<int>>& prerequisites,
    vector<vector<int>>& queries) {

    int n = numCourses;
    vector<vector<int>> graph(n);
    vector<int> inDegree(n, 0);
```

Same setup as Course Schedule I and II.

---

```cpp
    for (auto& p : prerequisites) {
        int a = p[0];
        int b = p[1];
        graph[a].push_back(b);
        inDegree[b]++;
    }
```

For each `[a, b]` (a is prerequisite of b):
edge from `a → b`. `inDegree[b]++`.

> _"The arrow points from prerequisite to dependent.
> 'I come before you.'"_

---

## 📥 Kahn's Algorithm -- Compute Topological Order

```cpp
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) {
            q.push(i);
        }
    }
```

Enqueue all nodes with no prerequisites.

---

```cpp
    vector<int> topoOrder;

    while (!q.empty()) {
        int node = q.front();
        q.pop();
        topoOrder.push_back(node);

        for (int next : graph[node]) {
            inDegree[next]--;
            if (inDegree[next] == 0) {
                q.push(next);
            }
        }
    }
```

Standard Kahn's BFS. `topoOrder` holds the result.

---

## 🗺️ Create the Reachability Matrix

```cpp
    vector<vector<bool>> isReachable(n, vector<bool>(n, false));
```

`isReachable[i][j] = true` means there is a path from `i` to `j`.
All entries start `false`.

---

## 🔗 Propagate Reachability in REVERSE Topological Order

```cpp
    for (int i = topoOrder.size() - 1; i >= 0; i--) {
        int u = topoOrder[i];
```

**Reverse iteration** -- process the LAST node in topo order first.

In a chain `0→1→2→3→4`, topo order is `[0,1,2,3,4]`.
Reverse processes: `4, 3, 2, 1, 0`.

Leaves (nodes with no outgoing edges) are processed first.
Sources (nodes with no incoming edges) are processed last.

> _"Process the deepest descendants first.
> Their reachability is trivial -- they reach nothing (or little).
> Then work backward toward the sources,
> copying each descendant's complete reachability."_

---

### 🔁 For Each Neighbor of u

```cpp
        for (int v : graph[u]) {
```

`v` is a direct neighbor of `u` (edge `u → v`).

Since we're in reverse topo order,
`v` comes AFTER `u` in topo order,
which means `v` was processed BEFORE `u` in our reverse loop.

**`v`'s reachability row is already complete.**

---

### ✅ Direct Reachability

```cpp
            isReachable[u][v] = true;
```

`u` can directly reach `v`.

---

### 🔗 Transitive Reachability -- Copy v's Row to u

```cpp
            for (int w = 0; w < n; w++) {
                if (isReachable[v][w]) {
                    isReachable[u][w] = true;
                }
            }
        }
    }
```

Everything `v` can reach, `u` can also reach through `v`.

Because `v` was processed first (reverse topo order),
`isReachable[v][w]` is COMPLETE -- it includes
all transitive reachability through `v`'s descendants.

> _"Everything my descendant can reach,
> I can reach through them.
> Their ancestry is complete.
> I inherit it fully."_

---

## 📋 Answer Each Query in O(1)

```cpp
    vector<bool> result;
    for (auto& query : queries) {
        result.push_back(isReachable[query[0]][query[1]]);
    }
    return result;
}
```

Each query `[u, v]`: is `u` a prerequisite of `v`?
One matrix lookup. O(1).

---

### 🎺 The Trial of the Ancestry Web

```cpp
int main() {
    vector<vector<int>> p1 = {{0,1},{1,2},{2,3},{3,4}};
    vector<vector<int>> q1 = {{0,4},{4,0},{1,3},{3,0}};
    auto r1 = checkIfPrerequisite(5, p1, q1);
    for (bool b : r1) cout << b << " "; cout << endl;
    // expected: 1 0 1 0 (true, false, true, false)

    vector<vector<int>> p2 = {{1,0}};
    vector<vector<int>> q2 = {{0,1},{1,0}};
    auto r2 = checkIfPrerequisite(2, p2, q2);
    for (bool b : r2) cout << b << " "; cout << endl;
    // expected: 0 1 (false, true)

    return 0;
}
```

---

**Full trace for chain 0→1→2→3→4:**

```
Graph: 0→[1], 1→[2], 2→[3], 3→[4], 4→[]
Topo order: [0, 1, 2, 3, 4]
Reverse processing: 4, 3, 2, 1, 0
```

**Process u=4:** no neighbors. Nothing.

**Process u=3:** neighbor 4.
-   `isReachable[3][4] = true`.
-   v=4's row: all false. No transitive copy.

**Process u=2:** neighbor 3.
-   `isReachable[2][3] = true`.
-   v=3's row: `isReachable[3][4] = true` → copy → `isReachable[2][4] = true`.

**Process u=1:** neighbor 2.
-   `isReachable[1][2] = true`.
-   v=2's row: `{3: true, 4: true}` → copy → `isReachable[1][3] = true`, `isReachable[1][4] = true`.

**Process u=0:** neighbor 1.
-   `isReachable[0][1] = true`.
-   v=1's row: `{2: true, 3: true, 4: true}` → copy → `isReachable[0][2] = true`, `isReachable[0][3] = true`, `isReachable[0][4] = true`.

**Final matrix:**

```
     0     1     2     3     4
0: [  -   true  true  true  true ]
1: [  -     -   true  true  true ]
2: [  -     -     -   true  true ]
3: [  -     -     -     -   true ]
4: [  -     -     -     -     -  ]
```

Query [0,4]: `true` ✓ (0 is prerequisite of 4 through chain)
Query [4,0]: `false` ✓ (4 is NOT a prerequisite of 0)
Query [1,3]: `true` ✓ (1→2→3)
Query [3,0]: `false` ✓ (3 cannot reach 0)

**Answer: [true, false, true, false]** ✓

---

**Trace for diamond graph:**

```
Prerequisites: [[0,1],[0,2],[1,3],[2,3]]
Graph: 0→[1,2], 1→[3], 2→[3]
Topo order: [0, 1, 2, 3] (or [0, 2, 1, 3])
Reverse: 3, 2, 1, 0
```

Process 3: no neighbors.
Process 2: neighbor 3. `isReachable[2][3] = true`.
Process 1: neighbor 3. `isReachable[1][3] = true`.
Process 0: neighbor 1. `isReachable[0][1] = true`. Copy 1's row: `isReachable[0][3] = true`.
           neighbor 2. `isReachable[0][2] = true`. Copy 2's row: `isReachable[0][3]` already true.

Query [0,3]: `true` ✓ (through either 1 or 2)

---

## 🔍 Why Forward Topo Order FAILS

Chain: `0→1→2→3→4`. Forward topo: process 0 first.

Process u=0: neighbor 1. `isReachable[0][1] = true`.
v=1's row: **all false** (1 hasn't been processed yet!).
No transitive copy. `isReachable[0][2]` stays false. **WRONG.**

The problem: when processing 0, node 1's descendants (2,3,4)
haven't been discovered yet. The copy gets nothing.

**Reverse topo order fixes this** by ensuring descendants
are fully computed before their ancestors need them.

---

## 🔄 Alternative -- Floyd-Warshall

```cpp
for (auto& p : prerequisites) isReachable[p[0]][p[1]] = true;

for (int k = 0; k < n; k++)
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (isReachable[i][k] && isReachable[k][j])
                isReachable[i][j] = true;
```

O(V³) but dead simple. No topological sort needed.
For small `n` (≤ 100), perfectly fine.

---

### 🧠 Memory of the Ancestry Web Law

-   **Precompute reachability matrix** `isReachable[i][j]`
-   **Kahn's Algorithm** to get topological order
-   **Process in REVERSE topo order** -- descendants before ancestors
-   **For each node u, for each neighbor v:**
    -   `isReachable[u][v] = true` (direct)
    -   Copy `v`'s entire row to `u` (transitive)
-   **Reverse order ensures** `v`'s row is complete when `u` reads it
-   **Answer queries in O(1):** `isReachable[query[0]][query[1]]`
-   **Floyd-Warshall alternative:** O(V³), no topo sort needed
-   **Time:** O(V² + Q)
-   **Space:** O(V²)
-   **Edge cases:**
    -   No prerequisites → all queries false
    -   Chain → full transitive reachability
    -   Diamond → multiple paths, same result

Thus is remembered the saga of **Course Schedule IV**,
where the Oracle precomputed the full ancestry of every course --
processing nodes in reverse topological order,
letting each descendant's complete reachability
flow upward to its ancestors --
so that every query could be answered
with a single glance at the matrix. 📚🔗✨
