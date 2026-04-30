# 🔄🔍 The Complete Guide to Cycle Detection in Graphs

> _"A cycle is a path that returns to where it started.
> In a graph, it means you can follow edges
> and end up at a node you've already visited --
> trapped in an endless loop.
> Detecting cycles is one of the most critical graph skills.
> The technique depends entirely on whether the graph
> is directed or undirected."_

---

## Table of Contents

1. [What Is a Cycle?](#1-what-is-a-cycle)
2. [Undirected Graph -- DFS with Parent Tracking](#2-undirected-graph----dfs-with-parent-tracking)
3. [Undirected Graph -- BFS with Parent Tracking](#3-undirected-graph----bfs-with-parent-tracking)
4. [Undirected Graph -- Union-Find](#4-undirected-graph----union-find)
5. [Directed Graph -- DFS with Three Colors](#5-directed-graph----dfs-with-three-colors)
6. [Directed Graph -- BFS (Kahn's Algorithm)](#6-directed-graph----bfs-kahns-algorithm)
7. [Why Undirected and Directed Need Different Techniques](#7-why-undirected-and-directed-need-different-techniques)
8. [Quick Reference](#8-quick-reference)

---

## 1. What Is a Cycle?

A cycle is a path of edges that starts and ends at the same node,
visiting at least one other node in between.

**Undirected cycle:**

```
0 -- 1 -- 2
|         |
+---------+

Path: 0 → 1 → 2 → 0.  Cycle!
```

**Directed cycle:**

```
0 → 1 → 2 → 0

Path: 0 → 1 → 2 → 0.  Cycle!
```

**Not a cycle (directed):**

```
0 → 1 → 2
0 → 2

Both paths reach 2, but there's no way back to 0.  No cycle.
```

**Why cycles matter:**
-   Course Schedule: a cycle means impossible prerequisites.
-   Deadlock detection: a cycle means processes are stuck.
-   Topological sort: only works on DAGs (no cycles).
-   Redundant Connection: the extra edge creates exactly one cycle.

---

## 2. Undirected Graph -- DFS with Parent Tracking

### The Problem

In an undirected graph, every edge goes both ways.
If node A connects to node B, then B also connects to A.

When DFS visits A and then moves to B,
B's neighbor list includes A.
But going back to A is NOT a cycle --
it's just the edge we came from.

**A cycle is when we reach a visited node
that is NOT the node we just came from (the parent).**

### The DFS Function

```cpp
bool hasCycleDFS(int node, int parent,
                 vector<vector<int>>& graph,
                 vector<bool>& visited) {
```

The Oracle entered a node, remembering who sent her here (`parent`).

---

```cpp
    visited[node] = true;
```

Mark this node as visited.

---

```cpp
    for (int neighbor : graph[node]) {
```

Check every neighbor.

---

### Case 1 -- Unvisited Neighbor → Recurse

```cpp
        if (!visited[neighbor]) {
            if (hasCycleDFS(neighbor, node, graph, visited)) {
                return true;
            }
        }
```

The neighbor hasn't been visited.
Recurse into it, passing `node` as the parent.

If the recursion finds a cycle deeper down → propagate `true` upward.

---

### Case 2 -- Visited Neighbor That Is NOT the Parent → CYCLE

```cpp
        else if (neighbor != parent) {
            return true;
        }
    }
```

The neighbor is already visited AND it's not the parent.

This means we've reached a node through a DIFFERENT path.
That's a cycle.

> _"If I find a visited node that isn't my parent --
> someone else reached it before me through a different route.
> We've formed a loop."_

---

### Case 3 -- Visited Neighbor That IS the Parent → Ignore

The `else if (neighbor != parent)` naturally skips the parent.
Going back to the parent is just retracing the edge we came from.
Not a cycle.

---

```cpp
    return false;
}
```

All neighbors checked. No cycle found from this node.

---

### The Main Function

```cpp
bool hasCycle(int n, vector<vector<int>>& graph) {
    vector<bool> visited(n, false);

    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            if (hasCycleDFS(i, -1, graph, visited)) {
                return true;
            }
        }
    }
    return false;
}
```

Start DFS from every unvisited node (handles disconnected graphs).
The first node has no parent → pass `-1`.

If any DFS call finds a cycle → return `true`.
If all finish without finding one → `false`.

---

### Trace for a Triangle (Cycle)

```
Graph: 0 -- 1 -- 2 -- 0

DFS from 0 (parent -1):
  Visit 0. Neighbor 1: unvisited → recurse.
    Visit 1 (parent 0). Neighbor 0: visited, IS parent → skip.
    Neighbor 2: unvisited → recurse.
      Visit 2 (parent 1). Neighbor 1: visited, IS parent → skip.
      Neighbor 0: visited, NOT parent → CYCLE FOUND!
```

**Result: true** ✓

### Trace for a Chain (No Cycle)

```
Graph: 0 -- 1 -- 2

DFS from 0 (parent -1):
  Visit 0. Neighbor 1: unvisited → recurse.
    Visit 1 (parent 0). Neighbor 0: visited, IS parent → skip.
    Neighbor 2: unvisited → recurse.
      Visit 2 (parent 1). Neighbor 1: visited, IS parent → skip.
      No more neighbors. Return false.
    Return false.
  Return false.
```

**Result: false** ✓

```
Time:  O(V + E)
Space: O(V)
```

---

## 3. Undirected Graph -- BFS with Parent Tracking

Same idea as DFS, but using a queue.

```cpp
bool hasCycleBFS(int start, vector<vector<int>>& graph,
                 vector<bool>& visited) {
    queue<pair<int,int>> q;
    q.push({start, -1});
    visited[start] = true;
```

Queue holds `{node, parent}` pairs.

---

```cpp
    while (!q.empty()) {
        auto [node, parent] = q.front();
        q.pop();

        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push({neighbor, node});
            }
```

Unvisited neighbor → mark visited, enqueue with `node` as parent.

---

```cpp
            else if (neighbor != parent) {
                return true;
            }
        }
    }
    return false;
}
```

Visited neighbor that isn't the parent → cycle.

Same logic as DFS, just iterative.

---

## 4. Undirected Graph -- Union-Find

The simplest approach when you have an edge list.

```cpp
bool hasCycle(int n, vector<vector<int>>& edges) {
    UnionFind uf(n);

    for (auto& e : edges) {
```

Process each edge.

---

```cpp
        if (uf.find(e[0]) == uf.find(e[1])) {
            return true;
        }
```

If both endpoints are already in the same set →
they're already connected. Adding this edge creates a cycle.

---

```cpp
        uf.unite(e[0], e[1]);
    }
    return false;
}
```

Different sets → union them. No cycle yet.

If all edges processed without finding same-set endpoints → no cycle.

> _"If two nodes are already kin
> and someone tries to connect them again --
> that connection closes a loop."_

```
Time:  O(E × α(V)) ≈ O(E)
Space: O(V)
```

---

## 5. Directed Graph -- DFS with Three Colors

### Why Parent Tracking Doesn't Work for Directed Graphs

In a directed graph, edges are one-way.
There's no "parent" in the same sense.

Consider:

```
0 → 1 → 2
0 → 2
```

DFS from 0: visit 0 → 1 → 2. Then backtrack.
From 0, visit 2 again. But 2 is already visited.
Is this a cycle? **NO.** There's no path from 2 back to 0.

Parent tracking would incorrectly flag this.
We need a smarter system.

### The Three-Color System

Every node has one of three states:

```
WHITE (0) = unvisited -- never touched
GRAY  (1) = in progress -- currently on the DFS path (in the recursion stack)
BLACK (2) = completed -- fully explored, all descendants done
```

**A cycle exists if and only if we find a GRAY → GRAY edge.**

A GRAY node is one we're currently exploring.
If we reach another GRAY node, we've found a back edge --
a path that loops back to a node still being explored.
That's a cycle.

A BLACK node is fully done. Reaching it means
we've found a cross edge or forward edge -- NOT a cycle.

### The DFS Function

```cpp
bool dfsCycle(int node, vector<vector<int>>& graph, vector<int>& color) {
```

---

```cpp
    color[node] = 1; // GRAY -- entering, in progress
```

This node is now on the current DFS path.

---

```cpp
    for (int neighbor : graph[node]) {
```

Check every outgoing edge.

---

### GRAY Neighbor → Cycle!

```cpp
        if (color[neighbor] == 1) {
            return true;
        }
```

The neighbor is GRAY -- it's on the current DFS path.
We've found a back edge. A cycle exists.

> _"If I reach a node that is still being explored --
> still GRAY, still on my current path --
> then I have looped back.
> That is a cycle."_

---

### WHITE Neighbor → Recurse

```cpp
        if (color[neighbor] == 0) {
            if (dfsCycle(neighbor, graph, color)) {
                return true;
            }
        }
```

Unvisited → explore it. If it finds a cycle → propagate.

---

### BLACK Neighbor → Safe, Skip

BLACK means fully explored. All its descendants are done.
No cycle through this node. Skip it.

(No explicit code needed -- the `if` conditions above
handle WHITE and GRAY. BLACK falls through naturally.)

---

```cpp
    color[node] = 2; // BLACK -- fully explored
    return false;
}
```

All neighbors checked. No cycle found through this node.
Mark it BLACK -- done forever.

---

### The Main Function

```cpp
bool hasCycle(int n, vector<vector<int>>& graph) {
    vector<int> color(n, 0); // all WHITE

    for (int i = 0; i < n; i++) {
        if (color[i] == 0) {
            if (dfsCycle(i, graph, color)) {
                return true;
            }
        }
    }
    return false;
}
```

Start DFS from every WHITE node (handles disconnected components).

---

### Trace for a Directed Cycle

```
Graph: 0 → 1 → 2 → 0

DFS from 0:
  color[0] = GRAY.
  Neighbor 1: WHITE → recurse.
    color[1] = GRAY.
    Neighbor 2: WHITE → recurse.
      color[2] = GRAY.
      Neighbor 0: GRAY → CYCLE FOUND!
```

**Result: true** ✓

### Trace for No Cycle (Diamond)

```
Graph: 0 → 1, 0 → 2, 1 → 3, 2 → 3

DFS from 0:
  color[0] = GRAY.
  Neighbor 1: WHITE → recurse.
    color[1] = GRAY.
    Neighbor 3: WHITE → recurse.
      color[3] = GRAY. No neighbors. color[3] = BLACK.
    color[1] = BLACK.
  Neighbor 2: WHITE → recurse.
    color[2] = GRAY.
    Neighbor 3: BLACK → skip (not GRAY, not a cycle).
    color[2] = BLACK.
  color[0] = BLACK.
```

**Result: false** ✓

Node 3 was reached twice (from 1 and from 2),
but the second time it was BLACK -- fully explored.
No cycle.

```
Time:  O(V + E)
Space: O(V)
```

---

## 6. Directed Graph -- BFS (Kahn's Algorithm)

Kahn's topological sort detects cycles as a side effect.

```cpp
bool hasCycle(int n, vector<vector<int>>& graph) {
    vector<int> inDegree(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : graph[u])
            inDegree[v]++;

    queue<int> q;
    for (int i = 0; i < n; i++)
        if (inDegree[i] == 0) q.push(i);

    int processed = 0;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        processed++;
        for (int next : graph[node])
            if (--inDegree[next] == 0) q.push(next);
    }

    return processed != n; // true = cycle exists
}
```

If not all nodes were processed → some are stuck in a cycle.
Their in-degree never reached 0.

This is exactly Course Schedule I.

---

## 7. Why Undirected and Directed Need Different Techniques

### The Core Difference

**Undirected:** every edge `(u, v)` creates a trivial "back path" `v → u`.
This is NOT a cycle -- it's just the same edge traversed backward.
We must ignore the parent to avoid false positives.

**Directed:** edges are one-way. There's no trivial back path.
A visited node might be reachable through multiple paths
without forming a cycle. We need the three-color system
to distinguish "still exploring" (GRAY) from "fully done" (BLACK).

### What Happens If You Use the Wrong Technique?

**Parent tracking on a directed graph:**

```
0 → 1 → 2
0 → 2
```

DFS from 0 → 1 → 2 (mark visited). Back to 0 → 2 (already visited, not parent).
Parent tracking says: CYCLE! But there's no cycle. **False positive.**

**Three-color on an undirected graph:**

It works, but you'd need to track the parent anyway
to avoid the trivial back-edge through the same undirected edge.
Simpler to just use parent tracking directly.

### Summary

| Graph Type | Technique                    | Why                              |
| ---------- | ---------------------------- | -------------------------------- |
| Undirected | DFS + parent tracking        | Ignore the trivial back-edge     |
| Undirected | Union-Find                   | Same-set endpoints = cycle       |
| Directed   | DFS + three colors           | Distinguish GRAY (cycle) from BLACK (no cycle) |
| Directed   | Kahn's (BFS topo sort)       | Unprocessed nodes = stuck in cycle |

---

## 8. Quick Reference

### Undirected -- DFS with Parent

```cpp
bool dfs(int node, int parent, vector<vector<int>>& g, vector<bool>& vis) {
    vis[node] = true;
    for (int nb : g[node]) {
        if (!vis[nb]) { if (dfs(nb, node, g, vis)) return true; }
        else if (nb != parent) return true;
    }
    return false;
}
```

### Undirected -- Union-Find

```cpp
for (auto& e : edges) {
    if (uf.find(e[0]) == uf.find(e[1])) return true; // cycle
    uf.unite(e[0], e[1]);
}
```

### Directed -- Three-Color DFS

```cpp
bool dfs(int node, vector<vector<int>>& g, vector<int>& color) {
    color[node] = 1; // GRAY
    for (int nb : g[node]) {
        if (color[nb] == 1) return true; // back edge = cycle
        if (color[nb] == 0 && dfs(nb, g, color)) return true;
    }
    color[node] = 2; // BLACK
    return false;
}
```

### Directed -- Kahn's

```cpp
// If processed count != n after Kahn's BFS → cycle exists
return processed != n;
```

### The Decision Rule

> **Undirected graph?** → Parent tracking (DFS/BFS) or Union-Find.
> **Directed graph?** → Three-color DFS or Kahn's topological sort.
> **Never mix them up.** 🔄🔍
