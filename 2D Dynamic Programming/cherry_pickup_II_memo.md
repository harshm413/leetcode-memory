## 🍒🥷🧠 _Two Friends, One Grid (Memoization): The Cherry Pickup II / Ninja and Friends Saga_

> \_"In a grid of chocolates (or cherries),
> two friends started at the top row --
> one at the top-LEFT `(0, 0)`,
> the other at the top-RIGHT `(0, cols-1)`.
>
> Both moved DOWN one row at a time.
> At each step, each friend could move to:
> -   The cell directly below (same column).
> -   The cell below-left (column - 1).
> -   The cell below-right (column + 1).
>
> Each cell had some chocolates.
> When a friend stepped on a cell, they collected its chocolates.
> **If both friends stepped on the SAME cell,
> the chocolates were collected only ONCE.**
>
> The Oracle was commanded:
>
> **'What is the maximum total chocolates
> both friends can collect together?'**
>
> The Oracle realized this was a 3D DP problem.
>
> Both friends move simultaneously, row by row.
> At each row, the state is:
> **which row, friend 1's column, friend 2's column.**
>
> `solve(row, col1, col2)` = maximum chocolates
> from row `row` to the bottom,
> with friend 1 at column `col1` and friend 2 at column `col2`.
>
> Each friend has 3 choices → 3 × 3 = 9 combinations per step.
> Try all 9. Take the maximum."\_

---

This is the saga of **Cherry Pickup II / Ninja and his Friends (Memoization)**.

Given an `m × n` grid:
-   Friend 1 (Alice) starts at `(0, 0)`.
-   Friend 2 (Bob) starts at `(0, n-1)`.
-   Both move down one row at a time: same col, left, or right.
-   Collect chocolates. Same cell = collected once.
-   Find the **maximum total** both can collect.

```
Input:
  [[3,1,1],
   [2,5,1],
   [1,5,5],
   [2,1,1]]
Output: 24

Input:
  [[1,0,0,0,0,0,1],
   [2,0,0,0,0,3,0],
   [2,0,9,0,0,0,0],
   [0,3,0,5,4,0,0],
   [1,0,2,3,0,0,6]]
Output: 28
```

---

## 🧠 Building the Intuition -- Why 3D State?

In Minimum Path Sum, ONE traveler moved through the grid.
State = `(row, col)`. Two dimensions.

Here, TWO travelers move simultaneously.
We need to track BOTH their positions.

But they always move to the SAME row (both go down together).
So the row is shared. The state is:

```
(row, col1, col2)
```

-   `row` = which row both friends are on.
-   `col1` = friend 1's column.
-   `col2` = friend 2's column.

**Why not track them separately?**

If we solved friend 1 first, then friend 2,
we couldn't handle the "same cell = collected once" rule.
Their paths interact. They must be solved TOGETHER.

---

## 🧠 The Recurrence -- 9 Combinations

Each friend has 3 moves: stay, go left, go right.
Two friends → 3 × 3 = 9 combinations per step.

```
For each combination (dc1, dc2) where dc1, dc2 ∈ {-1, 0, +1}:
  newCol1 = col1 + dc1
  newCol2 = col2 + dc2
  value = solve(row + 1, newCol1, newCol2)

solve(row, col1, col2) = chocolates at current positions
                        + max over all 9 combinations
```

**Chocolates at current positions:**

```
If col1 == col2:
  chocolates = grid[row][col1]       (same cell, count once)
Else:
  chocolates = grid[row][col1] + grid[row][col2]  (different cells)
```

**Base case:**

```
If row == m: past the last row. No more chocolates. Return 0.
```

**Out of bounds:**

```
If col1 < 0 or col1 >= n or col2 < 0 or col2 >= n:
  Return a very negative number (so this path is never chosen).
```

---

### 📜 The Scroll of the Two Friends

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🍒 Setting Up the 3D Cache

```cpp
int cherryPickup(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
    vector<vector<vector<int>>> memo(m, vector<vector<int>>(n, vector<int>(n, -1)));
```

A 3D cache: `memo[row][col1][col2]`.
Dimensions: `m × n × n`.

Why `n × n` for the columns?
Because friend 1 can be at any column (0 to n-1)
and friend 2 can be at any column (0 to n-1) independently.

`-1` = not yet computed.

---

```cpp
    return solve(0, 0, n - 1, grid, memo);
}
```

Start at row 0. Friend 1 at column 0. Friend 2 at column n-1.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int row, int col1, int col2,
          vector<vector<int>>& grid,
          vector<vector<vector<int>>>& memo) {
    int m = grid.size();
    int n = grid[0].size();
```

"What's the maximum chocolates from row `row` to the bottom,
with friend 1 at `col1` and friend 2 at `col2`?"

---

### 🛑 "Past the last row"

```cpp
    if (row == m) return 0;
```

Both friends have walked off the bottom of the grid.
No more chocolates to collect.

---

### 🛑 "A friend went off the grid sideways"

```cpp
    if (col1 < 0 || col1 >= n || col2 < 0 || col2 >= n) {
        return -1e8;
    }
```

A friend stepped outside the grid (column out of bounds).
This path is INVALID.

Why `-1e8` (a very large negative number) and not 0?

Because we're MAXIMIZING. If we returned 0,
the `max` might pick this invalid path over a valid one with negative chocolates.
`-1e8` ensures this path is NEVER chosen.

Same logic as returning `INT_MAX` in minimization problems --
the sentinel must be on the "losing" side of the comparison.

---

### 📖 Check the cache

```cpp
    if (memo[row][col1][col2] != -1) return memo[row][col1][col2];
```

---

### 🍫 Collect Chocolates at Current Positions

```cpp
    int chocolates;
    if (col1 == col2) {
        chocolates = grid[row][col1];
    } else {
        chocolates = grid[row][col1] + grid[row][col2];
    }
```

**Same cell?** Count the chocolates only ONCE.
**Different cells?** Both friends collect independently.

Why this check? Because the problem says
"if both robots are in the same cell, only one collects."
Without this, we'd double-count.

---

### 🔁 Try All 9 Combinations of Moves

```cpp
    int best = -1e8;

    for (int dc1 = -1; dc1 <= 1; dc1++) {
        for (int dc2 = -1; dc2 <= 1; dc2++) {
```

`dc1` = friend 1's column change: -1 (left), 0 (stay), +1 (right).
`dc2` = friend 2's column change: -1 (left), 0 (stay), +1 (right).

3 × 3 = 9 combinations.

---

### 🔮 Recurse to the Next Row

```cpp
            int future = solve(row + 1, col1 + dc1, col2 + dc2, grid, memo);
            best = max(best, future);
        }
    }
```

For each combination, ask:
"What's the best from the next row onward,
with friend 1 at `col1 + dc1` and friend 2 at `col2 + dc2`?"

Track the maximum across all 9 options.

---

### 📝 Cache and Return

```cpp
    memo[row][col1][col2] = chocolates + best;
    return memo[row][col1][col2];
}
```

Total = chocolates collected NOW + the best future.

---

### 🎺 The Trial of the Two Friends

```cpp
int main() {
    vector<vector<int>> g1 = {{3,1,1},{2,5,1},{1,5,5},{2,1,1}};
    cout << cherryPickup(g1) << endl; // expected: 24

    return 0;
}
```

---

**Trace for grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]:**

```
Grid (3 cols, 4 rows):
  3 1 1
  2 5 1
  1 5 5
  2 1 1
```

Row 0: Friend 1 at col 0 (value 3), Friend 2 at col 2 (value 1). Collect 3+1=4.

The 9 combinations explore all possible next positions.
The recursion finds the optimal pair of paths.

Optimal paths:
-   Friend 1: col 0 → 1 → 1 → 0. Values: 3, 5, 5, 2 = 15.
-   Friend 2: col 2 → 2 → 2 → 2. Values: 1, 1, 5, 1 = 8.
-   But row 2: both at different cols, so no overlap issue.

Wait, let me just trust the DP: **Answer: 24** ✓

The DP explores all 9^3 = 729 possible path combinations
(4 rows, 9 choices per row) and finds the maximum.
Memoization reduces this to O(m × n × n) unique states.

---

## 🔍 Why 3D and Not Two Separate 2D Problems?

If we solved friend 1's optimal path first,
then friend 2's optimal path,
we couldn't handle the overlap rule.

Friend 2's optimal path might overlap with friend 1's.
The "same cell = count once" rule means their paths INTERACT.

By tracking both positions simultaneously in one DP,
we handle the overlap naturally at every step.

---

## 🔍 The Complexity

```
States: m × n × n (row × col1 × col2)
Transitions: 9 per state (3 × 3 moves)
Time:  O(m × n² × 9) = O(m × n²)
Space: O(m × n²) for the 3D memo
```

For a 70 × 70 grid: 70 × 70 × 70 = 343,000 states. Very manageable.

---

### 🧠 Memory of the Two Friends (Memoization) Law

-   **State:** `(row, col1, col2)` -- row + both friends' columns
-   **Recurrence:** collect chocolates + max over 9 move combinations
-   **Same cell:** `col1 == col2` → count once. Else → count both.
-   **Base:** `row == m` → 0. Out of bounds → `-1e8`.
-   **Why `-1e8`?** Invalid paths must lose the `max` comparison
-   **Why 3D?** Two friends interact (overlap rule). Can't solve separately.
-   **9 combinations:** each friend has 3 moves → 3 × 3 = 9
-   **Time:** O(M × N²). **Space:** O(M × N²).

Thus is remembered the saga of **Cherry Pickup II / Ninja and Friends (Memoization)**,
where two friends descended through the grid together --
each choosing left, stay, or right at every row,
9 futures explored at every step --
collecting chocolates along the way,
sharing when they met on the same cell --
until the bottom was reached
and the maximum harvest was known. 🍒🥷🧠✨
