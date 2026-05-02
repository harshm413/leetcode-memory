## 🗺️💰🧠 _The Cheapest Road Through the Grid (Memoization): The Minimum Path Sum Saga_

> \_"A traveler stood at the top-left of a grid.
> Every cell had a cost -- a toll to pass through.
>
> The traveler could only move **down** or **right**.
>
> The Oracle was commanded:
>
> **'Find the path from top-left to bottom-right
> that minimizes the total cost.'**
>
> The Oracle saw this as the natural evolution
> of Unique Paths:
>
> **Unique Paths asked: HOW MANY paths? (count → addition)**
> **Minimum Path Sum asks: WHICH path is cheapest? (optimize → min)**
>
> Same grid. Same two moves. Different question.
>
> To reach cell `(i, j)`, the traveler came from
> either above `(i-1, j)` or the left `(i, j-1)`.
>
> The cheapest way to reach `(i, j)` =
> the cost of `(i, j)` itself
> PLUS the cheaper of the two ways to arrive:
>
> `minCost(i, j) = grid[i][j] + min(minCost(i-1, j), minCost(i, j-1))`
>
> Instead of ADDING paths (counting),
> we take the MIN of costs (optimizing).
> One word changed. The entire problem shifted."\_

---

This is the saga of **Minimum Path Sum (Memoization)**.

Given an `m × n` grid filled with non-negative numbers:
-   Move only **down** or **right**.
-   Find the path from `(0,0)` to `(m-1, n-1)` with the **minimum sum**.

```
Input:
  [[1,3,1],
   [1,5,1],
   [4,2,1]]
Output: 7   (path: 1→3→1→1→1 = 7)

Input:
  [[1,2,3],
   [4,5,6]]
Output: 12  (path: 1→2→3→6 = 12)
```

---

## 🧠 Building the Intuition -- The Cheapest Arrival

Stand at cell `(i, j)`. Ask: **"What's the cheapest way to get here?"**

The traveler came from above or from the left. No other option.

The cost to be HERE = the toll of THIS cell + the cheapest way to ARRIVE.

```
minCost(i, j) = grid[i][j] + min(minCost(i-1, j), minCost(i, j-1))
```

**Why `min` and not `+`?**

In Unique Paths, both arrivals contributed paths → we ADDED them.
Here, we want the CHEAPEST arrival → we take the MIN.

The traveler doesn't take both roads. He picks the cheaper one.

**Base cases -- where does the recursion stop?**

**The starting cell `(0, 0)`:**
The traveler is here. The cost is just this cell's value.
`minCost(0, 0) = grid[0][0]`.

**Off the grid (`i < 0` or `j < 0`):**
Doesn't exist. Return infinity -- so it never wins the `min`.

Why infinity and not 0?
Because we're minimizing. If we returned 0 for out-of-bounds,
the `min` would pick 0 over any real cost -- WRONG.
Infinity ensures out-of-bounds paths are never chosen.

---

### 📜 The Scroll of the Cheapest Road

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 🗺️ Setting Up the Cache

```cpp
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

2D cache. `memo[i][j]` = cheapest cost to reach `(i, j)`.
`-1` = not yet computed.

---

```cpp
    return solve(m - 1, n - 1, grid, memo);
}
```

Start from the destination. "What's the cheapest way to get here?"

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, vector<vector<int>>& grid,
          vector<vector<int>>& memo) {
```

"What is the minimum cost to reach cell `(i, j)` from `(0, 0)`?"

---

### 🛑 "I've gone off the grid"

```cpp
    if (i < 0 || j < 0) return INT_MAX;
```

Outside the grid. This path doesn't exist.
Return infinity so it NEVER wins the `min` comparison.

Why `INT_MAX` and not 0?
If we returned 0, then `min(realCost, 0) = 0`.
The `min` would think "going off the grid is free!" -- wrong.
Infinity means "this direction is impossible, don't pick it."

---

### 🏁 "I'm at the starting cell"

```cpp
    if (i == 0 && j == 0) return grid[0][0];
```

The traveler is at the origin. The cost is just this cell's toll.
No previous cells to add. This is where the journey began.

---

### 📖 Check the cache

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### 🔄 "I came from above or from the left -- which was cheaper?"

```cpp
    int fromAbove = solve(i - 1, j, grid, memo);
    int fromLeft  = solve(i, j - 1, grid, memo);
```

Ask both neighbors: "What's the cheapest way to reach YOU?"

`fromAbove` = cheapest cost to reach the cell above.
`fromLeft` = cheapest cost to reach the cell to the left.

---

### 💰 "My cost = my toll + the cheaper arrival"

```cpp
    memo[i][j] = grid[i][j] + min(fromAbove, fromLeft);
```

The cost to reach THIS cell =
this cell's own toll (I must pay it no matter what)
PLUS the cheaper of the two ways to arrive.

The traveler always picks the cheaper road.
He doesn't split -- he commits to one direction.

---

```cpp
    return memo[i][j];
}
```

---

### 🎺 The Trial of the Cheapest Road

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

**Full trace for grid = [[1,3,1],[1,5,1],[4,2,1]]:**

```
Grid:
  1 3 1
  1 5 1
  4 2 1
```

```
solve(2,2):
  fromAbove = solve(1,2)
    solve(1,2):
      fromAbove = solve(0,2)
        solve(0,2):
          fromAbove = solve(-1,2) → INT_MAX
          fromLeft = solve(0,1)
            solve(0,1):
              fromAbove = solve(-1,1) → INT_MAX
              fromLeft = solve(0,0) → 1
            memo[0][1] = 3 + min(INT_MAX, 1) = 3 + 1 = 4
          memo[0][2] = 1 + min(INT_MAX, 4) = 1 + 4 = 5
      fromLeft = solve(1,1)
        solve(1,1):
          fromAbove = solve(0,1) → 4 (cached!)
          fromLeft = solve(1,0)
            solve(1,0):
              fromAbove = solve(0,0) → 1
              fromLeft = solve(1,-1) → INT_MAX
            memo[1][0] = 1 + min(1, INT_MAX) = 1 + 1 = 2
          memo[1][1] = 5 + min(4, 2) = 5 + 2 = 7
      memo[1][2] = 1 + min(5, 7) = 1 + 5 = 6

  fromLeft = solve(2,1)
    solve(2,1):
      fromAbove = solve(1,1) → 7 (cached!)
      fromLeft = solve(2,0)
        solve(2,0):
          fromAbove = solve(1,0) → 2 (cached!)
          fromLeft = solve(2,-1) → INT_MAX
        memo[2][0] = 4 + min(2, INT_MAX) = 4 + 2 = 6
      memo[2][1] = 2 + min(7, 6) = 2 + 6 = 8

  memo[2][2] = 1 + min(6, 8) = 1 + 6 = 7
```

**Answer: 7** ✓

```
Cost grid (memo):
  1  4  5
  2  7  6
  6  8  7
```

Optimal path: (0,0)→(1,0)→(1,1)... wait, let me trace the actual path.
From memo: (2,2) came from above (1,2)=6 < left (2,1)=8.
(1,2) came from above (0,2)=5 < left (1,1)=7.
(0,2) came from left (0,1)=4.
(0,1) came from left (0,0)=1.

Path: (0,0)→(0,1)→(0,2)→(1,2)→(2,2). Cost: 1+3+1+1+1 = 7. ✓

---

**Trace for grid = [[1,2,3],[4,5,6]]:**

```
memo:
  1  3  6
  5  8  12
```

Path: (0,0)→(0,1)→(0,2)→(1,2). Cost: 1+2+3+6 = 12. ✓

---

## 🔍 The Grid DP Family -- Same Structure, Different Operation

| Problem              | Question          | Operation at each cell          |
| -------------------- | ----------------- | ------------------------------- |
| Unique Paths         | How many paths?   | `dp[i][j] = above + left`      |
| Unique Paths II      | How many (obstacles)? | Same + obstacle = 0          |
| Minimum Path Sum     | Cheapest path?    | `dp[i][j] = grid[i][j] + min(above, left)` |
| Maximum Path Sum     | Richest path?     | `dp[i][j] = grid[i][j] + max(above, left)` |

Same grid. Same two moves. Same structure.
The ONLY thing that changes is the operation: `+`, `min`, or `max`.

---

## 🔍 Why INT_MAX for Out-of-Bounds (Not 0)?

| Problem          | Out-of-bounds returns | Why                              |
| ---------------- | --------------------- | -------------------------------- |
| Unique Paths     | 0                     | No paths from outside → add 0   |
| Minimum Path Sum | INT_MAX               | Impossible path → never wins min |
| Maximum Path Sum | INT_MIN (or 0)        | Impossible path → never wins max |

The sentinel value depends on the operation:
-   Counting (addition) → 0 (identity of +).
-   Minimizing → INT_MAX (identity of min).
-   Maximizing → INT_MIN or 0 (identity of max).

---

### 🧠 Memory of the Cheapest Road (Memoization) Law

-   **Recurrence:** `minCost(i,j) = grid[i][j] + min(minCost(i-1,j), minCost(i,j-1))`
-   **Base:** `(0,0)` → `grid[0][0]`. Off-grid → `INT_MAX`.
-   **Why INT_MAX?** So impossible paths never win the `min`
-   **Why `min` not `+`?** We're optimizing (cheapest), not counting
-   **Same structure as Unique Paths** -- different operation
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Minimum Path Sum (Memoization)**,
where the Oracle stood at the destination
and asked "what was the cheapest way to get here?" --
from above or from the left,
always picking the cheaper arrival,
always adding this cell's toll --
until the starting cell answered "just my own cost"
and the minimum path bubbled up
through the grid of tolls. 🗺️💰🧠✨
