## 🗺️💰📋 _The Cheapest Road Through the Grid (Tabulation): The Minimum Path Sum Saga_

> \_"The Oracle had solved the cheapest road with memoization.
> Now she built the answer bottom-up.
>
> **Fill the grid cell by cell.
> Each cell's cost = its own toll + the cheaper of above and left.**
>
> The top row and left column are special --
> they have only ONE direction of arrival,
> so their costs are just running sums."\_

---

This is the saga of **Minimum Path Sum (Tabulation)**.

Same problem:
-   Grid with costs. Move only down or right.
-   Find the minimum sum path from `(0,0)` to `(m-1, n-1)`.

```
Input:  [[1,3,1],[1,5,1],[4,2,1]]  →  Output: 7
Input:  [[1,2,3],[4,5,6]]          →  Output: 12
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][j] = minimum cost to reach (i, j) from (0, 0)
```

**Starting cell:**

```
dp[0][0] = grid[0][0]
```

Just its own toll. The journey starts here.

**Top row (can only come from the left):**

```
dp[0][j] = dp[0][j-1] + grid[0][j]
```

Each cell in the top row can only be reached
by going right from the previous cell.
The cost accumulates left to right.

**Left column (can only come from above):**

```
dp[i][0] = dp[i-1][0] + grid[i][0]
```

Each cell in the left column can only be reached
by going down from the cell above.
The cost accumulates top to bottom.

**Everything else:**

```
dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])
```

This cell's toll + the cheaper arrival (above or left).

---

### 📜 The Scroll of the Bottom-Up Cheapest Road

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Grid

```cpp
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
    vector<vector<int>> dp(m, vector<int>(n, 0));
```

---

### 🏁 The Starting Cell

```cpp
    dp[0][0] = grid[0][0];
```

The journey begins here. Cost = this cell's toll.

---

### ➡️ Fill the Top Row -- Running Sum Going Right

```cpp
    for (int j = 1; j < n; j++) {
        dp[0][j] = dp[0][j - 1] + grid[0][j];
    }
```

Each top-row cell can only be reached from the left.
There's no cell above it. Only one road leads here.

The cost = previous cell's cost + this cell's toll.
It's a running sum -- no `min` needed because there's only one option.

Why no `min`? Because there's only ONE way to arrive.
`min` requires two options. The top row has only one: from the left.

---

### ⬇️ Fill the Left Column -- Running Sum Going Down

```cpp
    for (int i = 1; i < m; i++) {
        dp[i][0] = dp[i - 1][0] + grid[i][0];
    }
```

Same logic, vertical. Each left-column cell
can only be reached from above. Running sum downward.

---

### Fill the Rest -- The Cheaper Arrival

```cpp
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
```

---

### 💰 This cell's cost = toll + cheaper arrival

```cpp
            dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1]);
```

**`grid[i][j]`** -- the toll of this cell. Must be paid no matter what.

**`min(dp[i-1][j], dp[i][j-1])`** -- the cheaper of the two arrivals.
The traveler picks the road that cost less to reach.

Why `min`? Because the traveler wants the cheapest total.
He doesn't take both roads -- he commits to the cheaper one.

---

```cpp
        }
    }
    return dp[m - 1][n - 1];
}
```

The bottom-right cell holds the minimum path sum.

---

### 🎺 The Trial of the Bottom-Up Cheapest Road

```cpp
int main() {
    vector<vector<int>> g1 = {{1,3,1},{1,5,1},{4,2,1}};
    cout << minPathSum(g1) << endl; // expected: 7

    vector<vector<int>> g2 = {{1,2,3},{4,5,6}};
    cout << minPathSum(g2) << endl; // expected: 12

    return 0;
}
```

---

**Full table for grid = [[1,3,1],[1,5,1],[4,2,1]]:**

**Top row (running sum):**
`dp[0][0]=1, dp[0][1]=1+3=4, dp[0][2]=4+1=5`

**Left column (running sum):**
`dp[0][0]=1, dp[1][0]=1+1=2, dp[2][0]=2+4=6`

**Rest:**

| Cell   | grid[i][j] | above dp[i-1][j] | left dp[i][j-1] | min  | dp[i][j] = grid + min |
| ------ | ---------- | ----------------- | ---------------- | ---- | ---------------------- |
| (1,1)  | 5          | dp[0][1] = 4      | dp[1][0] = 2     | 2    | 5 + 2 = **7**         |
| (1,2)  | 1          | dp[0][2] = 5      | dp[1][1] = 7     | 5    | 1 + 5 = **6**         |
| (2,1)  | 2          | dp[1][1] = 7      | dp[2][0] = 6     | 6    | 2 + 6 = **8**         |
| (2,2)  | 1          | dp[1][2] = 6      | dp[2][1] = 8     | 6    | 1 + 6 = **7**         |

```
dp grid:
  1  4  5
  2  7  6
  6  8  7
```

**Answer: dp[2][2] = 7** ✓

At `(2,2)`: toll is 1. Arriving from above (6) is cheaper than from left (8).
Total: 1 + 6 = 7.

Optimal path: (0,0)→(0,1)→(0,2)→(1,2)→(2,2). Cost: 1+3+1+1+1 = 7.

---

**Table for grid = [[1,2,3],[4,5,6]]:**

```
dp grid:
   1   3   6
   5   8  12
```

Top row: 1, 1+2=3, 3+3=6.
Left col: 1, 1+4=5.
(1,1): 5 + min(3, 5) = 5+3 = 8.
(1,2): 6 + min(6, 8) = 6+6 = 12.

**Answer: 12** ✓

---

## 🔧 Space Optimization -- Single Row

Each row depends only on the previous row and the current row (left neighbor).
We can use a single 1D array.

```cpp
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    vector<int> dp(n);

    dp[0] = grid[0][0];
    for (int j = 1; j < n; j++) dp[j] = dp[j-1] + grid[0][j];

    for (int i = 1; i < m; i++) {
        dp[0] = dp[0] + grid[i][0];
        for (int j = 1; j < n; j++) {
            dp[j] = grid[i][j] + min(dp[j], dp[j-1]);
        }
    }
    return dp[n-1];
}
```

`dp[j]` before update = value from the row above (the "above" neighbor).
`dp[j-1]` after update = value from the left (already updated this row).
`min(dp[j], dp[j-1])` = min(above, left). Correct.

Present the 2D grid first. Mention this as a follow-up.

```
Time:  O(M × N)
Space: O(N)
```

---

## 🔍 The Grid DP Family -- Comparison

| Problem              | Operation          | Base (top row / left col)     | Out-of-bounds |
| -------------------- | ------------------ | ----------------------------- | ------------- |
| Unique Paths         | `above + left`     | All 1s                        | 0             |
| Unique Paths II      | `above + left`     | 1s until obstacle, then 0     | 0             |
| Minimum Path Sum     | `grid + min(above, left)` | Running sum              | INT_MAX       |

Same grid. Same fill order. Different operation and base cases.

---

## 🔍 Why Running Sums for Top Row and Left Column?

In the interior, each cell has TWO arrivals → we take `min`.
On the top row, each cell has ONE arrival (from the left) → no `min` needed.
On the left column, each cell has ONE arrival (from above) → no `min` needed.

With only one option, the cost is just the running sum of tolls.
There's no choice to optimize -- just accumulate.

---

### 🧠 Memory of the Cheapest Road (Tabulation) Law

-   **State:** `dp[i][j]` = min cost from `(0,0)` to `(i,j)`
-   **Base:** `dp[0][0] = grid[0][0]`. Top row and left col = running sums.
-   **Transition:** `dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])`
-   **Why running sums for edges?** Only one direction of arrival → no `min`
-   **Space optimization:** single row, `dp[j] = grid[i][j] + min(dp[j], dp[j-1])`
-   **Present 2D grid first.** Mention 1D as follow-up.
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Minimum Path Sum (Tabulation)**,
where the Oracle filled the grid cell by cell --
the top row and left column accumulating tolls in a straight line,
every other cell paying its toll plus the cheaper arrival --
until the bottom-right corner revealed
the cheapest road through the grid. 🗺️💰📋✨
