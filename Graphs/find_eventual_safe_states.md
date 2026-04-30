## 🛡️🔄 _The Nodes That Escape the Cycle: The Find Eventual Safe States Saga_

> \_"In the Kingdom of Directed Graphs,
> some nodes were **safe** --
> every path starting from them
> eventually reached a **terminal node**
> (a node with no outgoing edges).
>
> Other nodes were **unsafe** --
> at least one path from them
> led into a **cycle**, trapping the traveler forever.
>
> The Oracle was commanded:
>
> **'Find all the safe nodes.
> A node is safe if EVERY path from it
> leads to a terminal node.
> No path from a safe node can ever enter a cycle.'**
>
> The Oracle saw two approaches:
>
> **Approach One -- DFS with Three Colors.**
> A node is safe if it is NOT part of a cycle
> and does NOT lead to a cycle.
> Use the three-color DFS (WHITE/GRAY/BLACK).
> BLACK nodes are safe. GRAY nodes are in a cycle.
>
> **Approach Two -- Reverse Kahn's (Topological Sort).**
> Reverse all edges. Terminal nodes become sources (in-degree 0).
> Kahn's BFS peels safe nodes layer by layer.
> Nodes never peeled are unsafe (part of or leading to a cycle).
>
> Both approaches reveal the same truth:
> the safe nodes are those that live
> entirely outside the reach of any cycle."\_

---

This is the saga of **Find Eventual Safe States**.

Given a directed graph with `n` nodes (0 to n-1):
-   `graph[i]` = list of nodes that `i` has edges to.

A node is a **terminal node** if it has no outgoing edges.
A node is **safe** if every path from it leads to a terminal node.

Return all safe nodes in **sorted order**.

```
Input:  graph = [[1,2],[2,3],[5],[0],[5],[],[]]
Output: [2, 4, 5, 6]

Input:  graph = [[1,2,3,4],[1,2],[3,4],[0,4],[]]
Output: [4]
```

---

## 🧠 Approach One -- DFS with Three Colors

The three-color system from cycle detection
directly tells us which nodes are safe:

```
WHITE (0) = unvisited
GRAY  (1) = currently being explored (on the DFS path)
BLACK (2) = fully explored -- ALL paths from this node are safe
```

**A node becomes BLACK only if NONE of its descendants
are part of a cycle.**

If any descendant is GRAY (cycle) → the current node stays GRAY → unsafe.
If all descendants are BLACK (safe) → the current node becomes BLACK → safe.

**BLACK = safe. GRAY = unsafe.**

```
Time:  O(V + E)
Space: O(V)
```

---

### 📜 The Scroll of the Safe Kingdom

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

### 🔍 The DFS -- Determine If a Node Is Safe

```cpp
bool dfs(int node, vector<vector<int>>& graph, vector<int>& color) {
```

The Oracle entered a node to determine its fate.

---

#### Mark as GRAY -- Exploring

```cpp
    color[node] = 1;
```

This node is now on the current DFS path.
If we encounter it again → cycle.

---

#### Check Every Outgoing Edge

```cpp
    for (int next : graph[node]) {
```

Look at every node this node points to.

---

#### GRAY Neighbor → Cycle Found → Unsafe

```cpp
        if (color[next] == 1) {
            return false;
        }
```

The neighbor is GRAY -- still being explored.
We've found a back edge. A cycle.
This node is NOT safe.

---

#### WHITE Neighbor → Recurse

```cpp
        if (color[next] == 0) {
            if (!dfs(next, graph, color)) {
                return false;
            }
        }
```

The neighbor is unvisited. Explore it.
If the recursion returns `false` (found a cycle downstream) →
this node is also unsafe. Propagate `false`.

---

#### BLACK Neighbor → Already Proven Safe → Skip

No code needed. BLACK neighbors are safe.
The `if` conditions above handle GRAY and WHITE.
BLACK falls through -- no action needed.

---

#### All Descendants Safe → Mark BLACK

```cpp
    color[node] = 2;
    return true;
}
```

Every outgoing path was explored.
None led to a cycle.
This node is **safe**. Mark it BLACK.

> _"If every road from this node
> leads to a dead end (terminal) or another safe node --
> then this node itself is safe.
> It earns the BLACK mark -- the mark of safety."_

---

### 🏁 The Main Function

```cpp
vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
    int n = graph.size();
    vector<int> color(n, 0);
```

All nodes start WHITE (unvisited).

---

#### DFS from Every Unvisited Node

```cpp
    for (int i = 0; i < n; i++) {
        if (color[i] == 0) {
            dfs(i, graph, color);
        }
    }
```

Some nodes might not be reachable from others.
Start DFS from every unvisited node to cover the entire graph.

---

#### Collect All BLACK (Safe) Nodes

```cpp
    vector<int> safe;
    for (int i = 0; i < n; i++) {
        if (color[i] == 2) {
            safe.push_back(i);
        }
    }
    return safe;
}
```

Walk through all nodes.
Every BLACK node is safe. Collect them.

Since we iterate 0 to n-1, the result is already sorted.

---

### 🎺 The Trial of the Safe Kingdom

```cpp
int main() {
    vector<vector<int>> g1 = {{1,2},{2,3},{5},{0},{5},{},{}};
    auto r1 = eventualSafeNodes(g1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 2 4 5 6

    vector<vector<int>> g2 = {{1,2,3,4},{1,2},{3,4},{0,4},{}};
    auto r2 = eventualSafeNodes(g2);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 4

    return 0;
}
```

---

**Full trace for graph = [[1,2],[2,3],[5],[0],[5],[],[]]:**

```
Graph:
  0 → 1, 2
  1 → 2, 3
  2 → 5
  3 → 0       ← creates cycle: 0→1→3→0
  4 → 5
  5 → (terminal)
  6 → (terminal)
```

**DFS from node 0:**

```
dfs(0): GRAY.
  dfs(1): GRAY.
    dfs(2): GRAY.
      dfs(5): GRAY. No outgoing. BLACK. Return true.
    All safe. BLACK. Return true.   ← node 2 is safe
    dfs(3): GRAY.
      next=0: color[0]=GRAY → CYCLE! Return false.
    Return false.                    ← node 3 is unsafe (stays GRAY)
  Return false.                      ← node 1 is unsafe (stays GRAY)
Return false.                        ← node 0 is unsafe (stays GRAY)
```

**DFS from node 4:**

```
dfs(4): GRAY.
  next=5: color[5]=BLACK → skip.
All safe. BLACK. Return true.        ← node 4 is safe
```

**DFS from node 6:**

```
dfs(6): GRAY. No outgoing. BLACK.   ← node 6 is safe
```

**Final colors:**

```
Node: 0=GRAY, 1=GRAY, 2=BLACK, 3=GRAY, 4=BLACK, 5=BLACK, 6=BLACK
```

**Safe nodes (BLACK): [2, 4, 5, 6]** ✓

Nodes 0, 1, 3 are part of the cycle 0→1→3→0 → unsafe.
Node 2 points to 5 (terminal) → safe.
Node 4 points to 5 (terminal) → safe.
Nodes 5, 6 are terminals → safe.

---

**Trace for graph = [[1,2,3,4],[1,2],[3,4],[0,4],[]]:**

```
Node 0 → 1,2,3,4
Node 1 → 1,2       ← self-loop on 1!
Node 2 → 3,4
Node 3 → 0,4       ← cycle: 0→3→0
Node 4 → (terminal)
```

DFS from 0:
-   0 → 1 → 1 is GRAY (self-loop) → cycle → 1 unsafe → 0 gets false.
-   But wait, 0 also has edges to 2, 3, 4. However, once ANY path
    from 0 leads to a cycle, 0 is unsafe.

Actually, let me re-trace. The DFS returns false as soon as
any neighbor leads to a cycle:

```
dfs(0): GRAY.
  dfs(1): GRAY.
    next=1: color[1]=GRAY → CYCLE! Return false.
  1 is unsafe. Return false.
0 is unsafe.
```

DFS from 2:
```
dfs(2): GRAY.
  dfs(3): GRAY.
    next=0: color[0]=GRAY → CYCLE! Return false.
  3 is unsafe. Return false.
2 is unsafe.
```

DFS from 4:
```
dfs(4): GRAY. No outgoing. BLACK. Safe.
```

**Safe nodes: [4]** ✓

---

## 🧠 Approach Two -- Reverse Kahn's Algorithm

### The Idea

In the original graph, terminal nodes have **out-degree 0**.
In the **reversed** graph, terminal nodes have **in-degree 0**.

Kahn's on the reversed graph peels safe nodes layer by layer:

```
1. Reverse all edges.
2. Compute in-degrees in the reversed graph.
3. Enqueue all nodes with in-degree 0 (terminals in original).
4. BFS: dequeue → mark safe → reduce neighbors' in-degree → enqueue if 0.
5. All processed nodes are safe.
```

Nodes in cycles never reach in-degree 0 in the reversed graph →
never processed → unsafe.

```cpp
vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
    int n = graph.size();
    vector<vector<int>> reversed(n);
    vector<int> outDegree(n, 0);
```

---

```cpp
    for (int u = 0; u < n; u++) {
        outDegree[u] = graph[u].size();
        for (int v : graph[u]) {
            reversed[v].push_back(u);
        }
    }
```

Build the reversed graph. Track out-degree in the original
(which becomes in-degree in the reversed graph).

---

```cpp
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (outDegree[i] == 0) {
            q.push(i);
        }
    }
```

Terminal nodes (out-degree 0) are the starting points.

---

```cpp
    vector<bool> safe(n, false);
    while (!q.empty()) {
        int node = q.front(); q.pop();
        safe[node] = true;
        for (int prev : reversed[node]) {
            outDegree[prev]--;
            if (outDegree[prev] == 0) {
                q.push(prev);
            }
        }
    }
```

Process safe nodes. For each, reduce out-degree of predecessors.
When a predecessor's out-degree reaches 0 →
ALL its outgoing paths lead to safe nodes → it's safe too.

---

```cpp
    vector<int> result;
    for (int i = 0; i < n; i++) {
        if (safe[i]) result.push_back(i);
    }
    return result;
}
```

Collect all safe nodes in sorted order.

---

## 🔍 DFS vs Reverse Kahn's

| DFS Three-Color                   | Reverse Kahn's                    |
| --------------------------------- | --------------------------------- |
| Recursive                         | Iterative (BFS)                   |
| BLACK = safe, GRAY = unsafe       | Processed = safe, unprocessed = unsafe |
| Detects cycles via back edges     | Detects cycles via unprocessed nodes |
| No graph reversal needed          | Requires building reversed graph  |
| Simpler code                      | More intuitive for topo-sort fans |

Both are O(V + E). DFS is usually shorter to code.

---

## 🔍 Why Must EVERY Path Be Safe?

A node is safe only if **every** path from it leads to a terminal.
Not just one path -- ALL paths.

```
Node 0 → 1 (safe path to terminal)
Node 0 → 3 → 0 (cycle!)
```

Node 0 is NOT safe. Even though one path is safe,
another path leads to a cycle.

In the DFS approach, this is handled naturally:
if ANY neighbor returns `false` (unsafe), the current node is unsafe.
ALL neighbors must return `true` for the node to be BLACK.

---

### 🧠 Memory of the Safe States Law

**DFS Three-Color:**
-   WHITE=unvisited, GRAY=exploring, BLACK=safe
-   GRAY neighbor → cycle → return false (unsafe)
-   WHITE neighbor → recurse. If false → propagate false.
-   All neighbors safe → mark BLACK → return true
-   Collect all BLACK nodes = safe nodes

**Reverse Kahn's:**
-   Reverse all edges. Track out-degree.
-   Enqueue terminals (out-degree 0).
-   BFS: process → mark safe → reduce predecessors' out-degree → enqueue if 0.
-   Unprocessed nodes = unsafe.

-   **Time:** O(V + E)
-   **Space:** O(V + E)
-   **Edge cases:**
    -   All terminals → all safe
    -   All in one big cycle → none safe
    -   Self-loop → that node is unsafe
    -   Disconnected → each component handled independently

Thus is remembered the saga of **Find Eventual Safe States**,
where the Oracle colored every node with three fates --
WHITE for the unknown, GRAY for the endangered,
BLACK for the proven safe --
and through DFS, determined that a node was safe
only when EVERY path from it
led to the quiet peace of a terminal node,
never once straying into the endless trap of a cycle. 🛡️🔄✨
