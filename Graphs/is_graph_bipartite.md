## 🎨⚖️ _The Two-Color Kingdom: The Is Graph Bipartite Saga_

> \_"In the Kingdom of Nodes,
> the King issued a decree:
>
> **'Paint every node with one of two colors --
> Red or Blue.
> No two adjacent nodes may share the same color.'**
>
> If this was possible -- the graph was **bipartite**.
> If not -- it was not.
>
> The Oracle thought carefully.
>
> A graph is bipartite if and only if
> it contains **no odd-length cycle**.
>
> But she didn't need to find cycles.
> She just needed to try coloring.
>
> **BFS (or DFS) from any node.
> Color it Red.
> Color all its neighbors Blue.
> Color all their neighbors Red.
> And so on -- alternating colors level by level.**
>
> If at any point a neighbor already has
> the SAME color as the current node --
> the graph is NOT bipartite.
> Two adjacent nodes want the same color.
> Contradiction.
>
> If the entire graph is colored without conflict --
> it IS bipartite."\_

---

This is the saga of **Is Graph Bipartite?**

Given an undirected graph as an adjacency list `graph`:
-   `graph[i]` = list of neighbors of node `i`.

Return `true` if the graph is **bipartite** --
meaning nodes can be split into two groups
such that every edge connects a node in one group
to a node in the other group.

```
Input:  graph = [[1,2,3],[0,2],[0,1,3],[0,2]]
Output: false

Input:  graph = [[1,3],[0,2],[1,3],[0,2]]
Output: true
```

---

## 🧠 The Oracle's Core Insight -- Two-Coloring with BFS

A graph is bipartite if and only if
it can be **2-colored** -- every node gets color 0 or 1,
and no two adjacent nodes share the same color.

The algorithm:

```
For each uncolored node (handles disconnected graphs):
  BFS from that node.
  Color it 0.
  Color all its neighbors 1.
  Color all their neighbors 0.
  If any neighbor already has the SAME color → NOT bipartite.
```

BFS naturally alternates colors level by level.
Level 0 = color 0. Level 1 = color 1. Level 2 = color 0. Etc.

```
Time:  O(V + E) -- visit every node and edge once
Space: O(V) -- color array + queue
```

---

### 📜 The Scroll of the Two-Color Kingdom

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🎨 The Oracle's Coloring Ritual

```cpp
bool isBipartite(vector<vector<int>>& graph) {
    int n = graph.size();
    vector<int> color(n, -1);
```

`color[i]` holds the color of node `i`:
-   `-1` = uncolored (not yet visited).
-   `0` = Red.
-   `1` = Blue.

All nodes start uncolored.

---

## 🔁 Process Every Connected Component

```cpp
    for (int i = 0; i < n; i++) {
        if (color[i] != -1) continue;
```

The graph might be disconnected.
Each uncolored node starts a new BFS for its component.

Already-colored nodes were handled by a previous BFS. Skip them.

---

## 🌊 BFS -- Color Level by Level

### Start with Color 0

```cpp
        queue<int> q;
        q.push(i);
        color[i] = 0;
```

The starting node was colored Red (0).
It entered the queue.

---

### Process the Queue

```cpp
        while (!q.empty()) {
            int node = q.front();
            q.pop();
```

Dequeue a node. Its color is already set.

---

### Check Every Neighbor

```cpp
            for (int neighbor : graph[node]) {
```

Look at every adjacent node.

---

### Case 1 -- Neighbor Is Uncolored → Color It Opposite

```cpp
                if (color[neighbor] == -1) {
                    color[neighbor] = 1 - color[node];
                    q.push(neighbor);
                }
```

The neighbor hasn't been colored yet.
Give it the **opposite** color: `1 - color[node]`.

If `node` is Red (0) → neighbor gets Blue (1).
If `node` is Blue (1) → neighbor gets Red (0).

`1 - 0 = 1`. `1 - 1 = 0`. Simple toggle.

Enqueue the neighbor for further processing.

> _"You are my neighbor.
> You must wear the opposite color.
> If I am Red, you are Blue.
> If I am Blue, you are Red.
> This is the law of bipartiteness."_

---

### Case 2 -- Neighbor Has the SAME Color → NOT Bipartite

```cpp
                else if (color[neighbor] == color[node]) {
                    return false;
                }
```

The neighbor is already colored --
and it has the **same** color as the current node.

Two adjacent nodes with the same color.
The two-coloring has failed.
The graph is NOT bipartite.

> _"You are my neighbor.
> But you wear the same color as me.
> We cannot coexist in a bipartite world.
> The kingdom is broken."_

---

### Case 3 -- Neighbor Has the OPPOSITE Color → All Good

No code needed. If the neighbor is already colored
with the opposite color -- everything is fine.
The `if` and `else if` above handle the other cases.
This case falls through silently.

---

### Close the Loops

```cpp
            }
        }
    }
    return true;
}
```

If all components were colored without conflict --
the graph is bipartite. Return `true`.

---

### 🎺 The Trial of the Two-Color Kingdom

```cpp
int main() {
    vector<vector<int>> g1 = {{1,2,3},{0,2},{0,1,3},{0,2}};
    cout << isBipartite(g1) << endl; // expected: 0 (false)

    vector<vector<int>> g2 = {{1,3},{0,2},{1,3},{0,2}};
    cout << isBipartite(g2) << endl; // expected: 1 (true)

    return 0;
}
```

---

**Full trace for graph = [[1,3],[0,2],[1,3],[0,2]] (bipartite):**

```
Graph:
  0 -- 1
  |    |
  3 -- 2

This is a 4-cycle (even length). Bipartite.
```

| Dequeue | color[node] | Neighbor | Neighbor color | Action              |
| ------- | ----------- | -------- | -------------- | ------------------- |
| 0       | 0 (Red)     | 1        | -1 (uncolored) | color[1]=1, enqueue |
|         |             | 3        | -1             | color[3]=1, enqueue |
| 1       | 1 (Blue)    | 0        | 0 (Red)        | Opposite ✓ skip     |
|         |             | 2        | -1             | color[2]=0, enqueue |
| 3       | 1 (Blue)    | 0        | 0 (Red)        | Opposite ✓ skip     |
|         |             | 2        | 0 (Red)        | Opposite ✓ skip     |
| 2       | 0 (Red)     | 1        | 1 (Blue)       | Opposite ✓ skip     |
|         |             | 3        | 1 (Blue)       | Opposite ✓ skip     |

No conflicts. **Answer: true** ✓

Coloring: {0: Red, 1: Blue, 2: Red, 3: Blue}.
Group A = {0, 2}. Group B = {1, 3}.
Every edge crosses groups.

---

**Full trace for graph = [[1,2,3],[0,2],[0,1,3],[0,2]] (NOT bipartite):**

```
Graph:
  0 -- 1
  |  / |
  2 -- 3

Contains a triangle: 0-1-2. Odd cycle. Not bipartite.
```

| Dequeue | color[node] | Neighbor | Neighbor color | Action              |
| ------- | ----------- | -------- | -------------- | ------------------- |
| 0       | 0 (Red)     | 1        | -1             | color[1]=1, enqueue |
|         |             | 2        | -1             | color[2]=1, enqueue |
|         |             | 3        | -1             | color[3]=1, enqueue |
| 1       | 1 (Blue)    | 0        | 0 (Red)        | Opposite ✓ skip     |
|         |             | 2        | 1 (Blue)       | **SAME COLOR!**     |

Node 1 (Blue) and node 2 (Blue) are neighbors with the same color.

**Answer: false** ✓

The triangle 0-1-2 made it impossible.
0 is Red. 1 must be Blue (neighbor of 0). 2 must be Blue (neighbor of 0).
But 1 and 2 are also neighbors -- both Blue. Conflict.

---

**Trace for disconnected graph [[1],[0],[3],[2]]:**

```
Component 1: 0 -- 1
Component 2: 2 -- 3
```

BFS from 0: color 0=Red, 1=Blue. No conflict.
BFS from 2: color 2=Red, 3=Blue. No conflict.

**Answer: true** ✓ -- each component is independently bipartite.

---

**Trace for single node [[]]:**

Node 0 has no neighbors. Color it 0. No conflicts possible.

**Answer: true** ✓ -- a single node is trivially bipartite.

---

## 🔍 Why BFS and Not DFS?

Both work. BFS is slightly more intuitive here
because it naturally processes nodes level by level --
and bipartite coloring IS level-by-level alternation.

DFS version:

```cpp
bool dfs(int node, int c, vector<vector<int>>& graph, vector<int>& color) {
    color[node] = c;
    for (int neighbor : graph[node]) {
        if (color[neighbor] == -1) {
            if (!dfs(neighbor, 1 - c, graph, color)) return false;
        } else if (color[neighbor] == c) {
            return false;
        }
    }
    return true;
}
```

Same logic: color the node, recurse with opposite color,
check for same-color conflicts.

---

## 🔍 What Makes a Graph NOT Bipartite?

A graph is NOT bipartite if and only if
it contains an **odd-length cycle**.

```
Odd cycle (triangle): 0-1-2-0 (length 3)
  0=Red, 1=Blue, 2=Red... but 2 and 0 are neighbors, both Red. Conflict!

Even cycle (square): 0-1-2-3-0 (length 4)
  0=Red, 1=Blue, 2=Red, 3=Blue. No conflict. Bipartite!
```

The two-coloring BFS detects odd cycles implicitly --
without ever searching for cycles directly.

---

## 🌐 Where Bipartite Checking Appears

| Problem                          | Connection                         |
| -------------------------------- | ---------------------------------- |
| Is Graph Bipartite?              | Direct application                 |
| Possible Bipartition             | Same problem, different name       |
| Two-Coloring                     | Same concept                       |
| Matching problems                | Bipartite graphs have special matching properties |

---

### 🧠 Memory of the Two-Color Kingdom Law

-   **Color array:** `-1` = uncolored, `0` = Red, `1` = Blue
-   **BFS from each uncolored node** (handles disconnected graphs)
-   **Color starting node 0.** Alternate: neighbors get `1 - color[node]`
-   **Conflict:** neighbor already has SAME color → return false
-   **No conflict after all nodes** → return true
-   **DFS alternative:** recurse with opposite color, same conflict check
-   **Bipartite ↔ no odd-length cycle** (but we don't need to find cycles)
-   **Time:** O(V + E)
-   **Space:** O(V)
-   **Edge cases:**
    -   Single node → true
    -   Disconnected → check each component independently
    -   Self-loop → always false (node is its own neighbor with same color)
    -   Tree → always bipartite (trees have no cycles)

Thus is remembered the saga of **Is Graph Bipartite?**,
where the Oracle tried to paint the kingdom in two colors --
Red and Blue, alternating with every edge --
and when two neighbors demanded the same color,
the kingdom was declared broken,
unable to be split into two harmonious groups --
but when every edge crossed colors cleanly,
the bipartite law held true. 🎨⚖️✨
