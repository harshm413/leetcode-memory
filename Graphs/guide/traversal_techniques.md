# 🧭🔥 The Complete Guide to Graph Traversal Techniques

> _"A graph is useless if you cannot walk it.
> Traversal is the act of visiting every node --
> systematically, without missing any, without looping forever.
> Two ancient techniques exist: DFS and BFS.
> Every graph algorithm is built on one of them."_

---

## Table of Contents

1. [DFS -- Depth-First Search](#1-dfs----depth-first-search)
2. [BFS -- Breadth-First Search](#2-bfs----breadth-first-search)
3. [DFS vs BFS -- When to Use Which](#3-dfs-vs-bfs----when-to-use-which)
4. [Traversal on Grids](#4-traversal-on-grids)
5. [Multi-Source BFS](#5-multi-source-bfs)
6. [Topological Sort (BFS -- Kahn's)](#6-topological-sort-bfs----kahns)
7. [Topological Sort (DFS -- Reverse Post-Order)](#7-topological-sort-dfs----reverse-post-order)
8. [Cycle Detection](#8-cycle-detection)
9. [Quick Reference Templates](#9-quick-reference-templates)

---

## 1. DFS -- Depth-First Search

DFS goes as **deep** as possible before backtracking.
It follows one path all the way to a dead end,
then retraces its steps and tries the next path.

Think of it as exploring a maze by always turning left
until you hit a wall, then backing up.

### Recursive DFS on a Graph

```cpp
void dfs(vector<vector<int>>& graph, vector<bool>& visited, int node) {
```

Enter a node. The journey begins.

---

```cpp
    visited[node] = true;
```

Mark this node as visited.
Without this, cycles would cause infinite recursion.

---

```cpp
    for (int neighbor : graph[node]) {
```

Look at every neighbor of this node.

---

```cpp
        if (!visited[neighbor]) {
            dfs(graph, visited, neighbor);
        }
    }
}
```

If the neighbor hasn't been visited -- recurse into it.
Go deeper. Always deeper. Until there's nowhere left to go.

When all neighbors are visited -- the function returns.
This is the **backtrack**. The recursion unwinds one level
and tries the next unvisited neighbor.

---

### Iterative DFS (Using a Stack)

```cpp
void dfs_iterative(vector<vector<int>>& graph, vector<bool>& visited, int start) {
    stack<int> st;
    st.push(start);
```

Push the starting node onto an explicit stack.

---

```cpp
    while (!st.empty()) {
        int node = st.top();
        st.pop();
```

Pop the top node. This is the node we're visiting now.

---

```cpp
        if (visited[node]) continue;
        visited[node] = true;
```

If already visited -- skip. This can happen because
a node might be pushed multiple times by different neighbors.

---

```cpp
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                st.push(neighbor);
            }
        }
    }
}
```

Push all unvisited neighbors onto the stack.
The stack ensures we go deep first --
the last pushed neighbor is explored first (LIFO).

**When to use iterative DFS:**
-   Very deep graphs where recursion would stack overflow.
-   When you need explicit control over the traversal order.

**Recursive vs Iterative DFS:**
Both visit the same nodes. The order may differ slightly
(iterative processes neighbors in reverse order unless you reverse the push).
For most problems, recursive DFS is simpler and preferred.

---

### DFS Traversal Order

```
Graph:
  0 -- 1 -- 3
  |    |
  2    4

DFS from 0: visits 0, 1, 3, 4, 2  (goes deep into 1's branch first)
```

The exact order depends on the adjacency list order.
DFS always fully explores one branch before moving to the next.

---

### Time and Space

```
Time:  O(V + E) -- every node and edge visited once
Space: O(V) -- visited array + recursion stack (or explicit stack)
```

---

## 2. BFS -- Breadth-First Search

BFS goes **wide** before going deep.
It visits all neighbors first, then all neighbors' neighbors, and so on.

Think of it as ripples spreading outward from a stone dropped in water.

### BFS on a Graph

```cpp
void bfs(vector<vector<int>>& graph, vector<bool>& visited, int start) {
    queue<int> q;
    q.push(start);
    visited[start] = true;
```

Push the starting node into a queue. Mark it visited immediately.

**Mark visited WHEN ENQUEUING, not when dequeuing.**
This prevents the same node from being enqueued multiple times.

---

```cpp
    while (!q.empty()) {
        int node = q.front();
        q.pop();
```

Dequeue the front node. Process it.

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

The queue ensures we process nodes in order of distance from the start.
All distance-1 nodes are processed before any distance-2 nodes.

---

### BFS Traversal Order

```
Graph:
  0 -- 1 -- 3
  |    |
  2    4

BFS from 0: visits 0, 1, 2, 3, 4  (all distance-1 first, then distance-2)
```

Level 0: {0}
Level 1: {1, 2} (neighbors of 0)
Level 2: {3, 4} (neighbors of 1)

BFS naturally discovers nodes in order of their distance from the start.

---

### Level-by-Level BFS (Counting Levels)

When you need to know the distance or count levels:

```cpp
int level = 0;
while (!q.empty()) {
    int size = q.size();
```

`size` captures how many nodes are in the CURRENT level.

---

```cpp
    for (int i = 0; i < size; i++) {
        int node = q.front();
        q.pop();
        // process node at this level
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
    level++;
}
```

Process exactly `size` nodes (one full level).
Then increment the level counter.
Nodes enqueued during this loop belong to the NEXT level.

This is used in:
-   Rotting Oranges (level = minutes).
-   Word Ladder (level = transformations).
-   Shortest path in unweighted graph (level = distance).

---

### Time and Space

```
Time:  O(V + E) -- same as DFS
Space: O(V) -- visited array + queue
```

---

## 3. DFS vs BFS -- When to Use Which

### Use DFS When:

-   **Connected components** -- count or label them.
-   **Cycle detection** -- three-color marking in directed graphs.
-   **Topological sort** -- reverse post-order.
-   **Path existence** -- "can I reach B from A?"
-   **Backtracking** -- explore all possibilities (permutations, combinations).
-   **Flood fill** -- mark all connected cells.

DFS is simpler to code (recursive) and uses less memory
when the graph is wide but not deep.

### Use BFS When:

-   **Shortest path** in unweighted graph -- BFS guarantees it.
-   **Level-order traversal** -- process nodes by distance.
-   **Multi-source shortest path** -- Rotting Oranges, Walls and Gates.
-   **Minimum steps/moves** -- Open the Lock, Word Ladder.

BFS is the ONLY choice when you need shortest paths
in an unweighted graph. DFS cannot guarantee shortest paths.

### The Golden Rule

> **Need shortest path or minimum steps? → BFS.**
> **Everything else? → DFS (simpler).**

---

## 4. Traversal on Grids

Grids are implicit graphs. No adjacency list needed.

### DFS on a Grid

```cpp
void dfs(vector<vector<int>>& grid, int r, int c) {
    int rows = grid.size(), cols = grid[0].size();
```

---

```cpp
    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
```

Out of bounds -- stop.

---

```cpp
    if (grid[r][c] == 0) return;
```

Water or already visited -- stop.

---

```cpp
    grid[r][c] = 0;
```

Mark visited (in-place).

---

```cpp
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

Recurse to all 4 neighbors.

### BFS on a Grid

```cpp
void bfs(vector<vector<int>>& grid, int r, int c) {
    int rows = grid.size(), cols = grid[0].size();
    queue<pair<int,int>> q;
    q.push({r, c});
    grid[r][c] = 0;

    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};

    while (!q.empty()) {
        auto [cr, cc] = q.front(); q.pop();
        for (auto& d : dirs) {
            int nr = cr + d[0], nc = cc + d[1];
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && grid[nr][nc] == 1) {
                grid[nr][nc] = 0;
                q.push({nr, nc});
            }
        }
    }
}
```

Same logic, iterative with a queue.

---

## 5. Multi-Source BFS

Start BFS from MULTIPLE sources simultaneously.

```cpp
queue<pair<int,int>> q;

// Enqueue ALL sources
for (int r = 0; r < rows; r++)
    for (int c = 0; c < cols; c++)
        if (grid[r][c] == SOURCE)
            q.push({r, c});

// BFS outward from all sources at once
while (!q.empty()) {
    auto [r, c] = q.front(); q.pop();
    for (auto& d : dirs) {
        int nr = r + d[0], nc = c + d[1];
        if (valid(nr, nc) && unvisited(nr, nc)) {
            mark(nr, nc);
            q.push({nr, nc});
        }
    }
}
```

All sources start at distance 0.
The BFS wavefront expands uniformly from all of them.
The first wave to reach a cell = shortest distance from ANY source.

**Used in:**
-   Rotting Oranges (sources = rotten oranges).
-   Walls and Gates (sources = gates).
-   01 Matrix (sources = all 0-cells).

---

## 6. Topological Sort (BFS -- Kahn's)

For **directed acyclic graphs (DAGs)** only.
Produces an ordering where every edge `u → v` has `u` before `v`.

```cpp
vector<int> topoSort(int n, vector<vector<int>>& graph) {
    vector<int> inDegree(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : graph[u])
            inDegree[v]++;
```

Count in-degrees. How many edges point INTO each node.

---

```cpp
    queue<int> q;
    for (int i = 0; i < n; i++)
        if (inDegree[i] == 0)
            q.push(i);
```

Enqueue all nodes with in-degree 0 (no prerequisites).

---

```cpp
    vector<int> order;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        order.push_back(node);
```

Dequeue a node. Add it to the topological order.

---

```cpp
        for (int next : graph[node]) {
            inDegree[next]--;
            if (inDegree[next] == 0)
                q.push(next);
        }
    }
```

Reduce in-degree of all dependents.
If any reaches 0 -- enqueue it.

---

```cpp
    if ((int)order.size() != n) return {}; // cycle detected
    return order;
}
```

If not all nodes were processed -- a cycle exists.
Return empty (or signal failure).

**Used in:** Course Schedule I, II, IV. Alien Dictionary.

---

## 7. Topological Sort (DFS -- Reverse Post-Order)

Alternative to Kahn's. Uses DFS instead of BFS.

```cpp
void dfs(int node, vector<vector<int>>& graph,
         vector<int>& color, stack<int>& result, bool& hasCycle) {
    color[node] = 1; // GRAY -- in progress
```

Mark the node as "currently being explored."

---

```cpp
    for (int next : graph[node]) {
        if (color[next] == 1) { hasCycle = true; return; }
        if (color[next] == 0) {
            dfs(next, graph, color, result, hasCycle);
            if (hasCycle) return;
        }
    }
```

If we encounter a GRAY node -- we've found a back edge → cycle.
If WHITE (unvisited) -- recurse.
If BLACK (fully explored) -- skip.

---

```cpp
    color[node] = 2; // BLACK -- fully explored
    result.push(node);
}
```

After all descendants are explored -- push this node.
The stack gives reverse post-order = topological order.

---

```cpp
vector<int> topoSort(int n, vector<vector<int>>& graph) {
    vector<int> color(n, 0); // 0=WHITE, 1=GRAY, 2=BLACK
    stack<int> result;
    bool hasCycle = false;

    for (int i = 0; i < n; i++) {
        if (color[i] == 0) {
            dfs(i, graph, color, result, hasCycle);
        }
    }

    if (hasCycle) return {};

    vector<int> order;
    while (!result.empty()) {
        order.push_back(result.top());
        result.pop();
    }
    return order;
}
```

Pop the stack → topological order.

---

## 8. Cycle Detection

### Undirected Graph -- DFS with Parent Tracking

```cpp
bool hasCycle(int node, int parent,
              vector<vector<int>>& graph, vector<bool>& visited) {
    visited[node] = true;
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            if (hasCycle(neighbor, node, graph, visited))
                return true;
        } else if (neighbor != parent) {
            return true; // visited neighbor that isn't parent → cycle
        }
    }
    return false;
}
```

In an undirected graph, every edge creates a "back path" to the parent.
That's NOT a cycle. A cycle is when you reach a visited node
that is NOT your parent.

### Directed Graph -- Three-Color DFS

```
WHITE (0) = unvisited
GRAY  (1) = in the current DFS path
BLACK (2) = fully explored
```

A back edge (GRAY → GRAY) = cycle.
This is the three-color system from the topological sort DFS above.

### Undirected Graph -- Union-Find

```cpp
for (auto& e : edges) {
    if (uf.find(e[0]) == uf.find(e[1])) return true; // cycle!
    uf.unite(e[0], e[1]);
}
return false;
```

If two endpoints are already in the same set → adding this edge creates a cycle.

---

## 9. Quick Reference Templates

### DFS (Recursive)

```cpp
void dfs(vector<vector<int>>& graph, vector<bool>& visited, int node) {
    visited[node] = true;
    for (int neighbor : graph[node])
        if (!visited[neighbor])
            dfs(graph, visited, neighbor);
}
```

### BFS

```cpp
void bfs(vector<vector<int>>& graph, vector<bool>& visited, int start) {
    queue<int> q;
    q.push(start);
    visited[start] = true;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        for (int neighbor : graph[node])
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
    }
}
```

### Grid DFS

```cpp
void dfs(vector<vector<int>>& grid, int r, int c) {
    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
    if (grid[r][c] == 0) return;
    grid[r][c] = 0;
    dfs(grid, r-1, c); dfs(grid, r+1, c);
    dfs(grid, r, c-1); dfs(grid, r, c+1);
}
```

### Kahn's Topological Sort

```cpp
queue<int> q;
for (int i = 0; i < n; i++) if (inDegree[i] == 0) q.push(i);
while (!q.empty()) {
    int node = q.front(); q.pop();
    order.push_back(node);
    for (int next : graph[node])
        if (--inDegree[next] == 0) q.push(next);
}
```

### The Golden Rule (One More Time)

> **Shortest path / minimum steps → BFS.**
> **Everything else → DFS.**
> **Dependencies / ordering → Topological Sort.**
> **Dynamic connectivity → Union-Find.**

These four tools solve every graph problem. 🧭🔥
