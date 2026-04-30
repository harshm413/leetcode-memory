## 🏔️🛤️ _The Gentlest Climb: The Path With Minimum Effort Saga_

> \_"In the Mountain Grid,
> every cell held an elevation.
> A traveler needed to walk from the top-left `(0,0)`
> to the bottom-right `(rows-1, cols-1)`.
>
> The traveler could move in 4 directions (up, down, left, right).
> The **effort** of a single step was the
> **absolute difference in elevation** between two adjacent cells.
>
> The **effort of a path** was the
> **MAXIMUM effort among all its steps** --
> not the sum, but the worst single step.
>
> The Oracle was commanded:
>
> **'Find the path that minimizes this maximum effort.'**
>
> This was NOT a standard shortest path problem.
> The cost of a path was not the SUM of edge weights --
> it was the MAXIMUM edge weight along the path.
>
> But the Oracle saw that **Dijkstra's Algorithm**
> could be adapted:
>
> **Instead of minimizing the SUM of distances,
> minimize the MAXIMUM effort seen so far.**
>
> Use a min-heap keyed on the maximum effort to reach each cell.
> Always process the cell with the smallest max-effort first.
> When moving to a neighbor, the new effort is:
> `max(current effort, abs(height difference))`.
>
> Modified Dijkstra. Same structure. Different relaxation."\_

---

This is the saga of **Path With Minimum Effort**.

Given a 2D grid `heights` of size `rows × cols`:

-   Find a path from `(0,0)` to `(rows-1, cols-1)`.
-   The effort of a step = `|heights[r1][c1] - heights[r2][c2]|`.
-   The effort of a path = **maximum** effort among all steps.
-   Minimize this maximum effort.

```
Input:
  [[1,2,2],
   [3,8,2],
   [5,3,5]]
Output: 2

Input:
  [[1,2,3],
   [3,8,4],
   [5,3,5]]
Output: 1

Input:
  [[1,2,1,1,1],
   [1,2,1,2,1],
   [1,2,1,2,1],
   [1,2,1,2,1],
   [1,1,1,2,1]]
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Dijkstra with Max Instead of Sum

In standard Dijkstra:

```
dist[v] = min(dist[v], dist[u] + weight(u, v))
```

Here, the "distance" is the maximum effort along the path:

```
effort[v] = min(effort[v], max(effort[u], |height[u] - height[v]|))
```

**`max(effort[u], |diff|)`** -- the effort to reach `v` through `u`
is the worse of: the effort to reach `u`, or this new step's effort.

**`min(effort[v], ...)`** -- among all paths to `v`,
keep the one with the smallest maximum effort.

The min-heap always processes the cell
with the globally smallest max-effort first --
just like Dijkstra processes the smallest distance first.

```
Time:  O(M × N × log(M × N)) -- heap operations on grid cells
Space: O(M × N)
```

---

### 📜 The Scroll of the Mountain Path

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cmath>
using namespace std;
```

---

## 🏔️ The Oracle's Modified Dijkstra Ritual

```cpp
int minimumEffortPath(vector<vector<int>>& heights) {
    int rows = heights.size();
    int cols = heights[0].size();
```

Measure the mountain grid.

---

### 🗺️ The Effort Matrix

```cpp
    vector<vector<int>> effort(rows, vector<int>(cols, INT_MAX));
```

`effort[r][c]` = the minimum possible max-effort
to reach cell `(r, c)` from `(0, 0)`.

All start at `INT_MAX` (unreached).

---

### 📦 The Min-Heap

```cpp
    // {max_effort_so_far, row, col}
    priority_queue<tuple<int,int,int>,
                   vector<tuple<int,int,int>>,
                   greater<tuple<int,int,int>>> pq;
```

A min-heap of `{effort, row, col}`.
The cell with the smallest max-effort is always on top.

`greater<>` flips C++'s default max-heap into a min-heap.

---

### 🏁 Start at (0, 0) with Effort 0

```cpp
    effort[0][0] = 0;
    pq.push({0, 0, 0});
```

The starting cell has effort 0 --
no steps taken, no effort spent.

---

### 🧭 The 4 Directions

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

Up, down, left, right. No diagonals in this problem.

---

## 🌊 The Modified Dijkstra Loop

```cpp
    while (!pq.empty()) {
        auto [curEffort, r, c] = pq.top();
        pq.pop();
```

Pop the cell with the smallest max-effort.

---

### 🎯 Reached the Destination?

```cpp
        if (r == rows - 1 && c == cols - 1) {
            return curEffort;
        }
```

The first time we pop the destination from the heap --
its effort is guaranteed to be the minimum possible.

Dijkstra's guarantee: the first time a node is popped,
its distance (effort) is final.

> _"The first time the destination rises to the top of the heap,
> it carries the gentlest path.
> No future path can be gentler --
> because the heap always serves the smallest first."_

---

### 🛡️ Stale Entry Check

```cpp
        if (curEffort > effort[r][c]) continue;
```

The heap may contain old entries with higher effort.
If this entry's effort is worse than what we already know --
it's stale. Skip it.

Same lazy deletion as standard Dijkstra.

---

### 🧭 Check All 4 Neighbors

```cpp
        for (auto& d : dirs) {
            int nr = r + d[0];
            int nc = c + d[1];
```

Compute each neighbor.

---

### 🛡️ Boundary Check

```cpp
            if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) {
                continue;
            }
```

Stay within the grid.

---

### 📐 Compute the Effort to Reach This Neighbor

```cpp
            int stepEffort = abs(heights[nr][nc] - heights[r][c]);
```

The effort of this single step =
absolute difference in elevation between the two cells.

---

### 🏔️ The Modified Relaxation

```cpp
            int newEffort = max(curEffort, stepEffort);
```

The effort to reach the neighbor through this path =
the WORSE of: the effort to reach the current cell,
or this step's effort.

**This is the key difference from standard Dijkstra.**

Standard: `newDist = dist[u] + weight`.
Here: `newEffort = max(effort[u], stepWeight)`.

The path's cost is its worst step, not the sum of all steps.

> _"The chain is only as strong as its weakest link.
> The path is only as gentle as its hardest step.
> The maximum effort defines the path."_

---

### ✅ If This Path Is Better -- Update and Push

```cpp
            if (newEffort < effort[nr][nc]) {
                effort[nr][nc] = newEffort;
                pq.push({newEffort, nr, nc});
            }
        }
    }
```

If this path to the neighbor has a smaller max-effort
than any previously known path → update and push to heap.

---

### 🏁 Safety Return

```cpp
    return 0;
}
```

Should never reach here (destination is always reachable
in a connected grid). Safety net.

---

### 🎺 The Trial of the Mountain Path

```cpp
int main() {
    vector<vector<int>> h1 = {{1,2,2},{3,8,2},{5,3,5}};
    cout << minimumEffortPath(h1) << endl; // expected: 2

    vector<vector<int>> h2 = {{1,2,3},{3,8,4},{5,3,5}};
    cout << minimumEffortPath(h2) << endl; // expected: 1

    vector<vector<int>> h3 = {
        {1,2,1,1,1},{1,2,1,2,1},{1,2,1,2,1},{1,2,1,2,1},{1,1,1,2,1}
    };
    cout << minimumEffortPath(h3) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for `[[1,2,2],[3,8,2],[5,3,5]]`:**

```
Heights:
  1 2 2
  3 8 2
  5 3 5
```

| Pop (effort, r, c) | Neighbors explored                          | Updates                    |
| ------------------- | ------------------------------------------- | -------------------------- |
| (0, 0, 0)          | (0,1): step=1, new=max(0,1)=1              | effort[0][1]=1             |
|                     | (1,0): step=2, new=max(0,2)=2              | effort[1][0]=2             |
| (1, 0, 1)          | (0,2): step=0, new=max(1,0)=1              | effort[0][2]=1             |
|                     | (1,1): step=6, new=max(1,6)=6              | effort[1][1]=6             |
| (1, 0, 2)          | (1,2): step=0, new=max(1,0)=1              | effort[1][2]=1             |
| (1, 1, 2)          | (2,2): step=3, new=max(1,3)=3              | effort[2][2]=3             |
| (2, 1, 0)          | (2,0): step=2, new=max(2,2)=2              | effort[2][0]=2             |
| (2, 2, 0)          | (2,1): step=2, new=max(2,2)=2              | effort[2][1]=2             |
| (2, 2, 1)          | (2,2): step=2, new=max(2,2)=2 < 3          | effort[2][2]=2             |
| (2, 2, 2)          | **DESTINATION!** Return 2.                  |                            |

**Answer: 2** ✓

The optimal path: (0,0)→(0,1)→(0,2)→(1,2)→(2,2)... wait,
let me check: the path through the bottom gives effort 2.
Path: (0,0)→(1,0)→(2,0)→(2,1)→(2,2).
Steps: |1-3|=2, |3-5|=2, |5-3|=2, |3-5|=2. Max = 2. ✓

---

**Trace for `[[1,2,1,1,1],[1,2,1,2,1],...]` (all 1s and 2s):**

There exists a path using only cells with value 1.
Every step between two `1` cells has effort `|1-1| = 0`.
Max effort = 0.

**Answer: 0** ✓ -- the path snakes through all the `1` cells.

---

## 🔍 Why Dijkstra and Not BFS?

BFS works for unweighted graphs (all edges cost 1).
Here, edges have different costs (elevation differences).
BFS would not find the optimal path.

Dijkstra's min-heap ensures we always process
the cell with the globally smallest effort first --
guaranteeing optimality.

---

## 🔍 Why Max Instead of Sum?

The problem defines path cost as the **maximum** step effort,
not the sum. This changes the relaxation:

| Standard Dijkstra          | This Problem                |
| -------------------------- | --------------------------- |
| `newDist = dist[u] + w`   | `newEffort = max(eff[u], w)`|
| Minimize total distance    | Minimize worst single step  |
| Sum of edges               | Maximum edge                |

The heap and the structure are identical.
Only the relaxation formula changes.

---

## 🔄 Binary Search Alternative

You can also binary search on the answer:

```
Binary search on effort value (0 to max height difference).
For each candidate effort:
  BFS/DFS: can you reach (n-1, m-1) using only steps with effort <= candidate?
  If yes → try smaller.
  If no → try larger.
```

Time: O(M × N × log(maxHeight)). Also valid.
Dijkstra is cleaner and more direct.

---

### 🧠 Memory of the Gentlest Climb Law

-   **Modified Dijkstra** -- min-heap on max-effort instead of sum
-   **Effort of a step:** `abs(heights[nr][nc] - heights[r][c])`
-   **Effort of a path:** `max(effort so far, this step's effort)`
-   **Relaxation:** `newEffort = max(curEffort, stepEffort)`
    -   If `newEffort < effort[nr][nc]` → update and push
-   **First pop of destination = answer** (Dijkstra guarantee)
-   **Lazy deletion:** skip stale heap entries (`curEffort > effort[r][c]`)
-   **Binary search alternative:** binary search on effort + BFS feasibility check
-   **Time:** O(M × N × log(M × N))
-   **Space:** O(M × N)
-   **Edge cases:**
    -   1×1 grid → effort = 0
    -   All same heights → effort = 0
    -   Steep cliff → must find the path avoiding it

Thus is remembered the saga of **Path With Minimum Effort**,
where the Oracle adapted Dijkstra's Algorithm
to the mountains --
replacing sum with max,
always processing the cell with the gentlest known path,
relaxing neighbors by asking
"is this step harder than anything before?" --
until the destination was reached
via the path whose hardest step
was as easy as possible. 🏔️🛤️✨
