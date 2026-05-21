# 🌳⚡ The Complete Guide to Minimum Spanning Tree

> _"In a kingdom of cities connected by weighted roads,
> the King demanded a network that connected ALL cities
> using the LEAST total road cost.
> No cycles allowed -- just a tree.
> N nodes, N-1 edges, minimum total weight.
> This was the Minimum Spanning Tree.
>
> Two ancient algorithms existed to find it:
> Prim's -- grow the tree one node at a time.
> Kruskal's -- pick the cheapest edges one by one.
> Both found the same optimal tree."_

---

## Table of Contents

1. [What Is a Spanning Tree?](#1-what-is-a-spanning-tree)
2. [What Is a Minimum Spanning Tree?](#2-what-is-a-minimum-spanning-tree)
3. [Disjoint Set (Union-Find)](#3-disjoint-set-union-find)
4. [Prim's Algorithm](#4-prims-algorithm)
5. [Kruskal's Algorithm](#5-kruskals-algorithm)
6. [Prim's vs Kruskal's](#6-prims-vs-kruskals)
7. [Quick Reference](#7-quick-reference)

---

## 1. What Is a Spanning Tree?

A spanning tree of a graph is a subgraph that:
-   Contains **ALL N nodes** of the original graph.
-   Has exactly **N-1 edges**.
-   Is **connected** -- every node is reachable from every other.
-   Has **no cycles**.

A graph can have MULTIPLE spanning trees.
Each spanning tree connects all nodes but uses different edges.

```
Original graph (5 nodes, 6 edges):
  0 --2-- 1 --1-- 2
  |               |
  1               2
  |               |
  3 ------2------ 4

One spanning tree (sum=7):  0-1, 1-2, 0-3, 2-4
Another spanning tree (sum=6): 0-3, 0-1, 1-2, 3-4
```

---

## 2. What Is a Minimum Spanning Tree?

Among ALL possible spanning trees of a graph,
the **minimum spanning tree (MST)** is the one
with the **smallest sum of edge weights**.

```
If spanning trees have sums: 18, 24, 18, 16, 20
The MST has sum = 16 (the minimum).
```

**Key facts:**
-   A graph may have multiple MSTs (if edges have equal weights).
-   MST always has exactly N-1 edges.
-   MST connects all nodes with minimum total cost.
-   Only applies to **connected, undirected, weighted** graphs.

---

## 3. Disjoint Set (Union-Find)

A data structure for dynamic connectivity queries:
"Are nodes u and v in the same component?"

Used by Kruskal's algorithm to check if adding an edge creates a cycle.

### Why Disjoint Set?

DFS/BFS can answer connectivity but takes O(N+E) per query.
Disjoint Set answers in **nearly O(1)** per query (amortized O(4) ≈ O(1)).

### Two Key Operations

**find(node)** -- find the ultimate parent (root) of the node's set.
**union(u, v)** -- merge the sets containing u and v.

### Path Compression

When finding the root, connect every node on the path
directly to the root. Future finds become O(1).

```cpp
int find(int node) {
    if (parent[node] != node) {
        parent[node] = find(parent[node]);
    }
    return parent[node];
}
```

After path compression, every node points directly to the root.
No long chains to traverse.

---

### Union by Rank

Always attach the **shorter tree under the taller tree**.
This keeps the tree flat → faster finds.

```
rank = approximate height of the tree rooted at that node.
```

---

### Union by Size

Instead of rank, track the **size** (number of nodes) of each component.
Attach the smaller component under the larger one.

More intuitive than rank because path compression distorts rank
but size remains accurate.

---

### Complete Implementation

```cpp
class DisjointSet {
    vector<int> parent, rank, size;
public:
    DisjointSet(int n) {
        parent.resize(n + 1);
        rank.resize(n + 1, 0);
        size.resize(n + 1, 1);
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
        }
    }
```

Every node starts as its own parent. Rank = 0. Size = 1.
`n + 1` to handle 1-indexed nodes.

---

```cpp
    int find(int node) {
        if (node == parent[node]) return node;
        return parent[node] = find(parent[node]);
    }
```

**Path compression:** while finding the root,
flatten the tree by pointing every node directly to the root.

Base case: node IS the root (`parent[node] == node`).
Recursive case: find root, update parent, return root.

---

```cpp
    void unionByRank(int u, int v) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU == rootV) return;

        if (rank[rootU] < rank[rootV]) {
            parent[rootU] = rootV;
        } else if (rank[rootV] < rank[rootU]) {
            parent[rootV] = rootU;
        } else {
            parent[rootV] = rootU;
            rank[rootU]++;
        }
    }
```

Find both roots. If same → already connected, do nothing.

Attach shorter tree under taller tree.
If equal height → pick either, increment the winner's rank.

**Why shorter under taller?**
If taller goes under shorter, the merged tree becomes unnecessarily deep.
Keeping the taller one on top minimizes the height → faster future finds.

---

```cpp
    void unionBySize(int u, int v) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU == rootV) return;

        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
    }
};
```

Attach smaller component under larger component.
Update the size of the new root.

**Why size is more intuitive than rank:**
Path compression distorts rank (the tree becomes flat,
but rank doesn't decrease). Size always stays accurate --
it's just the count of nodes in the component.

---

### Trace

```
Edges: {1,2}, {2,3}, {4,5}, {6,7}, {5,6}

After unionByRank(1,2): parent[2]=1, rank[1]=1.
After unionByRank(2,3): find(2)=1, find(3)=3. parent[3]=1.
After unionByRank(4,5): parent[5]=4, rank[4]=1.
After unionByRank(6,7): parent[7]=6, rank[6]=1.
After unionByRank(5,6): find(5)=4, find(6)=6. parent[6]=4, rank[4]=2.

find(3) == find(7)?  find(3)=1, find(7)=4. NOT same. ✓

After unionByRank(3,7): find(3)=1, find(7)=4. parent[1]=4.

find(3) == find(7)?  find(3)=4, find(7)=4. SAME. ✓
```

```
Time:  O(4α) ≈ O(1) per operation (amortized)
Space: O(N)
```

---

## 4. Prim's Algorithm

Grow the MST one node at a time.
Always pick the **cheapest edge** connecting the MST to a non-MST node.

### The Idea

```
Start from any node (say node 0).
Use a min-heap to always get the cheapest available edge.
Mark nodes as visited when added to the MST.
Repeat until all nodes are in the MST.
```

This is a **greedy** approach -- at every step,
pick the locally cheapest edge that expands the tree.

### The Algorithm

```cpp
int spanningTree(int V, vector<vector<pair<int,int>>>& graph) {
    // Min-heap: {edge weight, node}
    priority_queue<pair<int,int>, vector<pair<int,int>>,
                    greater<pair<int,int>>> pq;

    vector<int> vis(V, 0);
```

Min-heap stores `{weight, node}`.
Visited array tracks which nodes are already in the MST.

---

```cpp
    // Start from node 0 with weight 0
    pq.push({0, 0});
    int sum = 0;
```

Push the starting node with edge weight 0.
`sum` will accumulate the total MST weight.

---

```cpp
    while (!pq.empty()) {
        auto [wt, node] = pq.top();
        pq.pop();
```

Pop the cheapest edge from the heap.

---

### Skip If Already in MST

```cpp
        if (vis[node] == 1) continue;
```

If this node is already part of the MST → skip.
The heap may contain stale entries for this node.

---

### Add to MST

```cpp
        vis[node] = 1;
        sum += wt;
```

Mark the node as part of the MST.
Add its edge weight to the total sum.

> _"This node joins the tree.
> The edge that brought it here
> is the cheapest connection available.
> Greedy choice -- always optimal for MST."_

---

### Push All Unvisited Neighbors

```cpp
        for (auto& [neighbor, edgeWeight] : graph[node]) {
            if (!vis[neighbor]) {
                pq.push({edgeWeight, neighbor});
            }
        }
    }
    return sum;
}
```

For every unvisited neighbor of the newly added node,
push `{edgeWeight, neighbor}` into the heap.

Only push unvisited neighbors -- visited ones are already in the MST.

---

### Trace (V=5)

```
Edges: {0,1,2},{0,2,1},{1,2,1},{2,3,2},{3,4,1},{4,2,2}

Graph:
  0 → (1,2), (2,1)
  1 → (0,2), (2,1)
  2 → (0,1), (1,1), (3,2), (4,2)
  3 → (2,2), (4,1)
  4 → (3,1), (2,2)
```

| Pop (wt, node) | vis[node]? | Action                    | sum | Push to heap          |
| -------------- | ---------- | ------------------------- | --- | --------------------- |
| (0, 0)         | No         | Add node 0. sum=0.        | 0   | (2,1), (1,2)          |
| (1, 2)         | No         | Add node 2. sum=1.        | 1   | (1,1), (2,3), (2,4)   |
| (1, 1)         | No         | Add node 1. sum=2.        | 2   | (already vis neighbors)|
| (1, 3)         | No         | Wait -- let me re-check.  |     |                       |

Let me redo more carefully:

**Heap after start:** `[(0,0)]`

**Pop (0,0):** vis[0]=1, sum=0. Push neighbors: (2,1), (1,2). Heap: `[(1,2),(2,1)]`

**Pop (1,2):** vis[2]=1, sum=1. Push unvisited neighbors of 2: (1,1),(2,3),(2,4). Heap: `[(1,1),(2,1),(2,3),(2,4)]`

**Pop (1,1):** vis[1]=1, sum=2. Neighbors of 1: node 0 (visited), node 2 (visited). Nothing pushed. Heap: `[(2,1),(2,3),(2,4)]`

**Pop (2,1):** vis[1]=1 → **skip** (already visited).

**Pop (2,3):** vis[3]=1, sum=4. Push unvisited neighbors of 3: (1,4). Heap: `[(1,4),(2,4)]`

**Pop (1,4):** vis[4]=1, sum=5. Neighbors of 4: node 3 (visited), node 2 (visited). Nothing pushed.

**Heap empty. Done.**

**MST sum = 5** ✓

MST edges: (0,2) weight 1, (2,1) weight 1, (2,3) weight 2, (3,4) weight 1.

---

### Storing the MST Edges (Optional)

If you need the actual edges, use triplets `{weight, node, parent}`:

```cpp
int spanningTree(int V, vector<vector<pair<int,int>>>& graph,
                 vector<pair<int,int>>& mstEdges) {
    priority_queue<tuple<int,int,int>, vector<tuple<int,int,int>>,
                    greater<tuple<int,int,int>>> pq;
    vector<int> vis(V, 0);

    pq.push({0, 0, -1});
    int sum = 0;

    while (!pq.empty()) {
        auto [wt, node, parent] = pq.top();
        pq.pop();

        if (vis[node]) continue;
        vis[node] = 1;
        sum += wt;

        if (parent != -1) {
            mstEdges.push_back({parent, node});
        }

        for (auto& [neighbor, edgeWeight] : graph[node]) {
            if (!vis[neighbor]) {
                pq.push({edgeWeight, neighbor, node});
            }
        }
    }
    return sum;
}
```

The parent tracks which node brought this node into the MST.
`{parent, node}` is one MST edge.

```
Time:  O(E log E) -- each edge pushed/popped from heap
Space: O(E + V) -- heap + visited array
```

---

## 5. Kruskal's Algorithm

Sort all edges by weight. Pick them one by one.
If an edge doesn't create a cycle → add it to the MST.
Use **Union-Find** to check for cycles.

### The Idea

```
1. Sort all edges by weight (ascending).
2. For each edge (u, v, w) in sorted order:
   - If u and v are in DIFFERENT components → add edge to MST.
   - If u and v are in the SAME component → skip (would create cycle).
3. Stop when MST has N-1 edges.
```

### The Algorithm

```cpp
int kruskal(int V, vector<vector<int>>& edges) {
    // Sort edges by weight
    sort(edges.begin(), edges.end(), [](auto& a, auto& b) {
        return a[2] < b[2];
    });
```

Sort all edges by their weight in ascending order.
Process cheapest edges first.

---

```cpp
    DisjointSet ds(V);
    int sum = 0;
    int edgesUsed = 0;
```

Initialize Union-Find with V nodes.
`sum` accumulates MST weight. `edgesUsed` counts MST edges.

---

```cpp
    for (auto& e : edges) {
        int u = e[0];
        int v = e[1];
        int wt = e[2];
```

Process each edge in sorted order.

---

### Check If Edge Creates a Cycle

```cpp
        if (ds.find(u) != ds.find(v)) {
            ds.unionByRank(u, v);
            sum += wt;
            edgesUsed++;
        }
```

If `u` and `v` are in **different** components →
adding this edge is safe (no cycle).
Union them. Add weight to sum.

If they're in the **same** component →
adding this edge would create a cycle. Skip it.

> _"If two nodes are already connected,
> adding another edge between them creates a loop.
> Union-Find detects this in O(1).
> Skip the edge. Move to the next cheapest."_

---

### Early Termination (Optional)

```cpp
        if (edgesUsed == V - 1) break;
    }
    return sum;
}
```

MST has exactly N-1 edges. Once we have N-1, we're done.
No need to process remaining edges.

---

### Trace (V=5)

```
Edges sorted by weight:
  {0,2,1}, {1,2,1}, {3,4,1}, {0,1,2}, {2,3,2}, {4,2,2}
```

| Edge      | find(u)==find(v)? | Action          | sum | edgesUsed |
| --------- | ----------------- | --------------- | --- | --------- |
| {0,2,1}   | 0≠2 → No         | Union(0,2). Add.| 1   | 1         |
| {1,2,1}   | 1≠0 → No         | Union(1,0). Add.| 2   | 2         |
| {3,4,1}   | 3≠4 → No         | Union(3,4). Add.| 3   | 3         |
| {0,1,2}   | 0==0 → **Yes**   | Skip (cycle).   | 3   | 3         |
| {2,3,2}   | 0≠3 → No         | Union(0,3). Add.| 5   | 4 = V-1   |

**Stop. MST sum = 5** ✓

Edge {4,2,2} was never processed (early termination).

```
Time:  O(E log E) -- sorting dominates
Space: O(V) -- Union-Find
```

---

## 6. Prim's vs Kruskal's

| Prim's Algorithm                  | Kruskal's Algorithm               |
| --------------------------------- | --------------------------------- |
| Grows MST from a starting node    | Picks cheapest edges globally     |
| Uses a min-heap (priority queue)  | Uses sorting + Union-Find         |
| Better for **dense** graphs       | Better for **sparse** graphs      |
| O(E log E)                        | O(E log E)                        |
| Works with adjacency list         | Works with edge list              |
| Greedy: cheapest edge to expand   | Greedy: cheapest edge overall     |

**When to use which:**
-   If the graph is given as an adjacency list → Prim's is natural.
-   If the graph is given as an edge list → Kruskal's is natural.
-   Both produce the same MST (or one of equal weight if multiple exist).

---

## 7. Quick Reference

### Disjoint Set (Union-Find)

```cpp
class DisjointSet {
    vector<int> parent, rank, size;
public:
    DisjointSet(int n) : parent(n+1), rank(n+1, 0), size(n+1, 1) {
        for (int i = 0; i <= n; i++) parent[i] = i;
    }
    int find(int node) {
        if (node == parent[node]) return node;
        return parent[node] = find(parent[node]);
    }
    void unionByRank(int u, int v) {
        int ru = find(u), rv = find(v);
        if (ru == rv) return;
        if (rank[ru] < rank[rv]) swap(ru, rv);
        parent[rv] = ru;
        if (rank[ru] == rank[rv]) rank[ru]++;
    }
    void unionBySize(int u, int v) {
        int ru = find(u), rv = find(v);
        if (ru == rv) return;
        if (size[ru] < size[rv]) swap(ru, rv);
        parent[rv] = ru;
        size[ru] += size[rv];
    }
};
```

### Prim's Algorithm

```cpp
int prims(int V, vector<vector<pair<int,int>>>& graph) {
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
    vector<int> vis(V, 0);
    pq.push({0, 0});
    int sum = 0;
    while (!pq.empty()) {
        auto [wt, node] = pq.top(); pq.pop();
        if (vis[node]) continue;
        vis[node] = 1;
        sum += wt;
        for (auto& [nb, ew] : graph[node])
            if (!vis[nb]) pq.push({ew, nb});
    }
    return sum;
}
```

### Kruskal's Algorithm

```cpp
int kruskals(int V, vector<vector<int>>& edges) {
    sort(edges.begin(), edges.end(), [](auto& a, auto& b) {
        return a[2] < b[2];
    });
    DisjointSet ds(V);
    int sum = 0;
    for (auto& e : edges) {
        if (ds.find(e[0]) != ds.find(e[1])) {
            ds.unionByRank(e[0], e[1]);
            sum += e[2];
        }
    }
    return sum;
}
```

### The Decision Rule

> **Need MST?** → Prim's (adjacency list) or Kruskal's (edge list).
> **Need dynamic connectivity?** → Disjoint Set (Union-Find).
> **Need cycle detection in undirected graph?** → Union-Find.
> **Dense graph?** → Prim's. **Sparse graph?** → Kruskal's.

Both Prim's and Kruskal's are O(E log E) and produce the same result. 🌳⚡
