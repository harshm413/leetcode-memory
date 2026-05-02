## 🗺️🧠 _The Grid of Choices (Memoization): The Unique Paths Saga_

> \_"A robot stood at the top-left corner of a grid --
> cell `(0, 0)`.
>
> It needed to reach the bottom-right corner --
> cell `(m-1, n-1)`.
>
> The robot could only move in two directions:
> **DOWN** or **RIGHT**. Never up. Never left.
>
> The Oracle was commanded:
>
> **'How many unique paths exist
> from the top-left to the bottom-right?'**
>
> The Oracle thought about the robot's journey.
>
> To arrive at any cell `(i, j)`,
> the robot must have come from either:
> -   The cell ABOVE: `(i-1, j)` -- it moved DOWN.
> -   The cell to the LEFT: `(i, j-1)` -- it moved RIGHT.
>
> There's no other way to reach `(i, j)`
> because the robot can only move down or right.
>
> So the number of paths to `(i, j)` =
> paths to the cell above + paths to the cell on the left.
>
> This was the same Fibonacci-like addition
> that powered Climbing Stairs --
> but now in TWO dimensions."\_

---

This is the saga of **Unique Paths (Memoization)**.

Given a grid of size `m × n`:
-   Robot starts at `(0, 0)`.
-   Robot can only move **down** or **right**.
-   How many unique paths to `(m-1, n-1)`?

```
Input:  m = 3, n = 7
Output: 28

Input:  m = 3, n = 2
Output: 3
```

---

## 🧠 Building the Intuition -- How Does the Robot Arrive?

Stand at any cell `(i, j)` and ask:
**"How did the robot get here?"**

It came from ABOVE `(i-1, j)` -- by moving down.
Or it came from the LEFT `(i, j-1)` -- by moving right.

Those are the ONLY two options.
The robot can't move up or left, so no other cell leads here.

So:

```
paths(i, j) = paths(i-1, j) + paths(i, j-1)
```

**Now, where does the recursion stop?**

**The top row (i = 0):**
The robot can only reach any cell in the top row
by moving RIGHT from `(0, 0)`.
There's exactly 1 path to every cell in the top row.

**The left column (j = 0):**
The robot can only reach any cell in the left column
by moving DOWN from `(0, 0)`.
There's exactly 1 path to every cell in the left column.

**Why 1 and not 0?**
Because there IS a path -- it's just a straight line.
`(0,0) → (0,1) → (0,2) → ...` is one path. Exactly one.
`(0,0) → (1,0) → (2,0) → ...` is one path. Exactly one.

**Base cases:**

```
If i == 0 OR j == 0: return 1
```

---

## 🔍 Why Memoization?

Without caching, the recursion tree has massive overlap:

```
paths(2,2)
├── paths(1,2)
│   ├── paths(0,2) = 1
│   └── paths(1,1)
│       ├── paths(0,1) = 1
│       └── paths(1,0) = 1
└── paths(2,1)
    ├── paths(1,1)       ← RECOMPUTED!
    │   ├── paths(0,1)
    │   └── paths(1,0)
    └── paths(2,0) = 1
```

`paths(1,1)` computed twice. For larger grids, this explodes.
Memoization: compute each `(i, j)` once, cache it.

---

### 📜 The Scroll of the Grid Journey

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🗺️ Setting Up the Cache

```cpp
int uniquePaths(int m, int n) {
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

A 2D cache: `memo[i][j]` = number of paths to cell `(i, j)`.
`-1` means "haven't figured this cell out yet."

Why 2D? Because the state has two dimensions: row and column.
In Climbing Stairs, the state was just one number (which step).
Here, the state is a pair (which row, which column).

---

```cpp
    return solve(m - 1, n - 1, memo);
}
```

Start from the destination `(m-1, n-1)` and ask:
"How many paths lead here?"

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, vector<vector<int>>& memo) {
```

"How many unique paths reach cell `(i, j)`?"

---

### 🛑 "I'm on the top row or left column"

```cpp
    if (i == 0 || j == 0) return 1;
```

If the robot is anywhere on the top row --
there's only one way to get here: go right, right, right...
A single straight line. One path.

If the robot is anywhere on the left column --
there's only one way: go down, down, down...
A single straight line. One path.

Why is this the base case?
Because these cells have only ONE direction of arrival.
Top row cells can only be reached from the left.
Left column cells can only be reached from above.
There's no "choice" -- just one straight road.

---

### 📖 "Have I already figured this cell out?"

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

Check the notebook. If this cell was already solved --
return the cached answer.

---

### 🔄 "The robot came from above or from the left"

```cpp
    memo[i][j] = solve(i - 1, j, memo) + solve(i, j - 1, memo);
```

The number of paths to `(i, j)` =
paths to the cell above `(i-1, j)` -- the robot moved DOWN to get here --
PLUS paths to the cell on the left `(i, j-1)` -- the robot moved RIGHT.

Why addition? Because these are SEPARATE sets of paths.
A path that comes from above is completely different
from a path that comes from the left.
The total is the sum of both.

This is the same logic as Climbing Stairs:
`ways(n) = ways(n-1) + ways(n-2)`.
But now in 2D: `paths(i,j) = paths(i-1,j) + paths(i,j-1)`.

---

```cpp
    return memo[i][j];
}
```

---

### 🎺 The Trial of the Grid Journey

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

**Full trace for m=3, n=2 (3 rows, 2 columns):**

```
Grid:
  (0,0) (0,1)
  (1,0) (1,1)
  (2,0) (2,1) ← destination
```

```
solve(2,1):
  i=2, j=1. Not on top row or left column.

  From above: solve(1,1)
    i=1, j=1. Not base case.
    From above: solve(0,1) → i==0 → return 1
    From left:  solve(1,0) → j==0 → return 1
    memo[1][1] = 1 + 1 = 2

  From left: solve(2,0) → j==0 → return 1

  memo[2][1] = 2 + 1 = 3
```

**Answer: 3** ✓

The 3 paths:
1. Right → Down → Down
2. Down → Right → Down
3. Down → Down → Right

---

**Trace for m=2, n=2:**

```
solve(1,1):
  From above: solve(0,1) → 1
  From left:  solve(1,0) → 1
  memo[1][1] = 1 + 1 = 2
```

**Answer: 2** ✓ (Right→Down or Down→Right)

---

**Trace for m=1, n=1:**

```
solve(0,0): i==0 → return 1
```

**Answer: 1** ✓ (already at destination)

---

**Why m=3, n=7 gives 28:**

The robot must make exactly `(m-1) + (n-1) = 2 + 6 = 8` moves.
Of those, exactly 2 must be DOWN and 6 must be RIGHT.
The number of ways to arrange 2 D's among 8 moves = C(8, 2) = 28.

This is a combinatorics shortcut -- but the DP approach
works even when the grid has obstacles (Unique Paths II).

---

## 🔍 The 2D Extension of Climbing Stairs

| Climbing Stairs                   | Unique Paths                      |
| --------------------------------- | --------------------------------- |
| 1D: which step?                   | 2D: which row, which column?      |
| `ways(n) = ways(n-1) + ways(n-2)` | `paths(i,j) = paths(i-1,j) + paths(i,j-1)` |
| Two sources: step n-1 and n-2     | Two sources: above and left       |
| Base: `ways(0)=1, ways(1)=1`     | Base: top row = 1, left col = 1   |
| Count paths (addition)            | Count paths (addition)            |

Same pattern. One more dimension.

---

## 🔍 How to Arrive at This Solution From Scratch

1. **Ask "how does the robot arrive at (i,j)?"** → from above or from left.
2. **Write the recurrence:** `paths(i,j) = paths(i-1,j) + paths(i,j-1)`.
3. **Find the base cases:** top row and left column = 1 (straight lines).
4. **Add memoization:** 2D cache `memo[i][j]`.

Same 4-step process as every DP problem.
The only new thing: the state is 2D instead of 1D.

---

### 🧠 Memory of the Grid Journey (Memoization) Law

-   **State:** `(i, j)` -- which cell the robot is at
-   **Recurrence:** `paths(i,j) = paths(i-1,j) + paths(i,j-1)`
    -   From above (moved down) + from left (moved right)
-   **Base cases:** `i == 0 || j == 0` → return 1 (straight line, one path)
-   **Why 1?** Because the top row and left column have only one way to reach them
-   **Memoization:** 2D cache `memo[m][n]`
-   **This is 2D Climbing Stairs** -- same addition, one more dimension
-   **Combinatorics shortcut:** answer = C(m+n-2, m-1) -- but DP generalizes to obstacles
-   **Time:** O(M × N) -- each cell solved once
-   **Space:** O(M × N) -- memo + recursion stack

Thus is remembered the saga of **Unique Paths (Memoization)**,
where the Oracle stood at the destination
and asked "how did the robot get here?" --
from above or from the left,
each answer leading to a smaller grid,
until the top row and left column
answered with certainty: "one path, one straight line" --
and the total paths bubbled up
through the grid of choices. 🗺️🧠✨
