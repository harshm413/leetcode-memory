## 📚📋 _The Order of the Scholars: The Course Schedule II Saga_

> \_"The Oracle had already answered the first question:
> CAN all courses be completed?
>
> Now the King demanded more:
>
> **'If they can be completed --
> tell me the ORDER in which to take them.
> A valid ordering where every prerequisite
> is completed before the course that needs it.'**
>
> This was **Topological Sort** --
> not just detecting if a DAG exists,
> but producing the actual ordering.
>
> Two weapons existed for this quest:
>
> **BFS (Kahn's Algorithm)** --
> peel nodes with no prerequisites layer by layer.
> The dequeue order IS the topological order.
>
> **DFS (Reverse Post-Order)** --
> explore every path to its deepest end.
> When a node finishes exploring all its descendants,
> push it onto a stack.
> The stack, reversed, gives the topological order.
>
> Both produce valid orderings.
> Both detect cycles.
> The Oracle mastered both."\_

---

This is the saga of **Course Schedule II**.

Given `numCourses` and `prerequisites`:

-   Return a valid **topological ordering** of all courses.
-   If impossible (cycle exists), return an **empty array**.
-   Multiple valid orderings may exist -- return any one.

```
Input:  numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
Output: [0,1,2,3] or [0,2,1,3]

Input:  numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: []  (cycle)

Input:  numCourses = 1, prerequisites = []
Output: [0]
```

---

## 🧠 Approach One -- BFS (Kahn's Algorithm)

Kahn's Algorithm peels the graph layer by layer:

```
1. Compute in-degree of every node.
2. Enqueue all nodes with in-degree 0 (no prerequisites).
3. BFS: dequeue → add to result → reduce neighbors' in-degree → enqueue if 0.
4. If result.size() == numCourses → return result.
   Else → cycle exists → return [].
```

The dequeue order IS the topological order --
every node dequeued has ALL its prerequisites already dequeued before it.

```
Time:  O(V + E)
Space: O(V + E)
```

---

### 📜 The Scroll of the Ordered Scholars (BFS)

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏗️ Build the Adjacency List and In-Degree Array

```cpp
vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
    vector<vector<int>> graph(numCourses);
    vector<int> inDegree(numCourses, 0);
```

Two structures:
-   `graph[b]` = courses that depend on course `b`.
-   `inDegree[a]` = how many prerequisites course `a` still needs.

---

```cpp
    for (auto& p : prerequisites) {
        int a = p[0];
        int b = p[1];
        graph[b].push_back(a);
        inDegree[a]++;
    }
```

For each `[a, b]` (a requires b):
edge from `b → a`, and `inDegree[a]++`.

> _"The edge says: when b is done, a loses one blocker.
> The in-degree counts how many blockers remain."_

---

## 📥 Enqueue All Courses with No Prerequisites

```cpp
    queue<int> q;
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) {
            q.push(i);
        }
    }
```

Courses with in-degree 0 have no prerequisites.
They can be taken immediately. They enter the queue first.

> _"The courses with no blockers go first.
> They owe nothing to anyone.
> They enter the queue freely."_

---

## 📋 Prepare the Result Array

```cpp
    vector<int> order;
```

`order` will hold the topological ordering.

---

## 🌊 BFS -- Peel and Record

```cpp
    while (!q.empty()) {
        int course = q.front();
        q.pop();
```

A course with in-degree 0 was dequeued.
All its prerequisites have been completed.

---

### 📝 Record This Course in the Order

```cpp
        order.push_back(course);
```

This course was added to the topological order.

> _"You enter the order only when all your blockers
> have already entered before you.
> This is the law of topological sort."_

---

### 🔁 Reduce In-Degree of Dependents

```cpp
        for (int next : graph[course]) {
            inDegree[next]--;
```

Every course that depended on the completed course
lost one prerequisite.

---

### 📥 Enqueue If All Prerequisites Met

```cpp
            if (inDegree[next] == 0) {
                q.push(next);
            }
        }
    }
```

If a dependent's in-degree reached 0 --
all its prerequisites were done. Enqueue it.

---

## 🏁 Check for Cycle and Return

```cpp
    if ((int)order.size() == numCourses) {
        return order;
    }
    return {};
}
```

If `order` contains all courses → valid topological sort. Return it.

If `order` is shorter → some courses were never dequeued.
They were trapped in a cycle. Return empty array.

> _"If the order is complete -- every scholar found their place.
> If it is incomplete -- a cycle of mutual dependency
> left some scholars forever waiting."_

---

### 🎺 The Trial of the Ordered Scholars (BFS)

```cpp
int main() {
    vector<vector<int>> p1 = {{1,0},{2,0},{3,1},{3,2}};
    auto r1 = findOrder(4, p1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 0 1 2 3 (or 0 2 1 3)

    vector<vector<int>> p2 = {{1,0},{0,1}};
    auto r2 = findOrder(2, p2);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: (empty)

    vector<vector<int>> p3 = {};
    auto r3 = findOrder(1, p3);
    for (int x : r3) cout << x << " "; cout << endl;
    // expected: 0

    return 0;
}
```

---

**Full trace for numCourses=4, prerequisites=[[1,0],[2,0],[3,1],[3,2]]:**

```
Graph:
  0 → [1, 2]
  1 → [3]
  2 → [3]
  3 → []

In-degrees: [0, 1, 1, 2]
```

**Initial queue:** [0] (only course with in-degree 0).

| Dequeue | order after    | Reduce in-degree of | Enqueue         |
| ------- | -------------- | ------------------- | --------------- |
| 0       | [0]            | 1 (1→0), 2 (1→0)   | 1, 2            |
| 1       | [0, 1]         | 3 (2→1)             | --              |
| 2       | [0, 1, 2]      | 3 (1→0)             | 3               |
| 3       | [0, 1, 2, 3]   | --                  | --              |

order.size() = 4 == numCourses. **Answer: [0, 1, 2, 3]** ✓

---

**Full trace for numCourses=2, prerequisites=[[1,0],[0,1]]:**

```
In-degrees: [1, 1]
```

No course has in-degree 0. Queue is empty.
BFS never runs. order = [].

order.size() = 0 ≠ 2. **Answer: []** ✓

Both courses are trapped in a cycle: 0 requires 1, 1 requires 0.
Neither can ever start.

---

---

## 🧠 Approach Two -- DFS (Reverse Post-Order)

The DFS approach uses the **three-color system**
and a **stack** to build the topological order.

### The Core Idea

In DFS, the **last** node to finish exploring all its descendants
is the one with the deepest dependencies.
It should come FIRST in topological order.

By pushing nodes onto a stack AFTER all their descendants
are fully explored (post-order), then reversing --
we get a valid topological order.

```
WHITE (0) = unvisited
GRAY  (1) = currently being explored (in the DFS stack)
BLACK (2) = fully explored (all descendants processed)
```

**GRAY → GRAY edge = cycle detected → return empty.**

When a node turns BLACK (all descendants done) → push to stack.
Reverse the stack → topological order.

**Why does this work?**

If edge `u → v` exists (u is prerequisite of v):
DFS from u will explore v BEFORE finishing u.
So v is pushed to the stack BEFORE u.
When reversed: u comes before v. Correct.

```
Time:  O(V + E)
Space: O(V + E)
```

---

### 📜 The Scroll of the Ordered Scholars (DFS)

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## 🔮 The DFS Function -- Explore and Push Post-Order

```cpp
bool dfs(int node, vector<vector<int>>& graph,
         vector<int>& color, stack<int>& result) {
```

The Oracle entered a node to explore its entire subtree.

---

### 🔵 Mark GRAY -- Entering This Node

```cpp
    color[node] = 1;
```

This node is now on the current DFS path.
If we encounter it again → cycle.

> _"I am being explored.
> If anyone on my path meets me again --
> we have formed a loop."_

---

### 🔁 Explore Every Outgoing Edge

```cpp
    for (int next : graph[node]) {
```

Check every course that depends on this one.

---

### 🔴 GRAY Neighbor → Cycle Detected!

```cpp
        if (color[next] == 1) {
            return false;
        }
```

The neighbor is GRAY -- still being explored.
We've found a back edge. A cycle exists.
Topological sort is impossible. Return `false`.

> _"I reached a node that is still on my current path.
> The path loops back on itself.
> A cycle. No valid ordering exists."_

---

### ⬜ WHITE Neighbor → Recurse

```cpp
        if (color[next] == 0) {
            if (!dfs(next, graph, color, result)) {
                return false;
            }
        }
```

The neighbor is unvisited. Explore it.
If the recursion finds a cycle → propagate `false` upward.

BLACK neighbors (color 2) are already fully explored → skip.
They're already on the stack. No action needed.

---

### ⬛ Mark BLACK -- All Descendants Done → Push to Stack

```cpp
    color[node] = 2;
    result.push(node);
    return true;
}
```

All descendants of this node have been fully explored
and pushed to the stack.

Now push THIS node. It goes on TOP of its descendants.
When we reverse the stack later,
this node will come BEFORE its descendants. Correct.

> _"All my descendants are done.
> They are already on the stack below me.
> Now I take my place on top.
> When the stack is reversed --
> I will come first, they will follow.
> Prerequisites before dependents."_

---

## 📋 The Main Function (DFS)

```cpp
vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
    vector<vector<int>> graph(numCourses);
```

Build the adjacency list.

---

```cpp
    for (auto& p : prerequisites) {
        int a = p[0];
        int b = p[1];
        graph[b].push_back(a);
    }
```

For each `[a, b]` (a requires b): edge from `b → a`.
Same direction as the BFS approach.

---

### 🎨 Initialize Colors and Stack

```cpp
    vector<int> color(numCourses, 0);
    stack<int> result;
```

All nodes start WHITE (unvisited).
The stack will accumulate nodes in reverse topological order.

---

### 🔁 DFS from Every Unvisited Node

```cpp
    for (int i = 0; i < numCourses; i++) {
        if (color[i] == 0) {
            if (!dfs(i, graph, color, result)) {
                return {};
            }
        }
    }
```

Start DFS from every WHITE node.
This handles disconnected components --
courses with no relationship to each other.

If any DFS call detects a cycle → return empty array.

---

### 📤 Pop the Stack → Topological Order

```cpp
    vector<int> order;
    while (!result.empty()) {
        order.push_back(result.top());
        result.pop();
    }
    return order;
}
```

The stack holds nodes in reverse topological order
(deepest dependencies on bottom, sources on top).

Popping gives the correct order:
sources first, then their dependents, then deeper dependents.

---

### 🎺 The Trial of the Ordered Scholars (DFS)

```cpp
int main() {
    vector<vector<int>> p1 = {{1,0},{2,0},{3,1},{3,2}};
    auto r1 = findOrder(4, p1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 0 2 1 3 (or 0 1 2 3 -- depends on DFS order)

    vector<vector<int>> p2 = {{1,0},{0,1}};
    auto r2 = findOrder(2, p2);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: (empty)

    return 0;
}
```

---

**Full DFS trace for numCourses=4, prerequisites=[[1,0],[2,0],[3,1],[3,2]]:**

```
Graph:
  0 → [1, 2]
  1 → [3]
  2 → [3]
  3 → []
```

**DFS from node 0:**

```
dfs(0): GRAY.
  → neighbor 1: WHITE → recurse.
    dfs(1): GRAY.
      → neighbor 3: WHITE → recurse.
        dfs(3): GRAY.
          → no neighbors.
        color[3] = BLACK. Push 3. Stack: [3]
      → return true.
    color[1] = BLACK. Push 1. Stack: [3, 1]
  → neighbor 2: WHITE → recurse.
    dfs(2): GRAY.
      → neighbor 3: BLACK → skip.
    color[2] = BLACK. Push 2. Stack: [3, 1, 2]
  → return true.
color[0] = BLACK. Push 0. Stack: [3, 1, 2, 0]
```

**Pop stack:** 0, 2, 1, 3.

**Answer: [0, 2, 1, 3]** ✓

Node 3 was pushed first (deepest dependency).
Node 0 was pushed last (source, no prerequisites).
Reversed: 0 comes first, 3 comes last. Correct.

---

**DFS trace for cycle: numCourses=2, prerequisites=[[1,0],[0,1]]:**

```
Graph:
  0 → [1]
  1 → [0]
```

```
dfs(0): GRAY.
  → neighbor 1: WHITE → recurse.
    dfs(1): GRAY.
      → neighbor 0: GRAY → CYCLE! Return false.
    Return false.
  Return false.
```

**Answer: []** ✓

Node 0 is GRAY. While exploring its descendant 1,
we find an edge back to 0 (still GRAY).
Back edge detected. Cycle confirmed.

---

**DFS trace for no edges: numCourses=3, prerequisites=[]:**

```
Graph: 0→[], 1→[], 2→[]
```

```
dfs(0): GRAY. No neighbors. BLACK. Push 0. Stack: [0]
dfs(1): GRAY. No neighbors. BLACK. Push 1. Stack: [0, 1]
dfs(2): GRAY. No neighbors. BLACK. Push 2. Stack: [0, 1, 2]
```

Pop: 2, 1, 0.

**Answer: [2, 1, 0]** ✓ (any order is valid when no dependencies exist)

---

## 🔍 Why the Stack Gives Correct Order

Consider edge `u → v` (u must come before v).

When DFS explores `u`, it will eventually reach `v`
(either directly or through a chain).

`v` finishes exploring BEFORE `u` finishes
(because `v` is deeper in the DFS tree).

So `v` is pushed to the stack BEFORE `u`.
Stack (bottom to top): `..., v, ..., u`.

When popped: `u` comes out before `v`. Correct!

> _"The deepest nodes finish first and sink to the bottom.
> The sources finish last and sit on top.
> Popping gives sources first -- the natural order."_

---

## 🔍 BFS (Kahn's) vs DFS (Reverse Post-Order)

| Kahn's Algorithm (BFS)            | DFS Reverse Post-Order            |
| --------------------------------- | --------------------------------- |
| Iterative (no recursion)          | Recursive (stack overflow risk)   |
| Produces order directly            | Produces reverse order (needs stack) |
| Cycle detection: count < n        | Cycle detection: GRAY→GRAY edge   |
| Easier to explain in interviews   | More "graph theory" flavored      |
| No risk of stack overflow          | Deep graphs may overflow          |
| Naturally handles disconnected     | Outer loop handles disconnected   |

Both are O(V + E). Both detect cycles correctly.
Both produce valid topological orderings (possibly different ones).

**Interview recommendation:**
Lead with Kahn's (cleaner, iterative, direct).
Mention DFS as an alternative to show depth of knowledge.

---

## 🔍 How This Differs from Course Schedule I

| Course Schedule I                 | Course Schedule II                |
| --------------------------------- | --------------------------------- |
| Return `true` or `false`          | Return the **ordering** or `[]`   |
| "Does a valid order exist?"       | "Give me the actual order"        |
| Count completed nodes             | Record completed nodes             |
| `completed == numCourses`         | `order.size() == numCourses`      |

The graph building and traversal logic is identical.
The only difference: **record instead of count**.

---

## 🔍 Why Multiple Valid Orderings Exist

When multiple nodes have in-degree 0 at the same time,
any of them can go first.

```
0 → 2
1 → 2
```

Both 0 and 1 have in-degree 0. Either can go first.
Valid orderings: `[0, 1, 2]` or `[1, 0, 2]`.

Kahn's produces one ordering based on queue order (FIFO).
DFS produces a different ordering based on traversal order.
Both are correct.

---

### 🧠 Memory of the Ordered Scholars Law

**BFS (Kahn's Algorithm):**
-   Build adjacency list + in-degree array
-   Enqueue all nodes with in-degree 0
-   BFS: dequeue → record in order → reduce neighbors' in-degree → enqueue if 0
-   `order.size() == numCourses` → valid; else → cycle → return `[]`
-   Dequeue order IS topological order

**DFS (Reverse Post-Order):**
-   Three colors: WHITE=unvisited, GRAY=in-progress, BLACK=done
-   DFS from every WHITE node
-   GRAY → GRAY edge = cycle → return `[]`
-   When node turns BLACK → push to stack
-   Pop stack → topological order
-   Deepest dependencies pushed first, sources pushed last

-   **Time:** O(V + E) for both
-   **Space:** O(V + E) for both
-   **Edge cases:**
    -   No prerequisites → any order is valid
    -   Cycle → return empty array
    -   Single course → return [0]
    -   Chain (0→1→2→3) → only one valid order
    -   Disconnected → both approaches handle naturally

Thus is remembered the saga of **Course Schedule II**,
where the Oracle did not merely ask "can it be done?"
but demanded the exact order --

With Kahn's, she peeled the graph layer by layer,
recording each course as it was freed from its prerequisites.

With DFS, she dove to the deepest dependencies first,
pushing each completed node onto a stack --
so that when the stack was reversed,
sources came first and dependents followed --

Until the full topological order was written
or a cycle left the scroll forever incomplete. 📚📋✨
