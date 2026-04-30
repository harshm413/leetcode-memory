## 📚🔄 _The Web of Prerequisites: The Course Schedule Saga_

> \_"In the University of Directed Graphs,
> courses depended on other courses.
>
> `[1, 0]` meant: to take course 1,
> you must first complete course 0.
>
> The Oracle was commanded:
>
> **'Can all courses be completed?
> Or does a circular dependency exist --
> a cycle where A requires B,
> B requires C, and C requires A --
> making it impossible to finish any of them?'**
>
> The Oracle recognized this instantly:
>
> **This is cycle detection in a directed graph.**
>
> If the prerequisite graph has a cycle → impossible.
> If it has NO cycle → all courses can be completed.
>
> A directed graph with no cycles is a **DAG**
> (Directed Acyclic Graph).
> The question was simply: is this graph a DAG?
>
> Two weapons existed for this battle:
>
> **DFS with three-color marking** --
> detect back edges (cycles) during traversal.
>
> **BFS with Kahn's Algorithm (Topological Sort)** --
> peel off nodes with no prerequisites layer by layer.
> If all nodes are peeled → no cycle.
> If some remain → cycle exists.
>
> The Oracle mastered both."\_

---

This is the saga of **Course Schedule**.

Given `numCourses` and a list of `prerequisites`:

-   `prerequisites[i] = [a, b]` means course `a` requires course `b`.
-   Return `true` if all courses can be finished. `false` if a cycle exists.

```
Input:  numCourses = 2, prerequisites = [[1,0]]
Output: true   (take 0 first, then 1)

Input:  numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false  (0 requires 1, 1 requires 0 → cycle)
```

---

## 🧠 Approach One -- BFS (Kahn's Algorithm / Topological Sort)

Kahn's Algorithm peels the graph layer by layer:

```
1. Compute in-degree of every node.
2. Enqueue all nodes with in-degree 0 (no prerequisites).
3. BFS: for each dequeued node, reduce in-degree of its neighbors.
   If a neighbor's in-degree drops to 0 → enqueue it.
4. If all nodes were processed → no cycle → return true.
   If some remain → cycle → return false.
```

Nodes in a cycle never reach in-degree 0 --
they always have at least one unresolved prerequisite
pointing at them from within the cycle.

```
Time:  O(V + E) -- visit every node and edge once
Space: O(V + E) -- adjacency list + in-degree array + queue
```

---

### 📜 The Scroll of the Prerequisite Web

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏗️ Build the Adjacency List

```cpp
bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
    vector<vector<int>> graph(numCourses);
    vector<int> inDegree(numCourses, 0);
```

Two structures:

-   `graph[b]` = list of courses that depend on course `b`.
    If `b` is completed, these courses lose one prerequisite.

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

For each prerequisite `[a, b]` (a requires b):

**`graph[b].push_back(a)`** -- edge from `b` to `a`.
When `b` is completed, `a` loses one prerequisite.

**`inDegree[a]++`** -- course `a` has one more prerequisite.

> _"The edge points from the prerequisite to the dependent.
> 'I must be done before you can begin.'
> The in-degree counts how many blockers each course has."_

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

Courses with in-degree 0 have **no prerequisites**.
They can be taken immediately.

These are the starting points of the topological sort.

> _"The courses with no blockers go first.
> They owe nothing to anyone.
> They enter the queue freely."_

---

## ⏱️ Initialize the Completed Counter

```cpp
    int completed = 0;
```

`completed` tracked how many courses were successfully processed.
If it reaches `numCourses` → all courses can be finished.

---

## 🌊 BFS -- Peel the Graph Layer by Layer

```cpp
    while (!q.empty()) {
        int course = q.front();
        q.pop();
        completed++;
```

A course with in-degree 0 was dequeued.
It was "completed" -- one more course done.

---

### 🔁 Reduce In-Degree of Dependents

```cpp
        for (int next : graph[course]) {
            inDegree[next]--;
```

For every course that depended on the completed course:
its in-degree dropped by 1.
One of its prerequisites was now satisfied.

---

### 📥 If All Prerequisites Met -- Enqueue

```cpp
            if (inDegree[next] == 0) {
                q.push(next);
            }
        }
    }
```

If a dependent's in-degree reached 0 --
ALL its prerequisites were now completed.
It was ready to be taken. Enqueue it.

> _"When your last blocker falls,
> you are free to enter the queue.
> Your time has come."_

---

## 🏁 Did We Complete Everything?

```cpp
    return completed == numCourses;
}
```

If `completed == numCourses` → every course was processed.
No cycle existed. The graph was a DAG. Return `true`.

If `completed < numCourses` → some courses were never processed.
They were trapped in a cycle -- their in-degree never reached 0.
Return `false`.

> _"If any course remains with in-degree > 0,
> it is trapped in a cycle of mutual dependency.
> It can never be freed."_

---

### 🎺 The Trial of the Prerequisite Web

```cpp
int main() {
    vector<vector<int>> p1 = {{1,0}};
    cout << canFinish(2, p1) << endl; // expected: 1 (true)

    vector<vector<int>> p2 = {{1,0},{0,1}};
    cout << canFinish(2, p2) << endl; // expected: 0 (false)

    vector<vector<int>> p3 = {{1,0},{2,0},{3,1},{3,2}};
    cout << canFinish(4, p3) << endl; // expected: 1 (true)

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

**Initial queue:** course 0 (in-degree 0).

| Dequeue | completed | Reduce in-degree of | Enqueue (in-degree → 0) |
| ------- | --------- | ------------------- | ----------------------- |
| 0       | 1         | 1 (1→0), 2 (1→0)   | 1, 2                    |
| 1       | 2         | 3 (2→1)             | --                      |
| 2       | 3         | 3 (1→0)             | 3                       |
| 3       | 4         | --                  | --                      |

completed = 4 == numCourses. **Answer: true** ✓

Topological order: 0 → 1 → 2 → 3 (or 0 → 2 → 1 → 3).

---

**Full trace for numCourses=2, prerequisites=[[1,0],[0,1]]:**

```
Graph:
  0 → [1]
  1 → [0]

In-degrees: [1, 1]
```

**Initial queue:** empty! No course has in-degree 0.

BFS never runs. completed = 0.

0 ≠ 2. **Answer: false** ✓

Both courses are trapped in a cycle: 0 requires 1, 1 requires 0.
Neither can ever start.

---

**Trace for numCourses=2, prerequisites=[[1,0]]:**

```
In-degrees: [0, 1]
```

Queue: [0]. Dequeue 0 → completed=1 → reduce inDegree[1] to 0 → enqueue 1.
Dequeue 1 → completed=2.

2 == 2. **Answer: true** ✓

---

**Trace for numCourses=1, prerequisites=[]:**

No edges. In-degree of course 0 = 0. Queue: [0].
Dequeue 0 → completed=1. 1 == 1. **Answer: true** ✓

---

## 🔄 Approach Two -- DFS with Three-Color Marking

Instead of BFS, use DFS to detect **back edges** (cycles).

Each node has three states:

```
WHITE (0) = unvisited
GRAY  (1) = currently being explored (in the DFS stack)
BLACK (2) = fully explored (all descendants processed)
```

A **back edge** = an edge from a GRAY node to another GRAY node.
This means we've found a cycle.

```cpp
bool dfs(int node, vector<vector<int>>& graph, vector<int>& color) {
    color[node] = 1;  // GRAY -- entering

    for (int next : graph[node]) {
        if (color[next] == 1) return false;  // back edge → cycle!
        if (color[next] == 0) {
            if (!dfs(next, graph, color)) return false;
        }
    }

    color[node] = 2;  // BLACK -- fully explored
    return true;
}

bool canFinish(int n, vector<vector<int>>& prereqs) {
    vector<vector<int>> graph(n);
    for (auto& p : prereqs) graph[p[1]].push_back(p[0]);

    vector<int> color(n, 0);  // all WHITE
    for (int i = 0; i < n; i++) {
        if (color[i] == 0) {
            if (!dfs(i, graph, color)) return false;
        }
    }
    return true;
}
```

**GRAY → GRAY** = cycle detected → return false.
**GRAY → BLACK** = already fully explored → safe, skip.
**GRAY → WHITE** = unexplored → recurse.

When DFS finishes a node → mark BLACK (fully explored).

---

## 🔍 BFS (Kahn's) vs DFS (Three-Color) -- Which to Choose?

| Kahn's Algorithm (BFS)            | DFS Three-Color                   |
| --------------------------------- | --------------------------------- |
| Iterative (no recursion)          | Recursive (stack overflow risk)   |
| Naturally produces topological order | Can produce reverse topo order  |
| Counts completed nodes            | Detects back edges directly       |
| Easier to explain in interviews   | More "graph theory" flavored      |

Both are O(V + E). Both detect cycles correctly.
**Kahn's is recommended for interviews** -- it's cleaner
and directly answers "can we process all nodes?"

---

## 🌐 The Topological Sort Pattern

| Problem              | What it asks                        | Approach          |
| -------------------- | ----------------------------------- | ----------------- |
| Course Schedule      | Can all courses be finished? (cycle?)| Kahn's / DFS      |
| Course Schedule II   | Return the order (topo sort)        | Kahn's            |
| Alien Dictionary     | Find letter order from sorted words | Build graph + topo|
| Task Scheduler       | Minimum time with cooldown          | Greedy / topo     |

Topological sort is the foundation for all dependency problems.

---

## 🔍 How Cycles Are Handled

### "What happens to nodes stuck in a cycle? Won't they loop forever?"

**No.** Kahn's Algorithm never visits cycle nodes at all.

A cycle like `A → B → C → A` means:
-   A has in-degree ≥ 1 (C points to it).
-   B has in-degree ≥ 1 (A points to it).
-   C has in-degree ≥ 1 (B points to it).

None of them EVER reach in-degree 0.
They are never enqueued. Never dequeued. Never processed.

The BFS simply ignores them -- they sit untouched
while the rest of the graph is peeled away.

At the end, `completed < numCourses` reveals their existence.

> _"Cycle nodes are not trapped in an infinite loop.
> They are trapped in SILENCE.
> They never enter the queue.
> They never get a chance to run.
> Their in-degree never drops to zero
> because their blockers are each other."_

### "How is this different from DFS cycle detection?"

In DFS, you COULD accidentally revisit cycle nodes forever --
that's why the three-color system exists.

GRAY means "I'm currently exploring this path."
If you encounter a GRAY node again → you've looped back → cycle.

But in Kahn's BFS, there is NO risk of infinite loops
because nodes are only enqueued when in-degree hits 0.
Cycle nodes never hit 0. They're never touched. Period.

### "What if a cycle is connected to non-cycle nodes?"

```
0 → 1 → 2 → 3 → 2  (cycle between 2 and 3)
```

Node 0 has in-degree 0 → enqueued → processed → reduces in-degree of 1.
Node 1 reaches in-degree 0 → enqueued → processed → reduces in-degree of 2.
Node 2 has in-degree from both 1 AND 3. After 1 is processed, in-degree = 1 (from 3).
Node 3 has in-degree from 2. Never processed because 2 is never processed.

completed = 2 (nodes 0 and 1). numCourses = 4. 2 ≠ 4 → **false**.

The non-cycle nodes are processed normally.
The cycle nodes remain stuck. The count reveals the problem.

### "Can Kahn's tell you WHICH nodes are in the cycle?"

Yes! After BFS, any node with `inDegree[i] > 0` is either:
-   In a cycle, OR
-   Depends on a cycle (transitively blocked).

These are the nodes that could never be completed.

---

### 🧠 Memory of the Prerequisite Web Law

**Kahn's Algorithm (BFS):**
-   Build adjacency list + in-degree array
-   Enqueue all nodes with in-degree 0
-   BFS: dequeue → completed++ → reduce neighbors' in-degree → enqueue if 0
-   `completed == numCourses` → true (no cycle); else → false (cycle)

**DFS Three-Color:**
-   WHITE=unvisited, GRAY=in-progress, BLACK=done
-   GRAY → GRAY edge = cycle detected
-   Mark BLACK when all descendants explored

-   **Time:** O(V + E)
-   **Space:** O(V + E)
-   **Edge cases:**
    -   No prerequisites → always true
    -   Self-loop `[0,0]` → cycle → false
    -   Disconnected graph → Kahn's handles naturally (multiple sources)
    -   Single course → always true

Thus is remembered the saga of **Course Schedule**,
where the Oracle modeled prerequisites as a directed graph
and asked the simplest yet deepest question:
does a cycle exist?

Using Kahn's Algorithm, she peeled the graph layer by layer --
starting from courses with no blockers,
freeing dependents as prerequisites were completed --
until every course was processed
or a stubborn cycle refused to unravel,
its courses trapped forever
in a web of mutual dependency. 📚🔄✨
