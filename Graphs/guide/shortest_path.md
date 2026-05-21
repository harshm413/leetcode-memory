# 🛤️⚡ The Complete Guide to Shortest Path Algorithms

> _"Given a graph, what is the shortest way from A to B?
> This is the most asked question in all of graph theory.
> The answer depends on the graph:
> Is it weighted or unweighted?
> Are weights positive or negative?
> Do you need one source or all pairs?
> Each scenario has its own weapon."_

---

## Table of Contents

1. [The Shortest Path Landscape](#1-the-shortest-path-landscape)
2. [BFS -- Unweighted Graphs](#2-bfs----unweighted-graphs)
3. [BFS on Grids](#3-bfs-on-grids)
4. [Dijkstra's Algorithm -- Weighted (Non-Negative)](#4-dijkstras-algorithm----weighted-non-negative)
5. [Bellman-Ford -- Weighted (Negative Allowed)](#5-bellman-ford----weighted-negative-allowed)
6. [Topological Sort + Relaxation -- DAGs](#6-topological-sort--relaxation----dags)
7. [Floyd-Warshall -- All Pairs](#7-floyd-warshall----all-pairs)
8. [0-1 BFS -- Weights Are Only 0 or 1](#8-0-1-bfs----weights-are-only-0-or-1)
9. [The Decision Flowchart](#9-the-decision-flowchart)
10. [Common Problems](#10-common-problems)

---

## 1. The Shortest Path Landscape

| Scenario                        | Algorithm              | Time              |
| ------------------------------- | ---------------------- | ----------------- |
| Unweighted graph                | BFS                    | O(V + E)          |
| Weighted, non-negative          | Dijkstra               | O((V+E) log V)    |
| Weighted, negative edges        | Bellman-Ford           | O(V × E)          |
| DAG (any weights)               | Topo sort + relaxation | O(V + E)          |
| All pairs, small V              | Floyd-Warshall         | O(V³)             |
| Weights are only 0 or 1         | 0-1 BFS (deque)        | O(V + E)          |

**The golden rule:**
> Unweighted → BFS.
> Weighted non-negative → Dijkstra.
> Negative edges → Bellman-Ford.
> DAG → Topo sort.
> All pairs → Floyd-Warshall.

---

## 2. BFS -- Unweighted Graphs

Every edge has the same cost (1 step).
BFS explores nodes level by level = distance by distance.
The first time BFS reaches a node = shortest distance.

### Single Source Shortest Path

```cpp
vector<int> shortestPath(vector<vector<int>>& edges, int N, int M, int src) {
    // Create adjacency list
    vector<vector<int>> graph(N);
    for (auto& e : edges) {
        graph[e[0]].push_back(e[1]);
        graph[e[1]].push_back(e[0]);
    }
```

Build undirected graph from edge list.

---

```cpp
    // Initialize distances to infinity
    vector<int> dist(N, 1e9);

    // Source distance = 0
    dist[src] = 0;

    // BFS queue
    queue<int> q;
    q.push(src);
```

All distances start at `1e9` (infinity). Source = 0.

---

```cpp
    while (!q.empty()) {
        int node = q.front();
        q.pop();

        for (int neighbor : graph[node]) {
            // Relaxation: if shorter path found
            if (dist[node] + 1 < dist[neighbor]) {
                dist[neighbor] = 1 + dist[node];
                q.push(neighbor);
            }
        }
    }
```

**Relaxation style** — `dist[node] + 1 < dist[neighbor]`.
If the current path offers a shorter distance → update and enqueue.

This mirrors how Dijkstra works — making it easier to see the connection.

---

```cpp
    // Convert unreachable nodes to -1
    vector<int> ans(N, -1);
    for (int i = 0; i < N; i++) {
        if (dist[i] != 1e9) {
            ans[i] = dist[i];
        }
    }
    return ans;
}
```

Nodes still at `1e9` are unreachable → mark as `-1`.

```
Time:  O(V + E)
Space: O(V + E)
```

---

### Trace (N=9, src=0)

```
Edges: {0,1},{0,3},{3,4},{4,5},{5,6},{1,2},{2,6},{6,7},{7,8},{6,8}

BFS from 0:
  dist[0]=0 → neighbors 1,3
  dist[1]=1, dist[3]=1 → neighbors 2,4
  dist[2]=2, dist[4]=2 → neighbors 6,5
  dist[6]=3, dist[5]=3 → neighbors 7,8
  dist[7]=4, dist[8]=4

Answer: [0, 1, 2, 1, 2, 3, 3, 4, 4]
```

---

## 3. BFS on Grids

Grids are unweighted graphs. Each step = 1 move.

```cpp
int shortestPath(vector<vector<int>>& grid, pair<int,int> start, pair<int,int> end) {
    int rows = grid.size(), cols = grid[0].size();
    vector<vector<int>> dist(rows, vector<int>(cols, -1));
    queue<pair<int,int>> q;

    dist[start.first][start.second] = 0;
    q.push(start);

    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};

    while (!q.empty()) {
        auto [r, c] = q.front(); q.pop();

        if (r == end.first && c == end.second) return dist[r][c];

        for (auto& d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && grid[nr][nc] == 0 && dist[nr][nc] == -1) {
                dist[nr][nc] = dist[r][c] + 1;
                q.push({nr, nc});
            }
        }
    }
    return -1; // unreachable
}
```

Same BFS. Neighbors = 4 adjacent cells. Walls = `grid[nr][nc] != 0`.

---

## 4. Dijkstra's Algorithm -- Weighted (Non-Negative)

When edges have different positive weights,
BFS doesn't work (a 2-hop path might be shorter than a 1-hop path
if the 1-hop edge is very heavy).

Dijkstra uses a **min-heap (priority queue)** or a **set**
to always process the node with the smallest known distance first.

### The Idea

```
Start: dist[source] = 0, all others = ∞.
Push (0, source) into min-heap.

While heap is not empty:
  Pop the node with smallest distance.
  If already finalized → skip.
  For each neighbor:
    newDist = dist[node] + weight(node, neighbor)
    If newDist < dist[neighbor]:
      dist[neighbor] = newDist
      Push (newDist, neighbor) into heap.
```

---

### 4a. Dijkstra with Priority Queue

```cpp
vector<int> dijkstra(int V, vector<vector<pair<int,int>>>& graph, int src) {
    // Distance array initialized to infinity
    vector<int> dist(V, 1e9);

    // Min-heap: {distance, node}
    priority_queue<pair<int,int>, vector<pair<int,int>>,
                    greater<pair<int,int>>> pq;

    // Source distance = 0
    dist[src] = 0;
    pq.push({0, src});
```

All distances start at `1e9`. Source pushed with distance 0.

---

```cpp
    while (!pq.empty()) {
        // Extract node with minimum distance
        int d = pq.top().first;
        int node = pq.top().second;
        pq.pop();

        // Skip if this distance is outdated (lazy deletion)
        if (d > dist[node]) continue;
```

**Lazy deletion** — the heap may contain stale entries.
If the popped distance is worse than current best → skip.

---

```cpp
        // Traverse all adjacent neighbors
        for (auto& [neighbor, weight] : graph[node]) {
            // Relaxation check
            if (dist[node] + weight < dist[neighbor]) {
                dist[neighbor] = dist[node] + weight;
                pq.push({dist[neighbor], neighbor});
            }
        }
    }
    return dist;
}
```

If shorter path found → update distance → push to heap.

**Why lazy deletion works:**
We don't remove old entries from the heap (expensive).
Instead, when we pop a stale entry, we just skip it.
The correct (shorter) entry will be popped first anyway
because the heap is ordered by distance.

```
Time:  O((V + E) log V)
Space: O(V + E)
```

---

### 4b. Dijkstra with Set

An alternative using `set` instead of `priority_queue`.
The set allows **explicit deletion** of outdated entries.

```cpp
vector<int> dijkstra(int V, vector<vector<pair<int,int>>>& graph, int src) {
    // Set stores {distance, node} in ascending order
    set<pair<int, int>> st;

    // Distance array
    vector<int> dist(V, 1e9);

    // Insert source
    st.insert({0, src});
    dist[src] = 0;
```

The set automatically keeps entries sorted by distance.
Smallest distance is always at `st.begin()`.

---

```cpp
    while (!st.empty()) {
        // Extract the node with minimum distance
        auto it = *(st.begin());
        int dis = it.first;
        int node = it.second;
        st.erase(it);
```

Pop the smallest entry. Unlike priority_queue,
we can also erase specific entries later.

---

```cpp
        for (auto& [neighbor, weight] : graph[node]) {
            if (dis + weight < dist[neighbor]) {
                // Erase old entry if it exists
                if (dist[neighbor] != 1e9)
                    st.erase({dist[neighbor], neighbor});

                // Update distance
                dist[neighbor] = dis + weight;

                // Insert new entry
                st.insert({dist[neighbor], neighbor});
            }
        }
    }
    return dist;
}
```

**Key difference from priority_queue version:**
When a shorter path is found, the OLD entry is **explicitly erased**
from the set before inserting the new one.

No stale entries remain. No lazy deletion needed.

---

### Set vs Priority Queue -- When to Use Which

| Priority Queue                    | Set                               |
| --------------------------------- | --------------------------------- |
| Cannot erase specific entries     | Can erase specific entries        |
| Uses lazy deletion (skip stale)   | No stale entries (explicit erase) |
| Slightly faster in practice       | Slightly slower (log N erase)     |
| Simpler code                      | More code but cleaner state       |
| **Preferred for interviews**      | Good to know as alternative       |

Both are O((V+E) log V). Priority queue is the standard choice.

---

### Why Priority Queue Is Used in Dijkstra

**Why not a regular queue (BFS)?**
BFS processes nodes in FIFO order — first in, first out.
This works when all edges have equal weight (distance = hops).
With different weights, a node discovered later might have
a SHORTER distance. BFS would process it too late.

**Why a min-heap?**
The min-heap always gives us the globally closest unfinalized node.
Processing nodes in order of distance guarantees that
when we finalize a node, no shorter path to it exists.

**Why not sort all nodes by distance?**
Distances change as we discover shorter paths.
A static sort would be wrong. The heap dynamically maintains
the correct order as distances are updated.

> _"The heap is the Oracle's compass.
> It always points to the nearest unexplored node.
> Process the nearest first — and its distance is final."_

---

## 5. Bellman-Ford -- Weighted (Negative Allowed)

When edges can be negative, Dijkstra fails.
Bellman-Ford handles negative edges (but NOT negative cycles).

### The Idea

```
Relax ALL edges V-1 times.
After V-1 iterations, all shortest paths are found
(a shortest path has at most V-1 edges).

Nth relaxation: if any distance still improves → negative cycle exists.
```

### The Algorithm

```cpp
vector<int> bellmanFord(int V, vector<vector<int>>& edges, int src) {
    vector<int> dist(V, 1e8);
    dist[src] = 0;
```

All distances start at `1e8` (large value, not INT_MAX to avoid overflow).
Source = 0.

---

```cpp
    // V-1 iterations of relaxation
    for (int i = 0; i < V - 1; i++) {
        for (auto& e : edges) {
            int u = e[0];
            int v = e[1];
            int wt = e[2];
            if (dist[u] != 1e8 && dist[u] + wt < dist[v]) {
                dist[v] = dist[u] + wt;
            }
        }
    }
```

Repeat V-1 times: for every edge `u→v` with weight `wt`,
if `u` is reachable AND going through `u` is shorter → update `v`.

**Why V-1 iterations?**
A shortest path in a graph with V nodes has at most V-1 edges.
Each iteration guarantees at least one more edge of the shortest path is finalized.
After V-1 iterations, all shortest paths (up to V-1 edges) are found.

**Why `dist[u] != 1e8`?**
Don't relax from unreachable nodes.
`1e8 + wt` would give a meaningless distance.

---

### Nth Relaxation -- Detect Negative Cycle

```cpp
    // Nth relaxation to check negative cycle
    for (auto& e : edges) {
        int u = e[0];
        int v = e[1];
        int wt = e[2];
        if (dist[u] != 1e8 && dist[u] + wt < dist[v]) {
            return {-1};
        }
    }
    return dist;
}
```

One more pass (the Vth iteration).
If ANY distance can still be improved → a negative cycle exists.

**Why?**
After V-1 iterations, all shortest paths are finalized
(assuming no negative cycles). If a Vth iteration still finds
a shorter path → distances can decrease infinitely → negative cycle.

> _"V-1 iterations find all honest shortest paths.
> The Vth iteration catches the liars --
> paths that keep getting shorter forever
> because they loop through negative weight."_

---

### Trace (V=6, S=0)

```
Edges: {3,2,6},{5,3,1},{0,1,5},{1,5,-3},{1,2,-2},{3,4,-2},{2,4,3}

Initial: dist = [0, 1e8, 1e8, 1e8, 1e8, 1e8]
```

**Iteration 1:**
- Edge 0→1 (5): dist[1] = 0+5 = 5
- Edge 1→5 (-3): dist[5] = 5+(-3) = 2
- Edge 1→2 (-2): dist[2] = 5+(-2) = 3
- Edge 5→3 (1): dist[3] = 2+1 = 3
- Edge 3→2 (6): dist[2] = min(3, 3+6) = 3 (no change)
- Edge 3→4 (-2): dist[4] = 3+(-2) = 1
- Edge 2→4 (3): dist[4] = min(1, 3+3) = 1 (no change)

After iter 1: `dist = [0, 5, 3, 3, 1, 2]`

**Iterations 2-5:** no further improvements (already optimal).

**Nth relaxation:** no improvement → no negative cycle.

**Answer: [0, 5, 3, 3, 1, 2]** ✓

```
Time:  O(V × E)
Space: O(V)
```

---

## 6. Topological Sort + Relaxation -- DAGs

For DAGs (no cycles), the fastest shortest path algorithm.
Works with ANY weights (positive, negative, zero).

### The Idea

```
1. DFS Topological sort the DAG (vis[] + stack).
2. Pop nodes from stack (topological order).
3. For each node, relax all outgoing edges.
```

Processing in topological order guarantees that
when we process node `u`, all nodes that could
contribute to `u`'s shortest path have already been processed.

### Step 1 -- DFS Topological Sort

```cpp
void topoSort(int node, vector<pair<int, int>> adj[], int vis[], stack<int>& st) {
    vis[node] = 1;
    for (auto it : adj[node]) {
        int v = it.first;
        if (!vis[v]) {
            topoSort(v, adj, vis, st);
        }
    }
    st.push(node);
}
```

Standard DFS topo sort — push node AFTER all descendants are done.
Uses `pair<int,int>` adjacency list: `{neighbor, weight}`.

---

### Step 2 -- Relax in Topological Order

```cpp
vector<int> shortestPath(int N, int M, vector<vector<int>>& edges) {
    // Build weighted adjacency list
    vector<pair<int, int>> adj[N];
    for (int i = 0; i < M; i++) {
        int u = edges[i][0];
        int v = edges[i][1];
        int wt = edges[i][2];
        adj[u].push_back({v, wt});
    }
```

Build directed weighted graph. DAG — no reverse edges.

---

```cpp
    // Topological sort using DFS
    int vis[N] = {0};
    stack<int> st;
    for (int i = 0; i < N; i++) {
        if (!vis[i]) {
            topoSort(i, adj, vis, st);
        }
    }
```

Get topological order in the stack.

---

```cpp
    // Initialize distances to infinity
    vector<int> dist(N);
    for (int i = 0; i < N; i++) dist[i] = 1e9;

    // Source = node 0
    dist[0] = 0;
```

All distances start at infinity. Source (node 0) = 0.

---

```cpp
    // Process nodes in topological order
    while (!st.empty()) {
        int node = st.top();
        st.pop();

        // Relax all outgoing edges
        for (auto it : adj[node]) {
            int v = it.first;
            int wt = it.second;
            if (dist[node] + wt < dist[v]) {
                dist[v] = wt + dist[node];
            }
        }
    }
```

Pop nodes from stack = topological order.
For each node, try to improve distances to all its neighbors.

**Why this works:**
When we process node `u`, every node that has an edge TO `u`
has already been processed (topological guarantee).
So `dist[u]` is already finalized when we use it for relaxation.

---

```cpp
    // Replace unreachable with -1
    for (int i = 0; i < N; i++) {
        if (dist[i] == 1e9) dist[i] = -1;
    }
    return dist;
}
```

Nodes still at infinity are unreachable → mark as -1.

---

### Trace (N=6, source=0)

```
Edges: {0,1,2},{0,4,1},{4,5,4},{4,2,2},{1,2,3},{2,3,6},{5,3,1}

Graph:
  0 → 1(2), 4(1)
  1 → 2(3)
  2 → 3(6)
  4 → 5(4), 2(2)
  5 → 3(1)

Topo order (from stack): 0, 4, 5, 1, 2, 3
```

| Process node | Relax edges                          | dist after              |
| ------------ | ------------------------------------ | ----------------------- |
| 0            | 0→1: 0+2=2, 0→4: 0+1=1             | [0, 2, 1e9, 1e9, 1, 1e9] |
| 4            | 4→5: 1+4=5, 4→2: 1+2=3             | [0, 2, 3, 1e9, 1, 5]   |
| 5            | 5→3: 5+1=6                          | [0, 2, 3, 6, 1, 5]     |
| 1            | 1→2: 2+3=5 > 3, no update           | [0, 2, 3, 6, 1, 5]     |
| 2            | 2→3: 3+6=9 > 6, no update           | [0, 2, 3, 6, 1, 5]     |
| 3            | no outgoing edges                    | [0, 2, 3, 6, 1, 5]     |

**Answer: [0, 2, 3, 6, 1, 5]** ✓

---

### Why This Is Faster Than Dijkstra for DAGs

| Dijkstra                          | DAG Topo Sort + Relaxation        |
| --------------------------------- | --------------------------------- |
| O((V+E) log V)                    | O(V + E)                          |
| Needs a heap                      | No heap needed                    |
| Works on any non-negative graph   | Only works on DAGs                |
| Cannot handle negative weights    | Handles negative weights fine     |

If the graph is a DAG → always use topo sort + relaxation.
It's faster AND handles negative weights.

```
Time:  O(V + E)
Space: O(V + E)
```

---

## 7. Floyd-Warshall -- All Pairs

Find shortest path between EVERY pair of nodes.

### The Idea

```
For each intermediate node k (0 to V-1):
  For each pair (i, j):
    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

"Can going through node k improve the path from i to j?"

### The Algorithm (with -1 as "no edge" sentinel)

```cpp
void shortest_distance(vector<vector<int>>& matrix) {
    int n = matrix.size();

    // For each intermediate node k
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {

                // If k is not reachable from i, or j not reachable from k, skip
                if (matrix[i][k] == -1 || matrix[k][j] == -1)
                    continue;

                // If no direct path from i to j exists
                if (matrix[i][j] == -1) {
                    matrix[i][j] = matrix[i][k] + matrix[k][j];
                }
                // Else update to minimum of both paths
                else {
                    matrix[i][j] = min(matrix[i][j],
                                       matrix[i][k] + matrix[k][j]);
                }
            }
        }
    }
}
```

**Key differences from INT_MAX version:**

-   `-1` means "no edge" (not infinity).
-   Must check `== -1` before adding (can't add to -1).
-   Two cases: no existing path (`== -1` → just assign) vs existing path (take min).

---

### Why `-1` Instead of INT_MAX?

Some problems (like GFG) use `-1` to represent "no edge."
The logic is slightly different:

| INT_MAX sentinel                  | -1 sentinel                       |
| --------------------------------- | --------------------------------- |
| `if (dist[i][k] != INT_MAX && ...)`| `if (matrix[i][k] == -1) continue`|
| Single min() call                 | Two cases: assign or min          |
| Risk of overflow (INT_MAX + w)    | No overflow risk                  |

Both are correct. Use whichever the problem expects.

---

### The `k` Loop MUST Be Outermost

```
CORRECT:  for k → for i → for j
WRONG:    for i → for j → for k
```

Why? When considering `k` as intermediate,
we need ALL paths through nodes `0..k-1` to already be computed.
The outer `k` loop ensures this.

If `k` is inner, we'd try to use node 5 as intermediate
before we've found paths through nodes 0-4. Wrong results.

---

### Trace (4 nodes)

```
Initial matrix:
  0  2 -1 -1
  1  0  3 -1
 -1 -1  0 -1
  3  5  4  0
```

**k=0:** Can going through node 0 improve any path?
- matrix[1][0]=1, matrix[0][1]=2 → matrix[1][1] already 0, skip.
- matrix[3][0]=3, matrix[0][1]=2 → matrix[3][1] = min(5, 3+2) = 5 (no change).

**k=1:** Can going through node 1 improve any path?
- matrix[0][1]=2, matrix[1][2]=3 → matrix[0][2] = -1 → set to 2+3=5.
- matrix[3][1]=5, matrix[1][2]=3 → matrix[3][2] = min(4, 5+3) = 4 (no change).

**k=2:** Can going through node 2 improve any path?
- matrix[0][2]=5, matrix[2][...] all -1 except matrix[2][2]=0. No improvements.

**k=3:** Can going through node 3 improve any path?
- matrix[0][3]=-1, skip row 0.
- matrix[1][3]=-1, skip row 1.
- matrix[2][3]=-1, skip row 2.

```
Final matrix:
  0  2  5 -1
  1  0  3 -1
 -1 -1  0 -1
  3  5  4  0
```

**Detecting negative cycles:** if `matrix[i][i] < 0` for any `i` → negative cycle.

```
Time:  O(V³)
Space: O(V²) -- in-place modification of the matrix
```

---

## 8. 0-1 BFS -- Weights Are Only 0 or 1

A special case: all edge weights are either 0 or 1.
Dijkstra works but is O((V+E) log V).
0-1 BFS uses a **deque** and runs in O(V + E).

### The Idea

```
Use a deque instead of a priority queue.
Weight-0 edges: push to FRONT (same distance level).
Weight-1 edges: push to BACK (next distance level).
```

This maintains the deque in sorted order of distance --
front = smallest distance, back = largest.

### The Algorithm

```cpp
vector<int> bfs01(int start, int n, vector<vector<pair<int,int>>>& graph) {
    vector<int> dist(n, INT_MAX);
    deque<int> dq;

    dist[start] = 0;
    dq.push_front(start);

    while (!dq.empty()) {
        int node = dq.front();
        dq.pop_front();

        for (auto& [neighbor, weight] : graph[node]) {
            int newDist = dist[node] + weight;
            if (newDist < dist[neighbor]) {
                dist[neighbor] = newDist;
                if (weight == 0) dq.push_front(neighbor);
                else dq.push_back(neighbor);
            }
        }
    }
    return dist;
}
```

Weight 0 → front (process immediately, same level).
Weight 1 → back (process later, next level).

```
Time:  O(V + E)
Space: O(V)
```

---

## 9. The Decision Flowchart

```
Is the graph unweighted?
  YES → BFS. Done.
  NO ↓

Is it a DAG?
  YES → Topological sort + relaxation. Done.
  NO ↓

Are all weights non-negative?
  YES → Are weights only 0 or 1?
    YES → 0-1 BFS. Done.
    NO  → Dijkstra. Done.
  NO ↓

Are there negative edges?
  YES → Bellman-Ford. Done.

Need all pairs?
  YES → Floyd-Warshall. Done.
```

### Summary Table

| Algorithm        | Weights          | Negative? | Cycles? | Time           | Use Case              |
| ---------------- | ---------------- | --------- | ------- | -------------- | --------------------- |
| BFS              | Unweighted (all 1)| N/A      | OK      | O(V+E)         | Grids, unweighted     |
| Dijkstra         | Non-negative     | ❌        | OK      | O((V+E)log V)  | Weighted, no negatives|
| Bellman-Ford     | Any              | ✅        | Detects | O(V×E)         | Negative edges        |
| DAG relaxation   | Any              | ✅        | No cycles| O(V+E)        | DAGs only             |
| Floyd-Warshall   | Any              | ✅        | Detects | O(V³)          | All pairs, small V    |
| 0-1 BFS          | Only 0 and 1     | ❌        | OK      | O(V+E)         | Binary weights        |

---

## 10. Common Problems

| Problem                          | Algorithm              | Why                          |
| -------------------------------- | ---------------------- | ---------------------------- |
| Shortest Path in Binary Matrix   | BFS                    | Unweighted grid              |
| Word Ladder                      | BFS                    | Unweighted implicit graph    |
| Open the Lock                    | BFS                    | Unweighted state graph       |
| Network Delay Time               | Dijkstra               | Weighted, non-negative       |
| Cheapest Flights Within K Stops  | Bellman-Ford / BFS     | Negative-like constraint     |
| Path With Minimum Effort         | Dijkstra (modified)    | Minimize max edge weight     |
| Shortest Path in DAG             | Topo sort + relaxation | DAG                          |
| Shortest Path Visiting All Nodes | BFS + bitmask          | State-space BFS              |
| Floyd-Warshall problems          | Floyd-Warshall         | All pairs needed             |

---

## The Golden Rules (One Last Time)

> **Unweighted → BFS. Always.**
> **Weighted non-negative → Dijkstra. Always.**
> **Negative edges → Bellman-Ford.**
> **DAG → Topo sort + relaxation (fastest possible).**
> **All pairs → Floyd-Warshall.**
> **Weights 0 or 1 → 0-1 BFS (deque trick).**

Pick the right tool. The graph tells you which one. 🛤️⚡
