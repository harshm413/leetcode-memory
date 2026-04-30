# 📋🔗 The Complete Guide to Topological Sort

> _"In a world of dependencies,
> some things must happen before others.
> Course A before Course B.
> Task X before Task Y.
> Foundation before walls. Walls before roof.
>
> Topological sort is the art of finding an ordering
> that respects every single dependency --
> so that nothing is attempted
> before its prerequisites are complete.
>
> It works ONLY on Directed Acyclic Graphs (DAGs).
> If a cycle exists -- no valid ordering is possible.
> You cannot do A before B and B before A simultaneously."_

---

## Table of Contents

1. [What Is Topological Sort?](#1-what-is-topological-sort)
2. [When Does It Apply?](#2-when-does-it-apply)
3. [Kahn's Algorithm (BFS)](#3-kahns-algorithm-bfs)
4. [DFS Reverse Post-Order](#4-dfs-reverse-post-order)
5. [Cycle Detection During Topological Sort](#5-cycle-detection-during-topological-sort)
6. [Multiple Valid Orderings](#6-multiple-valid-orderings)
7. [Topological Sort on Different Graph Types](#7-topological-sort-on-different-graph-types)
8. [Common Questions and Deep Dives](#8-common-questions-and-deep-dives)
9. [Problems That Use Topological Sort](#9-problems-that-use-topological-sort)
10. [Quick Reference](#10-quick-reference)

---

## 1. What Is Topological Sort?

A topological ordering of a directed graph is a linear sequence
of all its nodes such that for every directed edge `u → v`,
node `u` appears BEFORE node `v` in the sequence.

```
Graph:
  0 → 1 → 3
  0 → 2 → 3

Valid topological orders:
  [0, 1, 2, 3]
  [0, 2, 1, 3]

Invalid:
  [1, 0, 2, 3]  ← 1 before 0, but edge 0→1 requires 0 first
```

Think of it as a "to-do list" where every prerequisite
is completed before the task that needs it.

**Key facts:**
-   Only works on **DAGs** (Directed Acyclic Graphs).
-   If a cycle exists → no topological order is possible.
-   The ordering is NOT unique (multiple valid orderings may exist).
-   Every DAG has at least one valid topological ordering.

---

## 2. When Does It Apply?

Topological sort applies whenever you have:

-   **Dependencies:** A must happen before B.
-   **Prerequisites:** course A required before course B.
-   **Build order:** compile file A before file B.
-   **Task scheduling:** task X before task Y.

The graph must be:
-   **Directed** -- dependencies have a direction (A before B ≠ B before A).
-   **Acyclic** -- no circular dependencies.

**Does NOT apply to:**
-   Undirected graphs (no concept of "before").
-   Graphs with cycles (impossible to order).

---

## 3. Kahn's Algorithm (BFS)

The most intuitive approach. Peel the graph layer by layer.

### The Idea

```
Nodes with in-degree 0 have NO prerequisites.
They can go first.

Process them. Remove their outgoing edges.
This may create NEW nodes with in-degree 0.
Process those. Repeat.

If all nodes are processed → valid topological order.
If some remain (in-degree never reached 0) → cycle exists.
```

### Step 1 -- Build the Graph and Compute In-Degrees

```cpp
vector<vector<int>> graph(n);
vector<int> inDegree(n, 0);

for (auto& edge : edges) {
    graph[edge[0]].push_back(edge[1]);
    inDegree[edge[1]]++;
}
```

`graph[u]` = list of nodes that depend on `u`.
`inDegree[v]` = how many nodes must come before `v`.

---

### Step 2 -- Enqueue All Nodes with In-Degree 0

```cpp
queue<int> q;
for (int i = 0; i < n; i++) {
    if (inDegree[i] == 0) {
        q.push(i);
    }
}
```

These nodes have no prerequisites. They can be processed first.

> _"The nodes that owe nothing to anyone go first.
> They are free. They enter the queue."_

---

### Step 3 -- BFS: Process and Reduce

```cpp
vector<int> order;

while (!q.empty()) {
    int node = q.front();
    q.pop();
    order.push_back(node);
```

Dequeue a node. Add it to the topological order.
All its prerequisites have already been processed
(that's why its in-degree was 0).

---

```cpp
    for (int next : graph[node]) {
        inDegree[next]--;
        if (inDegree[next] == 0) {
            q.push(next);
        }
    }
}
```

For every node that depends on the processed node:
reduce its in-degree by 1 (one prerequisite satisfied).

If in-degree reaches 0 → all prerequisites done → enqueue it.

> _"When your last blocker falls, you are free.
> Enter the queue. Your time has come."_

---

### Step 4 -- Check for Cycle

```cpp
if ((int)order.size() != n) {
    // CYCLE EXISTS -- not all nodes were processed
    return {};
}
return order;
```

If `order.size() < n` → some nodes were never enqueued.
Their in-degree never reached 0.
They are trapped in a cycle.

> _"Nodes in a cycle never reach in-degree 0.
> They are never enqueued. Never processed.
> They sit in silence -- forever waiting
> for a prerequisite that is also waiting for them."_

---

### Full Example

```
Graph: 0→1, 0→2, 1→3, 2→3
In-degrees: [0, 1, 1, 2]
```

| Dequeue | order      | Reduce in-degree of | Enqueue     |
| ------- | ---------- | ------------------- | ----------- |
| 0       | [0]        | 1 (1→0), 2 (1→0)   | 1, 2        |
| 1       | [0,1]      | 3 (2→1)             | --          |
| 2       | [0,1,2]    | 3 (1→0)             | 3           |
| 3       | [0,1,2,3]  | --                  | --          |

**Order: [0, 1, 2, 3]** ✓

```
Time:  O(V + E)
Space: O(V + E)
```

---

## 4. DFS Reverse Post-Order

An alternative using DFS instead of BFS.

### The Idea

In DFS, the **last** node to finish exploring
is the one with the deepest dependencies.
It should come FIRST in topological order.

By pushing nodes onto a stack AFTER all their descendants
are fully explored (post-order), then reversing --
we get a valid topological order.

### The Three-Color DFS

```cpp
void dfs(int node, vector<vector<int>>& graph,
         vector<int>& color, stack<int>& result, bool& hasCycle) {
```

---

```cpp
    color[node] = 1; // GRAY -- currently exploring
```

This node is on the current DFS path.

---

```cpp
    for (int next : graph[node]) {
```

Explore every outgoing edge.

---

#### GRAY Neighbor → Cycle!

```cpp
        if (color[next] == 1) {
            hasCycle = true;
            return;
        }
```

We've reached a node that's still being explored.
A back edge. A cycle. Topological sort is impossible.

---

#### WHITE Neighbor → Recurse

```cpp
        if (color[next] == 0) {
            dfs(next, graph, color, result, hasCycle);
            if (hasCycle) return;
        }
```

Unvisited → explore it. If it finds a cycle → propagate.

BLACK neighbors (color 2) are already fully explored → skip.

---

#### Mark BLACK and Push to Stack

```cpp
    color[node] = 2; // BLACK -- fully explored
    result.push(node);
}
```

All descendants are done. This node is complete.
Push it onto the stack.

The stack accumulates nodes in reverse topological order.
The deepest dependencies are pushed first (bottom of stack).
The sources are pushed last (top of stack).

---

### The Main Function

```cpp
vector<int> topoSort(int n, vector<vector<int>>& graph) {
    vector<int> color(n, 0);
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

Start DFS from every unvisited node.
Pop the stack → topological order.

---

### Full Example

```
Graph: 0→1, 0→2, 1→3, 2→3

DFS from 0:
  Enter 0 (GRAY).
    Enter 1 (GRAY).
      Enter 3 (GRAY). No outgoing. Push 3. BLACK.
    Push 1. BLACK.
    Enter 2 (GRAY).
      3 is BLACK → skip.
    Push 2. BLACK.
  Push 0. BLACK.

Stack (top to bottom): 0, 2, 1, 3
Pop order: [0, 2, 1, 3]
```

**Order: [0, 2, 1, 3]** ✓ (different from Kahn's but equally valid)

---

## 5. Cycle Detection During Topological Sort

Both algorithms detect cycles as a side effect:

### Kahn's (BFS)

```
If order.size() < n → cycle exists.
```

Nodes in a cycle never reach in-degree 0.
They are never processed. The count falls short.

### DFS (Three-Color)

```
If a GRAY node encounters another GRAY node → cycle.
```

A back edge in the DFS tree means a cycle.

### Which Nodes Are in the Cycle?

**Kahn's:** after BFS, any node with `inDegree[i] > 0`
is either in a cycle or depends on a cycle.

**DFS:** the cycle is between the two GRAY nodes.
Tracking the exact cycle requires extra bookkeeping.

---

## 6. Multiple Valid Orderings

Topological sort is NOT unique.

When multiple nodes have in-degree 0 simultaneously,
any of them can go first.

```
Graph: 0→2, 1→2

Both 0 and 1 have in-degree 0.
Valid orders: [0, 1, 2] or [1, 0, 2].
```

**Kahn's** produces one ordering based on queue order (FIFO).
**DFS** produces a different ordering based on DFS traversal order.

Both are correct. The problem usually accepts any valid ordering.

### Unique Topological Order

A topological order is unique if and only if
there is a **Hamiltonian path** in the DAG --
a path that visits every node exactly once.

Equivalently: at every step of Kahn's algorithm,
the queue has exactly ONE node.
If the queue ever has 2+ nodes → multiple valid orderings exist.

---

## 7. Topological Sort on Different Graph Types

### DAG (Directed Acyclic Graph) ✅

Topological sort works perfectly. This is its home.

### Directed Graph with Cycles ❌

No topological order exists. Both algorithms detect this.

### Undirected Graph ❌

Topological sort does not apply.
Undirected edges have no direction → no concept of "before."

If you need ordering on an undirected graph,
you must first assign directions (orient the edges).

### Disconnected DAG ✅

Works fine. Both algorithms handle disconnected components:
-   Kahn's: multiple nodes start with in-degree 0 from different components.
-   DFS: the outer loop starts DFS from every unvisited node.

### Single Node ✅

Trivially: the order is just that one node.

### Empty Graph (No Edges) ✅

Every node has in-degree 0. Any permutation is a valid topological order.

---

## 8. Common Questions and Deep Dives

### "Can topological sort work on undirected graphs?"

No. Undirected edges have no direction.
"A before B" and "B before A" are both implied by an undirected edge --
a contradiction. Topological sort requires directed edges.

### "What if the graph is disconnected?"

Both algorithms handle it naturally.
Kahn's enqueues all in-degree-0 nodes from all components.
DFS starts from every unvisited node.
The result interleaves nodes from different components.

### "How do I get ALL valid topological orderings?"

This requires backtracking -- try every possible choice
when multiple nodes have in-degree 0.
Exponential in the worst case. Rarely needed in practice.

### "Kahn's vs DFS -- which should I use?"

| Kahn's (BFS)                      | DFS Reverse Post-Order            |
| --------------------------------- | --------------------------------- |
| Iterative (no recursion)          | Recursive (stack overflow risk)   |
| Produces order directly           | Produces reverse order (needs stack) |
| Cycle detection: count < n        | Cycle detection: GRAY→GRAY edge   |
| Easier to explain                 | More "graph theory" flavored      |
| Can detect unique ordering        | Cannot easily detect uniqueness   |

**Recommendation: Kahn's for most problems.**
It's cleaner, iterative, and directly produces the order.

### "What about weighted DAGs?"

Topological sort + relaxation = shortest/longest path in a DAG.

```
Process nodes in topological order.
For each node u, for each edge u→v with weight w:
  dist[v] = min(dist[v], dist[u] + w)   // shortest path
  dist[v] = max(dist[v], dist[u] + w)   // longest path
```

This is O(V + E) -- faster than Dijkstra for DAGs.

### "What about topological sort with priorities?"

If you need the **lexicographically smallest** topological order,
replace the queue in Kahn's with a **min-heap (priority queue)**.

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
// instead of queue<int> q;
```

This always picks the smallest available node first.

---

## 9. Problems That Use Topological Sort

| Problem                  | What It Asks                          | Approach          |
| ------------------------ | ------------------------------------- | ----------------- |
| Course Schedule I        | Can all courses be finished? (cycle?) | Kahn's            |
| Course Schedule II       | Return the ordering                   | Kahn's            |
| Course Schedule IV       | Is u a prerequisite of v? (queries)   | Kahn's + reachability |
| Alien Dictionary         | Find letter order from sorted words   | Build graph + Kahn's |
| Parallel Courses         | Minimum semesters to finish all       | Kahn's level-by-level |
| Longest Path in DAG      | Longest path from source              | Topo sort + relaxation |
| Task Scheduler           | Minimum time with cooldown            | Greedy (related)  |
| Build Order              | Compile order for files               | Kahn's            |
| Sequence Reconstruction  | Is topo order unique?                 | Kahn's (queue size check) |

---

## 10. Quick Reference

### Kahn's Algorithm (BFS) -- The Default Choice

```cpp
vector<int> topoSort(int n, vector<vector<int>>& graph) {
    vector<int> inDegree(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : graph[u])
            inDegree[v]++;

    queue<int> q;
    for (int i = 0; i < n; i++)
        if (inDegree[i] == 0)
            q.push(i);

    vector<int> order;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        order.push_back(node);
        for (int next : graph[node])
            if (--inDegree[next] == 0)
                q.push(next);
    }

    if ((int)order.size() != n) return {}; // cycle
    return order;
}
```

### DFS Reverse Post-Order

```cpp
bool dfs(int node, vector<vector<int>>& graph,
         vector<int>& color, stack<int>& st) {
    color[node] = 1;
    for (int next : graph[node]) {
        if (color[next] == 1) return false; // cycle
        if (color[next] == 0 && !dfs(next, graph, color, st))
            return false;
    }
    color[node] = 2;
    st.push(node);
    return true;
}
```

### The Decision Rule

> **Need topological order?** → Kahn's Algorithm.
> **Need cycle detection in a directed graph?** → Kahn's or Three-Color DFS.
> **Need lexicographically smallest order?** → Kahn's with min-heap.
> **Need shortest/longest path in DAG?** → Topo sort + relaxation.
> **Undirected graph?** → Topological sort does NOT apply. 📋🔗
