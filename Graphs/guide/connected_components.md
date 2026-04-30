# 🏘️🔗 The Complete Guide to Connected Components

> _"A graph is not always one piece.
> Sometimes it is scattered -- islands of nodes
> with no bridge between them.
> Each island is a connected component.
> Counting them, finding them, and working with them
> is one of the most fundamental graph skills."_

---

## Table of Contents

1. [What Is a Connected Component?](#1-what-is-a-connected-component)
2. [Finding Components with DFS](#2-finding-components-with-dfs)
3. [Finding Components with BFS](#3-finding-components-with-bfs)
4. [Finding Components with Union-Find](#4-finding-components-with-union-find)
5. [Connected Components in Grids](#5-connected-components-in-grids)
6. [Connected Components in Directed Graphs](#6-connected-components-in-directed-graphs)
7. [Common Patterns and Problems](#7-common-patterns-and-problems)

---

## 1. What Is a Connected Component?

In an **undirected** graph, a connected component is a group of nodes
where every node can reach every other node through some path.

```
Graph:
  0 -- 1 -- 2       3 -- 4       5

Component 1: {0, 1, 2}
Component 2: {3, 4}
Component 3: {5}

Number of connected components = 3
```

Node 0 can reach 2 (through 1). But node 0 cannot reach 3.
So {0,1,2} and {3,4} are separate components.
Node 5 is completely alone -- it's its own component.

**Key properties:**

-   Every node belongs to exactly ONE component.
-   Within a component, there's a path between any two nodes.
-   Between components, there is NO path.
-   A single isolated node is a component of size 1.

---

## 2. Finding Components with DFS

The most common approach. Same pattern as Number of Islands.

**The idea:**

```
Walk through every node.
When you find an unvisited node:
  - A new component is discovered.
  - DFS from that node to visit the entire component.
  - Every node reached belongs to this component.
```

### The DFS Function

```cpp
void dfs(vector<vector<int>>& graph, vector<bool>& visited, int node) {
```

Enter a node. Mark it. Explore all its unvisited neighbors.

---

```cpp
    visited[node] = true;
```

This node is now part of the current component.
It will never be visited again.

---

```cpp
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfs(graph, visited, neighbor);
        }
    }
}
```

For every unvisited neighbor -- recurse.
The recursion naturally explores the entire connected component.

---

### The Main Loop

```cpp
int countComponents(int n, vector<vector<int>>& edges) {
    vector<vector<int>> graph(n);
    for (auto& e : edges) {
        graph[e[0]].push_back(e[1]);
        graph[e[1]].push_back(e[0]);
    }
```

Build the adjacency list. Undirected → add both directions.

---

```cpp
    vector<bool> visited(n, false);
    int components = 0;
```

Every node starts unvisited. Component count starts at 0.

---

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
1. New component found → `components++`.
2. DFS visits the entire component.
3. Next unvisited node must be in a DIFFERENT component.

**Number of DFS calls = number of components.**

```
Time:  O(V + E)
Space: O(V + E) -- graph + visited + recursion stack
```

---

### Labeling Components (Which Node Belongs Where?)

Sometimes you need to know WHICH component each node belongs to.

```cpp
void dfs(vector<vector<int>>& graph, vector<int>& componentId,
         int node, int id) {
    componentId[node] = id;
    for (int neighbor : graph[node]) {
        if (componentId[neighbor] == -1) {
            dfs(graph, componentId, neighbor, id);
        }
    }
}
```

Instead of `visited[node] = true`, use `componentId[node] = id`.
`-1` means unvisited. Any other value = the component's label.

---

```cpp
vector<int> componentId(n, -1);
int id = 0;
for (int i = 0; i < n; i++) {
    if (componentId[i] == -1) {
        dfs(graph, componentId, i, id);
        id++;
    }
}
```

After this, `componentId[i]` tells you which component node `i` belongs to.
Nodes in the same component share the same ID.

---

### Getting Component Sizes

```cpp
vector<int> componentSize(id, 0);
for (int i = 0; i < n; i++) {
    componentSize[componentId[i]]++;
}
```

`componentSize[c]` = number of nodes in component `c`.

---

## 3. Finding Components with BFS

Replace DFS recursion with a BFS queue. Same result.

```cpp
void bfs(vector<vector<int>>& graph, vector<bool>& visited, int start) {
    queue<int> q;
    q.push(start);
    visited[start] = true;
```

Start BFS from the given node. Mark it visited immediately.

---

```cpp
    while (!q.empty()) {
        int node = q.front();
        q.pop();
```

Dequeue one node.

---

```cpp
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

For each unvisited neighbor: mark visited, enqueue.

The main loop is identical to DFS -- just call `bfs` instead of `dfs`.

**When to prefer BFS over DFS:**
-   Very deep graphs (DFS may stack overflow).
-   When you also need shortest paths or level information.

**When to prefer DFS:**
-   Simpler code (fewer lines).
-   Most graph problems don't need level information.

Both are O(V + E). Both find the same components.

---

## 4. Finding Components with Union-Find

No adjacency list needed. Just process edges.

### The Idea

```
Start with n components (every node is its own set).
For each edge (u, v): union the sets of u and v.
After all edges: count the remaining distinct sets.
```

### The Union-Find Class

```cpp
class UnionFind {
    vector<int> parent, rank;
    int components;
public:
    UnionFind(int n) : parent(n), rank(n, 0), components(n) {
        for (int i = 0; i < n; i++) parent[i] = i;
    }
```

Every node starts as its own parent. `n` components initially.

---

```cpp
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
```

Find the root. Path compression flattens the tree.

---

```cpp
    void unite(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return;
        if (rank[ra] < rank[rb]) swap(ra, rb);
        parent[rb] = ra;
        if (rank[ra] == rank[rb]) rank[ra]++;
        components--;
    }
```

Different roots → merge. Shorter tree under taller.
Same height → rank goes up by 1.
`components--` because two sets became one.

---

```cpp
    int getComponents() { return components; }
};
```

### Using It

```cpp
int countComponents(int n, vector<vector<int>>& edges) {
    UnionFind uf(n);
    for (auto& e : edges) {
        uf.unite(e[0], e[1]);
    }
    return uf.getComponents();
}
```

Process every edge. Return the remaining component count.

```
Time:  O(V + E × α(V)) ≈ O(V + E)
Space: O(V)
```

**When to prefer Union-Find:**
-   Input is an edge list (no adjacency list needed).
-   Dynamic connectivity (edges added over time).
-   Need to check "are u and v in the same component?" → `find(u) == find(v)`.

---

## 5. Connected Components in Grids

Grid problems are connected component problems in disguise.

**Number of Islands:** count components of `1`s.
**Max Area of Island:** find the largest component.
**Flood Fill:** change one component's color.

The grid IS the graph. No adjacency list needed.

```cpp
void dfs(vector<vector<int>>& grid, int r, int c) {
    int rows = grid.size(), cols = grid[0].size();
    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
    if (grid[r][c] == 0) return;

    grid[r][c] = 0;  // mark visited

    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

Same DFS pattern. Neighbors are the 4 adjacent cells.
`grid[r][c] = 0` serves as the visited marker (in-place).

```cpp
int numIslands(vector<vector<int>>& grid) {
    int count = 0;
    for (int r = 0; r < grid.size(); r++) {
        for (int c = 0; c < grid[0].size(); c++) {
            if (grid[r][c] == 1) {
                count++;
                dfs(grid, r, c);
            }
        }
    }
    return count;
}
```

Each DFS call = one component = one island.

---

## 5b. Connected Components in an Adjacency Matrix

When the graph is given as an `n × n` matrix
(like Number of Provinces / Friend Circles):

`isConnected[i][j] = 1` means node `i` and node `j` are connected.

No adjacency list needed. The matrix row IS the neighbor list.

```cpp
void dfs(vector<vector<int>>& matrix, vector<bool>& visited, int node) {
    visited[node] = true;
    for (int other = 0; other < (int)matrix.size(); other++) {
        if (matrix[node][other] == 1 && !visited[other]) {
            dfs(matrix, visited, other);
        }
    }
}
```

Scan row `node` for `1`s. Each `1` is a neighbor.
Recurse into unvisited neighbors.

```cpp
int countComponents(vector<vector<int>>& matrix) {
    int n = matrix.size();
    vector<bool> visited(n, false);
    int components = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            components++;
            dfs(matrix, visited, i);
        }
    }
    return components;
}
```

Same "walk and burn" pattern. The only difference:
neighbors come from scanning a matrix row (O(N) per node)
instead of an adjacency list (O(degree) per node).

**Total time: O(N²)** -- must scan the entire matrix.

---

## 6. Connected Components in Directed Graphs

In directed graphs, "connected" is more nuanced.

### Weakly Connected Components

Ignore edge directions. Treat the graph as undirected.
Use the same DFS/BFS/Union-Find as above.

Two nodes are weakly connected if there's a path
between them when you ignore arrow directions.

### Strongly Connected Components (SCCs)

Two nodes are **strongly connected** if there's a path
from A to B AND from B to A (following edge directions).

SCCs are found using specialized algorithms:
-   **Kosaraju's Algorithm** (two DFS passes).
-   **Tarjan's Algorithm** (single DFS with low-link values).

These are advanced topics beyond basic connected components.

For most problems, "connected components"
means the undirected/weakly-connected version.

---

## 7. Common Patterns and Problems

### Pattern: Count Components

Just count how many DFS/BFS calls are made,
or use Union-Find's component counter.

**Problems:** Number of Islands, Number of Connected Components,
Number of Provinces.

### Pattern: Largest Component

Track the size of each DFS/BFS traversal. Return the max.

```cpp
int size = dfs(graph, visited, i); // DFS returns count
maxSize = max(maxSize, size);
```

**Problems:** Max Area of Island, Largest Component Size by Common Factor.

### Pattern: Are Two Nodes in the Same Component?

Union-Find: `find(u) == find(v)`.
DFS: run DFS from u, check if v is visited.

**Problems:** Graph Valid Tree, Redundant Connection.

### Pattern: Merge Components

Union-Find naturally merges components as edges are added.

**Problems:** Accounts Merge, Redundant Connection, Minimum Cost to Connect All Points.

### Pattern: Component Labeling

Assign each node a component ID. Group nodes by ID.

```cpp
componentId[node] = id;
```

**Problems:** Accounts Merge, Similar String Groups.

---

## Quick Reference

**DFS component counting (the template you'll use most):**

```cpp
// Build graph
vector<vector<int>> graph(n);
for (auto& e : edges) {
    graph[e[0]].push_back(e[1]);
    graph[e[1]].push_back(e[0]);
}

// Count components
vector<bool> visited(n, false);
int components = 0;
for (int i = 0; i < n; i++) {
    if (!visited[i]) {
        components++;
        dfs(graph, visited, i);
    }
}
```

**Union-Find component counting:**

```cpp
UnionFind uf(n);
for (auto& e : edges) uf.unite(e[0], e[1]);
int components = uf.getComponents();
```

**Grid component counting:**

```cpp
int components = 0;
for (int r = 0; r < rows; r++)
    for (int c = 0; c < cols; c++)
        if (grid[r][c] == 1) {
            components++;
            dfs(grid, r, c);
        }
```

Three templates. Memorize them. They cover everything. 🏘️🔗
