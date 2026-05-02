## 🍒🥷📋 _Two Friends, One Grid (Tabulation): The Cherry Pickup II / Ninja and Friends Saga_

> \_"The Oracle had solved the two-friend problem with memoization.
> Now she built the answer bottom-up.
>
> **Start from the last row.
> Both friends can be at any column.
> Fill the 3D table row by row, moving upward.
> At each state, try all 9 move combinations.
> When the table reaches row 0 --
> the answer is at `dp[0][0][n-1]`.**"\_

---

This is the saga of **Cherry Pickup II / Ninja and Friends (Tabulation)**.

Same problem:
-   Two friends descend a grid simultaneously.
-   3 moves each → 9 combinations per step.
-   Same cell = collected once.
-   Maximize total chocolates.

```
Input:  [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]  →  Output: 24
```

---

## 🧠 The Bottom-Up Formulation

```
dp[row][c1][c2] = max chocolates from row `row` to the bottom,
                  with friend 1 at column c1 and friend 2 at column c2.
```

**Base case (last row):**

```
dp[m-1][c1][c2] = grid[m-1][c1] + grid[m-1][c2]   if c1 != c2
                 = grid[m-1][c1]                     if c1 == c2
```

**Transition (fill upward):**

```
dp[row][c1][c2] = chocolates(row, c1, c2)
                + max over all 9 (dc1, dc2) of dp[row+1][c1+dc1][c2+dc2]
```

**Answer:** `dp[0][0][n-1]`.

---

### 📜 The Scroll of the Bottom-Up Two Friends

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the 3D DP Table

```cpp
int cherryPickup(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
    vector<vector<vector<int>>> dp(m, vector<vector<int>>(n, vector<int>(n, 0)));
```

`dp[row][c1][c2]` = max chocolates from row `row` to bottom.
All initialized to 0.

---

### 🏁 Fill the Last Row -- Base Cases

```cpp
    for (int c1 = 0; c1 < n; c1++) {
        for (int c2 = 0; c2 < n; c2++) {
            if (c1 == c2) {
                dp[m - 1][c1][c2] = grid[m - 1][c1];
            } else {
                dp[m - 1][c1][c2] = grid[m - 1][c1] + grid[m - 1][c2];
            }
        }
    }
```

On the last row, there's nowhere left to go.
The chocolates are just what's at the current positions.

Same cell → count once. Different cells → count both.

---

### 🔁 Fill Upward -- Row by Row

```cpp
    for (int row = m - 2; row >= 0; row--) {
```

From the second-to-last row up to row 0.

---

### 🔁 For Every Possible Position Pair

```cpp
        for (int c1 = 0; c1 < n; c1++) {
            for (int c2 = 0; c2 < n; c2++) {
```

Try every combination of friend 1's column and friend 2's column.

---

### 🍫 Collect Chocolates at Current Positions

```cpp
                int chocolates;
                if (c1 == c2) {
                    chocolates = grid[row][c1];
                } else {
                    chocolates = grid[row][c1] + grid[row][c2];
                }
```

Same overlap rule as the memo version.

---

### 🔁 Try All 9 Move Combinations

```cpp
                int best = 0;
                for (int dc1 = -1; dc1 <= 1; dc1++) {
                    for (int dc2 = -1; dc2 <= 1; dc2++) {
                        int nc1 = c1 + dc1;
                        int nc2 = c2 + dc2;
```

Each friend moves left (-1), stays (0), or goes right (+1).
Compute the new columns.

---

### 🛡️ Bounds Check

```cpp
                        if (nc1 >= 0 && nc1 < n && nc2 >= 0 && nc2 < n) {
                            best = max(best, dp[row + 1][nc1][nc2]);
                        }
                    }
                }
```

Only consider valid column positions.
Out-of-bounds combinations are simply skipped.

`dp[row + 1][nc1][nc2]` is already filled
(we're iterating rows bottom to top).

---

### 📝 Fill This Cell

```cpp
                dp[row][c1][c2] = chocolates + best;
            }
        }
    }
```

Chocolates collected NOW + the best future from the 9 options.

---

### 📤 The Answer

```cpp
    return dp[0][0][n - 1];
}
```

Row 0, friend 1 at column 0, friend 2 at column n-1.
That's where they start.

---

### 🎺 The Trial of the Bottom-Up Two Friends

```cpp
int main() {
    vector<vector<int>> g1 = {{3,1,1},{2,5,1},{1,5,5},{2,1,1}};
    cout << cherryPickup(g1) << endl; // expected: 24

    return 0;
}
```

---

**Trace overview for grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]:**

```
Grid (m=4, n=3):
  3 1 1
  2 5 1
  1 5 5
  2 1 1
```

**Last row (row 3) base cases:**

`dp[3][c1][c2]` for all c1, c2:

```
       c2=0  c2=1  c2=2
c1=0:   2     3     3
c1=1:   3     1     2
c1=2:   3     2     1
```

(Diagonal: same cell, count once. Off-diagonal: sum both.)

**Row 2:** for each (c1, c2), try 9 moves, pick best from row 3.

**Row 1:** same, using row 2's results.

**Row 0:** same, using row 1's results.

**Answer: dp[0][0][2] = 24** ✓

The full 3D table has 4 × 3 × 3 = 36 cells.
Each cell considers 9 moves. Total work: 36 × 9 = 324 operations.

---

## 🔧 Space Optimization -- Two Layers

Each row depends only on the row below.
We only need TWO layers of the `n × n` table: current and next.

```cpp
int cherryPickup(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    vector<vector<int>> next(n, vector<int>(n, 0));
    vector<vector<int>> curr(n, vector<int>(n, 0));

    // Fill last row into 'next'
    for (int c1 = 0; c1 < n; c1++)
        for (int c2 = 0; c2 < n; c2++)
            next[c1][c2] = (c1 == c2) ? grid[m-1][c1] : grid[m-1][c1] + grid[m-1][c2];

    // Fill upward
    for (int row = m - 2; row >= 0; row--) {
        for (int c1 = 0; c1 < n; c1++) {
            for (int c2 = 0; c2 < n; c2++) {
                int choc = (c1 == c2) ? grid[row][c1] : grid[row][c1] + grid[row][c2];
                int best = 0;
                for (int dc1 = -1; dc1 <= 1; dc1++)
                    for (int dc2 = -1; dc2 <= 1; dc2++) {
                        int nc1 = c1+dc1, nc2 = c2+dc2;
                        if (nc1 >= 0 && nc1 < n && nc2 >= 0 && nc2 < n)
                            best = max(best, next[nc1][nc2]);
                    }
                curr[c1][c2] = choc + best;
            }
        }
        next = curr;
    }
    return next[0][n-1];
}
```

Space reduced from O(M × N²) to O(N²).
Present the full 3D table first. Mention this as a follow-up.

---

## 🔍 Why This Problem Is Hard

| Aspect              | Unique Paths / Min Path Sum | Cherry Pickup II            |
| -------------------- | --------------------------- | --------------------------- |
| Travelers            | 1                           | 2 (simultaneous)            |
| State dimensions     | 2D (row, col)               | 3D (row, col1, col2)        |
| Choices per step     | 2 (down, right)             | 9 (3 × 3)                  |
| Interaction          | None                        | Same cell = count once       |
| Complexity           | O(M × N)                   | O(M × N²)                  |

The jump from 1 traveler to 2 travelers
adds an entire dimension to the state
and multiplies the choices per step.

---

## 🔍 Why Not Solve Each Friend Separately?

If friend 1 takes the globally optimal path,
friend 2's optimal path might overlap heavily.
The "same cell = count once" rule means
the combined optimal is NOT the sum of individual optimals.

They must be solved TOGETHER so the overlap is handled at every step.

---

### 🧠 Memory of the Two Friends (Tabulation) Law

-   **State:** `dp[row][c1][c2]` -- row + both columns
-   **Base:** last row. Same cell → count once. Different → count both.
-   **Transition:** chocolates + max over 9 move combinations from row below
-   **Fill order:** bottom to top, all (c1, c2) pairs per row
-   **Answer:** `dp[0][0][n-1]`
-   **Space optimization:** two layers (curr + next) → O(N²)
-   **Time:** O(M × N² × 9) = O(M × N²)
-   **Present 3D table first.** Mention 2-layer optimization as follow-up.

Thus is remembered the saga of **Cherry Pickup II / Ninja and Friends (Tabulation)**,
where the Oracle built the answer from the bottom of the grid --
filling every possible position pair for both friends,
trying all 9 move combinations at each step,
handling the overlap when paths crossed --
until the top of the grid revealed
the maximum chocolates two friends could collect together. 🍒🥷📋✨
