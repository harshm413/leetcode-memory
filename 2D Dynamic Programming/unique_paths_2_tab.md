## 🗺️🧱📋 _The Grid with Obstacles (Tabulation): The Unique Paths II Saga_

> \_"The Oracle had solved the obstructed grid with memoization.
> Now she built the answer bottom-up.
>
> The grid was filled cell by cell, just like Unique Paths I.
> But every obstacle cell was set to 0 --
> and the base cases for the top row and left column
> needed careful handling,
> because an obstacle in the first row
> blocks everything after it."\_

---

This is the saga of **Unique Paths II (Tabulation)**.

Same problem:
-   Grid with obstacles (`1`). Open cells (`0`).
-   Count paths from `(0,0)` to `(m-1, n-1)`, moving only down or right.

```
Input:  [[0,0,0],[0,1,0],[0,0,0]]  →  Output: 2
Input:  [[0,1],[0,0]]              →  Output: 1
Input:  [[1,0]]                    →  Output: 0
```

---

## 🧠 What Changes from Unique Paths I Tabulation?

Two things:

**1. Obstacle cells get `dp[i][j] = 0`.**
No path passes through a wall.

**2. The top row and left column are no longer all 1s.**
In Unique Paths I, we blindly set them to 1.
Now, an obstacle in the top row at `(0, 3)` means
`(0, 3)`, `(0, 4)`, `(0, 5)`, ... are ALL 0.
The straight-line path is broken. Everything after the obstacle is unreachable.

Same for the left column.

---

### 📜 The Scroll of the Bottom-Up Obstructed Grid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Grid

```cpp
int uniquePathsWithObstacles(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
```

---

### 🛑 Start or end blocked?

```cpp
    if (grid[0][0] == 1 || grid[m - 1][n - 1] == 1) return 0;
```

If either endpoint is an obstacle -- no paths exist.

---

### Build the dp table

```cpp
    vector<vector<int>> dp(m, vector<int>(n, 0));
```

All cells start at 0. We'll fill in the reachable ones.

---

### Fill the Top Row -- But Stop at the First Obstacle

```cpp
    for (int j = 0; j < n; j++) {
        if (grid[0][j] == 1) break;
        dp[0][j] = 1;
    }
```

Walk the top row left to right.
Each open cell gets 1 (one straight-line path from `(0,0)`).

**But the moment we hit an obstacle -- STOP.**
Everything after it in this row is unreachable via the top-row path.

Why `break` and not `continue`?
Because the straight-line path is BLOCKED.
Cell `(0, 5)` can only be reached from `(0, 4)` in the top row.
If `(0, 3)` is an obstacle, `(0, 4)` is unreachable,
so `(0, 5)` is unreachable too. The blockage cascades.

`break` stops filling. All remaining cells stay 0. Correct.

---

### Fill the Left Column -- Same Logic

```cpp
    for (int i = 0; i < m; i++) {
        if (grid[i][0] == 1) break;
        dp[i][0] = 1;
    }
```

Walk the left column top to bottom.
Each open cell gets 1. Stop at the first obstacle.

---

### Fill the Rest -- Row by Row

```cpp
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
```

---

### 🧱 Obstacle? Zero.

```cpp
            if (grid[i][j] == 1) {
                dp[i][j] = 0;
                continue;
            }
```

If this cell is an obstacle -- it stays 0.
No path passes through it. Skip to the next cell.

Why explicitly set to 0 and continue?
Because the array was initialized to 0, this line is technically redundant.
But it makes the intent CRYSTAL CLEAR to anyone reading the code:
"this cell is an obstacle, I'm deliberately setting it to zero."

In a whiteboard setting, being explicit is better than being clever.

---

### The familiar recurrence

```cpp
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        }
    }
```

Paths from above + paths from the left.
Identical to Unique Paths I.

If the cell above was an obstacle → `dp[i-1][j] = 0` → contributes nothing.
If the cell to the left was an obstacle → `dp[i][j-1] = 0` → contributes nothing.
The zeros from obstacles propagate naturally through the addition.

---

### The answer

```cpp
    return dp[m - 1][n - 1];
}
```

---

### 🎺 The Trial of the Bottom-Up Obstructed Grid

```cpp
int main() {
    vector<vector<int>> g1 = {{0,0,0},{0,1,0},{0,0,0}};
    cout << uniquePathsWithObstacles(g1) << endl; // expected: 2

    vector<vector<int>> g2 = {{0,1},{0,0}};
    cout << uniquePathsWithObstacles(g2) << endl; // expected: 1

    vector<vector<int>> g3 = {{1,0}};
    cout << uniquePathsWithObstacles(g3) << endl; // expected: 0

    return 0;
}
```

---

**Full table for grid = [[0,0,0],[0,1,0],[0,0,0]]:**

**Top row:** no obstacles → `dp[0] = [1, 1, 1]`.
**Left column:** no obstacles → `dp[0][0]=1, dp[1][0]=1, dp[2][0]=1`.

| Cell   | Obstacle? | dp[i-1][j] | dp[i][j-1] | dp[i][j] |
| ------ | --------- | ---------- | ---------- | --------- |
| (1,1)  | **YES**   | --         | --         | **0**     |
| (1,2)  | No        | dp[0][2]=1 | dp[1][1]=0 | 1+0 = 1  |
| (2,1)  | No        | dp[1][1]=0 | dp[2][0]=1 | 0+1 = 1  |
| (2,2)  | No        | dp[1][2]=1 | dp[2][1]=1 | 1+1 = **2** |

```
dp grid:
  1  1  1
  1  0  1
  1  1  2
```

**Answer: 2** ✓

The obstacle at `(1,1)` has `dp = 0`.
Its neighbors `(1,2)` and `(2,1)` each get only 1 path
(from the side that ISN'T blocked).
The destination sums both: 1+1 = 2.

---

**Table for grid = [[0,1],[0,0]]:**

**Top row:** `(0,0)` = 1. `(0,1)` = obstacle → `break`. dp[0] = [1, 0].
**Left column:** `(0,0)` = 1. `(1,0)` = 1. No obstacles.

| Cell   | dp[i-1][j] | dp[i][j-1] | dp[i][j] |
| ------ | ---------- | ---------- | --------- |
| (1,1)  | dp[0][1]=0 | dp[1][0]=1 | 0+1 = 1  |

```
dp grid:
  1  0
  1  1
```

**Answer: 1** ✓ (Down → Right. The top-right is blocked.)

---

**Table for grid = [[0,0,0,0],[0,0,1,0],[0,0,0,0]]:**

**Top row:** all open → [1, 1, 1, 1].
**Left column:** all open → dp[0][0]=1, dp[1][0]=1, dp[2][0]=1.

```
dp grid:
  1  1  1  1
  1  2  0  1
  1  3  3  4
```

`(1,2)` is obstacle → 0. Its right neighbor `(1,3)` gets only from above (1).
`(2,2)` gets from above (0) + left (3) = 3.
`(2,3)` gets from above (1) + left (3) = 4.

**Answer: 4** ✓

---

**Table for grid with obstacle in top row: [[0,0,1,0]]:**

Top row: `(0,0)=1, (0,1)=1, (0,2)=obstacle → break`.
dp = [1, 1, 0, 0].

**Answer: dp[0][3] = 0** ✓ -- can't reach past the obstacle.

---

## 🔧 Space Optimization -- Single Row

Same trick as Unique Paths I, but reset to 0 when hitting an obstacle:

```cpp
int uniquePathsWithObstacles(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    if (grid[0][0] == 1 || grid[m-1][n-1] == 1) return 0;

    vector<int> dp(n, 0);
    dp[0] = 1;

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1) {
                dp[j] = 0;
            } else if (j > 0) {
                dp[j] = dp[j] + dp[j - 1];
            }
        }
    }
    return dp[n - 1];
}
```

If obstacle → force `dp[j] = 0`.
Otherwise → `dp[j] += dp[j-1]` (above + left, same as Unique Paths I).

Present the 2D dp grid first. Mention this as a follow-up optimization.

---

## 🔍 The Differences from Unique Paths I (Summary)

| Unique Paths I                    | Unique Paths II                   |
| --------------------------------- | --------------------------------- |
| Top row: all 1                    | Top row: 1 until first obstacle, then 0 |
| Left col: all 1                   | Left col: 1 until first obstacle, then 0 |
| No obstacle check                 | `if (grid[i][j] == 1) dp = 0`    |
| Base: `i==0 \|\| j==0 → 1`       | Base: fill with `break` on obstacle |
| Recurrence: same                  | Recurrence: same                  |

Two changes: obstacle check + careful base case filling.
Everything else is identical.

---

### 🧠 Memory of the Obstructed Grid (Tabulation) Law

-   **Same as Unique Paths I** with obstacle handling
-   **Top row / left column:** fill with 1, `break` at first obstacle
-   **Obstacle cell:** `dp[i][j] = 0` (no paths through walls)
-   **Recurrence:** `dp[i][j] = dp[i-1][j] + dp[i][j-1]` (unchanged)
-   **Zeros propagate:** obstacle's 0 reduces all downstream paths
-   **Check start/end** for obstacles upfront
-   **Space optimization:** single row, force 0 on obstacles
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Unique Paths II (Tabulation)**,
where the Oracle filled the grid cell by cell --
setting obstacles to zero,
breaking the top row and left column at the first wall --
and letting those zeros cascade through the addition,
silently removing every path that would have hit a wall,
until only the surviving paths were counted
at the bottom-right corner. 🗺️🧱📋✨
