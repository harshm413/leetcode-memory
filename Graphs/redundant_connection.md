## 🌳✂️ _The Edge That Broke the Tree: The Redundant Connection Saga_

> \_"In the Kingdom of Trees,
> a perfect tree once stood --
> `n` nodes connected by exactly `n - 1` edges,
> no cycles, every node reachable from every other.
>
> But someone added ONE extra edge --
> creating exactly ONE cycle.
>
> The Oracle was commanded:
>
> **'Find the redundant edge --
> the one that, if removed,
> would restore the graph to a valid tree.
> If multiple answers exist,
> return the one that appears LAST in the input.'**
>
> The Oracle reached for **Union-Find**.
>
> She processed edges one by one, in order.
> For each edge, she asked:
>
> **'Are these two nodes already connected?'**
>
> If NO → union them. The edge is necessary.
> If YES → this edge creates a cycle. It is the redundant one.
>
> The LAST edge that connects two already-connected nodes
> is the answer.
>
> Union-Find detected the cycle
> the instant it formed."\_

---

This is the saga of **Redundant Connection**.

Given a graph that was originally a tree with `n` nodes,
but has ONE extra edge added (creating exactly one cycle):

-   Return the edge that can be removed to restore the tree.
-   If multiple answers, return the one appearing **last** in the input.

```
Input:  edges = [[1,2],[1,3],[2,3]]
Output: [2,3]

Input:  edges = [[1,2],[2,3],[3,4],[1,4],[1,5]]
Output: [1,4]
```

---

## 🧠 The Oracle's Core Insight -- Union-Find Detects the Cycle Edge

A tree with `n` nodes has exactly `n - 1` edges.
Adding one more edge creates exactly one cycle.

If we process edges one by one using Union-Find:

-   Each edge connects two nodes.
-   If they're in DIFFERENT sets → union them. No cycle.
-   If they're in the SAME set → they're already connected.
    This edge creates a cycle. **This is the redundant edge.**

Since we process edges in input order,
the LAST such edge found is the answer
(the problem guarantees exactly one cycle,
so exactly one edge will trigger the "same set" condition).

```
Time:  O(N × α(N)) ≈ O(N)
Space: O(N)
```

---

### 📜 The Scroll of the Broken Tree

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏗️ The Union-Find Structure

### Parent Array -- Who Is Your Leader?

```cpp
class UnionFind {
    vector<int> parent;
    vector<int> rank;
```

-   `parent[i]` -- the parent of node `i` in the set tree.
-   `rank[i]` -- approximate depth, for balancing.

---

### 🏁 Initialize -- Every Node Is Its Own Set

```cpp
public:
    UnionFind(int n) : parent(n + 1), rank(n + 1, 0) {
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
        }
    }
```

Nodes are labeled `1` to `n` (1-indexed), so size is `n + 1`.
Every node started as its own parent -- `n` separate trees.

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

Follow the parent chain to the root.
Along the way, flatten the tree --
every node on the path points directly to the root.

This makes future finds nearly O(1).

> _"Who is your ultimate leader?
> Follow the chain. And while you're at it,
> shortcut everyone directly to the root."_

---

### 🔗 Union -- Returns False If Already Connected

```cpp
    bool unite(int a, int b) {
        int rootA = find(a);
        int rootB = find(b);
```

Find the roots of both nodes.

---

```cpp
        if (rootA == rootB) return false;
```

**Same root → already connected → cycle detected!**

Return `false` to signal that this edge is redundant.

> _"If both nodes already share the same leader,
> connecting them again creates a loop.
> This is the forbidden edge."_

---

```cpp
        if (rank[rootA] < rank[rootB]) swap(rootA, rootB);
```

`rank[i]` is just the **height** of the tree rooted at `i`.
Higher rank = taller tree.

We always want the **shorter tree to go UNDER the taller tree**.

After the swap, `rootA` is guaranteed to be the taller (or equal) root.
So we always attach `rootB` under `rootA`.

Why? If the taller tree went under the shorter one,
the result would be unnecessarily deep.
Keeping the taller one on top keeps everything flat and fast.

---

### 🌳 Attach rootB Under rootA

```cpp
        parent[rootB] = rootA;
```

The shorter tree's root now points to the taller tree's root.
Done. They are one set now.

---

### 📏 If Both Were the Same Height -- The Merged Tree Grew Taller

```cpp
        if (rank[rootA] == rank[rootB]) rank[rootA]++;
```

If rootA was already taller -- absorbing a shorter tree
doesn't make it any taller. Rank stays the same.

But if both had the **same height** -- stacking one under the other
adds one level. So rank goes up by 1.

That's it. That's all rank does.

---

### ✅ Return True -- Edge Was Valid

```cpp
        return true;
    }
};
```

The two sets were different → merging them was valid.
No cycle was created. Return `true`.

---

## ✂️ The Main Ritual -- Find the Redundant Edge

```cpp
vector<int> findRedundantConnection(vector<vector<int>>& edges) {
    int n = edges.size();
    UnionFind uf(n);
```

The number of edges equals `n` (tree has `n-1`, plus 1 extra).
Initialize Union-Find for `n` nodes.

---

### 🔁 Process Each Edge in Order

```cpp
    for (auto& e : edges) {
```

Edges were processed in the order they appeared in the input.

---

### 🔗 Try to Union the Two Endpoints

```cpp
        if (!uf.unite(e[0], e[1])) {
            return e;
        }
    }
```

If `unite` returned `false` --
the two nodes were already connected.
This edge created the cycle.

**Return it immediately.**

Since we process in input order and the problem guarantees
exactly one cycle, this is the LAST edge that could be removed
(the first one we encounter that closes the cycle).

> _"The moment two already-connected nodes
> try to shake hands again --
> that handshake is the redundant edge.
> Cut it, and the tree is restored."_

---

### 🛡️ Safety Return

```cpp
    return {};
}
```

This line is never reached (the problem guarantees a redundant edge exists).
It's a safety net for the compiler.

---

### 🎺 The Trial of the Broken Tree

```cpp
int main() {
    vector<vector<int>> e1 = {{1,2},{1,3},{2,3}};
    auto r1 = findRedundantConnection(e1);
    cout << "[" << r1[0] << "," << r1[1] << "]" << endl;
    // expected: [2,3]

    vector<vector<int>> e2 = {{1,2},{2,3},{3,4},{1,4},{1,5}};
    auto r2 = findRedundantConnection(e2);
    cout << "[" << r2[0] << "," << r2[1] << "]" << endl;
    // expected: [1,4]

    return 0;
}
```

---

**Full trace for edges = [[1,2],[1,3],[2,3]]:**

```
Graph: triangle 1-2-3
```

| Edge  | find(a) | find(b) | Same root? | Action          | Sets after       |
| ----- | ------- | ------- | ---------- | --------------- | ---------------- |
| [1,2] | 1       | 2       | No         | Union 1,2       | {1,2}, {3}       |
| [1,3] | 1       | 3       | No         | Union {1,2},3   | {1,2,3}          |
| [2,3] | 1       | 1       | **Yes!**   | **Redundant!**  | --               |

At edge [2,3]: both nodes 2 and 3 already belong to the set rooted at 1.
Connecting them again would create a cycle.

**Answer: [2,3]** ✓

---

**Full trace for edges = [[1,2],[2,3],[3,4],[1,4],[1,5]]:**

```
Graph: 1-2-3-4-1 (cycle) with 5 hanging off 1
```

| Edge  | find(a) | find(b) | Same root? | Action          | Sets after           |
| ----- | ------- | ------- | ---------- | --------------- | -------------------- |
| [1,2] | 1       | 2       | No         | Union           | {1,2}                |
| [2,3] | 1       | 3       | No         | Union           | {1,2,3}              |
| [3,4] | 1       | 4       | No         | Union           | {1,2,3,4}            |
| [1,4] | 1       | 1       | **Yes!**   | **Redundant!**  | --                   |

At edge [1,4]: nodes 1 and 4 are already in the same set.
The cycle `1→2→3→4→1` was completed by this edge.

Edge [1,5] was never even processed -- we returned early.

**Answer: [1,4]** ✓

---

**Trace for a chain with the last edge closing the cycle:**

```
edges = [[1,2],[2,3],[3,4],[4,5],[5,1]]
```

| Edge  | Same root? | Action    |
| ----- | ---------- | --------- |
| [1,2] | No         | Union     |
| [2,3] | No         | Union     |
| [3,4] | No         | Union     |
| [4,5] | No         | Union     |
| [5,1] | **Yes!**   | Redundant |

**Answer: [5,1]** ✓ -- the last edge closes the cycle.

---

## 🔍 Why Union-Find and Not DFS?

**DFS approach:** for each edge, check if the two endpoints
are already connected (DFS/BFS from one to the other).
O(N) per edge × N edges = O(N²).

**Union-Find:** O(α(N)) per operation ≈ O(1).
Total: O(N). Much faster.

Union-Find is the natural tool for dynamic connectivity --
"are these two nodes already connected?"
is exactly what `find` answers.

---

## 🔍 Why Does "Last in Input" Work?

The problem says: if multiple edges could be removed,
return the one appearing last in the input.

With Union-Find processing edges in order,
the FIRST edge that triggers "same root"
is the one that CLOSES the cycle.
Since there's exactly one cycle,
there's exactly one such edge.

If the problem had multiple cycles,
we'd need a different approach.
But with exactly one extra edge,
Union-Find finds it cleanly.

---

## 🌐 Where Union-Find Shines

| Problem                          | Union-Find Role                    |
| -------------------------------- | ---------------------------------- |
| Redundant Connection             | Detect the cycle-creating edge     |
| Connected Components             | Count distinct sets                |
| Accounts Merge                   | Group accounts by shared emails    |
| Graph Valid Tree                  | n-1 edges + no cycle = tree        |
| Minimum Spanning Tree (Kruskal)  | Add edges without creating cycles  |

Union-Find is the backbone of all these problems.

---

### 🧠 Memory of the Broken Tree Law

-   **Union-Find** processes edges one by one
-   **For each edge [a, b]:** `find(a)` and `find(b)`
    -   Different roots → union them (edge is valid)
    -   Same root → **redundant edge** (creates cycle) → return it
-   **Path compression** in `find` → nearly O(1) per operation
-   **Union by rank** → keeps trees balanced
-   **Nodes are 1-indexed** → parent array size `n + 1`
-   **Exactly one cycle** guaranteed → exactly one edge triggers same-root
-   **Time:** O(N × α(N)) ≈ O(N)
-   **Space:** O(N)
-   **Edge cases:**
    -   Triangle (3 nodes, 3 edges) → last edge of the triangle
    -   Cycle at the end of input → last edge returned
    -   Star graph with one extra edge → the extra edge

Thus is remembered the saga of **Redundant Connection**,
where the Oracle processed edges one by one
with Union-Find --
uniting nodes that were strangers,
and the moment two nodes
who were already kin tried to connect again --
that edge was declared redundant,
the one that broke the tree,
the one that must be cut
to restore the kingdom to a cycle-free forest. 🌳✂️✨
