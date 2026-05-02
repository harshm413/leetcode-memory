## 🪜🧠 _The Staircase of Choices (Memoization): The Climbing Stairs Saga_

> \_"At the base of a great staircase stood a traveler.
> The staircase had `n` steps.
> At each moment, the traveler could climb
> **1 step** or **2 steps**.
>
> The Oracle was commanded:
>
> **'How many DISTINCT ways can the traveler
> reach the top of the staircase?'**
>
> The Oracle thought recursively:
>
> **To reach step `n`, the traveler must have come from
> either step `n-1` (took 1 step)
> or step `n-2` (took 2 steps).**
>
> So the number of ways to reach step `n` =
> ways to reach step `n-1` + ways to reach step `n-2`.
>
> This was the **Fibonacci recurrence** in disguise.
>
> But naive recursion would recompute the same subproblems
> over and over -- exponential time.
>
> The Oracle added **memoization** --
> a cache that remembered every answer once computed.
> If a subproblem was already solved -- return the cached answer.
> If not -- solve it, cache it, then return.
>
> Recursion + cache = memoization.
> Top-down dynamic programming."\_

---

This is the saga of **Climbing Stairs (Memoization / Top-Down DP)**.

You are climbing a staircase with `n` steps.
Each time you can climb **1 or 2 steps**.
How many **distinct ways** can you reach the top?

```
Input:  n = 2
Output: 2   (1+1 or 2)

Input:  n = 3
Output: 3   (1+1+1, 1+2, 2+1)

Input:  n = 5
Output: 8
```

---

## 🧠 The Oracle's Core Insight -- The Recurrence

To reach step `n`, the last move was either:
-   **1 step** from step `n-1`, OR
-   **2 steps** from step `n-2`.

So:

```
ways(n) = ways(n-1) + ways(n-2)
```

**Base cases:**
-   `ways(0) = 1` -- there's 1 way to stand at the ground (do nothing).
-   `ways(1) = 1` -- there's 1 way to reach step 1 (one single step).

This is exactly the **Fibonacci sequence**:
`1, 1, 2, 3, 5, 8, 13, 21, ...`

---

## 🔍 Why Naive Recursion Is Too Slow

Without memoization:

```
ways(5)
├── ways(4)
│   ├── ways(3)
│   │   ├── ways(2)
│   │   │   ├── ways(1) = 1
│   │   │   └── ways(0) = 1
│   │   └── ways(1) = 1
│   └── ways(2)          ← RECOMPUTED!
│       ├── ways(1) = 1
│       └── ways(0) = 1
└── ways(3)              ← RECOMPUTED!
    ├── ways(2)          ← RECOMPUTED AGAIN!
    │   ├── ways(1) = 1
    │   └── ways(0) = 1
    └── ways(1) = 1
```

`ways(3)` is computed twice. `ways(2)` is computed three times.
For large `n`, this explodes to **O(2ⁿ)** -- exponential.

**Memoization fixes this:** compute each subproblem ONCE,
store the result, reuse it forever.

With memoization: **O(n)** time, **O(n)** space.

---

### 📜 The Scroll of the Staircase

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🪜 The Memoization Ritual

### The Cache

```cpp
int climbStairs(int n) {
    vector<int> memo(n + 1, -1);
```

`memo[i]` stores the number of ways to reach step `i`.
`-1` means "not yet computed."

Size `n + 1` because we need indices 0 through n.

> _"The cache is the Oracle's memory.
> Once a step's answer is known,
> it is written here and never recomputed."_

---

### Launch the Recursion

```cpp
    return solve(n, memo);
}
```

Start solving from step `n` (the top).
The recursion will work its way down to the base cases.

---

## 🔮 The Recursive Function

```cpp
int solve(int n, vector<int>& memo) {
```

The Oracle was asked: "How many ways to reach step `n`?"

---

### 🛑 Base Case -- Ground Level

```cpp
    if (n == 0) return 1;
```

Standing at the ground. There's exactly 1 way to be here:
do nothing. This is the "empty path."

---

### 🛑 Base Case -- First Step

```cpp
    if (n == 1) return 1;
```

Step 1. There's exactly 1 way: take one single step from the ground.

---

### 📖 Check the Cache

```cpp
    if (memo[n] != -1) return memo[n];
```

If this subproblem was already solved --
return the cached answer immediately.
No recomputation needed.

> _"Have I answered this question before?
> If yes -- the answer is already in my memory.
> I do not think twice about what I already know."_

---

### 🔄 The Recurrence -- Solve and Cache

```cpp
    memo[n] = solve(n - 1, memo) + solve(n - 2, memo);
```

The number of ways to reach step `n` =
ways to reach step `n-1` (then take 1 step)
PLUS ways to reach step `n-2` (then take 2 steps).

The result was stored in the cache for future use.

> _"To reach this step,
> I could have come from one step below
> or two steps below.
> The total ways = the sum of both."_

---

### 📤 Return the Answer

```cpp
    return memo[n];
}
```

The cached answer was returned.

---

### 🎺 The Trial of the Staircase

```cpp
int main() {
    cout << climbStairs(2) << endl; // expected: 2
    cout << climbStairs(3) << endl; // expected: 3
    cout << climbStairs(5) << endl; // expected: 8
    cout << climbStairs(10) << endl; // expected: 89
    return 0;
}
```

---

**Full recursion trace for n = 5 (with memoization):**

```
solve(5)
  solve(4)
    solve(3)
      solve(2)
        solve(1) → 1 (base case)
        solve(0) → 1 (base case)
      memo[2] = 1 + 1 = 2
      solve(1) → 1 (base case)
    memo[3] = 2 + 1 = 3
    solve(2) → 2 (CACHED! no recomputation)
  memo[4] = 3 + 2 = 5
  solve(3) → 3 (CACHED! no recomputation)
memo[5] = 5 + 3 = 8
```

**Answer: 8** ✓

Without memoization: 15 function calls.
With memoization: 9 function calls (6 unique subproblems).
For large n, the savings are astronomical.

---

**Trace for n = 3:**

```
solve(3)
  solve(2)
    solve(1) → 1
    solve(0) → 1
  memo[2] = 2
  solve(1) → 1
memo[3] = 2 + 1 = 3
```

**Answer: 3** ✓

The 3 ways: `1+1+1`, `1+2`, `2+1`.

---

**Trace for n = 2:**

```
solve(2)
  solve(1) → 1
  solve(0) → 1
memo[2] = 1 + 1 = 2
```

**Answer: 2** ✓

The 2 ways: `1+1`, `2`.

---

## 🔍 Why `ways(0) = 1` and Not 0?

This is the most common confusion.

`ways(0) = 1` means: "there is 1 way to stand at the ground."
That way is: **do nothing** (the empty path).

If `ways(0) = 0`, then `ways(2) = ways(1) + ways(0) = 1 + 0 = 1`.
But we know there are 2 ways to reach step 2 (`1+1` and `2`).
So `ways(0)` MUST be 1 for the recurrence to work.

Think of it as: the path `2` (take 2 steps from ground)
starts at step 0 and jumps to step 2.
That path EXISTS → `ways(0)` must count it → `ways(0) = 1`.

---

## 🔍 Why This Is Fibonacci

```
ways(0) = 1
ways(1) = 1
ways(2) = ways(1) + ways(0) = 1 + 1 = 2
ways(3) = ways(2) + ways(1) = 2 + 1 = 3
ways(4) = ways(3) + ways(2) = 3 + 2 = 5
ways(5) = ways(4) + ways(3) = 5 + 3 = 8
```

This IS the Fibonacci sequence (shifted by 1 index).
The staircase problem is Fibonacci in disguise.

---

## 🔍 Memoization -- The Top-Down Pattern

```
1. Define the recursive function with parameters = state.
2. Check base cases → return directly.
3. Check cache → if already solved, return cached answer.
4. Solve the recurrence → cache the result → return it.
```

This pattern works for ALL DP problems.
The state here is just `n` (which step).
For harder problems, the state might be `(i, j)` or `(i, remaining)`.

---

### 🧠 Memory of the Staircase (Memoization) Law

-   **Recurrence:** `ways(n) = ways(n-1) + ways(n-2)`
-   **Base cases:** `ways(0) = 1`, `ways(1) = 1`
-   **Memoization:** `memo[n]` caches the answer for step `n`
-   **Check cache before computing** → avoids recomputation
-   **This is Fibonacci** -- same recurrence, same base cases
-   **Top-down:** start from `n`, recurse down to base cases
-   **Time:** O(N) -- each subproblem solved once
-   **Space:** O(N) -- memo array + recursion stack
-   **Edge cases:**
    -   n = 0 → 1 (do nothing)
    -   n = 1 → 1 (one step)
    -   n = 2 → 2 (1+1 or 2)

Thus is remembered the saga of **Climbing Stairs (Memoization)**,
where the Oracle stood at the top of the staircase
and asked "how did I get here?" --
recursing down to the base,
caching every answer along the way,
so that no step's count was ever computed twice --
and the total number of paths
rose from the base like Fibonacci itself. 🪜🧠✨
