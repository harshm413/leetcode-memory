## 🪜📋 _The Staircase of Choices (Tabulation): The Climbing Stairs Saga_

> \_"The Oracle had solved the staircase with memoization --
> top-down, recursive, cached.
>
> But now she sought a cleaner path:
>
> **Build the answer from the bottom up.
> No recursion. No stack. No cache lookups.
> Just a table, filled step by step,
> from the ground to the top.**
>
> This was **tabulation** -- bottom-up dynamic programming.
>
> Start at step 0. Fill step 1. Then step 2.
> Each step's answer was computed from
> the two steps below it -- already filled.
>
> When the table reached step `n` --
> the answer was sitting right there.
>
> No recursion overhead.
> No risk of stack overflow.
> Pure iteration."\_

---

This is the saga of **Climbing Stairs (Tabulation / Bottom-Up DP)**.

Same problem:
-   `n` steps. Climb 1 or 2 at a time.
-   How many distinct ways to reach the top?

```
Input:  n = 2  →  Output: 2
Input:  n = 3  →  Output: 3
Input:  n = 5  →  Output: 8
```

---

## 🧠 The Oracle's Core Insight -- Fill the Table Bottom-Up

Instead of starting at `n` and recursing down (top-down),
start at `0` and iterate up (bottom-up).

```
dp[0] = 1   (base case)
dp[1] = 1   (base case)
dp[i] = dp[i-1] + dp[i-2]   for i = 2, 3, ..., n
```

Each entry depends only on the two entries before it.
Fill left to right. No recursion needed.

```
Time:  O(N)
Space: O(N) -- the dp array
```

---

### 📜 The Scroll of the Bottom-Up Staircase

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int climbStairs(int n) {
    if (n <= 1) return 1;
```

Handle the trivial cases.
`n = 0` → 1 way (do nothing).
`n = 1` → 1 way (one step).

---

```cpp
    vector<int> dp(n + 1);
```

`dp[i]` = number of ways to reach step `i`.
Size `n + 1` for indices 0 through n.

---

### 🏁 Fill the Base Cases

```cpp
    dp[0] = 1;
    dp[1] = 1;
```

Step 0: 1 way (stand still).
Step 1: 1 way (one step from ground).

These are the foundation. Everything else builds on them.

> _"The ground is certain. The first step is certain.
> From these two truths, all other truths are built."_

---

### 🔁 Fill the Table Step by Step

```cpp
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
```

For each step from 2 to n:

The number of ways to reach step `i` =
ways to reach step `i-1` (then take 1 step)
PLUS ways to reach step `i-2` (then take 2 steps).

Both `dp[i-1]` and `dp[i-2]` are already filled
(we're iterating left to right).

> _"Each step looks back at the two steps below.
> Both are already known.
> The sum is the answer.
> No recursion. No waiting. Just reading what's already there."_

---

### 📤 Return the Answer

```cpp
    return dp[n];
}
```

The answer for step `n` was computed and sits in `dp[n]`.

---

### 🎺 The Trial of the Bottom-Up Staircase

```cpp
int main() {
    cout << climbStairs(2) << endl;  // expected: 2
    cout << climbStairs(3) << endl;  // expected: 3
    cout << climbStairs(5) << endl;  // expected: 8
    cout << climbStairs(10) << endl; // expected: 89
    return 0;
}
```

---

**Full table trace for n = 5:**

| i | dp[i-2] | dp[i-1] | dp[i] = dp[i-1] + dp[i-2] |
|---|---------|---------|---------------------------|
| 0 | --      | --      | 1 (base case)             |
| 1 | --      | --      | 1 (base case)             |
| 2 | dp[0]=1 | dp[1]=1 | 1 + 1 = **2**            |
| 3 | dp[1]=1 | dp[2]=2 | 2 + 1 = **3**            |
| 4 | dp[2]=2 | dp[3]=3 | 3 + 2 = **5**            |
| 5 | dp[3]=3 | dp[4]=5 | 5 + 3 = **8**            |

**dp = [1, 1, 2, 3, 5, 8]**

**Answer: dp[5] = 8** ✓

---

**Table for n = 10:**

```
dp = [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
```

**Answer: dp[10] = 89** ✓

Pure Fibonacci.

---

## 🔧 Space Optimization -- O(1) Space

Each step only depends on the **two previous** steps.
We don't need the entire array -- just two variables.

```cpp
int climbStairs(int n) {
    if (n <= 1) return 1;

    int prev2 = 1;  // dp[i-2], starts as dp[0]
    int prev1 = 1;  // dp[i-1], starts as dp[1]
```

Two variables replace the entire array.
`prev2` = the step two below. `prev1` = the step one below.

---

```cpp
    for (int i = 2; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
```

Each iteration:
1. `curr = prev1 + prev2` -- compute the current step.
2. Slide the window: `prev2` becomes old `prev1`, `prev1` becomes `curr`.

> _"The Oracle does not need to remember every step.
> She only needs the last two.
> The past beyond that is irrelevant."_

---

```cpp
    return prev1;
}
```

After the loop, `prev1` holds `dp[n]`.

```
Time:  O(N)
Space: O(1) -- only two variables
```

---

**Trace of space-optimized version for n = 5:**

| i | prev2 | prev1 | curr = prev1 + prev2 |
|---|-------|-------|----------------------|
| - | 1     | 1     | --                   |
| 2 | 1     | 1     | 2                    |
| 3 | 1     | 2     | 3                    |
| 4 | 2     | 3     | 5                    |
| 5 | 3     | 5     | 8                    |

**Answer: prev1 = 8** ✓

Same answer. Zero extra memory.

---

## 🔍 Memoization vs Tabulation -- Side by Side

| Memoization (Top-Down)            | Tabulation (Bottom-Up)            |
| --------------------------------- | --------------------------------- |
| Recursive                         | Iterative                         |
| Starts from `n`, goes down        | Starts from `0`, goes up          |
| Uses a cache (`memo[]`)           | Uses a table (`dp[]`)             |
| Solves only needed subproblems    | Solves ALL subproblems 0 to n     |
| Recursion stack overhead          | No recursion overhead             |
| Risk of stack overflow for large n| No stack overflow risk            |
| O(N) time, O(N) space            | O(N) time, O(N) or O(1) space    |

**For this problem:** tabulation with O(1) space is the best.
But memoization is easier to derive from the recurrence.

**General advice:**
-   Start with memoization (easier to think about).
-   Convert to tabulation (more efficient).
-   Optimize space if possible (sliding window on the table).

---

## 🔍 Why Tabulation Doesn't Need a Cache Check

In memoization, we check `if (memo[n] != -1)` to avoid recomputation.

In tabulation, there's no recomputation possible --
we fill each entry exactly once, left to right.
`dp[i]` is computed once and never touched again.

The iteration order guarantees that dependencies
(`dp[i-1]` and `dp[i-2]`) are always ready
before `dp[i]` is computed.

> _"In tabulation, the past is always complete.
> There is no need to check if something was already computed.
> The table fills forward, never backward."_

---

## 🔍 The Three Levels of DP Mastery

```
Level 1: Memoization (recursion + cache)
  → Easiest to derive. Think recursively, add a cache.

Level 2: Tabulation (iterative table)
  → Convert the recurrence to a loop. Fill bottom-up.

Level 3: Space Optimization (sliding window)
  → If dp[i] depends on only the last K entries,
    use K variables instead of the full array.
```

For Climbing Stairs:
-   Level 1: `memo[]` array, recursive.
-   Level 2: `dp[]` array, iterative.
-   Level 3: Two variables `prev1`, `prev2`. O(1) space.

---

### 🧠 Memory of the Staircase (Tabulation) Law

-   **Recurrence:** `dp[i] = dp[i-1] + dp[i-2]`
-   **Base cases:** `dp[0] = 1`, `dp[1] = 1`
-   **Fill order:** left to right, `i = 2` to `n`
-   **Space optimization:** only two variables needed (`prev1`, `prev2`)
-   **This is Fibonacci** -- tabulation is the classic iterative Fibonacci
-   **No recursion, no cache checks** -- pure iteration
-   **Time:** O(N)
-   **Space:** O(N) for table, O(1) for optimized
-   **Edge cases:**
    -   n = 0 → 1
    -   n = 1 → 1
    -   n = 2 → 2

Thus is remembered the saga of **Climbing Stairs (Tabulation)**,
where the Oracle did not recurse from the top
but instead built the answer from the ground up --
filling a table step by step,
each entry the sum of the two before it --
until the answer at step `n` was known,
computed in pure iteration,
with nothing but two variables
carrying the weight of the entire staircase. 🪜📋✨
