## 🟩🔢 _The Counting of Perfect Squares: The Count Square Submatrices Saga_

> \_"In the Grid of Ones and Zeros,
> the Oracle was commanded:
>
> **'Count the total number of square submatrices
> that contain ALL ones.'**
>
> Not just the largest square --
> ALL squares of ALL sizes.
> A 1×1 square counts. A 2×2 counts. A 3×3 counts.
> Every valid square at every position.
>
> The Oracle saw an elegant DP:
>
> **`dp[i][j]` = the size of the LARGEST square
> with its bottom-right corner at `(i, j)`.**
>
> If `dp[i][j] = 3`, that means:
> -   There's a 3×3 square ending here.
> -   There's also a 2×2 square ending here.
> -   There's also a 1×1 square ending here.
> -   Total squares ending at (i, j) = 3.
>
> **The answer = sum of all `dp[i][j]` values.**
>
> Each cell tells us how many squares END at that cell.
> Sum them all → total squares in the grid.
>
> The recurrence:
> `dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1`
> (only if `grid[i][j] == 1`)
>
> The minimum of the three neighbors determines
> the largest square that can be formed here."\_

---

This is the saga of **Count Square Submatrices with All Ones**.

Given an `m × n` binary matrix:

-   Return the total number of square submatrices that have all ones.

```
Input:
  [[0,1,1,1],
   [1,1,1,1],
   [0,1,1,1]]
Output: 15

Input:
  [[1,0,1],
   [1,1,0],
   [1,1,0]]
Output: 7
```

---

## 🧠 The Oracle's Core Insight -- dp[i][j] = Largest Square Ending Here

`dp[i][j]` = side length of the largest square of all-ones
with its **bottom-right corner** at cell `(i, j)`.

**Why does this count ALL squares?**

If `dp[i][j] = 3`:
-   A 3×3 all-ones square ends here → 1 square.
-   A 2×2 all-ones square ends here → 1 square.
-   A 1×1 all-ones square ends here → 1 square.
-   Total squares ending at (i, j) = `dp[i][j]` = 3.

**Answer = sum of all dp[i][j].**

---

## 🧠 The Recurrence

For each cell `(i, j)`:

**If `grid[i][j] == 0`:** no square can end here. `dp[i][j] = 0`.

**If `grid[i][j] == 1`:**

```
dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
```

**Why the minimum of three neighbors?**

To form a square of size `k` at `(i, j)`, we need:
-   A square of size `k-1` ending at `(i-1, j)` (above).
-   A square of size `k-1` ending at `(i, j-1)` (left).
-   A square of size `k-1` ending at `(i-1, j-1)` (diagonal).

If ANY of these is smaller than `k-1`, we can't form a `k×k` square.
The bottleneck is the MINIMUM of the three.

```
  □ □ □
  □ □ □    ← to extend to 3×3, all three 2×2 sub-squares must exist
  □ □ ■
```

> _"A square is only as large as its weakest corner.
> The top, the left, and the diagonal
> must all support the same size.
> The minimum determines the limit."_

---

### 📜 The Scroll of the Perfect Squares

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🟩 The DP Ritual

```cpp
int countSquares(vector<vector<int>>& grid) {
    int rows = grid.size();
    int cols = grid[0].size();
    vector<vector<int>> dp(rows, vector<int>(cols, 0));
    int total = 0;
```

`dp[i][j]` = largest square ending at (i, j).
`total` = sum of all dp values = answer.

---

### 🔁 Fill the DP Table

```cpp
    for (int i = 0; i < rows; i++) {
        for (int c = 0; c < cols; c++) {
```

Process every cell, row by row, left to right.

---

### 🛑 Cell Is Zero -- No Square Here

```cpp
            if (grid[i][c] == 0) {
                dp[i][c] = 0;
            }
```

A zero cell cannot be the bottom-right corner of any all-ones square.

---

### 🏁 First Row or First Column -- At Most 1×1

```cpp
            else if (i == 0 || c == 0) {
                dp[i][c] = 1;
            }
```

Cells on the first row or first column can form at most a 1×1 square
(no room above or to the left for anything larger).

---

### 📐 Interior Cell -- The Min of Three Neighbors + 1

```cpp
            else {
                dp[i][c] = min({dp[i-1][c], dp[i][c-1], dp[i-1][c-1]}) + 1;
            }
```

The largest square ending here = 1 + the minimum of:
-   `dp[i-1][c]` -- largest square ending directly above.
-   `dp[i][c-1]` -- largest square ending directly left.
-   `dp[i-1][c-1]` -- largest square ending diagonally above-left.

---

### 📊 Accumulate the Count

```cpp
            total += dp[i][c];
        }
    }
    return total;
}
```

Each `dp[i][c]` tells us how many squares end at that cell.
Sum them all → total number of all-ones squares in the grid.

---

### 🎺 The Trial of the Perfect Squares

```cpp
int main() {
    vector<vector<int>> g1 = {{0,1,1,1},{1,1,1,1},{0,1,1,1}};
    cout << countSquares(g1) << endl; // expected: 15

    vector<vector<int>> g2 = {{1,0,1},{1,1,0},{1,1,0}};
    cout << countSquares(g2) << endl; // expected: 7

    return 0;
}
```

---

**Full trace for grid = [[0,1,1,1],[1,1,1,1],[0,1,1,1]]:**

```
Grid:
  0 1 1 1
  1 1 1 1
  0 1 1 1
```

**Building dp table:**

| (i,c) | grid | i==0 or c==0? | dp[i-1][c], dp[i][c-1], dp[i-1][c-1] | dp[i][c] |
|--------|------|---------------|---------------------------------------|----------|
| (0,0)  | 0    | --            | --                                    | 0        |
| (0,1)  | 1    | i==0          | --                                    | 1        |
| (0,2)  | 1    | i==0          | --                                    | 1        |
| (0,3)  | 1    | i==0          | --                                    | 1        |
| (1,0)  | 1    | c==0          | --                                    | 1        |
| (1,1)  | 1    | No            | min(1, 1, 0) + 1 = 1                 | 1        |
| (1,2)  | 1    | No            | min(1, 1, 1) + 1 = 2                 | 2        |
| (1,3)  | 1    | No            | min(1, 2, 1) + 1 = 2                 | 2        |
| (2,0)  | 0    | --            | --                                    | 0        |
| (2,1)  | 1    | No            | min(1, 0, 1) + 1 = 1                 | 1        |
| (2,2)  | 1    | No            | min(2, 1, 1) + 1 = 2                 | 2        |
| (2,3)  | 1    | No            | min(2, 2, 2) + 1 = 3                 | 3        |

**DP table:**

```
  0 1 1 1
  1 1 2 2
  0 1 2 3
```

**Total = 0+1+1+1+1+1+2+2+0+1+2+3 = 15** ✓

Breakdown:
-   1×1 squares: 10 (every cell with value ≥ 1)
-   2×2 squares: 4 (cells with value ≥ 2: (1,2),(1,3),(2,2),(2,3) → but only those with dp≥2 count one 2×2 each)
-   3×3 squares: 1 (cell (2,3) with dp=3)
-   Total: 10 + 4 + 1 = 15 ✓

---

**Trace for grid = [[1,0,1],[1,1,0],[1,1,0]]:**

```
Grid:        DP table:
  1 0 1        1 0 1
  1 1 0        1 1 0
  1 1 0        1 2 0
```

| Cell   | dp value |
|--------|----------|
| (0,0)  | 1        |
| (0,1)  | 0        |
| (0,2)  | 1        |
| (1,0)  | 1        |
| (1,1)  | min(0,1,1)+1 = 1 |
| (1,2)  | 0        |
| (2,0)  | 1        |
| (2,1)  | min(1,1,1)+1 = 2 |
| (2,2)  | 0        |

**Total = 1+0+1+1+1+0+1+2+0 = 7** ✓

---

## 🔍 Why min(top, left, diagonal)?

Visualize what's needed for a 3×3 square at (i, j):

```
  A A A
  A A A
  A A ■  ← (i, j)
```

-   `dp[i-1][j] ≥ 2` → the 2×2 above-right exists.
-   `dp[i][j-1] ≥ 2` → the 2×2 left-bottom exists.
-   `dp[i-1][j-1] ≥ 2` → the 2×2 above-left exists.

If ANY of these is < 2, the 3×3 can't be formed.
The minimum is the bottleneck.

General: to form a `k×k` square, all three neighbors must support `k-1`.

---

## 🔍 Why Sum of dp = Total Count?

If `dp[i][j] = 3`:
-   There's exactly 1 square of size 3 ending here.
-   There's exactly 1 square of size 2 ending here.
-   There's exactly 1 square of size 1 ending here.
-   Contribution to total = 3.

If `dp[i][j] = 1`:
-   Only a 1×1 square ends here.
-   Contribution = 1.

Summing all dp values counts every square exactly once
(each square is counted at its bottom-right corner).

---

## 🔍 Space Optimization

Since `dp[i][c]` only depends on the current row and the previous row,
we can use a 1D array + one variable for the diagonal:

```cpp
int countSquares(vector<vector<int>>& grid) {
    int rows = grid.size(), cols = grid[0].size();
    vector<int> dp(cols, 0);
    int total = 0;

    for (int i = 0; i < rows; i++) {
        int prev = 0; // dp[i-1][c-1]
        for (int c = 0; c < cols; c++) {
            int temp = dp[c]; // save before overwriting (becomes next prev)
            if (grid[i][c] == 0) {
                dp[c] = 0;
            } else if (i == 0 || c == 0) {
                dp[c] = 1;
            } else {
                dp[c] = min({dp[c], dp[c-1], prev}) + 1;
            }
            total += dp[c];
            prev = temp;
        }
    }
    return total;
}
```

```
Time:  O(m × n)
Space: O(n) -- single row
```

---

## 🔍 Connection to Maximal Square (LeetCode 221)

| Count Square Submatrices (this)   | Maximal Square                    |
| --------------------------------- | --------------------------------- |
| Count ALL squares                 | Find the LARGEST square            |
| Answer = sum of dp values         | Answer = max of dp values (squared)|
| Same dp recurrence                | Same dp recurrence                 |

The dp table is identical. Only what you extract from it differs.

---

### 🧠 Memory of the Perfect Squares Law

-   **State:** `dp[i][j]` = side length of largest all-ones square ending at (i, j)
-   **Base:** first row/column → `dp = grid[i][j]` (0 or 1)
-   **Transition:** `dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1` (if grid=1)
-   **Answer:** sum of all dp values
-   **Why sum?** `dp[i][j] = k` means k squares (sizes 1 to k) end at that cell
-   **Why min of three?** All three corners must support the square
-   **Space optimization:** 1D array + prev variable → O(n)
-   **Same recurrence as Maximal Square** — different extraction
-   **Time:** O(m × n). **Space:** O(m × n) or O(n) optimized.
-   **Edge cases:**
    -   All zeros → 0
    -   All ones → sum of min(i+1, j+1) for each cell
    -   Single cell → 0 or 1

Thus is remembered the saga of **Count Square Submatrices**,
where the Oracle at each cell asked:
"How large a square can end here?" --
limited by the smallest of the three neighbors above, left, and diagonal --
and knowing that a cell with value `k`
contributed `k` squares to the total count --
summed every cell's contribution
to reveal the total number of perfect squares
hidden within the grid of ones. 🟩🔢✨
