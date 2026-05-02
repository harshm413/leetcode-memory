## 🔺📋 _The Descent Through the Pyramid (Tabulation): The Triangle Saga_

> \_"The Oracle had solved the pyramid top-down with memoization.
>
> Now she flipped the direction entirely.
>
> **Start from the BOTTOM row.
> Each cell in the bottom row is its own cost.
> Work UPWARD row by row.
> Each cell picks the cheaper of its two children below.
> When you reach the top -- the answer is there.**
>
> This bottom-up approach was elegant because:
> -   The bottom row IS the base case (no computation needed).
> -   Each row depends only on the row below (already filled).
> -   The answer naturally lands at `dp[0][0]`.
>
> No recursion. No sentinel values.
> The pyramid solves itself from the base up."\_

---

This is the saga of **Triangle (Tabulation)**.

Same problem:
-   Triangle of numbers. Move down or down-right.
-   Find minimum sum path from top to bottom.

```
Input:  [[2],[3,4],[6,5,7],[4,1,8,3]]  →  Output: 11
```

---

## 🧠 The Bottom-Up Insight -- Start from the Base

The bottom row needs no computation -- each cell's cost is just itself.

The row above it: each cell picks the cheaper of its two children.

```
Row 3 (bottom): [4, 1, 8, 3]   ← these are the base cases

Row 2: [6, 5, 7]
  6 + min(4, 1) = 6+1 = 7
  5 + min(1, 8) = 5+1 = 6
  7 + min(8, 3) = 7+3 = 10

Row 1: [3, 4]
  3 + min(7, 6) = 3+6 = 9
  4 + min(6, 10) = 4+6 = 10

Row 0: [2]
  2 + min(9, 10) = 2+9 = 11
```

The answer is at the top: **11**.

Why is bottom-up natural here?

Because the bottom row has NO dependencies -- it's the base.
Each row above depends only on the row below -- already computed.
The answer bubbles up to the single cell at the top.

---

### 📜 The Scroll of the Bottom-Up Pyramid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int minimumTotal(vector<vector<int>>& triangle) {
    int n = triangle.size();
    vector<vector<int>> dp(n);
    for (int i = 0; i < n; i++) {
        dp[i].resize(i + 1);
    }
```

A jagged 2D array matching the triangle's shape.
Row `i` has `i+1` elements -- same as the triangle.

Why jagged and not square?
Because the triangle IS jagged. Matching its shape
makes the code clearer and wastes no space.

---

### 🏁 Fill the Bottom Row -- The Base Cases

```cpp
    for (int j = 0; j < n; j++) {
        dp[n - 1][j] = triangle[n - 1][j];
    }
```

The bottom row of the dp table = the bottom row of the triangle.
No computation. Each cell's minimum path cost is just its own value.

Why? Because the bottom row is where paths END.
There's nowhere left to go. The cost is just the toll of this final cell.

---

### 🔁 Fill Upward -- Row by Row

```cpp
    for (int row = n - 2; row >= 0; row--) {
```

Start from the second-to-last row. Work upward to row 0.

Why `n - 2`? Because the bottom row (`n - 1`) is already filled.
The next row to compute is `n - 2`.

---

### 🔁 For Each Cell in This Row

```cpp
        for (int col = 0; col <= row; col++) {
```

Row `row` has `row + 1` elements (indices 0 to `row`).

---

### ⬇️ Look at the Two Children Below

```cpp
            int below = dp[row + 1][col];
            int belowRight = dp[row + 1][col + 1];
```

**`below`** = the minimum path cost from `(row+1, col)` to the bottom.
Already computed (we filled row `row+1` before this row).

**`belowRight`** = the minimum path cost from `(row+1, col+1)` to the bottom.
Also already computed.

These are the two paths the traveler can take from this cell.

---

### 💰 This Cell's Cost = Its Value + Cheaper Child

```cpp
            dp[row][col] = triangle[row][col] + min(below, belowRight);
        }
    }
```

The traveler pays this cell's toll,
then takes the cheaper of the two paths below.

---

### 📤 The Answer Is at the Top

```cpp
    return dp[0][0];
}
```

The single cell at the top of the pyramid
holds the minimum sum from top to bottom.

---

### 🎺 The Trial of the Bottom-Up Pyramid

```cpp
int main() {
    vector<vector<int>> t1 = {{2},{3,4},{6,5,7},{4,1,8,3}};
    cout << minimumTotal(t1) << endl; // expected: 11

    vector<vector<int>> t2 = {{-10}};
    cout << minimumTotal(t2) << endl; // expected: -10

    return 0;
}
```

---

**Full table trace for [[2],[3,4],[6,5,7],[4,1,8,3]]:**

**Bottom row (base):**

```
dp[3] = [4, 1, 8, 3]
```

**Row 2 (fill upward):**

| col | triangle | below dp[3][col] | belowRight dp[3][col+1] | min  | dp[2][col] |
| --- | -------- | ----------------- | ----------------------- | ---- | ---------- |
| 0   | 6        | 4                 | 1                       | 1    | 6+1 = **7**  |
| 1   | 5        | 1                 | 8                       | 1    | 5+1 = **6**  |
| 2   | 7        | 8                 | 3                       | 3    | 7+3 = **10** |

**Row 1:**

| col | triangle | below dp[2][col] | belowRight dp[2][col+1] | min  | dp[1][col] |
| --- | -------- | ----------------- | ----------------------- | ---- | ---------- |
| 0   | 3        | 7                 | 6                       | 6    | 3+6 = **9**  |
| 1   | 4        | 6                 | 10                      | 6    | 4+6 = **10** |

**Row 0:**

| col | triangle | below dp[1][0] | belowRight dp[1][1] | min  | dp[0][0]    |
| --- | -------- | -------------- | -------------------- | ---- | ----------- |
| 0   | 2        | 9              | 10                   | 9    | 2+9 = **11** |

```
dp pyramid:
    11
   9  10
  7  6  10
 4  1  8  3
```

**Answer: dp[0][0] = 11** ✓

---

## 🔧 Space Optimization -- Use Only One Row

Each row depends only on the row below it.
We don't need the entire 2D table -- just the current bottom row.

Start with the bottom row. Update it in-place, working upward.

```cpp
int minimumTotal(vector<vector<int>>& triangle) {
    int n = triangle.size();
    vector<int> dp(triangle[n - 1].begin(), triangle[n - 1].end());
```

Initialize `dp` with the bottom row of the triangle.

---

```cpp
    for (int row = n - 2; row >= 0; row--) {
        for (int col = 0; col <= row; col++) {
            dp[col] = triangle[row][col] + min(dp[col], dp[col + 1]);
        }
    }
    return dp[0];
}
```

For each row (bottom to top):
`dp[col] = triangle[row][col] + min(dp[col], dp[col+1])`.

**`dp[col]`** before update = the value from the row below (the "below" child).
**`dp[col+1]`** = the "below-right" child (not yet overwritten because we go left to right and `col+1 > col`).

After processing all rows, `dp[0]` holds the answer.

Why does this work without corruption?
We update `dp[col]` using `dp[col]` and `dp[col+1]`.
Since we iterate `col` from 0 upward,
`dp[col+1]` hasn't been overwritten yet when we read it.
No stale data.

```
Time:  O(N²)
Space: O(N) -- single row
```

Present the 2D dp table first. Mention this as a follow-up.

---

**Space-optimized trace:**

```
Initial dp (bottom row): [4, 1, 8, 3]

Row 2:
  col=0: dp[0] = 6 + min(4, 1) = 7
  col=1: dp[1] = 5 + min(1, 8) = 6
  col=2: dp[2] = 7 + min(8, 3) = 10
  dp = [7, 6, 10, 3]

Row 1:
  col=0: dp[0] = 3 + min(7, 6) = 9
  col=1: dp[1] = 4 + min(6, 10) = 10
  dp = [9, 10, 10, 3]

Row 0:
  col=0: dp[0] = 2 + min(9, 10) = 11
  dp = [11, 10, 10, 3]
```

**Answer: dp[0] = 11** ✓

---

## 🔍 Top-Down vs Bottom-Up for Triangle

| Top-Down (Memo)                   | Bottom-Up (Tab)                   |
| --------------------------------- | --------------------------------- |
| Start at `(0, 0)`, recurse down   | Start at bottom row, iterate up   |
| Base: bottom row                  | Base: bottom row (filled first)   |
| Answer at `memo[0][0]`           | Answer at `dp[0][0]`             |
| Recursion stack overhead           | No recursion                      |
| O(N²) space (memo)               | O(N) space (single row)           |

Bottom-up is cleaner for this problem because
the base case IS the bottom row -- no sentinel values needed.

---

## 🔍 Why Bottom-Up Is Especially Clean for Triangle

In Minimum Path Sum, bottom-up goes top-to-bottom (same as the traveler).
In Triangle, bottom-up goes BOTTOM-to-top (opposite of the traveler).

Why? Because the bottom row is the simplest base case --
each cell is just its own value, no dependencies.
Building upward from there is natural.

Top-to-bottom tabulation would require handling
"which cells in the bottom row can I end at?" -- messier.
Bottom-to-top avoids this entirely.

---

### 🧠 Memory of the Pyramid Descent (Tabulation) Law

-   **Fill bottom-up:** start from the bottom row, work upward
-   **Base:** bottom row = triangle's bottom row (just copy it)
-   **Transition:** `dp[row][col] = triangle[row][col] + min(dp[row+1][col], dp[row+1][col+1])`
-   **Answer:** `dp[0][0]` -- the top of the pyramid
-   **Space optimization:** single 1D array, update in-place bottom to top
    -   `dp[col] = triangle[row][col] + min(dp[col], dp[col+1])`
-   **No out-of-bounds issues** -- triangle shape guarantees valid indices
-   **Time:** O(N²). **Space:** O(N²), optimizable to O(N).

Thus is remembered the saga of **Triangle (Tabulation)**,
where the Oracle started at the base of the pyramid --
each bottom cell knowing only its own cost --
and built upward row by row,
each cell choosing the cheaper of its two children --
until the peak held the answer:
the minimum sum from top to bottom,
computed from bottom to top. 🔺📋✨
