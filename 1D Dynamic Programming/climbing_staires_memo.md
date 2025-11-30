## 🧗 _The Ascent of the Twin-Choice Mountain: The Climbing Stairs (Memoization) Saga_

> \*"Upon the Mountain of Steps stood an ancient challenge:
> a summit of `n` stairs,
> where each climber could rise by **one** or **two** steps at a time.
>
> Many tried to count the number of ways to reach the peak,
> but the paths grew tangled —
> repeating patterns, familiar slopes.
>
> The Oracle discovered a truth:
>
> **‘Every height remembers its ways.’**
>
> Using Memoization,
> she stored the wisdom of each step,
> preventing the climb from being repeated endlessly."\*

---

A traveler wished to know **how many distinct ways** existed
to climb a staircase of `n` steps,
moving either **1 step** or **2 steps** at a time.

The Oracle recognized this as a sacred pattern —
a recursion of overlapping climbs:

-   Ways to reach step `n` =
    ways to reach step `n-1` + ways to reach step `n-2`

But to prevent climbing the same sub-mountains repeatedly,
she carved a memory cache into the cliffside:
**memoization**.

Thus began the saga of **Climbing Stairs with Memoization**.

---

### 📜 The Scroll of the Mountain Paths

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The ascent would be guided by recursive wisdom
and the memory of past climbs.

---

## 🧗‍♀️ The Oracle’s Memoized Climb

```cpp
int climb(int n, vector<int>& memo) {
    if (n <= 2) return n;
```

At the base of the mountain:

-   1 step → 1 way
-   2 steps → 2 ways

These truths needed no further recursion.

---

### ⛰️ Step 1 — Check the Carved Memory

```cpp
    if (memo[n] != -1) return memo[n];
```

If a height had been climbed before,
its number of paths was already etched into the cliff.
No need to rediscover it.

---

### 🌀 Step 2 — Climb Recursively

```cpp
    memo[n] = climb(n - 1, memo) + climb(n - 2, memo);
    return memo[n];
}
```

The Oracle ascended two possible routes:

-   A single-step rise from `n-1`
-   A double-step leap from `n-2`

Their sum was inscribed into memory,
ensuring no height was climbed twice.

---

## 🌄 The Invocation of the Great Ascent

```cpp
int climbStairs(int n) {
    vector<int> memo(n + 1, -1);
    return climb(n, memo);
}
```

The Oracle prepared the memo table,
all values initially unmarked (`-1`).
She then began her climb.

---

### 🎺 The Trial of the Summit

```cpp
int main() {
    cout << climbStairs(5) << endl; // expected: 8
    cout << climbStairs(10) << endl; // expected: 89
    return 0;
}
```

For 5 steps,
the Oracle discovered **8 unique ways**.

For 10 steps,
the mountain revealed **89 paths**,
each remembered through memoization.

---

### 🧠 Memory of the Mountain

-   At each step `n`:
    `ways(n) = ways(n-1) + ways(n-2)`
-   Memoization prevents recomputation of subpaths.
-   **Time:** O(n)
-   **Space:** O(n) for the memo.
-   Mirrors the Fibonacci pattern of ascents.

Thus is remembered the saga of **Climbing Stairs (Memoization)**,
where the Oracle ascends the twin-choice mountain,
etching every step’s wisdom into stone
until the summit is reached with perfect efficiency. ⛰️✨
