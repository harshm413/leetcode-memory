## 🏊🌊 _The Tide That Opens the Path: The Swim in Rising Water Saga_

> \_"In the Grid of Elevations,
> every cell held a height.
> At time `t`, the water level rose to `t` --
> flooding every cell with elevation ≤ `t`.
>
> A swimmer stood at the top-left `(0, 0)`
> and needed to reach the bottom-right `(n-1, n-1)`.
> She could swim to any adjacent cell (up, down, left, right)
> as long as BOTH the current cell AND the destination cell
> were flooded (elevation ≤ current time `t`).
>
> The Oracle was commanded:
>
> **'What is the MINIMUM time `t`
> at which the swimmer can reach the destination?'**
>
> The Oracle recognized this instantly:
>
> **The cost of a path is the MAXIMUM elevation
> along that path.**
>
> At time `t`, all cells with elevation ≤ `t` are passable.
> The swimmer can complete the journey at the earliest time
> when there EXISTS a path where every cell has elevation ≤ `t`.
>
> This is: **minimize the maximum elevation on any path
> from (0,0) to (n-1,n-1).**
>
> The exact same pattern as **Path With Minimum Effort** --
> but instead of absolute differences between neighbors,
> the cost is the elevation of the cell itself.
>
> **Modified Dijkstra.**
> Min-heap keyed on the maximum elevation seen so far.
> Always process the cell with the smallest max-elevation first.
> The first time we reach the destination = the answer."\_

---

This is the saga of **Swim in Rising Water**.

Given an `n × n` grid where `grid[r][c]` is the elevation:

-   At time `t`, water level = `t`. Cells with elevation ≤ `t` are flooded.
-   You can swim to adjacent flooded cells (4 directions).
-   Start at `(0, 0)`, reach `(n-1, n-1)`.
-   Return the **minimum time** to reach the destination.

All values in the grid are unique integers from `0` to `n²-1`.

```
Input:  [[0,2],[1,3]]
Output: 3

Input:  [[0,1,2,3,4],
         [24,23,22,21,5],
         [12,13,14,15,16],
         [11,17,18,19,20],
         [10,9,8,7,6]]
Output: 16
```

---

## 🧠 The Oracle's Core Insight -- Minimize the Maximum Elevation

The swimmer can traverse a cell only when `t ≥ grid[r][c]`.
To complete the journey at time `t`, EVERY cell on the path
must have elevation ≤ `t`.

So the minimum time = the **minimum possible maximum elevation**
along any path from `(0,0)` to `(n-1,n-1)`.

This is identical to **Path With Minimum Effort**:
-   There: minimize the max absolute difference between adjacent cells.
-   Here: minimize the max elevation of any cell on the path.

**Modified Dijkstra:**
-   State: `{max_elevation_so_far, row, col}`.
-   Min-heap: always process the cell with the smallest max-elevation.
-   Relaxation: `newMax = max(currentMax, grid[nr][nc])`.
-   First pop of destination = answer.

```
Time:  O(n² log n²) = O(n² log n)
Space: O(n²)
```

---

### 📜 The Scroll of the Rising Tide

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏊 The Oracle's Modified Dijkstra Ritual

```cpp
int swimInWater(vector<vector<int>>& grid) {
    int n = grid.size();
```

Measure the grid.

---

### 🗺️ The Max-Elevation Matrix

```cpp
    vector<vector<int>> maxElev(n, vector<int>(n, INT_MAX));
```

`maxElev[r][c]` = the minimum possible max-elevation
to reach cell `(r, c)` from `(0, 0)`.

All start at `INT_MAX` (unreached).

---

### 📦 The Min-Heap

```cpp
    // {max_elevation_so_far, row, col}
    priority_queue<tuple<int,int,int>,
                   vector<tuple<int,int,int>>,
                   greater<tuple<int,int,int>>> pq;
```

Min-heap of `{maxElev, row, col}`.
The cell with the smallest max-elevation is always on top.

---

### 🏁 Start at (0, 0)

```cpp
    maxElev[0][0] = grid[0][0];
    pq.push({grid[0][0], 0, 0});
```

The starting cell's max-elevation = its own elevation.
(We must wait at least until `t = grid[0][0]` to even start.)

---

### 🧭 The 4 Directions

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

Up, down, left, right.

---

## 🌊 The Modified Dijkstra Loop

```cpp
    while (!pq.empty()) {
        auto [curMax, r, c] = pq.top();
        pq.pop();
```

Pop the cell with the smallest max-elevation.

---

### 🎯 Reached the Destination?

```cpp
        if (r == n - 1 && c == n - 1) {
            return curMax;
        }
```

The first time we pop the destination from the heap,
its max-elevation is guaranteed to be the minimum possible.

Dijkstra's guarantee: the first pop = optimal.

> _"The first time the destination rises to the top of the heap,
> it carries the gentlest tide.
> No future path can require a lower water level."_

---

### 🛡️ Stale Entry Check

```cpp
        if (curMax > maxElev[r][c]) continue;
```

If this entry's max-elevation is worse than what we already know,
it's stale. Skip.

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
            if (nr < 0 || nr >= n || nc < 0 || nc >= n) continue;
```

Stay within the grid.

---

### 📐 Compute the Max-Elevation to Reach This Neighbor

```cpp
            int newMax = max(curMax, grid[nr][nc]);
```

The max-elevation to reach the neighbor through this path =
the WORSE of: the max-elevation to reach the current cell,
or the neighbor's own elevation.

**This is the key relaxation.**

To swim to the neighbor, the water must be at least `grid[nr][nc]`.
And to have reached the current cell, the water was at least `curMax`.
So the time needed = `max(curMax, grid[nr][nc])`.

> _"The tide must be high enough to flood EVERY cell on the path.
> The bottleneck is the tallest cell encountered so far."_

---

### ✅ If This Path Is Better -- Update and Push

```cpp
            if (newMax < maxElev[nr][nc]) {
                maxElev[nr][nc] = newMax;
                pq.push({newMax, nr, nc});
            }
        }
    }
```

If this path offers a lower max-elevation to the neighbor
than any previously known path → update and push to heap.

---

### 🏁 Safety Return

```cpp
    return -1;
}
```

Should never reach here (destination is always reachable).

---

### 🎺 The Trial of the Rising Tide

```cpp
int main() {
    vector<vector<int>> g1 = {{0,2},{1,3}};
    cout << swimInWater(g1) << endl; // expected: 3

    vector<vector<int>> g2 = {
        {0,1,2,3,4},
        {24,23,22,21,5},
        {12,13,14,15,16},
        {11,17,18,19,20},
        {10,9,8,7,6}
    };
    cout << swimInWater(g2) << endl; // expected: 16

    return 0;
}
```

---

**Full trace for grid = [[0,2],[1,3]]:**

```
Grid:
  0  2
  1  3

Start: (0,0), elevation 0.
Destination: (1,1), elevation 3.
```

**Initial:** maxElev[0][0] = 0. Push {0, 0, 0}.

| Pop (max, r, c) | Neighbors                                    | Updates                    |
| ---------------- | -------------------------------------------- | -------------------------- |
| (0, 0, 0)       | (0,1): max(0,2)=2. (1,0): max(0,1)=1.       | maxElev[0][1]=2, maxElev[1][0]=1 |
| (1, 1, 0)       | (0,0): max(1,0)=1 > 0, skip. (1,1): max(1,3)=3. | maxElev[1][1]=3 |
| (2, 0, 1)       | (0,0): skip. (1,1): max(2,3)=3, not < 3, skip. | no updates |
| (3, 1, 1)       | **DESTINATION!** Return 3.                   |                            |

**Answer: 3** ✓

At time t=3, water floods all cells (0≤3, 2≤3, 1≤3, 3≤3).
Path: (0,0)→(1,0)→(1,1). Max elevation on path = max(0, 1, 3) = 3.

---

**Trace for the 5×5 grid (answer = 16):**

The optimal path snakes along the border:
```
(0,0)→(0,1)→(0,2)→(0,3)→(0,4)→(1,4)→... →(4,4)→...→(4,0)→(3,0)→(2,0)→(2,1)→(2,2)→(2,3)→(2,4)
```

Wait -- the actual optimal path goes:
```
0→1→2→3→4→5→6→7→8→9→10→11→12→13→14→15→16
```

Following the cells with values 0 through 16 in order
(they form a connected path around the border and through row 2).
The maximum elevation on this path = 16.

**Answer: 16** ✓

---

## 🔍 Connection to Path With Minimum Effort

| Path With Minimum Effort          | Swim in Rising Water              |
| --------------------------------- | --------------------------------- |
| Minimize max **difference** between adjacent cells | Minimize max **elevation** of any cell |
| `newMax = max(curMax, abs(h[nr][nc] - h[r][c]))` | `newMax = max(curMax, grid[nr][nc])` |
| Edge weight = difference           | Edge weight = destination cell's elevation |
| Same Dijkstra structure            | Same Dijkstra structure            |

The only difference is what constitutes the "cost" of moving to a neighbor.
The algorithm is identical.

---

## 🔄 Binary Search Alternative

Binary search on the answer `t`:

```
For each candidate time t:
  BFS/DFS: can you reach (n-1,n-1) using only cells with elevation ≤ t?
  If yes → try smaller t.
  If no → try larger t.
```

```cpp
int swimInWater(vector<vector<int>>& grid) {
    int n = grid.size();
    int left = max(grid[0][0], grid[n-1][n-1]);
    int right = n * n - 1;
    int answer = right;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        // BFS: can we reach (n-1,n-1) using only cells with elevation <= mid?
        vector<vector<bool>> visited(n, vector<bool>(n, false));
        queue<pair<int,int>> q;
        if (grid[0][0] <= mid) {
            q.push({0, 0});
            visited[0][0] = true;
        }
        bool found = false;
        int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
        while (!q.empty() && !found) {
            auto [r, c] = q.front(); q.pop();
            if (r == n-1 && c == n-1) { found = true; break; }
            for (auto& d : dirs) {
                int nr = r+d[0], nc = c+d[1];
                if (nr>=0 && nr<n && nc>=0 && nc<n
                    && !visited[nr][nc] && grid[nr][nc] <= mid) {
                    visited[nr][nc] = true;
                    q.push({nr, nc});
                }
            }
        }
        if (found) { answer = mid; right = mid - 1; }
        else { left = mid + 1; }
    }
    return answer;
}
```

```
Time:  O(n² × log(n²)) = O(n² log n)
Space: O(n²)
```

Same complexity as Dijkstra. Dijkstra is more elegant.
Binary search is a valid alternative to mention in interviews.

---

## 🔍 Why the Start Cell's Elevation Matters

```cpp
maxElev[0][0] = grid[0][0];
```

Even before moving, the swimmer must wait until `t ≥ grid[0][0]`.
The starting cell must be flooded before the journey can begin.

Similarly, the answer is always ≥ `grid[n-1][n-1]`
(the destination must also be flooded).

So the answer is at least `max(grid[0][0], grid[n-1][n-1])`.

---

### 🧠 Memory of the Rising Tide Law

-   **Modified Dijkstra** -- min-heap on max-elevation (not sum)
-   **State:** `{max_elevation_so_far, row, col}`
-   **Relaxation:** `newMax = max(curMax, grid[nr][nc])`
    -   If `newMax < maxElev[nr][nc]` → update and push
-   **First pop of destination = answer** (Dijkstra guarantee)
-   **Same pattern as Path With Minimum Effort** -- minimize max along path
-   **Binary search alternative:** binary search on `t` + BFS feasibility check
-   **Start cell matters:** must wait until `t ≥ grid[0][0]`
-   **Time:** O(n² log n)
-   **Space:** O(n²)
-   **Edge cases:**
    -   1×1 grid → return grid[0][0]
    -   Start or end has the highest elevation → that's the answer
    -   All cells have same value → return that value

Thus is remembered the saga of **Swim in Rising Water**,
where the Oracle adapted Dijkstra to the rising tide --
tracking the maximum elevation along every path,
always processing the cell with the gentlest flood level,
relaxing neighbors by asking
"is this cell taller than anything before?" --
until the destination was reached
at the earliest possible time
when the tide had risen just enough
to open a path through the grid. 🏊🌊✨
