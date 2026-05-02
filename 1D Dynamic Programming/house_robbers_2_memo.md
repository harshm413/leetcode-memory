## 🏠🔄🧠 _The Circular Street (Memoization): The House Robber II Saga_

> \_"The thief had mastered the straight street.
> But now the houses were arranged in a **circle** --
> the first house and the last house were neighbors.
>
> This changed everything.
>
> **If the thief robbed house 0,
> he could NOT rob house n-1 (they are adjacent in the circle).**
>
> **If the thief robbed house n-1,
> he could NOT rob house 0.**
>
> The Oracle was commanded:
>
> **'What is the maximum gold the thief can steal
> from this circular street?'"\_

---

This is the saga of **House Robber II (Memoization)**.

Same rules as House Robber I:
-   Cannot rob two adjacent houses.
-   Maximize total gold.

**New constraint:** the houses are in a **circle** --
house 0 and house n-1 are adjacent.

```
Input:  nums = [2, 3, 2]
Output: 3

Input:  nums = [1, 2, 3, 1]
Output: 4

Input:  nums = [200, 3, 140, 20, 10]
Output: 340
```

---

## 🧠 Building the Intuition -- What Makes the Circle Hard?

On a straight street, house 0 and house n-1 are far apart.
The thief can rob both without worry.

On a circular street, they're NEIGHBORS.
Robbing both triggers the alarm.

So the thief faces a new constraint that didn't exist before:
**the first and last houses are linked.**

How do you handle a constraint between the FIRST and LAST elements
when your DP only looks backward one or two steps?

You can't -- not directly. The recurrence `solve(i) = max(rob, skip)`
only knows about `i-1` and `i-2`. It has no idea whether house 0
was robbed or not by the time it reaches house n-1.

---

## 🧠 The Breakthrough -- Break the Circle

The Oracle realized something elegant:

> **The optimal solution either includes house 0 or it doesn't.**

**If it includes house 0:**
Then house n-1 is FORBIDDEN (they're neighbors in the circle).
So the thief is really solving House Robber I on houses `0` to `n-2`.
The last house doesn't exist in this world.

**If it does NOT include house 0:**
Then house n-1 is AVAILABLE (no conflict).
So the thief is really solving House Robber I on houses `1` to `n-1`.
The first house doesn't exist in this world.

**The answer = max of these two scenarios.**

Why does this cover everything?

-   If the optimal robs house 0 → captured by scenario 1.
-   If the optimal robs house n-1 → captured by scenario 2.
-   If the optimal robs neither → captured by BOTH (max picks either).
-   It can NEVER rob both → so no scenario is missed.

**The circle becomes two straight lines.**
Each line is a standard House Robber I.
We already know how to solve that.

---

### 📜 The Scroll of the Circular Street

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏠 The House Robber I Helper

This is the exact same memoized House Robber I,
but it works on a subrange `[start, end]` instead of the full array.

### The Recursive Solver

```cpp
int solve(int i, int start, vector<int>& nums, vector<int>& memo) {
```

"What's the most gold from houses `start` to `i`?"

The `start` parameter is new -- it tells the recursion
where the street begins. Everything before `start` doesn't exist.

---

### "I've gone past the beginning of MY street"

```cpp
    if (i < start) return 0;
```

The thief has walked past the beginning of his assigned street.
No houses here. Zero gold.

In House Robber I, this was `if (i < 0)`.
Now it's `if (i < start)` because the street might not start at 0.

---

### "There's only the first house of MY street"

```cpp
    if (i == start) return nums[start];
```

One house on this street. Rob it.

---

### Check the notebook

```cpp
    if (memo[i] != -1) return memo[i];
```

---

### The same dilemma -- rob or skip

```cpp
    int pick = nums[i] + solve(i - 2, start, nums, memo);
    int skip = solve(i - 1, start, nums, memo);
```

Identical to House Robber I.
Rob: take gold, jump two back.
Skip: take nothing, stay one back.

The only difference: the recursion stops at `start`, not at 0.

---

### Choose and cache

```cpp
    memo[i] = max(pick, skip);
    return memo[i];
}
```

---

## 🔄 The Circular Wrapper -- Two Straight Streets

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
```

---

### One house? Just rob it.

```cpp
    if (n == 1) return nums[0];
```

A circle of one house. No neighbors to worry about.

---

### Two houses? Pick the richer one.

```cpp
    if (n == 2) return max(nums[0], nums[1]);
```

Two houses in a circle -- they're neighbors.
Can only rob one. Pick the fatter purse.

---

### Scenario 1 -- "I might rob house 0"

```cpp
    vector<int> memo1(n, -1);
    int case1 = solve(n - 2, 0, nums, memo1);
```

The thief considers a world where house n-1 doesn't exist.
He solves House Robber I on houses `0` to `n-2`.

Why `n-2`? Because if house 0 is in play,
house n-1 must be removed (they're circular neighbors).
The street ends one house early.

---

### Scenario 2 -- "I skip house 0"

```cpp
    vector<int> memo2(n, -1);
    int case2 = solve(n - 1, 1, nums, memo2);
```

The thief considers a world where house 0 doesn't exist.
He solves House Robber I on houses `1` to `n-1`.

Why start at 1? Because house 0 is removed.
Now house n-1 is safe -- it has no circular neighbor to conflict with.

---

### The richer world wins

```cpp
    return max(case1, case2);
}
```

Two parallel universes. Two optimal plans.
The thief picks the universe with more gold.

---

### 🎺 The Trial of the Circular Street

```cpp
int main() {
    vector<int> n1 = {2, 3, 2};
    cout << rob(n1) << endl; // expected: 3

    vector<int> n2 = {1, 2, 3, 1};
    cout << rob(n2) << endl; // expected: 4

    vector<int> n3 = {200, 3, 140, 20, 10};
    cout << rob(n3) << endl; // expected: 340

    return 0;
}
```

---

**Full trace for nums = [2, 3, 2]:**

**Scenario 1: houses 0 to 1 → solve(1, 0, ...)**

The thief's street is just `[2, 3]`.

```
solve(1, start=0):
  Rob house 1: 3 + solve(-1, 0) = 3 + 0 = 3
  Skip house 1: solve(0, 0) = nums[0] = 2
  max(3, 2) = 3
```

Case 1 = 3.

**Scenario 2: houses 1 to 2 → solve(2, 1, ...)**

The thief's street is just `[3, 2]`.

```
solve(2, start=1):
  Rob house 2: 2 + solve(0, 1) → 0 < 1 → 0. So 2 + 0 = 2.
  Skip house 2: solve(1, 1) = nums[1] = 3
  max(2, 3) = 3
```

Case 2 = 3.

**Answer: max(3, 3) = 3** ✓

The thief robbed house 1 (gold 3). Houses 0 and 2 are circular neighbors --
robbing both (2+2=4) would trigger the alarm.

---

**Full trace for nums = [1, 2, 3, 1]:**

**Scenario 1: houses 0 to 2 → [1, 2, 3]**

```
solve(0) = 1
solve(1) = max(2+0, 1) = 2
solve(2) = max(3+1, 2) = 4
```

Case 1 = 4.

**Scenario 2: houses 1 to 3 → [2, 3, 1]**

```
solve(1) = 2
solve(2) = max(3+0, 2) = 3
solve(3) = max(1+2, 3) = 3
```

Case 2 = 3.

**Answer: max(4, 3) = 4** ✓

Rob houses 0 and 2: 1+3=4. House 3 (circular neighbor of 0) was excluded in scenario 1.

---

**Trace for nums = [200, 3, 140, 20, 10]:**

**Scenario 1: houses 0 to 3 → [200, 3, 140, 20]**

```
dp[0]=200, dp[1]=max(3,200)=200
dp[2]=max(140+200, 200)=340
dp[3]=max(20+200, 340)=340
```

Case 1 = 340.

**Scenario 2: houses 1 to 4 → [3, 140, 20, 10]**

```
dp[1]=3, dp[2]=max(140,3)=140
dp[3]=max(20+3, 140)=140
dp[4]=max(10+140, 140)=150
```

Case 2 = 150.

**Answer: max(340, 150) = 340** ✓

Rob houses 0 and 2: 200+140=340.

---

## 🔍 Why Two Subproblems and Not One?

You might think: "just remove one house and solve once."

But removing house 0 misses solutions that include house 0.
Removing house n-1 misses solutions that include house n-1.

You need BOTH subproblems because you don't know in advance
which house the optimal solution includes.
`max` picks the better one after both are solved.

---

## 🔍 The "Break the Circle" Pattern

Whenever a circular constraint makes DP hard,
try splitting into two linear subproblems:

-   **House Robber II** → two House Robber I calls
-   **Circular Subarray Maximum** → two Kadane calls
-   **Pizza with 3n Slices** → two linear DP calls

The circle adds ONE constraint (first ↔ last).
Two linear problems, each removing one end, cover all cases.

---

## 🔍 How to Arrive at This Solution From Scratch

1. **Recognize the circle:** house 0 and n-1 are adjacent.
2. **Realize the conflict:** can't rob both. But DP doesn't track house 0.
3. **Key insight:** the optimal either includes house 0 or it doesn't.
4. **Split:** two House Robber I problems, each excluding one end.
5. **Combine:** `max(case1, case2)`.

The hard part is step 3 -- realizing you can split the circle.
Once you see it, the code writes itself.

---

### 🧠 Memory of the Circular Street (Memoization) Law

-   **The circle problem:** house 0 and n-1 are neighbors
-   **The split:** two straight streets
    -   Case 1: houses `0` to `n-2` (may include first, exclude last)
    -   Case 2: houses `1` to `n-1` (exclude first, may include last)
-   **Each case** is standard House Robber I memoization
-   **Answer = max(case1, case2)**
-   **Why it works:** optimal either includes house 0 or house n-1, never both
-   **The helper** is House Robber I with a `start` parameter
-   **Edge cases:** n=1 → rob it. n=2 → rob the richer.
-   **Time:** O(N). **Space:** O(N).

Thus is remembered the saga of **House Robber II (Memoization)**,
where the Oracle broke the circular street
into two straight streets --
one where the first house existed and the last didn't,
one where the last house existed and the first didn't --
solving each with the familiar rob-or-skip dilemma,
and choosing the richer universe --
because in a circle,
the first and last can never both be robbed. 🏠🔄🧠✨
