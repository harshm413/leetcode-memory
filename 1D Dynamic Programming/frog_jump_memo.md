## 🐸🧠 _The Frog's Cheapest Path (Memoization): The Frog Jump Saga_

> \_"A frog stood at the base of a staircase of stones.
> Each stone had a height.
> The frog could jump to the next stone (i+1)
> or skip one and jump to the stone after (i+2).
>
> Every jump had a cost:
> **the absolute difference in height
> between the stone it left and the stone it landed on.**
>
> The Oracle was commanded:
>
> **'Find the minimum total cost
> for the frog to reach the last stone.'**
>
> The Oracle thought recursively:
>
> **To reach stone `i`, the frog must have jumped from
> either stone `i-1` (1-step jump)
> or stone `i-2` (2-step jump).**
>
> The cost to reach stone `i` =
> the MINIMUM of:
> -   cost to reach `i-1` + cost of jumping from `i-1` to `i`
> -   cost to reach `i-2` + cost of jumping from `i-2` to `i`
>
> Same structure as Climbing Stairs --
> but instead of COUNTING paths (addition),
> we are MINIMIZING cost (min).
>
> Recursion + memoization.
> Top-down DP."\_

---

This is the saga of **Frog Jump (Memoization / Top-Down DP)**.

Given an array `heights` of `n` stones:
-   The frog starts at stone `0`.
-   It can jump to stone `i+1` or `i+2`.
-   Cost of a jump from stone `a` to stone `b` = `|heights[a] - heights[b]|`.
-   Find the **minimum total cost** to reach stone `n-1`.

```
Input:  heights = [10, 30, 40, 20]
Output: 30

Paths:
  0→1→2→3: |10-30| + |30-40| + |40-20| = 20+10+20 = 50
  0→1→3:   |10-30| + |30-20| = 20+10 = 30
  0→2→3:   |10-40| + |40-20| = 30+20 = 50
Minimum = 30

Input:  heights = [10, 20, 10]
Output: 0   (0→2: |10-10| = 0)
```

---

## 🧠 The Oracle's Core Insight -- The Recurrence

To reach stone `i`, the frog came from either `i-1` or `i-2`.

```
cost(i) = min(
    cost(i-1) + |heights[i] - heights[i-1]|,
    cost(i-2) + |heights[i] - heights[i-2]|
)
```

**Base cases:**
-   `cost(0) = 0` -- the frog starts here. No jump needed. Zero cost.
-   `cost(1) = |heights[1] - heights[0]|` -- only one possible jump.

**Comparison with Climbing Stairs:**

```
Climbing Stairs: ways(i) = ways(i-1) + ways(i-2)       ← COUNT (add)
Frog Jump:       cost(i) = min(cost(i-1)+c1, cost(i-2)+c2)  ← OPTIMIZE (min)
```

Same skeleton. Different operation.

---

## 🔍 Why Memoization Is Needed

Without memoization, the recursion tree has overlapping subproblems:

```
cost(4)
├── cost(3)
│   ├── cost(2)
│   │   ├── cost(1)
│   │   └── cost(0)
│   └── cost(1)        ← RECOMPUTED
└── cost(2)            ← RECOMPUTED
    ├── cost(1)        ← RECOMPUTED
    └── cost(0)
```

`cost(2)` computed twice. `cost(1)` computed three times.
Exponential without memoization. Linear with it.

---

### 📜 The Scroll of the Frog's Journey

```cpp
#include <iostream>
#include <vector>
#include <cmath>
using namespace std;
```

---

## 🐸 The Memoization Ritual

### The Cache

```cpp
int frogJump(vector<int>& heights) {
    int n = heights.size();
    vector<int> memo(n, -1);
```

`memo[i]` = minimum cost to reach stone `i`.
`-1` means "not yet computed."

---

### Launch the Recursion

```cpp
    return solve(n - 1, heights, memo);
}
```

Start from the last stone (`n-1`).
The recursion works its way down to stone 0.

---

## 🔮 The Recursive Function

```cpp
int solve(int i, vector<int>& heights, vector<int>& memo) {
```

The Oracle was asked: "What is the minimum cost to reach stone `i`?"

---

### 🛑 Base Case -- Starting Stone

```cpp
    if (i == 0) return 0;
```

The frog starts at stone 0. No jump needed. Cost = 0.

---

### 📖 Check the Cache

```cpp
    if (memo[i] != -1) return memo[i];
```

Already solved? Return the cached answer.

---

### 🐸 Option 1 -- Jump from Stone i-1 (1-step jump)

```cpp
    int oneStep = solve(i - 1, heights, memo)
                  + abs(heights[i] - heights[i - 1]);
```

The cost of reaching stone `i` via stone `i-1`:
-   Cost to reach `i-1` (recursively solved).
-   Plus the jump cost: `|heights[i] - heights[i-1]|`.

This option ALWAYS exists (as long as `i >= 1`).

> _"The frog could have jumped from the stone just below.
> The cost is whatever it took to get there,
> plus the effort of this one jump."_

---

### 🐸 Option 2 -- Jump from Stone i-2 (2-step jump)

```cpp
    int twoStep = INT_MAX;
    if (i >= 2) {
        twoStep = solve(i - 2, heights, memo)
                  + abs(heights[i] - heights[i - 2]);
    }
```

The cost of reaching stone `i` via stone `i-2`:
-   Cost to reach `i-2` (recursively solved).
-   Plus the jump cost: `|heights[i] - heights[i-2]|`.

This option only exists if `i >= 2`.
For stone 1, there's no stone `i-2 = -1`.
`INT_MAX` ensures this option is ignored when unavailable.

> _"The frog could have skipped a stone
> and jumped from two below.
> But only if that stone exists."_

---

### ⚖️ Take the Minimum

```cpp
    memo[i] = min(oneStep, twoStep);
```

The frog chose the cheaper option.
The result was cached.

> _"Two roads led here.
> The frog took the one that cost less.
> That is the essence of dynamic programming --
> optimal substructure."_

---

### 📤 Return the Answer

```cpp
    return memo[i];
}
```

---

### 🎺 The Trial of the Frog's Journey

```cpp
int main() {
    vector<int> h1 = {10, 30, 40, 20};
    cout << frogJump(h1) << endl; // expected: 30

    vector<int> h2 = {10, 20, 10};
    cout << frogJump(h2) << endl; // expected: 0

    vector<int> h3 = {30, 10, 60, 10, 60, 50};
    cout << frogJump(h3) << endl; // expected: 40

    return 0;
}
```

---

**Full recursion trace for heights = [10, 30, 40, 20]:**

```
solve(3):
  oneStep = solve(2) + |20 - 40|
    solve(2):
      oneStep = solve(1) + |40 - 30|
        solve(1):
          oneStep = solve(0) + |30 - 10| = 0 + 20 = 20
          twoStep = N/A (i < 2)
        memo[1] = 20
      oneStep = 20 + 10 = 30
      twoStep = solve(0) + |40 - 10| = 0 + 30 = 30
    memo[2] = min(30, 30) = 30
  oneStep = 30 + 20 = 50

  twoStep = solve(1) + |20 - 30|
    solve(1) → 20 (CACHED!)
  twoStep = 20 + 10 = 30

memo[3] = min(50, 30) = 30
```

**Answer: 30** ✓

Path: 0→1→3. Cost: |10-30| + |30-20| = 20 + 10 = 30.

---

**Trace for heights = [10, 20, 10]:**

```
solve(2):
  oneStep = solve(1) + |10 - 20|
    solve(1) = solve(0) + |20 - 10| = 0 + 10 = 10
  oneStep = 10 + 10 = 20

  twoStep = solve(0) + |10 - 10| = 0 + 0 = 0

memo[2] = min(20, 0) = 0
```

**Answer: 0** ✓

Path: 0→2. Cost: |10-10| = 0. The frog skipped stone 1 entirely.

---

**Trace for heights = [30, 10, 60, 10, 60, 50]:**

```
memo[0] = 0
memo[1] = 0 + |10-30| = 20
memo[2] = min(20 + |60-10|, 0 + |60-30|) = min(70, 30) = 30
memo[3] = min(30 + |10-60|, 20 + |10-10|) = min(80, 20) = 20
memo[4] = min(20 + |60-10|, 30 + |60-60|) = min(70, 30) = 30
memo[5] = min(30 + |50-60|, 20 + |50-10|) = min(40, 60) = 40
```

**Answer: 40** ✓

---

## 🔍 The DP Pattern -- Optimization Over Choices

This problem follows the classic DP optimization pattern:

```
At each state i:
  Consider all choices (jump 1 or jump 2).
  For each choice: cost = subproblem cost + transition cost.
  Take the minimum (or maximum, depending on the problem).
  Cache the result.
```

This pattern appears in:
-   Frog Jump (min cost, 1 or 2 steps)
-   House Robber (max profit, rob or skip)
-   Coin Change (min coins)
-   Minimum Path Sum (min cost in grid)

The skeleton is always the same. Only the choices and costs change.

---

### 🧠 Memory of the Frog's Journey (Memoization) Law

-   **Recurrence:** `cost(i) = min(cost(i-1) + |h[i]-h[i-1]|, cost(i-2) + |h[i]-h[i-2]|)`
-   **Base case:** `cost(0) = 0` (start here, no cost)
-   **Memoization:** `memo[i]` caches the minimum cost to reach stone `i`
-   **Two choices:** 1-step jump (always available) or 2-step jump (if `i >= 2`)
-   **Take the min** of both choices
-   **Same structure as Climbing Stairs** but `min` instead of `+`
-   **Time:** O(N) -- each stone solved once
-   **Space:** O(N) -- memo array + recursion stack
-   **Edge cases:**
    -   Single stone → 0 (already there)
    -   Two stones → `|h[1] - h[0]|` (only one jump)
    -   Equal heights → cost can be 0

Thus is remembered the saga of **Frog Jump (Memoization)**,
where the frog stood at the last stone
and asked "what was the cheapest way to get here?" --
looking back at the two stones below,
computing the cost of each path,
choosing the minimum --
and caching every answer
so no stone's cost was ever computed twice. 🐸🧠✨
