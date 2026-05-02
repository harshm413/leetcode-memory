## ⚖️🔢🧠 _Counting the Splits (Memoization): The Count Partitions with Given Difference Saga_

> \_"The Oracle was given an array and a difference `d`.
>
> She was commanded:
>
> **'Split this array into two subsets S1 and S2
> such that S1 - S2 = d.
> How many ways can you do this?'**
>
> The Oracle did not panic.
> She had already mastered Count Subsets with Sum K.
>
> She just needed to figure out:
> **what target should I count subsets for?**
>
> She wrote two equations:
>
> ```
> S1 + S2 = total       (both subsets together = everything)
> S1 - S2 = d           (the required difference)
> ```
>
> Adding both equations:
>
> ```
> 2 * S1 = total + d
> S1 = (total + d) / 2
> ```
>
> **The problem reduces to:
> Count the number of subsets that sum to `(total + d) / 2`.**
>
> That's Count Subsets with Sum K.
> Already solved.
>
> The hard part was the REDUCTION --
> turning a partition-with-difference problem
> into a subset-sum-counting problem.
> The DP itself was identical."\_

---

This is the saga of **Count Partitions with Given Difference (Memoization)**.

Given:
-   An array `arr` of non-negative integers.
-   A difference `d`.

Split `arr` into two subsets S1 and S2 such that `S1 - S2 = d` (S1 ≥ S2).
Return the **number of ways** to do this.

```
Input:  arr = [5, 2, 6, 4], d = 3
Output: 1   (S1={5,4}=9, S2={2,6}=8, diff=1... hmm let me recalculate)
Actually: S1={5,6}=11, S2={2,4}=6, diff=5. Not 3.
S1={5,2,4}=11, S2={6}=6, diff=5. 
S1={5,2}=7, S2={6,4}=10, diff=-3 → |diff|=3 but S1<S2.
S1={6,4}=10, S2={5,2}=7, diff=3. ✓

Input:  arr = [5, 2, 6, 4], d = 3
Output: 1

Input:  arr = [1, 1, 1, 1], d = 0
Output: 6   (C(4,2) = 6 ways to split into two equal halves)

Input:  arr = [1, 2, 3, 1, 2], d = 1
Output: ?   (total=9, target=(9+1)/2=5. Count subsets summing to 5.)
```

---

## 🧠 Building the Intuition -- The Algebraic Reduction

We want to split the array into S1 and S2 where:

```
S1 + S2 = total       ... (1)  everything must be in one subset or the other
S1 - S2 = d           ... (2)  the required difference
```

Add equations (1) and (2):

```
2 * S1 = total + d
S1 = (total + d) / 2
```

**So the problem becomes:**
Count the number of subsets that sum to `(total + d) / 2`.

That's **Count Subsets with Sum K** where `K = (total + d) / 2`.

**When is this impossible?**

1. **`(total + d)` is odd** → `S1` would be a fraction. Impossible.
   Return 0.

2. **`(total + d)` is negative** → `S1` would be negative. Impossible.
   This happens when `d > total`. Return 0.

3. **`total + d < 0`** → same issue. Return 0.

Check these BEFORE running the DP.

---

## 🧠 Why This Reduction Works

If we find a subset S1 summing to `(total + d) / 2`,
then S2 = everything else = `total - S1 = total - (total + d)/2 = (total - d)/2`.

Check: `S1 - S2 = (total+d)/2 - (total-d)/2 = d`. ✓

Every subset summing to `(total + d) / 2`
gives a valid partition with difference `d`.
And every valid partition has S1 = `(total + d) / 2`.
So the count is exact.

---

### 📜 The Scroll of the Counted Splits

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## ⚖️ The Wrapper -- Reduce to Count Subsets

```cpp
int countPartitions(vector<int>& arr, int d) {
    int total = accumulate(arr.begin(), arr.end(), 0);
```

Compute the total sum.

---

### 🛑 Impossible Cases

```cpp
    if ((total + d) % 2 != 0) return 0;
```

`total + d` is odd → `S1` would be a fraction. Impossible.

---

```cpp
    if (total + d < 0) return 0;
```

`S1` would be negative. Impossible.
(This also catches `d > total`.)

---

### 🎯 Compute the Target

```cpp
    int target = (total + d) / 2;
```

The problem is now: count subsets summing to `target`.

---

### 🔢 Call Count Subsets

```cpp
    int n = arr.size();
    vector<vector<int>> memo(n, vector<int>(target + 1, -1));
    return solve(0, target, arr, memo);
}
```

Standard Count Subsets with Sum K. Already mastered.

---

## 🔮 The Count Subsets Recursion (Same as Before)

```cpp
int solve(int index, int remaining, vector<int>& arr,
          vector<vector<int>>& memo) {
```

---

### 🎯 Target met

```cpp
    if (remaining == 0) return 1;
```

---

### 🛑 No more elements

```cpp
    if (index == (int)arr.size()) return 0;
```

---

### 📖 Cache check

```cpp
    if (memo[index][remaining] != -1) return memo[index][remaining];
```

---

### Include or Exclude

```cpp
    int include = 0;
    if (arr[index] <= remaining) {
        include = solve(index + 1, remaining - arr[index], arr, memo);
    }
    int exclude = solve(index + 1, remaining, arr, memo);
```

---

### Add both counts

```cpp
    memo[index][remaining] = include + exclude;
    return memo[index][remaining];
}
```

Identical to Count Subsets with Sum K. No changes.

---

### 🎺 The Trial of the Counted Splits

```cpp
int main() {
    vector<int> a1 = {5, 2, 6, 4};
    cout << countPartitions(a1, 3) << endl; // expected: 1

    vector<int> a2 = {1, 1, 1, 1};
    cout << countPartitions(a2, 0) << endl; // expected: 6

    return 0;
}
```

---

**Full trace for arr = [5, 2, 6, 4], d = 3:**

```
total = 17. target = (17 + 3) / 2 = 10.
```

Count subsets of [5, 2, 6, 4] summing to 10.

Subsets summing to 10:
-   {6, 4} = 10 ✓

Only 1 subset. **Answer: 1** ✓

S1 = {6, 4} = 10. S2 = {5, 2} = 7. Diff = 10 - 7 = 3. ✓

---

**Trace for arr = [1, 1, 1, 1], d = 0:**

```
total = 4. target = (4 + 0) / 2 = 2.
```

Count subsets of [1, 1, 1, 1] summing to 2.

How many ways to pick 2 ones from 4? C(4, 2) = 6.

**Answer: 6** ✓

Each way gives S1 = 2, S2 = 2, diff = 0.

---

**Trace for arr = [1, 2, 3], d = 6:**

```
total = 6. target = (6 + 6) / 2 = 6.
```

Count subsets summing to 6: {1, 2, 3} = 6. One way.

**Answer: 1** ✓

S1 = {1, 2, 3} = 6. S2 = {} = 0. Diff = 6. ✓

---

**Trace for arr = [1, 2], d = 4:**

```
total = 3. (3 + 4) = 7. 7 % 2 != 0 → impossible.
```

**Answer: 0** ✓

---

## 🔍 The Reduction Chain

```
Count Partitions with Difference d
  ↓ "S1 + S2 = total, S1 - S2 = d → S1 = (total + d) / 2"
Count Subsets with Sum K, where K = (total + d) / 2
  ↓ "Include or exclude each element, add counts"
Standard 0/1 Knapsack counting DP
```

Three levels. One underlying algorithm.

---

## 🔍 This Problem Is Also "Target Sum" (LeetCode 494)

LeetCode 494 asks: assign `+` or `-` to each element.
How many ways to reach a target sum?

```
+arr[0] ± arr[1] ± arr[2] ... = target
```

Let P = set of elements with `+`, N = set with `-`.
P - N = target. P + N = total.
→ P = (total + target) / 2.

**Identical reduction.** Same problem, different story.

---

## 🔍 The Impossible Cases -- Why They Matter

Without the checks:
-   Odd `(total + d)` → `target` is not an integer → array index out of bounds.
-   `d > total` → `target` is negative → nonsensical.
-   `total + d < 0` → same issue.

These checks prevent crashes AND save computation.
Always validate before running DP.

---

### 🧠 Memory of the Counted Splits (Memoization) Law

-   **Reduction:** `S1 = (total + d) / 2`. Count subsets summing to S1.
-   **Impossible if:** `(total + d)` is odd, or `total + d < 0`
-   **The DP is Count Subsets with Sum K** -- no changes
-   **Also solves LeetCode 494 Target Sum** (same reduction)
-   **Time:** O(N × target). **Space:** O(N × target).

Thus is remembered the saga of **Count Partitions with Given Difference (Memoization)**,
where the Oracle did not solve a new problem
but reduced it algebraically --
`S1 = (total + d) / 2` --
and counted the subsets that hit that target,
knowing each one gave a valid partition
with exactly the required difference. ⚖️🔢🧠✨
