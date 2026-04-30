## 🏘️🔗 _The Scattered Villages: The Number of Connected Components Saga_

> \_"In the Kingdom of Nodes,
> villages were scattered across the land.
> Some were connected by roads.
> Some stood isolated -- no road leading to them.
>
> A **connected component** was a group of villages
> where you could travel between ANY two
> by following roads.
>
> The Oracle was commanded:
>
> **'How many separate groups of connected villages exist?'**
>
> The Oracle recognized this as the same problem
> as Number of Islands -- but on a general graph
> instead of a grid.
>
> **Walk through every node.
> When you find an unvisited node --
> a new component has been discovered.
> DFS/BFS to visit every node in that component.
> Increment the count.**
>
> The number of DFS/BFS calls = the number of components.
>
> She also knew a second weapon:
>
> **Union-Find (Disjoint Set Union).**
>
> Start with every node as its own component.
> For each edge, UNION the two endpoints.
> The number of remaining distinct sets = the answer.
>
> Two approaches. Same answer."\_

---

This is the saga of **Number of Connected Components in an Undirected Graph**.

Given `n` nodes labeled `0` to `n-1`
and a list of undirected `edges`:

-   Return the number of **connected components**.

```
Input:  n = 5, edges = [[0,1],[1,2],[3,4]]
Output: 2   (component {0,1,2} and component {3,4})

Input:  n = 5, edges = [[0,1],[1,2],[2,3],[3,4]]
Output: 1   (all connected)

Input:  n = 5, edges = []
Output: 5   (every node is its own component)
```

---

## 🧠 Approach One -- DFS

The same "walk and burn" pattern as Number of Islands:

```
For each node 0 to n-1:
  If not visited:
    components++
    DFS to visit all nodes in this component
```

```
Time:  O(V + E)
Space: O(V + E) -- adjacency list + visited array + recursion stack
```

---

### 📜 The Scroll of the Scattered Villages (DFS)

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏗️ Build the Adjacency List

```cpp
int countComponents_dfs(int n, vector<vector<int>>& edges) {
    vector<vector<int>> graph(n);
```

An adjacency list for `n` nodes. Each entry holds the neighbors.

---

```cpp
    for (auto& e : edges) {
        graph[e[0]].push_back(e[1]);
        graph[e[1]].push_back(e[0]);
    }
```

The graph is **undirected** -- each edge adds connections in BOTH directions.

> _"If village A has a road to village B,
> then village B also has a road to village A.
> Both sides know about the connection."_

---

## 🗺️ Prepare the Visited Array

```cpp
    vector<bool> visited(n, false);
    int components = 0;
```

`visited[i]` tracked whether node `i` had been explored.
`components` counted the number of separate groups.

---

## 🔁 Walk Every Node

```cpp
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            components++;
            dfs(graph, visited, i);
        }
    }
    return components;
}
```

For each unvisited node:
1. **`components++`** -- a new group discovered.
2. **`dfs(...)`** -- explore the entire component, marking all its nodes visited.

The next unvisited node found must belong to a DIFFERENT component.

---

## 🔥 The DFS -- Explore One Component

```cpp
void dfs(vector<vector<int>>& graph, vector<bool>& visited, int node) {
    visited[node] = true;
```

Mark this node as visited. It belongs to the current component.

---

```cpp
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfs(graph, visited, neighbor);
        }
    }
}
```

Visit every unvisited neighbor. Recurse into them.
The recursion naturally explores the entire connected component.

> _"The fire spreads to every village reachable by road.
> When it dies out -- one component is fully explored."_

---

### 🎺 The Trial (DFS)

```cpp
int main() {
    vector<vector<int>> e1 = {{0,1},{1,2},{3,4}};
    cout << countComponents_dfs(5, e1) << endl; // expected: 2

    vector<vector<int>> e2 = {{0,1},{1,2},{2,3},{3,4}};
    cout << countComponents_dfs(5, e2) << endl; // expected: 1

    vector<vector<int>> e3 = {};
    cout << countComponents_dfs(5, e3) << endl; // expected: 5

    return 0;
}
```

---

**Full trace for n=5, edges=[[0,1],[1,2],[3,4]]:**

```
Graph:
  0 → [1]
  1 → [0, 2]
  2 → [1]
  3 → [4]
  4 → [3]
```

| Node i | Visited? | Action                              | components |
| ------ | -------- | ----------------------------------- | ---------- |
| 0      | No       | components++. DFS visits 0, 1, 2.   | 1          |
| 1      | Yes      | Skip.                               | 1          |
| 2      | Yes      | Skip.                               | 1          |
| 3      | No       | components++. DFS visits 3, 4.      | 2          |
| 4      | Yes      | Skip.                               | 2          |

**Answer: 2** ✓

Component 1: {0, 1, 2}. Component 2: {3, 4}.

---

**Trace for n=5, edges=[]:**

No edges. Every node is isolated.
Each node triggers a new DFS (which visits only itself).

**Answer: 5** ✓ -- five isolated villages.

---

## 🧠 Approach Two -- Union-Find (Disjoint Set Union)

Union-Find is an alternative that avoids building an adjacency list.

**Idea:**
-   Start with `n` components (every node is its own parent).
-   For each edge `[a, b]`: UNION the sets containing `a` and `b`.
-   Count the remaining distinct sets.

---

### 🏗️ The Union-Find Structure

```cpp
class UnionFind {
    vector<int> parent;
    vector<int> rank;
    int components;
```

-   `parent[i]` -- the parent of node `i` in the set tree.
    If `parent[i] == i`, node `i` is the root of its set.
-   `rank[i]` -- approximate depth of the tree rooted at `i`.
    Used for **union by rank** to keep trees balanced.
-   `components` -- number of distinct sets remaining.

---

### 🏁 Initialize -- Every Node Is Its Own Set

```cpp
public:
    UnionFind(int n) : parent(n), rank(n, 0), components(n) {
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
```

Every node started as its own parent -- `n` separate sets.

---

### 🔍 Find -- With Path Compression

```cpp
    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }
```

Find the **root** of the set containing `x`.

**Path compression:** during the search,
every node along the path is directly connected to the root.
This flattens the tree, making future finds nearly O(1).

> _"Who is your leader?
> Follow the chain of parents until you find the root.
> And while you're at it -- shortcut everyone
> directly to the root for next time."_

---

### 🔗 Union -- Merge Two Sets

```cpp
    void unite(int a, int b) {
        int rootA = find(a);
        int rootB = find(b);
```

Find the roots of both nodes.

---

```cpp
        if (rootA == rootB) return;
```

If they share the same root -- they're already in the same set.
Nothing to do.

---

```cpp
        if (rank[rootA] < rank[rootB]) swap(rootA, rootB);
        parent[rootB] = rootA;
        if (rank[rootA] == rank[rootB]) rank[rootA]++;
        components--;
    }
```

**Union by rank:** attach the shorter tree under the taller tree.
This keeps the tree balanced.

If both trees have the same rank -- the merged tree grows by 1.

**`components--`** -- two sets merged into one. One fewer component.

> _"Two villages join forces.
> The smaller group bows to the larger.
> One component disappears."_

---

### 📊 Get the Component Count

```cpp
    int getComponents() {
        return components;
    }
};
```

The number of remaining distinct sets.

---

## 🏘️ The Main Ritual (Union-Find)

```cpp
int countComponents_uf(int n, vector<vector<int>>& edges) {
    UnionFind uf(n);

    for (auto& e : edges) {
        uf.unite(e[0], e[1]);
    }

    return uf.getComponents();
}
```

Start with `n` components.
For each edge, union the two endpoints.
Return the remaining component count.

> _"Every edge merges two villages.
> When all edges are processed,
> the number of surviving groups is the answer."_

---

### 🎺 The Trial (Union-Find)

**Trace for n=5, edges=[[0,1],[1,2],[3,4]]:**

| Edge  | find(a) | find(b) | Same root? | Action          | components |
| ----- | ------- | ------- | ---------- | --------------- | ---------- |
| Start | --      | --      | --         | --              | 5          |
| [0,1] | 0       | 1       | No         | Union 0 and 1   | 4          |
| [1,2] | 0       | 2       | No         | Union 0 and 2   | 3          |
| [3,4] | 3       | 4       | No         | Union 3 and 4   | 2          |

**Answer: 2** ✓

---

**Trace for n=5, edges=[[0,1],[1,2],[2,3],[3,4]]:**

| Edge  | Action          | components |
| ----- | --------------- | ---------- |
| [0,1] | Union 0,1       | 4          |
| [1,2] | Union {0,1},2   | 3          |
| [2,3] | Union {0,1,2},3 | 2          |
| [3,4] | Union {0,1,2,3},4 | 1        |

**Answer: 1** ✓ -- all connected.

---

## 🔍 DFS vs Union-Find -- When to Choose Which

| DFS                               | Union-Find                        |
| --------------------------------- | --------------------------------- |
| Needs adjacency list              | No adjacency list needed          |
| O(V + E) time                     | O(V + E × α(V)) ≈ O(V + E)      |
| O(V + E) space                    | O(V) space                        |
| Recursive (stack overflow risk)   | Iterative                         |
| Familiar pattern                  | Requires Union-Find implementation|
| Better for grid problems          | Better for edge-list problems     |

`α(V)` is the inverse Ackermann function -- effectively O(1).

Both are equally valid. Union-Find is more elegant
when you're given an edge list directly.
DFS is more natural when you already have an adjacency list.

---

## 🌐 Where Connected Components Appear

| Problem                          | Variation                          |
| -------------------------------- | ---------------------------------- |
| Number of Islands                | Grid-based connected components    |
| Connected Components (this)      | General graph connected components |
| Accounts Merge                   | Union-Find on string groups        |
| Redundant Connection             | Find the edge that creates a cycle |
| Graph Valid Tree                  | n-1 edges + 1 component = tree     |

---

### 🧠 Memory of the Scattered Villages Law

**DFS approach:**
-   Build adjacency list (undirected → add both directions)
-   For each unvisited node → components++ → DFS to visit entire component
-   Number of DFS calls = number of components

**Union-Find approach:**
-   Initialize n components (each node is its own set)
-   For each edge → union the two endpoints → components--
-   Return remaining component count
-   Path compression + union by rank → nearly O(1) per operation

-   **Time:** O(V + E) for both
-   **Space:** O(V + E) for DFS, O(V) for Union-Find
-   **Edge cases:**
    -   No edges → n components (every node isolated)
    -   Fully connected → 1 component
    -   Single node → 1 component

Thus is remembered the saga of **Connected Components**,
where the Oracle counted the scattered villages --
either by walking the land with DFS,
lighting a fire in each new village
and letting it spread to every connected neighbor --
or by wielding Union-Find,
merging villages edge by edge
until only the distinct groups remained. 🏘️🔗✨
