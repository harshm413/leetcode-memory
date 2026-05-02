## 🗺️📋 _The Grid of Choices (Tabulation): The Unique Paths Saga_

> \_"The Oracle had solved the grid with memoization --
> starting at the destination, recursing backward.
>
> Now she flipped the direction.
>
> **Start at the top-left. Fill the grid cell by cell.
> Each cell's answer comes from the cell above and the cell to the left --
> both already filled.**
>
> When the grid is complete,
> the bottom-right cell holds the answer."\_

---

This is the saga of **Unique Paths (Tabulation)**.

Same problem:
-   `m × n` grid. Move only down or right.
-   Count unique paths from `(0,0)` to `(m-1, n-1)`.

```
Input:  m = 3, n = 7  →  Output: 28
Input:  m = 3, n = 2  →  Output: 3
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][j] = number of unique paths from (0,0) to (i,j)
```

**Base cases:**

```
dp[0][j] = 1  for all j   (top row: only one way -- go right)
dp[i][0] = 1  for all i   (left column: only one way -- go down)
```

**Transition:**

```
dp[i][j] = dp[i-1][j] + dp[i][j-1]
```

The cell above was already filled (we fill top to bottom).
The cell to the left was already filled (we fill left to right).
Their sum = paths to this cell.

---

### 📜 The Scroll of the Bottom-Up Grid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Grid

```cpp
int uniquePaths(int m, int n) {
    vector<vector<int>> dp(m, vector<int>(n, 0));
```

A 2D table. `dp[i][j]` will hold the number of paths to `(i, j)`.
All start at 0 -- no paths known yet.

---

### Fill the Top Row -- One Straight Road

```cpp
    for (int j = 0; j < n; j++) {
        dp[0][j] = 1;
    }
```

Every cell in the top row has exactly 1 path:
go right, right, right from `(0,0)`.

There's no cell above any top-row cell.
The only way to reach `(0, j)` is from `(0, j-1)` -- one straight line.

---

### Fill the Left Column -- One Straight Road

```cpp
    for (int i = 0; i < m; i++) {
        dp[i][0] = 1;
    }
```

Every cell in the left column has exactly 1 path:
go down, down, down from `(0,0)`.

There's no cell to the left of any left-column cell.
The only way to reach `(i, 0)` is from `(i-1, 0)` -- one straight line.

---

### Fill the Rest -- Row by Row, Left to Right

```cpp
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
```

Start from `(1, 1)` -- the first cell that has BOTH
a cell above and a cell to the left.

---

### The Two Sources of Arrival

```cpp
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
```

**From above:** `dp[i-1][j]` paths led to the cell above.
Each of those paths can extend one step DOWN to reach `(i, j)`.

**From the left:** `dp[i][j-1]` paths led to the cell on the left.
Each of those paths can extend one step RIGHT to reach `(i, j)`.

These two sets of paths are completely separate --
a path from above never overlaps with a path from the left.
So the total is the sum.

Why is `dp[i-1][j]` already correct when we read it?
Because we fill top to bottom, left to right.
Row `i-1` was filled before row `i`.
Cell `(i, j-1)` was filled before `(i, j)` in the same row.
Dependencies are always satisfied.

---

### The Answer

```cpp
        }
    }
    return dp[m - 1][n - 1];
}
```

The bottom-right cell holds the total number of unique paths.

---

### 🎺 The Trial of the Bottom-Up Grid

```cpp
int main() {
    cout << uniquePaths(3, 7) << endl; // expected: 28
    cout << uniquePaths(3, 2) << endl; // expected: 3
    cout << uniquePaths(1, 1) << endl; // expected: 1
    cout << uniquePaths(2, 2) << endl; // expected: 2
    return 0;
}
```

---

**Full table for m=3, n=2:**

```
     col 0   col 1
row 0:  1       1
row 1:  1       2
row 2:  1       3
```

| Cell   | dp[i-1][j] (above) | dp[i][j-1] (left) | dp[i][j] |
| ------ | ------------------- | ------------------ | --------- |
| (0,0)  | --                  | --                 | 1 (base)  |
| (0,1)  | --                  | --                 | 1 (base)  |
| (1,0)  | --                  | --                 | 1 (base)  |
| (1,1)  | dp[0][1] = 1        | dp[1][0] = 1       | 1+1 = 2   |
| (2,0)  | --                  | --                 | 1 (base)  |
| (2,1)  | dp[1][1] = 2        | dp[2][0] = 1       | 2+1 = **3** |

**Answer: 3** ✓

---

**Full table for m=3, n=7:**

```
     c0  c1  c2  c3  c4  c5  c6
r0:   1   1   1   1   1   1   1
r1:   1   2   3   4   5   6   7
r2:   1   3   6  10  15  21  28
```

Each cell = cell above + cell to the left.
Bottom-right = **28** ✓

Notice: this is Pascal's Triangle rotated 45 degrees.
`dp[2][6] = C(8, 2) = 28`. The combinatorics connection.

---

**Table for m=2, n=2:**

```
  1  1
  1  2
```

**Answer: 2** ✓ (Right→Down or Down→Right)

---

## 🔧 Space Optimization -- One Row at a Time

Each row only depends on the row above it.
We don't need the entire 2D grid -- just the previous row.

Actually, we can do even better: use a SINGLE row
and update it in-place, left to right.

```cpp
int uniquePaths(int m, int n) {
    vector<int> dp(n, 1);
```

Start with the top row: all 1s.

---

```cpp
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[j] = dp[j] + dp[j - 1];
        }
    }
    return dp[n - 1];
}
```

**`dp[j]`** before update = the value from the row above (it hasn't been overwritten yet for this row).
**`dp[j-1]`** = the value from the left (already updated in this row).

So `dp[j] = dp[j] + dp[j-1]` is exactly `above + left`.

Why does this work?
We process left to right. When we compute `dp[j]`:
-   `dp[j]` still holds the OLD value (from row `i-1`) = "above."
-   `dp[j-1]` was just updated (in this row) = "left."
-   Adding them gives the correct new value.

```
Time:  O(M × N)
Space: O(N) -- single row
```

---

**Space-optimized trace for m=3, n=7:**

```
Initial (row 0): [1, 1, 1, 1, 1, 1, 1]

Row 1:
  j=1: dp[1] = 1+1 = 2
  j=2: dp[2] = 1+2 = 3
  j=3: dp[3] = 1+3 = 4
  j=4: dp[4] = 1+4 = 5
  j=5: dp[5] = 1+5 = 6
  j=6: dp[6] = 1+6 = 7
  → [1, 2, 3, 4, 5, 6, 7]

Row 2:
  j=1: dp[1] = 2+1 = 3
  j=2: dp[2] = 3+3 = 6
  j=3: dp[3] = 4+6 = 10
  j=4: dp[4] = 5+10 = 15
  j=5: dp[5] = 6+15 = 21
  j=6: dp[6] = 7+21 = 28
  → [1, 3, 6, 10, 15, 21, 28]
```

**Answer: dp[6] = 28** ✓

---

## 🔍 The 2D DP Table Pattern

This is the template for ALL 2D grid DP problems:

```
1. Create dp[m][n].
2. Fill base cases (first row, first column, or specific cells).
3. Fill the rest: dp[i][j] = f(dp[i-1][j], dp[i][j-1]).
4. Answer at dp[m-1][n-1].
5. Space optimize: single row if each cell depends only on current and previous row.
```

This pattern appears in:
-   **Unique Paths** → count paths (addition)
-   **Unique Paths II** → count paths with obstacles
-   **Minimum Path Sum** → min cost (min instead of +)
-   **Edit Distance** → min operations (min of three)
-   **Longest Common Subsequence** → max length

Same grid. Same fill order. Different operation at each cell.

---

### 🧠 Memory of the Grid Journey (Tabulation) Law

-   **State:** `dp[i][j]` = paths from `(0,0)` to `(i,j)`
-   **Base:** top row = 1, left column = 1 (straight lines)
-   **Transition:** `dp[i][j] = dp[i-1][j] + dp[i][j-1]` (above + left)
-   **Fill order:** top to bottom, left to right
-   **Answer:** `dp[m-1][n-1]`
-   **Space optimization:** single row, `dp[j] = dp[j] + dp[j-1]`
    -   `dp[j]` before update = above. `dp[j-1]` after update = left.
-   **Present dp grid first.** Mention 1D optimization as follow-up.
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Unique Paths (Tabulation)**,
where the Oracle filled the grid cell by cell --
the top row and left column each holding the certainty of one straight path,
every other cell summing the paths from above and from the left --
until the bottom-right corner revealed
the total number of unique journeys
through the grid of choices. 🗺️📋✨
