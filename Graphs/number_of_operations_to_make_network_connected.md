## 🖧🔗 _The Bridges Between Islands: The Number of Operations to Make Network Connected Saga_

> \_"In the Kingdom of Computers,
> `n` machines stood in a network --
> some connected by cables, some isolated.
>
> The Oracle was commanded:
>
> **'What is the MINIMUM number of cable moves
> needed to connect ALL computers?
> You may disconnect a cable from one pair
> and reconnect it between another pair.
> If it is impossible -- return -1.'**
>
> The Oracle studied the network.
>
> She saw **connected components** --
> groups of computers already linked together.
> If there were `c` components,
> she needed exactly `c - 1` cables
> to bridge them all into one network.
>
> But where would those cables come from?
>
> **Extra cables** -- redundant connections within a component.
> A component with `k` nodes needs only `k - 1` cables.
> Any cable beyond that is a **spare** --
> it can be removed without disconnecting anything.
>
> The Oracle realized:
>
> **If total cables ≥ n - 1** → it's possible.
> (A tree connecting n nodes needs exactly n-1 edges.)
>
> **Answer = number of components - 1.**
>
> Each spare cable can bridge one gap between components.
> We need `c - 1` bridges. As long as we have enough
> total cables (≥ n-1), we have enough spares.
>
> Count the components. Subtract one. Done."\_

---

This is the saga of **Number of Operations to Make Network Connected**.

Given:
-   `n` computers labeled `0` to `n-1`.
-   `connections[i] = [a, b]` -- a cable between computers `a` and `b`.

Return the **minimum number of operations** (cable moves)
to connect all computers. If impossible, return `-1`.

```
Input:  n = 4, connections = [[0,1],[0,2],[1,2]]
Output: 1

Input:  n = 6, connections = [[0,1],[0,2],[0,3],[1,2],[1,3]]
Output: 2

Input:  n = 6, connections = [[0,1],[0,2],[0,3],[1,2]]
Output: -1
```

---

## 🧠 The Oracle's Core Insight -- Components - 1

**Key observation 1: When is it impossible?**

To connect `n` nodes into a single tree, you need at least `n - 1` edges.
If `connections.size() < n - 1` → not enough cables → return `-1`.

**Key observation 2: What's the answer?**

If we have `c` connected components,
we need exactly `c - 1` operations to bridge them all.

Each operation takes a redundant cable from inside a component
and uses it to connect two separate components.

**We don't need to figure out WHICH cables to move.**
We just need to know: do we have enough cables total?
And how many components exist?

```
If connections.size() < n - 1 → return -1
Else → return (number of components) - 1
```

```
Time:  O(N + E) with Union-Find or DFS
Space: O(N)
```

---

### 📜 The Scroll of the Network Bridges

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏗️ The Union-Find Approach

### The Disjoint Set

```cpp
class DisjointSet {
    vector<int> parent, rank;
public:
    DisjointSet(int n) : parent(n), rank(n, 0) {
        for (int i = 0; i < n; i++) parent[i] = i;
    }
```

Every computer starts as its own parent -- `n` separate components.

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

Follow the chain to the root. Flatten along the way.

---

### Union by Rank

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
};
```

If both nodes share the same root → already connected → return `false`.
Otherwise merge them → return `true`.

The return value tells us whether this edge was useful (connected two components)
or redundant (both endpoints already in the same component).

---

## 🖧 The Main Ritual

```cpp
int makeConnected(int n, vector<vector<int>>& connections) {
```

---

### 🛑 Impossible Check -- Not Enough Cables

```cpp
    if ((int)connections.size() < n - 1) return -1;
```

A tree with `n` nodes needs exactly `n - 1` edges.
If we don't even have that many cables total,
it's impossible to connect everyone. Return `-1`.

> _"You cannot build a bridge without material.
> If the total cables are fewer than n-1,
> no rearrangement can save the network."_

---

### 🔗 Process All Connections with Union-Find

```cpp
    DisjointSet ds(n);
    int components = n;
```

Start with `n` components (every computer is its own island).

---

```cpp
    for (auto& conn : connections) {
        if (ds.unite(conn[0], conn[1])) {
            components--;
        }
    }
```

For each cable:
-   If it connects two different components → `unite` returns `true` → `components--`.
-   If both endpoints are already in the same component → redundant cable → components unchanged.

After processing all cables, `components` holds the number of separate groups.

> _"Every successful union merges two islands into one.
> The component count drops by one.
> Redundant cables change nothing --
> they are the spares we can redistribute."_

---

### 🏁 The Answer

```cpp
    return components - 1;
}
```

We need `components - 1` bridges to connect all groups into one.
Each bridge uses one spare cable.

We already verified we have enough total cables (the `-1` check at the start).
So we're guaranteed to have enough spares.

> _"If there are 3 islands, we need 2 bridges.
> If there are 5 islands, we need 4 bridges.
> The answer is always: components minus one."_

---

### 🎺 The Trial of the Network Bridges

```cpp
int main() {
    vector<vector<int>> c1 = {{0,1},{0,2},{1,2}};
    cout << makeConnected(4, c1) << endl; // expected: 1

    vector<vector<int>> c2 = {{0,1},{0,2},{0,3},{1,2},{1,3}};
    cout << makeConnected(6, c2) << endl; // expected: 2

    vector<vector<int>> c3 = {{0,1},{0,2},{0,3},{1,2}};
    cout << makeConnected(6, c3) << endl; // expected: -1

    return 0;
}
```

---

**Full trace for n=4, connections=[[0,1],[0,2],[1,2]]:**

```
4 computers: 0, 1, 2, 3
3 cables. n-1 = 3. 3 >= 3 → possible.
```

| Cable  | find(a) | find(b) | Same? | Action          | components |
| ------ | ------- | ------- | ----- | --------------- | ---------- |
| Start  | --      | --      | --    | --              | 4          |
| [0,1]  | 0       | 1       | No    | Union. comp--   | 3          |
| [0,2]  | 0       | 2       | No    | Union. comp--   | 2          |
| [1,2]  | 0       | 0       | Yes   | Redundant. Skip.| 2          |

Components = 2. Answer = 2 - 1 = **1** ✓

Computer 3 is isolated. One spare cable exists (the [1,2] cable is redundant).
Move it to connect computer 3 to the rest. One operation.

---

**Full trace for n=6, connections=[[0,1],[0,2],[0,3],[1,2],[1,3]]:**

```
6 computers: 0, 1, 2, 3, 4, 5
5 cables. n-1 = 5. 5 >= 5 → possible.
```

| Cable  | Same? | Action          | components |
| ------ | ----- | --------------- | ---------- |
| [0,1]  | No    | Union. comp--   | 5          |
| [0,2]  | No    | Union. comp--   | 4          |
| [0,3]  | No    | Union. comp--   | 3          |
| [1,2]  | Yes   | Redundant.      | 3          |
| [1,3]  | Yes   | Redundant.      | 3          |

Components = 3 (group {0,1,2,3}, isolated {4}, isolated {5}).
Answer = 3 - 1 = **2** ✓

Two spare cables exist. Use them to connect computers 4 and 5.

---

**Full trace for n=6, connections=[[0,1],[0,2],[0,3],[1,2]]:**

```
6 computers. 4 cables. n-1 = 5. 4 < 5 → IMPOSSIBLE.
```

**Answer: -1** ✓

Not enough cables to form a spanning tree. No rearrangement can help.

---

## 🔄 DFS Alternative

Instead of Union-Find, count components with DFS:

```cpp
int makeConnected(int n, vector<vector<int>>& connections) {
    if ((int)connections.size() < n - 1) return -1;

    vector<vector<int>> graph(n);
    for (auto& conn : connections) {
        graph[conn[0]].push_back(conn[1]);
        graph[conn[1]].push_back(conn[0]);
    }

    vector<bool> visited(n, false);
    int components = 0;

    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            components++;
            dfs(graph, visited, i);
        }
    }
    return components - 1;
}

void dfs(vector<vector<int>>& graph, vector<bool>& visited, int node) {
    visited[node] = true;
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfs(graph, visited, neighbor);
        }
    }
}
```

Build adjacency list → DFS to count components → answer = components - 1.

Same time complexity. Union-Find is slightly cleaner here
since we already have an edge list (no need to build adjacency list).

---

## 🔍 Why the Answer Is Simply Components - 1

Imagine 4 separate islands. To connect them all:
-   Bridge island 1 to island 2 → 3 groups remain.
-   Bridge one of those to island 3 → 2 groups remain.
-   Bridge to island 4 → 1 group. Done.

3 bridges for 4 islands. Always `c - 1`.

**Where do the spare cables come from?**

A component with `k` nodes and `e` edges has `e - (k-1)` spare cables.
(A tree needs `k-1` edges. Anything beyond that is redundant.)

Total spares across all components = `total_edges - (n - components)`.
We need `components - 1` spares.

The check `connections.size() >= n - 1` guarantees enough spares exist.
(If total edges ≥ n-1, then spares ≥ components - 1. Always.)

---

## 🔍 Why Not Track Which Cables to Move?

The problem asks for the MINIMUM NUMBER of operations --
not which specific cables to move.

The minimum is always `components - 1` regardless of which cables we choose.
Any redundant cable can be repurposed. The specific choice doesn't matter.

---

### 🧠 Memory of the Network Bridges Law

-   **Impossible check:** `connections.size() < n - 1` → return -1
-   **Answer:** number of connected components - 1
-   **Union-Find approach:**
    -   Start with `n` components
    -   For each edge: if `unite` succeeds → `components--`
    -   Return `components - 1`
-   **DFS approach:** build graph → count components with DFS → return components - 1
-   **Why components - 1?** Each bridge merges two groups. Need c-1 bridges for c groups.
-   **Why the impossible check works:** n nodes need n-1 edges minimum for a spanning tree
-   **Time:** O(N + E × α(N)) ≈ O(N + E) for Union-Find, O(N + E) for DFS
-   **Space:** O(N)
-   **Edge cases:**
    -   n = 1 → already connected → 0
    -   No connections → need n-1 operations (if n-1 cables exist... but they don't → -1)
    -   All already connected → 0 (1 component, answer = 0)
    -   Fully disconnected with enough cables → n-1 operations

Thus is remembered the saga of **Number of Operations to Make Network Connected**,
where the Oracle did not search for which cables to move
but simply counted the islands in the network --
knowing that each island beyond the first
needed exactly one bridge to join the mainland --
and as long as enough total cables existed,
the spare ones within each island
could always be redistributed
to connect the disconnected. 🖧🔗✨
