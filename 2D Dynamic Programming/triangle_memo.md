## 🔺🧠 _The Descent Through the Pyramid (Memoization): The Triangle Saga_

> \_"A pyramid of numbers stood before the Oracle.
> Each row had one more element than the last.
>
> ```
>     2
>    3 4
>   6 5 7
>  4 1 8 3
> ```
>
> Starting from the top,
> the traveler moved DOWN one row at a time.
> At each step, he could move to the element
> **directly below** or **directly below-right**.
>
> From position `(row, col)`:
> -   Move to `(row+1, col)` -- directly below.
> -   Move to `(row+1, col+1)` -- below-right.
>
> The Oracle was commanded:
>
> **'Find the minimum sum path from top to bottom.'**
>
> The Oracle saw this as a grid DP problem --
> but with a twist.
>
> In Minimum Path Sum, the traveler moved DOWN or RIGHT.
> Here, the traveler moves to `(row+1, col)` or `(row+1, col+1)`.
>
> The shape is a triangle, not a rectangle.
> But the DP thinking is identical:
> at each cell, two choices, take the cheaper one."\_

---

This is the saga of **Triangle (Memoization)**.

Given a triangle array:
-   Row `i` has `i+1` elements.
-   From `(row, col)`, move to `(row+1, col)` or `(row+1, col+1)`.
-   Find the **minimum sum** path from top to bottom.

```
Input:
  [[2],
   [3,4],
   [6,5,7],
   [4,1,8,3]]
Output: 11  (path: 2→3→5→1 = 11)

Input:  [[-10]]
Output: -10
```

---

## 🧠 Building the Intuition -- Top-Down or Bottom-Up?

**Top-down thinking (from the top):**

At `(row, col)`, the traveler goes to `(row+1, col)` or `(row+1, col+1)`.
The minimum cost FROM here = this cell's value + the cheaper of the two paths below.

```
minCost(row, col) = triangle[row][col] + min(
    minCost(row+1, col),
    minCost(row+1, col+1)
)
```

**Base case:** the bottom row. No more moves. Cost = just this cell's value.

```
If row == last row: return triangle[row][col]
```

**Why start from the top?**

Because the problem says "start from the top."
The recursion naturally follows the traveler's journey downward.

**But there's a beautiful alternative:**

Start from the BOTTOM and work UP.
At each cell in the bottom row, the cost is just itself.
Each cell above picks the cheaper of its two children.
The answer bubbles up to the top.

We'll use top-down memoization here.
The tabulation version will use bottom-up.

---

### 📜 The Scroll of the Pyramid Descent

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔺 Setting Up the Cache

```cpp
int minimumTotal(vector<vector<int>>& triangle) {
    int n = triangle.size();
    vector<vector<int>> memo(n, vector<int>(n, INT_MAX));
```

`memo[row][col]` = minimum sum from `(row, col)` to the bottom.
`INT_MAX` = not yet computed.

Why `n × n` and not jagged?
Row `i` has `i+1` elements, so the max width is `n`.
A square `n × n` array is simpler than a jagged one.
The extra cells are never accessed.

---

```cpp
    return solve(0, 0, triangle, memo);
}
```

Start at the top of the pyramid: `(0, 0)`.
"What's the cheapest path from here to the bottom?"

---

## 🔮 The Recursive Thinking

```cpp
int solve(int row, int col, vector<vector<int>>& triangle,
          vector<vector<int>>& memo) {
```

"What is the minimum sum from `(row, col)` to the bottom?"

---

### 🛑 "I've reached the bottom row"

```cpp
    if (row == (int)triangle.size() - 1) {
        return triangle[row][col];
    }
```

The bottom row. No more moves. The cost is just this cell's value.

Why is this the base case?
Because the traveler has reached the bottom of the pyramid.
There's nowhere left to go. The path ends here.
The cost is just the toll of this final cell.

---

### 📖 Check the cache

```cpp
    if (memo[row][col] != INT_MAX) return memo[row][col];
```

Already solved? Return it.

---

### ⬇️ "I go directly below"

```cpp
    int goDown = solve(row + 1, col, triangle, memo);
```

The traveler moves to `(row+1, col)` -- the element directly below.

From the triangle:
```
    2
   3 4
  6 5 7
```
From `(1, 0)` which is `3`, going down leads to `(2, 0)` which is `6`.

---

### ↘️ "I go below-right"

```cpp
    int goDownRight = solve(row + 1, col + 1, triangle, memo);
```

The traveler moves to `(row+1, col+1)` -- the element below and to the right.

From `(1, 0)` which is `3`, going down-right leads to `(2, 1)` which is `5`.

---

### 💰 "My cost = my value + the cheaper path below"

```cpp
    memo[row][col] = triangle[row][col] + min(goDown, goDownRight);
```

This cell's value is paid no matter what.
The traveler picks the cheaper of the two paths below.

---

```cpp
    return memo[row][col];
}
```

---

### 🎺 The Trial of the Pyramid Descent

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

**Full trace for triangle = [[2],[3,4],[6,5,7],[4,1,8,3]]:**

```
Triangle:
    2
   3 4
  6 5 7
 4 1 8 3
```

```
solve(0,0): 2 + min(solve(1,0), solve(1,1))

  solve(1,0): 3 + min(solve(2,0), solve(2,1))
    solve(2,0): 6 + min(solve(3,0), solve(3,1))
      solve(3,0) → 4 (bottom row)
      solve(3,1) → 1 (bottom row)
    memo[2][0] = 6 + min(4, 1) = 6 + 1 = 7
    solve(2,1): 5 + min(solve(3,1), solve(3,2))
      solve(3,1) → 1 (cached!)
      solve(3,2) → 8 (bottom row)
    memo[2][1] = 5 + min(1, 8) = 5 + 1 = 6
  memo[1][0] = 3 + min(7, 6) = 3 + 6 = 9

  solve(1,1): 4 + min(solve(2,1), solve(2,2))
    solve(2,1) → 6 (cached!)
    solve(2,2): 7 + min(solve(3,2), solve(3,3))
      solve(3,2) → 8 (cached!)
      solve(3,3) → 3 (bottom row)
    memo[2][2] = 7 + min(8, 3) = 7 + 3 = 10
  memo[1][1] = 4 + min(6, 10) = 4 + 6 = 10

memo[0][0] = 2 + min(9, 10) = 2 + 9 = 11
```

**Answer: 11** ✓

Path: 2 → 3 → 5 → 1 = 11.

The memo grid:
```
    11
   9  10
  7  6  10
 4  1  8  3
```

---

## 🔍 How This Differs from Minimum Path Sum

| Minimum Path Sum                  | Triangle                          |
| --------------------------------- | --------------------------------- |
| Rectangular grid                  | Triangular grid                   |
| Move DOWN or RIGHT                | Move DOWN or DOWN-RIGHT           |
| `(i-1, j)` and `(i, j-1)`       | `(row+1, col)` and `(row+1, col+1)` |
| Start top-left, end bottom-right  | Start top, end anywhere on bottom |
| One destination                   | Multiple destinations (any bottom cell) |

The triangle has multiple possible endpoints --
the traveler can end at ANY cell in the bottom row.
The recursion from the top naturally explores all of them.

---

## 🔍 Why No Out-of-Bounds Check?

In Minimum Path Sum, we needed `if (i < 0 || j < 0) return INT_MAX`.

Here, the moves are `(row+1, col)` and `(row+1, col+1)`.
Since `col` ranges from `0` to `row` on each row,
and `row+1` has indices `0` to `row+1`,
both `col` and `col+1` are always valid indices on row `row+1`.

The triangle's shape guarantees no out-of-bounds.
The base case at the bottom row stops the recursion before going further.

---

### 🧠 Memory of the Pyramid Descent (Memoization) Law

-   **State:** `(row, col)` -- position in the triangle
-   **Recurrence:** `minCost(row, col) = triangle[row][col] + min(below, belowRight)`
    -   Below: `(row+1, col)`. Below-right: `(row+1, col+1)`.
-   **Base case:** bottom row → return `triangle[row][col]`
-   **No out-of-bounds check needed** -- triangle shape guarantees valid indices
-   **Multiple endpoints** -- any cell in the bottom row can be the end
-   **Time:** O(N²) where N = number of rows (total cells in triangle)
-   **Space:** O(N²) for memo + O(N) recursion stack

Thus is remembered the saga of **Triangle (Memoization)**,
where the Oracle stood at the peak of the pyramid
and asked "what's the cheapest way down?" --
at each level choosing between the cell below
and the cell below-right,
always picking the cheaper descent --
until the bottom row answered with its own values
and the minimum path bubbled up to the top. 🔺🧠✨
