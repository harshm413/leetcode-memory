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
vector<int> bfsShortestPath(int start, vector<vector<int>>& graph, int n) {
    vector<int> dist(n, -1);
    queue<int> q;
```

`dist[i] = -1` means unreachable. `q` is the BFS queue.

---

```cpp
    dist[start] = 0;
    q.push(start);
```

The source has distance 0 to itself.

---

```cpp
    while (!q.empty()) {
        int node = q.front();
        q.pop();
```

Dequeue a node. Its distance is final.

---

```cpp
        for (int neighbor : graph[node]) {
            if (dist[neighbor] == -1) {
                dist[neighbor] = dist[node] + 1;
                q.push(neighbor);
            }
        }
    }
    return dist;
}
```

Unvisited neighbor → distance = current + 1. Enqueue.
Already visited → skip (first visit was shortest).

**Why BFS guarantees shortest path:**
BFS processes ALL distance-d nodes before ANY distance-(d+1) node.
So the first time a node is reached, it's via the shortest path.

```
Time:  O(V + E)
Space: O(V)
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

Dijkstra uses a **min-heap (priority queue)** to always process
the node with the smallest known distance first.

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

### Building the Weighted Graph

```cpp
vector<vector<pair<int,int>>> graph(n);
// graph[u] = list of {neighbor, weight}

for (auto& e : edges) {
    graph[e[0]].push_back({e[1], e[2]});
    graph[e[1]].push_back({e[0], e[2]}); // if undirected
}
```

### The Algorithm

```cpp
vector<int> dijkstra(int start, vector<vector<pair<int,int>>>& graph, int n) {
    vector<int> dist(n, INT_MAX);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
```

`dist[i]` = shortest known distance to node `i`. Starts at ∞.
`pq` = min-heap of `{distance, node}`. `greater` makes it a min-heap.

---

```cpp
    dist[start] = 0;
    pq.push({0, start});
```

Source has distance 0.

---

```cpp
    while (!pq.empty()) {
        auto [d, node] = pq.top();
        pq.pop();
```

Pop the node with the smallest distance.

---

```cpp
        if (d > dist[node]) continue;
```

**The lazy deletion check.**

The heap may contain stale entries (old, longer distances).
If the popped distance is greater than the current best →
this entry is outdated. Skip it.

This is cheaper than removing old entries from the heap.

---

```cpp
        for (auto& [neighbor, weight] : graph[node]) {
            int newDist = dist[node] + weight;
```

Compute the distance to the neighbor through this node.

---

```cpp
            if (newDist < dist[neighbor]) {
                dist[neighbor] = newDist;
                pq.push({newDist, neighbor});
            }
        }
    }
    return dist;
}
```

If the new distance is shorter → update and push to heap.

The heap ensures we always process the globally closest
unfinalized node next. This is why Dijkstra works.

**Why Dijkstra fails with negative edges:**
A node might be "finalized" with distance 10,
but a negative edge later could reduce it to 7.
Dijkstra never revisits finalized nodes → misses the improvement.

```
Time:  O((V + E) log V) -- each push/pop is O(log V)
Space: O(V + E)
```

---

### Trace Example

```
Graph: 0 --1-- 1 --3-- 2
       0 --4-- 2

Edges: 0→1 (w=1), 1→2 (w=3), 0→2 (w=4)
```

| Pop (d, node) | Update                    | dist after     |
| ------------- | ------------------------- | -------------- |
| (0, 0)        | dist[1]=1, dist[2]=4      | [0, 1, 4]      |
| (1, 1)        | dist[2]=min(4, 1+3=4)=4   | [0, 1, 4]      |
| (4, 2)        | no updates                | [0, 1, 4]      |

Shortest: 0→1 = 1, 0→2 = 4 (via direct edge, same as via 1).

---

## 5. Bellman-Ford -- Weighted (Negative Allowed)

When edges can be negative, Dijkstra fails.
Bellman-Ford handles negative edges (but NOT negative cycles).

### The Idea

```
Relax ALL edges V-1 times.
After V-1 iterations, all shortest paths are found
(a shortest path has at most V-1 edges).
```

### The Algorithm

```cpp
vector<int> bellmanFord(int start, int n, vector<tuple<int,int,int>>& edges) {
    vector<int> dist(n, INT_MAX);
    dist[start] = 0;
```

---

```cpp
    for (int i = 0; i < n - 1; i++) {
        for (auto& [u, v, w] : edges) {
            if (dist[u] != INT_MAX && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
        }
    }
```

Repeat V-1 times: for every edge, try to improve the distance.

`dist[u] != INT_MAX` prevents relaxing from unreachable nodes.

---

### Detecting Negative Cycles

```cpp
    for (auto& [u, v, w] : edges) {
        if (dist[u] != INT_MAX && dist[u] + w < dist[v]) {
            // NEGATIVE CYCLE DETECTED
            return {};
        }
    }
    return dist;
}
```

One more pass. If any distance can still be improved →
a negative cycle exists (distances can decrease infinitely).

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
1. Topological sort the DAG.
2. Process nodes in topological order.
3. For each node, relax all outgoing edges.
```

Processing in topological order guarantees that
when we process node `u`, all nodes that could
contribute to `u`'s shortest path have already been processed.

### The Algorithm

```cpp
vector<int> dagShortestPath(int start, int n, vector<vector<pair<int,int>>>& graph) {
    // Step 1: Topological sort (Kahn's)
    vector<int> inDegree(n, 0);
    for (int u = 0; u < n; u++)
        for (auto& [v, w] : graph[u])
            inDegree[v]++;

    queue<int> q;
    for (int i = 0; i < n; i++)
        if (inDegree[i] == 0) q.push(i);

    vector<int> topo;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        topo.push_back(node);
        for (auto& [next, w] : graph[node])
            if (--inDegree[next] == 0) q.push(next);
    }
```

Standard Kahn's topological sort.

---

```cpp
    // Step 2: Relax in topological order
    vector<int> dist(n, INT_MAX);
    dist[start] = 0;

    for (int u : topo) {
        if (dist[u] == INT_MAX) continue;
        for (auto& [v, w] : graph[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
        }
    }
    return dist;
}
```

Process each node in topological order.
Relax all outgoing edges.

```
Time:  O(V + E) -- the fastest possible
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

### The Algorithm

```cpp
vector<vector<int>> floydWarshall(int n, vector<vector<int>>& dist) {
    // dist[i][j] initialized from edges. INT_MAX if no edge. 0 on diagonal.

    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (dist[i][k] != INT_MAX && dist[k][j] != INT_MAX) {
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
    return dist;
}
```

The `k` loop MUST be the outermost loop.
This ensures that when considering `k` as intermediate,
all paths through nodes `0..k-1` are already computed.

```
Time:  O(V³)
Space: O(V²)
```

**Detecting negative cycles:** if `dist[i][i] < 0` for any `i` → negative cycle.

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
