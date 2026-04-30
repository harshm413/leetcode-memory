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
> The Oracle reached for Kahn's Algorithm again --
> the same BFS she used for Course Schedule I.
> But this time, instead of just counting completed nodes,
> she RECORDED the order in which they were dequeued.
>
> The dequeue order of Kahn's Algorithm
> IS a valid topological ordering.
>
> If a cycle existed -- some nodes were never dequeued --
> return an empty array."\_

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

## 🧠 The Oracle's Core Insight -- Kahn's Dequeue Order = Topological Order

In Course Schedule I, we counted completed nodes.
Here, we **record** them.

Every node dequeued by Kahn's Algorithm
has ALL its prerequisites already dequeued before it.
That's exactly what topological order means.

```
1. Build adjacency list + in-degree array.
2. Enqueue all nodes with in-degree 0.
3. BFS: dequeue → add to result → reduce neighbors' in-degree → enqueue if 0.
4. If result.size() == numCourses → return result.
   Else → cycle exists → return [].
```

```
Time:  O(V + E)
Space: O(V + E)
```

---

### 📜 The Scroll of the Ordered Scholars

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

Same setup as Course Schedule I:
-   `graph[b]` = courses that depend on `b`.
-   `inDegree[a]` = number of prerequisites for course `a`.

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

Courses with in-degree 0 can be taken immediately.
They have no prerequisites. They enter the queue first.

---

## 📋 Prepare the Result Array

```cpp
    vector<int> order;
```

`order` will hold the topological ordering --
the sequence in which courses should be taken.

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

**Why is this correct?**
Every course dequeued has in-degree 0 at the time of dequeuing.
That means every prerequisite it needed
was already dequeued (and recorded) earlier.

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

### 🎺 The Trial of the Ordered Scholars

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

Note: if 2 was dequeued before 1 (both had in-degree 0 at the same time),
the order would be `[0, 2, 1, 3]` -- also valid.
Topological sort is not unique when multiple nodes have in-degree 0 simultaneously.

---

**Full trace for numCourses=2, prerequisites=[[1,0],[0,1]]:**

```
In-degrees: [1, 1]
```

No course has in-degree 0. Queue is empty.
BFS never runs. order = [].

order.size() = 0 ≠ 2. **Answer: []** ✓

---

**Trace for numCourses=3, prerequisites=[[1,0],[2,1]]:**

```
Graph: 0 → [1], 1 → [2], 2 → []
In-degrees: [0, 1, 1]
```

Queue: [0]. Dequeue 0 → order=[0] → inDegree[1]=0 → enqueue 1.
Dequeue 1 → order=[0,1] → inDegree[2]=0 → enqueue 2.
Dequeue 2 → order=[0,1,2].

**Answer: [0, 1, 2]** ✓ -- a simple chain.

---

**Trace for numCourses=3, prerequisites=[] (no edges):**

```
In-degrees: [0, 0, 0]
```

All three courses have in-degree 0. All enqueued.
Dequeue order depends on queue: [0, 1, 2].

**Answer: [0, 1, 2]** ✓ -- no dependencies, any order works.

---

## 🔍 How This Differs from Course Schedule I

| Course Schedule I                 | Course Schedule II                |
| --------------------------------- | --------------------------------- |
| Return `true` or `false`          | Return the **ordering** or `[]`   |
| Count completed nodes             | Record completed nodes in `order` |
| `completed == numCourses`         | `order.size() == numCourses`      |
| Same Kahn's BFS                   | Same Kahn's BFS                   |

The only difference: **record instead of count**.
The BFS logic is identical.

---

## 🔍 Why Multiple Valid Orderings Exist

When multiple nodes have in-degree 0 at the same time,
any of them can be dequeued first.

```
0 → 2
1 → 2
```

Both 0 and 1 have in-degree 0. Either can go first.
Valid orderings: `[0, 1, 2]` or `[1, 0, 2]`.

The queue's internal ordering decides which comes first.
Both are correct topological sorts.

---

## 🔄 DFS Alternative -- Reverse Post-Order

DFS can also produce a topological sort:

```
For each unvisited node:
  DFS(node):
    Mark as visiting (GRAY)
    For each neighbor:
      If GRAY → cycle!
      If WHITE → recurse
    Mark as visited (BLACK)
    Push node to stack (post-order)

Reverse the stack → topological order
```

The last node to finish DFS (deepest dependency)
goes to the bottom of the stack.
Reversing gives the correct order.

Kahn's is generally preferred -- it's iterative,
naturally handles cycles (incomplete count),
and produces the order directly without reversing.

---

### 🧠 Memory of the Ordered Scholars Law

-   **Same as Course Schedule I** but record the dequeue order
-   **Kahn's Algorithm:** enqueue in-degree 0 → BFS → dequeue = topo order
-   **Dequeue order IS topological order** -- every node's prerequisites are dequeued first
-   **Cycle check:** `order.size() == numCourses` → valid; else → cycle → return `[]`
-   **Multiple valid orderings** exist when multiple nodes have in-degree 0 simultaneously
-   **DFS alternative:** reverse post-order gives topological sort
-   **Time:** O(V + E)
-   **Space:** O(V + E)
-   **Edge cases:**
    -   No prerequisites → any order is valid
    -   Cycle → return empty array
    -   Single course → return [0]
    -   Chain (0→1→2→3) → only one valid order

Thus is remembered the saga of **Course Schedule II**,
where the Oracle did not merely ask "can it be done?"
but demanded the exact order --
peeling the graph layer by layer with Kahn's Algorithm,
recording each course as it was freed from its prerequisites --
until the full topological order was written
or a cycle left the scroll forever incomplete. 📚📋✨
