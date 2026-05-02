## 🐸📋 _The Frog's Cheapest Path (Tabulation): The Frog Jump Saga_

> \_"The Oracle had solved the frog's journey with memoization --
> top-down, recursive, cached.
>
> Now she built the answer from the ground up.
>
> **Start at stone 0. Cost = 0.
> For each stone, compute the minimum cost
> using the two stones before it.
> Fill the table left to right.
> When the table reaches the last stone --
> the answer is there.**
>
> No recursion. No stack. Pure iteration."\_

---

This is the saga of **Frog Jump (Tabulation / Bottom-Up DP)**.

Same problem:
-   Frog jumps 1 or 2 stones. Cost = `|heights[a] - heights[b]|`.
-   Find minimum total cost to reach the last stone.

```
Input:  heights = [10, 30, 40, 20]  →  Output: 30
Input:  heights = [10, 20, 10]      →  Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Fill Bottom-Up

```
dp[0] = 0                                          (base case)
dp[1] = |heights[1] - heights[0]|                  (only one jump possible)
dp[i] = min(
    dp[i-1] + |heights[i] - heights[i-1]|,
    dp[i-2] + |heights[i] - heights[i-2]|
)
```

Each entry depends only on the two entries before it.
Fill left to right. No recursion.

```
Time:  O(N)
Space: O(N) -- the dp array (or O(1) with optimization)
```

---

### 📜 The Scroll of the Bottom-Up Frog

```cpp
#include <iostream>
#include <vector>
#include <cmath>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int frogJump(vector<int>& heights) {
    int n = heights.size();
    if (n == 1) return 0;
```

Single stone → frog is already there. Cost = 0.

---

```cpp
    vector<int> dp(n);
```

`dp[i]` = minimum cost to reach stone `i`.

---

### 🏁 Fill the Base Cases

```cpp
    dp[0] = 0;
```

Stone 0: the frog starts here. No jump. No cost.

---

```cpp
    dp[1] = abs(heights[1] - heights[0]);
```

Stone 1: only one way to get here -- jump from stone 0.
Cost = the height difference.

> _"The first stone is free.
> The second stone has only one path.
> From these two truths, everything else follows."_

---

### 🔁 Fill the Table Stone by Stone

```cpp
    for (int i = 2; i < n; i++) {
```

For each stone from 2 to n-1.

---

### 🐸 Option 1 -- Came from Stone i-1

```cpp
        int oneStep = dp[i - 1] + abs(heights[i] - heights[i - 1]);
```

Cost to reach `i` via `i-1`:
the cost to reach `i-1` (already computed)
plus the jump cost from `i-1` to `i`.

---

### 🐸 Option 2 -- Came from Stone i-2

```cpp
        int twoStep = dp[i - 2] + abs(heights[i] - heights[i - 2]);
```

Cost to reach `i` via `i-2`:
the cost to reach `i-2` (already computed)
plus the jump cost from `i-2` to `i`.

---

### ⚖️ Take the Minimum

```cpp
        dp[i] = min(oneStep, twoStep);
    }
```

The frog chose the cheaper path.

> _"Two roads led to this stone.
> The frog took the cheaper one.
> The table remembers only the best."_

---

### 📤 Return the Answer

```cpp
    return dp[n - 1];
}
```

The minimum cost to reach the last stone.

---

### 🎺 The Trial of the Bottom-Up Frog

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

**Full table trace for heights = [10, 30, 40, 20]:**

| i | heights[i] | oneStep (from i-1)          | twoStep (from i-2)          | dp[i]     |
|---|-----------|-----------------------------|-----------------------------|-----------|
| 0 | 10        | --                          | --                          | 0 (base)  |
| 1 | 30        | dp[0]+\|30-10\| = 0+20 = 20 | --                          | 20        |
| 2 | 40        | dp[1]+\|40-30\| = 20+10 = 30| dp[0]+\|40-10\| = 0+30 = 30 | min(30,30) = 30 |
| 3 | 20        | dp[2]+\|20-40\| = 30+20 = 50| dp[1]+\|20-30\| = 20+10 = 30| min(50,30) = **30** |

**dp = [0, 20, 30, 30]**

**Answer: dp[3] = 30** ✓

---

**Table trace for heights = [10, 20, 10]:**

| i | oneStep          | twoStep          | dp[i]          |
|---|------------------|------------------|----------------|
| 0 | --               | --               | 0              |
| 1 | 0+10 = 10        | --               | 10             |
| 2 | 10+10 = 20       | 0+0 = 0         | min(20, 0) = **0** |

**Answer: 0** ✓

The 2-step jump from stone 0 to stone 2 costs 0 (same height).

---

**Table trace for heights = [30, 10, 60, 10, 60, 50]:**

| i | heights[i] | oneStep | twoStep | dp[i] |
|---|-----------|---------|---------|-------|
| 0 | 30        | --      | --      | 0     |
| 1 | 10        | 0+20=20 | --      | 20    |
| 2 | 60        | 20+50=70| 0+30=30 | 30    |
| 3 | 10        | 30+50=80| 20+0=20 | 20    |
| 4 | 60        | 20+50=70| 30+0=30 | 30    |
| 5 | 50        | 30+10=40| 20+40=60| **40** |

**Answer: 40** ✓

---

## 🔧 Space Optimization -- O(1) Space

Each stone only depends on the **two previous** stones.
Replace the array with two variables.

```cpp
int frogJump(vector<int>& heights) {
    int n = heights.size();
    if (n == 1) return 0;

    int prev2 = 0;
    int prev1 = abs(heights[1] - heights[0]);
```

`prev2` = `dp[i-2]`, starts as `dp[0] = 0`.
`prev1` = `dp[i-1]`, starts as `dp[1]`.

---

```cpp
    for (int i = 2; i < n; i++) {
        int oneStep = prev1 + abs(heights[i] - heights[i - 1]);
        int twoStep = prev2 + abs(heights[i] - heights[i - 2]);
        int curr = min(oneStep, twoStep);
        prev2 = prev1;
        prev1 = curr;
    }
```

Each iteration:
1. Compute both options using `prev1` and `prev2`.
2. Take the minimum.
3. Slide the window: `prev2 = prev1`, `prev1 = curr`.

---

```cpp
    return prev1;
}
```

After the loop, `prev1` holds `dp[n-1]`.

```
Time:  O(N)
Space: O(1)
```

---

**Space-optimized trace for heights = [10, 30, 40, 20]:**

| i | prev2 | prev1 | oneStep | twoStep | curr = min |
|---|-------|-------|---------|---------|------------|
| - | 0     | 20    | --      | --      | --         |
| 2 | 0     | 20    | 30      | 30      | 30         |
| 3 | 20    | 30    | 50      | 30      | 30         |

**Answer: prev1 = 30** ✓

---

## 🔍 Memoization vs Tabulation for Frog Jump

| Memoization (Top-Down)            | Tabulation (Bottom-Up)            |
| --------------------------------- | --------------------------------- |
| `solve(n-1)` → recurse down      | `for i = 2 to n-1` → iterate up  |
| Cache check: `memo[i] != -1`     | No cache check needed             |
| Recursion stack overhead          | No recursion                      |
| O(N) time, O(N) space            | O(N) time, O(1) space possible    |

Tabulation with space optimization is the cleanest solution.

---

## 🔍 How This Extends to K Steps (Frog Jump with K)

What if the frog can jump up to K stones instead of just 2?

```
dp[i] = min over j from 1 to K of:
    dp[i-j] + |heights[i] - heights[i-j]|
    (only if i-j >= 0)
```

The inner loop checks all K possible previous stones.

```
Time:  O(N × K)
Space: O(N) or O(K)
```

The 2-step version is just K=2.

---

## 🔍 The Optimization DP Pattern

```
At each state i:
  For each choice (1-step, 2-step, ... K-step):
    cost = dp[previous state] + transition cost
  dp[i] = min of all choices
```

This pattern appears everywhere:
-   Frog Jump → min cost, 1 or 2 steps
-   House Robber → max profit, rob or skip
-   Coin Change → min coins to make amount
-   Minimum Cost Climbing Stairs → min cost, 1 or 2 steps with step costs

Same skeleton. Different costs and operations.

---

### 🧠 Memory of the Frog's Journey (Tabulation) Law

-   **Recurrence:** `dp[i] = min(dp[i-1] + |h[i]-h[i-1]|, dp[i-2] + |h[i]-h[i-2]|)`
-   **Base cases:** `dp[0] = 0`, `dp[1] = |h[1] - h[0]|`
-   **Fill order:** left to right, `i = 2` to `n-1`
-   **Space optimization:** two variables `prev1`, `prev2` → O(1)
-   **Extends to K steps:** inner loop over K choices → O(N×K)
-   **Same structure as Climbing Stairs** but `min` instead of `+`
-   **Time:** O(N)
-   **Space:** O(1) optimized
-   **Edge cases:**
    -   Single stone → 0
    -   Two stones → `|h[1] - h[0]|`
    -   All same heights → 0 (every jump is free)

Thus is remembered the saga of **Frog Jump (Tabulation)**,
where the Oracle built the frog's cheapest path
from the ground up --
filling a table stone by stone,
each entry the minimum of two choices --
until the last stone's cost was known,
computed in pure iteration,
with nothing but two variables
carrying the frog across the staircase. 🐸📋✨
