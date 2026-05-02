## 🗺️🧱🧠 _The Grid with Obstacles (Memoization): The Unique Paths II Saga_

> \_"The robot returned to the grid.
> But this time, some cells were blocked --
> **obstacles** that the robot could not pass through.
>
> The grid was no longer a clean open field.
> Walls stood in the way.
>
> The Oracle was commanded:
>
> **'How many unique paths exist
> from top-left to bottom-right,
> avoiding all obstacles?'**
>
> The Oracle's thinking was almost identical
> to Unique Paths I:
>
> **paths(i, j) = paths from above + paths from the left.**
>
> But with one critical addition:
>
> **If cell (i, j) IS an obstacle -- zero paths go through it.
> An obstacle is a dead end. No path can use it.
> It contributes nothing.**
>
> And the base cases needed more care --
> an obstacle in the top row or left column
> blocks everything AFTER it in that row or column."\_

---

This is the saga of **Unique Paths II (Memoization)**.

Given an `m × n` grid where:
-   `0` = open cell.
-   `1` = obstacle.

Robot starts at `(0, 0)`, moves only **down** or **right**.
How many unique paths to `(m-1, n-1)`, avoiding obstacles?

```
Input:
  [[0,0,0],
   [0,1,0],
   [0,0,0]]
Output: 2

Input:
  [[0,1],
   [0,0]]
Output: 1

Input:
  [[1,0]]
Output: 0  (start is blocked)
```

---

## 🧠 Building the Intuition -- What Changes from Unique Paths I?

In Unique Paths I, every cell was open.
The recurrence was clean: `paths(i,j) = paths(i-1,j) + paths(i,j-1)`.

With obstacles, we add ONE rule:

> **If a cell is an obstacle, it has ZERO paths. Period.**

An obstacle can't be walked on. No path passes through it.
It doesn't contribute to any neighbor's count.

The recurrence becomes:

```
If grid[i][j] == 1:  return 0   (obstacle -- dead end)
If i == 0 AND j == 0: return 1  (start cell, if not obstacle)
paths(i, j) = paths(i-1, j) + paths(i, j-1)
```

**What about the base cases?**

In Unique Paths I, the entire top row and left column were 1.
With obstacles, that's no longer true.

If there's an obstacle at `(0, 3)` in the top row,
then `(0, 4)`, `(0, 5)`, ... are ALL unreachable from the top row path.
The straight-line path is BLOCKED.

But with memoization, we don't need to handle this explicitly.
The recursion naturally handles it:
-   `paths(0, 4)` calls `paths(0, 3)`.
-   `paths(0, 3)` sees an obstacle → returns 0.
-   `paths(0, 4)` also calls `paths(-1, 4)` → out of bounds → 0.
-   `paths(0, 4) = 0 + 0 = 0`. Correct!

The obstacle propagates its "zero" through the recursion.

---

### 📜 The Scroll of the Obstructed Grid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🗺️ Setting Up the Cache

```cpp
int uniquePathsWithObstacles(vector<vector<int>>& grid) {
    int m = grid.size();
    int n = grid[0].size();
```

---

### 🛑 "Is the start or end blocked?"

```cpp
    if (grid[0][0] == 1 || grid[m - 1][n - 1] == 1) return 0;
```

If the starting cell is an obstacle -- the robot can't even begin.
If the destination is an obstacle -- the robot can never arrive.
Either way: zero paths.

Checking this upfront avoids wasted recursion.

---

### The 2D cache

```cpp
    vector<vector<int>> memo(m, vector<int>(n, -1));
    return solve(m - 1, n - 1, grid, memo);
}
```

Start from the destination. Recurse backward.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, vector<vector<int>>& grid,
          vector<vector<int>>& memo) {
```

"How many paths reach cell `(i, j)`?"

---

### 🛑 "I've gone off the grid"

```cpp
    if (i < 0 || j < 0) return 0;
```

Negative row or column = outside the grid.
No paths lead here. Zero.

This handles the edges naturally --
when a top-row cell asks "what's above me?" → `i-1 < 0` → 0.
When a left-column cell asks "what's to my left?" → `j-1 < 0` → 0.

---

### 🧱 "This cell is an obstacle"

```cpp
    if (grid[i][j] == 1) return 0;
```

An obstacle. Dead end. No path can use this cell.
Return 0 immediately.

This is the ONLY new line compared to Unique Paths I.
One line. That's the entire difference.

Why does this work so cleanly?
Because when a neighbor asks "how many paths reach you?",
the obstacle answers "zero."
That zero propagates into the neighbor's sum.
The obstacle silently kills every path that would have used it.

---

### 🏁 "I'm at the starting cell"

```cpp
    if (i == 0 && j == 0) return 1;
```

The robot is at `(0, 0)`. It's here. One path: do nothing.

We already checked that `(0, 0)` is not an obstacle (in the wrapper).
So this is safe.

Why `i == 0 && j == 0` instead of `i == 0 || j == 0`?

In Unique Paths I, the entire top row and left column returned 1.
Here, we can't do that -- an obstacle in the top row
would make cells after it unreachable.

By using `i == 0 && j == 0` as the only base case
and letting the recursion handle the rest,
obstacles in the top row and left column are handled automatically.

---

### 📖 Check the cache

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### 🔄 The same recurrence -- from above + from the left

```cpp
    memo[i][j] = solve(i - 1, j, grid, memo)
               + solve(i, j - 1, grid, memo);
```

Paths from above + paths from the left.
Identical to Unique Paths I.

If the cell above is an obstacle → it returns 0 → contributes nothing.
If the cell to the left is an obstacle → it returns 0 → contributes nothing.
The obstacles silently remove themselves from the count.

---

```cpp
    return memo[i][j];
}
```

---

### 🎺 The Trial of the Obstructed Grid

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

**Full trace for grid = [[0,0,0],[0,1,0],[0,0,0]]:**

```
Grid:
  0 0 0
  0 1 0
  0 0 0
```

```
solve(2,2):
  From above: solve(1,2)
    solve(1,2):
      From above: solve(0,2)
        solve(0,2):
          From above: solve(-1,2) → 0 (off grid)
          From left: solve(0,1)
            solve(0,1):
              From above: solve(-1,1) → 0
              From left: solve(0,0) → 1 (start!)
            memo[0][1] = 0 + 1 = 1
          memo[0][2] = 0 + 1 = 1
      From left: solve(1,1)
        grid[1][1] == 1 → OBSTACLE → return 0
      memo[1][2] = 1 + 0 = 1

  From left: solve(2,1)
    solve(2,1):
      From above: solve(1,1) → 0 (OBSTACLE, cached)
      From left: solve(2,0)
        solve(2,0):
          From above: solve(1,0)
            solve(1,0):
              From above: solve(0,0) → 1
              From left: solve(1,-1) → 0
            memo[1][0] = 1
          From left: solve(2,-1) → 0
        memo[2][0] = 1 + 0 = 1
      memo[2][1] = 0 + 1 = 1

  memo[2][2] = 1 + 1 = 2
```

**Answer: 2** ✓

The obstacle at `(1,1)` blocked the diagonal paths.
Only 2 paths survive:
1. Right → Right → Down → Down
2. Down → Down → Right → Right

---

**Trace for grid = [[0,1],[0,0]]:**

```
solve(1,1):
  From above: solve(0,1) → grid[0][1]==1 → OBSTACLE → 0
  From left: solve(1,0)
    From above: solve(0,0) → 1
    From left: solve(1,-1) → 0
  memo[1][0] = 1
  memo[1][1] = 0 + 1 = 1
```

**Answer: 1** ✓ (Down → Right. The top-right path is blocked.)

---

**Trace for grid = [[1,0]]:**

Start cell is obstacle → return 0 immediately.

**Answer: 0** ✓

---

## 🔍 The ONE Line That Changed Everything

Unique Paths I → Unique Paths II.
The ONLY difference in the recursion:

```cpp
if (grid[i][j] == 1) return 0;
```

One line. That's it.

Everything else -- the recurrence, the base case at `(0,0)`,
the cache, the structure -- is identical.

The obstacle check returns 0, and that zero propagates
through every path that would have used this cell.
The recursion handles the rest automatically.

---

## 🔍 Why the Base Case Changed from `i==0 || j==0` to `i==0 && j==0`

In Unique Paths I, the top row and left column were all 1.
We could use `if (i == 0 || j == 0) return 1`.

With obstacles, a cell at `(0, 5)` might be unreachable
if `(0, 3)` is an obstacle. Returning 1 would be WRONG.

By making the only base case `(0, 0)` and letting the recursion
handle everything else, obstacles in the top row and left column
naturally propagate zeros. No special handling needed.

---

### 🧠 Memory of the Obstructed Grid (Memoization) Law

-   **Same as Unique Paths I** with ONE addition: obstacle check
-   **If `grid[i][j] == 1`** → return 0 (dead end, no paths)
-   **If `i < 0 || j < 0`** → return 0 (off grid)
-   **If `i == 0 && j == 0`** → return 1 (start cell)
-   **Recurrence:** `paths(i,j) = paths(i-1,j) + paths(i,j-1)` (unchanged)
-   **Obstacles propagate zeros** through the recursion automatically
-   **Check start/end** for obstacles upfront
-   **Base case is `(0,0)` only** -- not the entire top row/left column
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Unique Paths II (Memoization)**,
where the Oracle added ONE line to the clean grid solution --
"if this cell is an obstacle, return zero" --
and that single zero propagated through the recursion,
silently killing every path that dared pass through a wall,
leaving only the paths that found a way around. 🗺️🧱🧠✨
