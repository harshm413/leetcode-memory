## 🪞🕸️ _The Mirror of the Web: The Clone Graph Saga_

> \_"In the Kingdom of Connected Nodes,
> a graph existed -- an undirected web
> where each node held a value
> and a list of neighbors.
>
> The Oracle was commanded:
>
> **'Create a DEEP COPY of this graph.
> Every node must be a NEW object.
> Every neighbor relationship must be preserved.
> No original node may appear in the clone.'**
>
> The Oracle faced two challenges:
>
> **Challenge One -- Cycles.**
> The graph could have cycles.
> If she naively cloned by following neighbors,
> she would loop forever --
> visiting the same node again and again.
>
> **Challenge Two -- Shared References.**
> A node might be a neighbor of multiple nodes.
> She must not create duplicate clones of the same node.
> Every original node must map to exactly ONE clone.
>
> The solution to both:
>
> **A HashMap from original node → cloned node.**
>
> Before cloning a node, check the map.
> If already cloned -- return the existing clone.
> If not -- create a new clone, store it in the map,
> then recursively clone all its neighbors.
>
> The map served as both a **visited set** (preventing cycles)
> and a **clone registry** (preventing duplicates).
>
> DFS through the graph.
> Clone as you go.
> The map remembers everything."\_

---

This is the saga of **Clone Graph**.

Given a reference to a node in a connected undirected graph:

-   Return a **deep copy** (clone) of the graph.
-   Each node has a `val` and a list of `neighbors`.
-   Nodes are labeled 1 to n. No duplicate edges. No self-loops.

```
Input:  adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: deep copy of the same graph structure

Node 1 → neighbors [2, 4]
Node 2 → neighbors [1, 3]
Node 3 → neighbors [2, 4]
Node 4 → neighbors [1, 3]
```

---

## 🧠 The Oracle's Core Insight -- HashMap as Clone Registry

The key data structure:

```
unordered_map<Node*, Node*> cloned;
  key   = pointer to original node
  value = pointer to its clone
```

This map serves two purposes:

**1. Visited check** -- if a node is in the map, it's already been cloned.
Don't clone it again. Return the existing clone.
This prevents infinite loops in cyclic graphs.

**2. Clone registry** -- ensures each original node
maps to exactly one clone. Multiple neighbors pointing
to the same original node will all point to the same clone.

The algorithm:

```
DFS(node):
  If node is null → return null
  If node is already in the map → return its clone
  Create a new clone of node
  Store it in the map BEFORE recursing (critical for cycles!)
  For each neighbor of node:
    clone.neighbors.push_back(DFS(neighbor))
  Return the clone
```

```
Time:  O(N + E) -- visit every node and edge once
Space: O(N) -- the hash map + recursion stack
```

---

### 📜 The Scroll of the Mirror Web

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

### 🧱 The Structure of a Node

```cpp
class Node {
public:
    int val;
    vector<Node*> neighbors;

    Node(int _val) : val(_val), neighbors(vector<Node*>()) {}
};
```

Each node carried a value and a list of neighbor pointers.
The graph was undirected -- if A neighbors B, then B neighbors A.

---

## 🪞 The Clone Registry

```cpp
class Solution {
    unordered_map<Node*, Node*> cloned;
```

The Oracle's map -- original node → its clone.
Declared as a class member so all recursive calls share it.

---

## ⚔️ The DFS Cloning Ritual

```cpp
public:
    Node* cloneGraph(Node* node) {
```

The Oracle received a pointer to any node in the original graph.
She would return a pointer to the corresponding node in the clone.

---

### 🛑 Base Case -- Null Node

```cpp
        if (node == nullptr) return nullptr;
```

An empty graph has no nodes to clone.

---

### 🔍 Already Cloned? Return the Existing Clone

```cpp
        if (cloned.count(node)) {
            return cloned[node];
        }
```

If this node was already cloned --
return the existing clone immediately.

This is the **cycle breaker**.
Without this check, a cycle like `A → B → A → B → ...`
would recurse forever.

> _"If I have already built your mirror image,
> I will not build another.
> I simply point to the one that already exists."_

---

### 🆕 Create the Clone

```cpp
        Node* clone = new Node(node->val);
        cloned[node] = clone;
```

A new node was created with the same value.
It was immediately stored in the map **before** recursing.

**Why store before recursing?**

Because a neighbor might point back to this node (cycle).
If the clone isn't in the map yet,
the recursive call would try to clone it again -- infinite loop.

By storing first, the recursive call finds it in the map
and returns the existing clone.

> _"Register the clone BEFORE exploring its neighbors.
> This is the shield against cycles.
> When a neighbor looks back and asks
> 'have you been cloned?' --
> the answer must already be yes."_

---

### 🔁 Clone All Neighbors Recursively

```cpp
        for (Node* neighbor : node->neighbors) {
            clone->neighbors.push_back(cloneGraph(neighbor));
        }
```

For each neighbor of the original node:

-   Recursively clone that neighbor (or retrieve its existing clone).
-   Add the cloned neighbor to the clone's neighbor list.

The recursion naturally traversed the entire graph via DFS.
Every node was visited exactly once (the map prevented revisits).

> _"For every friend the original has,
> the clone must have the mirror of that friend.
> The recursion finds or creates each mirror."_

---

### 🏁 Return the Clone

```cpp
        return clone;
    }
};
```

The fully connected clone was returned.
Its neighbors pointed to other clones -- never to original nodes.

---

### 🎺 The Trial of the Mirror Web

```cpp
int main() {
    // Build: 1 -- 2 -- 3 -- 4 -- 1 (cycle)
    //        1 -- 4,  2 -- 3
    Node* n1 = new Node(1);
    Node* n2 = new Node(2);
    Node* n3 = new Node(3);
    Node* n4 = new Node(4);

    n1->neighbors = {n2, n4};
    n2->neighbors = {n1, n3};
    n3->neighbors = {n2, n4};
    n4->neighbors = {n1, n3};

    Solution sol;
    Node* clone = sol.cloneGraph(n1);

    // Verify: clone is a different object with same structure
    cout << (clone != n1) << endl;                    // 1 (different pointer)
    cout << (clone->val == n1->val) << endl;          // 1 (same value)
    cout << (clone->neighbors.size() == 2) << endl;   // 1 (same neighbor count)
    return 0;
}
```

---

**Full DFS trace for the 4-node cycle graph:**

```
Graph:
  1 -- 2
  |    |
  4 -- 3
```

| Call              | Node | In map? | Action                              | Map after              |
| ----------------- | ---- | ------- | ----------------------------------- | ---------------------- |
| cloneGraph(1)     | 1    | No      | Create clone(1), store in map       | {1→c1}                 |
|   → neighbor 2    |      |         |                                     |                        |
| cloneGraph(2)     | 2    | No      | Create clone(2), store in map       | {1→c1, 2→c2}          |
|   → neighbor 1    |      |         |                                     |                        |
| cloneGraph(1)     | 1    | **Yes** | Return existing c1                  | (no change)            |
|   → neighbor 3    |      |         |                                     |                        |
| cloneGraph(3)     | 3    | No      | Create clone(3), store in map       | {1→c1, 2→c2, 3→c3}   |
|   → neighbor 2    |      |         |                                     |                        |
| cloneGraph(2)     | 2    | **Yes** | Return existing c2                  | (no change)            |
|   → neighbor 4    |      |         |                                     |                        |
| cloneGraph(4)     | 4    | No      | Create clone(4), store in map       | {1→c1,...,4→c4}       |
|   → neighbor 1    |      |         |                                     |                        |
| cloneGraph(1)     | 1    | **Yes** | Return existing c1                  | (no change)            |
|   → neighbor 3    |      |         |                                     |                        |
| cloneGraph(3)     | 3    | **Yes** | Return existing c3                  | (no change)            |

**Result:** 4 new nodes created. Each clone's neighbors point to other clones.
No original node appears in the cloned graph. Cycles handled correctly.

The map was checked 8 times (once per neighbor traversal).
4 times it was a miss (new clone created).
4 times it was a hit (existing clone returned -- cycle broken).

---

**Trace for single node (no neighbors):**

```
cloneGraph(1): not in map → create clone(1) → no neighbors → return clone(1)
```

**Result:** one cloned node with empty neighbor list. ✓

---

**Trace for null input:**

```
cloneGraph(nullptr): return nullptr
```

**Result:** null. ✓

---

## 🔄 BFS Alternative

Instead of DFS recursion, use BFS with a queue:

```cpp
Node* cloneGraph(Node* node) {
    if (!node) return nullptr;

    unordered_map<Node*, Node*> cloned;
    cloned[node] = new Node(node->val);
    queue<Node*> q;
    q.push(node);

    while (!q.empty()) {
        Node* curr = q.front(); q.pop();
        for (Node* neighbor : curr->neighbors) {
            if (!cloned.count(neighbor)) {
                cloned[neighbor] = new Node(neighbor->val);
                q.push(neighbor);
            }
            cloned[curr]->neighbors.push_back(cloned[neighbor]);
        }
    }
    return cloned[node];
}
```

BFS explores level by level.
Each node is cloned when first encountered (before entering the queue).
Neighbors are linked after the clone exists.

Same time and space complexity. Different traversal order.

---

## 🔍 Why Store in Map BEFORE Recursing?

This is the most critical detail.

```cpp
// CORRECT:
Node* clone = new Node(node->val);
cloned[node] = clone;              // store FIRST
for (neighbor : node->neighbors)   // THEN recurse
    clone->neighbors.push_back(cloneGraph(neighbor));

// WRONG:
Node* clone = new Node(node->val);
for (neighbor : node->neighbors)   // recurse FIRST
    clone->neighbors.push_back(cloneGraph(neighbor));
cloned[node] = clone;              // store AFTER -- TOO LATE!
```

In the wrong version, if neighbor points back to this node,
`cloneGraph(node)` is called again before the map entry exists.
Infinite recursion. Stack overflow.

The map entry must exist before any recursive call
that might circle back to this node.

---

### 🧠 Memory of the Mirror Web Law

-   **HashMap** `original → clone` serves as both visited set and clone registry
-   **DFS cloning:**
    1. Null check → return null
    2. Already in map → return existing clone (cycle breaker)
    3. Create clone → **store in map BEFORE recursing**
    4. For each neighbor → recursively clone → add to clone's neighbors
    5. Return clone
-   **Store before recurse** -- critical for handling cycles
-   **BFS alternative:** queue-based, clone on first encounter, link neighbors after
-   **Time:** O(N + E) -- every node and edge visited once
-   **Space:** O(N) -- hash map + recursion stack (or queue)
-   **Edge cases:**
    -   Null input → return null
    -   Single node, no neighbors → clone with empty list
    -   Fully connected graph → every node is every other's neighbor
    -   Self-referencing (if allowed) → map handles it

Thus is remembered the saga of **Clone Graph**,
where the Oracle built a mirror of the web --
DFS-ing through every node,
creating clones and registering them in a map
before exploring their neighbors --
so that when cycles circled back,
the map already held the answer,
and when the recursion unwound,
every clone was connected to its mirror neighbors --
a perfect deep copy of the original,
with not a single shared pointer. 🪞🕸️✨
