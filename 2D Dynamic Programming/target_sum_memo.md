## ➕➖🧠 _The Signs of Destiny (Memoization): The Target Sum Saga_

> \_"The Oracle was given an array of non-negative integers
> and a target value.
>
> She was commanded:
>
> **'Assign a `+` or `-` sign to each element.
> How many ways can you make the total equal the target?'**
>
> Every element MUST be used.
> Every element gets exactly one sign: `+` or `-`.
>
> The Oracle saw two ways to think about this:
>
> **Approach 1 -- Pure recursion on choices.**
> At each element, two choices: add it or subtract it.
> Count the ways that reach the target.
>
> **Approach 2 -- Reduce to Count Subsets.**
> The elements with `+` form subset P.
> The elements with `-` form subset N.
> P - N = target. P + N = total.
> → P = (total + target) / 2.
> Count subsets summing to P.
>
> Both approaches give the same answer.
> Approach 1 is more intuitive.
> Approach 2 is more efficient.
> The Oracle mastered both."\_

---

This is the saga of **Target Sum (Memoization)**.

Given:
-   `nums[]` -- array of non-negative integers.
-   `target` -- the desired sum after assigning signs.

Return the **number of ways** to assign `+` or `-` to each element
such that the total equals `target`.

```
Input:  nums = [1, 1, 1, 1, 1], target = 3
Output: 5

Input:  nums = [1], target = 1
Output: 1

Input:  nums = [0, 0, 0, 1], target = 1
Output: 8
```

---

## 🧠 Approach 1 -- Direct Recursion (Add or Subtract)

The most natural way to think about it:

At each element, two choices:
-   **Add it:** running sum increases by `nums[i]`.
-   **Subtract it:** running sum decreases by `nums[i]`.

After all elements, if the running sum equals `target` → count it.

```
solve(index, currentSum):
  If index == n:
    return currentSum == target ? 1 : 0
  add = solve(index + 1, currentSum + nums[index])
  sub = solve(index + 1, currentSum - nums[index])
  return add + sub
```

**Why addition?** Because both choices lead to DIFFERENT assignments.
The total count = ways via adding + ways via subtracting.

**Base case:** all elements assigned.
If the sum equals target → 1 way found.
If not → 0 ways.

**State:** `(index, currentSum)`.
`currentSum` can be negative (if we subtract a lot).

---

### 📜 The Scroll of the Signs

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## ➕➖ The Direct Recursion

### The Cache

```cpp
int findTargetSumWays(vector<int>& nums, int target) {
    int n = nums.size();
    vector<unordered_map<int, int>> memo(n);
```

Why `unordered_map` instead of a 2D vector?

Because `currentSum` can be NEGATIVE.
Array indices can't be negative.
A hash map handles any integer key naturally.

`memo[index][sum]` = number of ways to reach `target`
from index `index` with current sum `sum`.

---

```cpp
    return solve(0, 0, nums, target, memo);
}
```

Start at index 0 with sum 0.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int index, int currentSum, vector<int>& nums, int target,
          vector<unordered_map<int, int>>& memo) {
```

"Starting from index `index` with running sum `currentSum`,
how many ways can I reach `target` by the end?"

---

### 🎯 All elements assigned

```cpp
    if (index == (int)nums.size()) {
        return currentSum == target ? 1 : 0;
    }
```

Every element has been given a sign.
If the sum equals the target → 1 valid assignment found.
If not → 0.

---

### 📖 Cache check

```cpp
    if (memo[index].count(currentSum)) {
        return memo[index][currentSum];
    }
```

Already solved this (index, sum) pair? Return it.

---

### ➕ Choice 1 -- Add This Element

```cpp
    int add = solve(index + 1, currentSum + nums[index], nums, target, memo);
```

Assign `+` to this element. Sum increases.
Count the ways from here.

---

### ➖ Choice 2 -- Subtract This Element

```cpp
    int sub = solve(index + 1, currentSum - nums[index], nums, target, memo);
```

Assign `-` to this element. Sum decreases.
Count the ways from here.

---

### ➕ Total Ways

```cpp
    memo[index][currentSum] = add + sub;
    return memo[index][currentSum];
}
```

Both choices lead to different assignments.
Total = sum of both.

---

### 🎺 The Trial of the Signs

```cpp
int main() {
    vector<int> n1 = {1, 1, 1, 1, 1};
    cout << findTargetSumWays(n1, 3) << endl; // expected: 5

    vector<int> n2 = {1};
    cout << findTargetSumWays(n2, 1) << endl; // expected: 1

    vector<int> n3 = {0, 0, 0, 1};
    cout << findTargetSumWays(n3, 1) << endl; // expected: 8

    return 0;
}
```

---

**Full trace for nums = [1, 1, 1, 1, 1], target = 3:**

Each element is 1. Five elements. Target = 3.

We need: (number of +1s) - (number of -1s) = 3.
Let p = number of +1s, q = number of -1s.
p + q = 5, p - q = 3 → p = 4, q = 1.

How many ways to choose 1 element to be negative? C(5, 1) = 5.

The recursion explores all 2⁵ = 32 assignments
and finds exactly 5 that sum to 3.

**Answer: 5** ✓

---

**Trace for nums = [0, 0, 0, 1], target = 1:**

The `1` must be `+1` (since `-1` would give target ≤ -1 + 0s = -1 or +1... 
actually +0+0+0+1=1 or +0+0+0-1=-1. So 1 must be positive.)

But each `0` can be `+0` or `-0` -- both give 0.
Three zeros, each with 2 choices = 2³ = 8 ways.

**Answer: 8** ✓

---

## 🧠 Approach 2 -- Reduce to Count Subsets (More Efficient)

The algebraic reduction (same as Count Partitions with Difference):

```
Let P = sum of elements with +
Let N = sum of elements with -
P - N = target
P + N = total
→ P = (total + target) / 2
```

**Count the number of subsets summing to `(total + target) / 2`.**

This is Count Subsets with Sum K. Already mastered.

### The Optimized Version

```cpp
int findTargetSumWays(vector<int>& nums, int target) {
    int total = 0;
    for (int x : nums) total += x;
```

---

### Impossible cases

```cpp
    if ((total + target) % 2 != 0) return 0;
    if (total + target < 0) return 0;
```

Odd sum → fractional target → impossible.
Negative sum → impossible.

---

### Reduce to Count Subsets

```cpp
    int subsetTarget = (total + target) / 2;
    int n = nums.size();
```

---

### 1D DP (Space Optimized Count Subsets)

```cpp
    vector<int> dp(subsetTarget + 1, 0);
    dp[0] = 1;

    for (int i = 0; i < n; i++) {
        for (int t = subsetTarget; t >= nums[i]; t--) {
            dp[t] += dp[t - nums[i]];
        }
    }
    return dp[subsetTarget];
}
```

Standard Count Subsets tabulation with 1D optimization.
Right to left (0/1 knapsack -- each element used once).

**Zero handling:** when `nums[i] = 0`, the inner loop
runs for all `t` down to 0, and `dp[t] += dp[t - 0] = dp[t] += dp[t]`.
This doubles every cell -- correct, because each zero
doubles the number of valid assignments.

---

## 🔍 Approach 1 vs Approach 2

| Direct Recursion (Approach 1)     | Subset Reduction (Approach 2)     |
| --------------------------------- | --------------------------------- |
| State: `(index, currentSum)`      | State: `(index, remaining)`       |
| Sum can be negative → hash map    | Sum always ≥ 0 → array            |
| O(n × totalRange) time            | O(n × subsetTarget) time          |
| More intuitive                    | More efficient                    |
| Easier to derive                  | Requires algebraic insight         |

Approach 1 is what you'd think of first.
Approach 2 is what you'd optimize to.

Present Approach 1 to show understanding.
Mention Approach 2 as the optimization.

---

## 🔍 The Connection to Count Partitions with Difference

Target Sum with `target = d` is IDENTICAL to
Count Partitions with Difference `d`.

```
Target Sum:       assign +/- to reach target.
Count Partitions: split into S1, S2 where S1 - S2 = d.
```

P (positive elements) = S1.
N (negative elements) = S2.
Same reduction. Same DP. Same answer.

---

**Trace of Approach 2 for nums = [1, 1, 1, 1, 1], target = 3:**

```
total = 5. subsetTarget = (5 + 3) / 2 = 4.
Count subsets of [1,1,1,1,1] summing to 4.
= C(5, 4) = 5.
```

**Answer: 5** ✓

---

**Trace of Approach 2 for nums = [0, 0, 0, 1], target = 1:**

```
total = 1. subsetTarget = (1 + 1) / 2 = 1.
Count subsets of [0,0,0,1] summing to 1.

dp after each element:
  Start: dp = [1, 0]
  After 0: dp[0] += dp[0] → dp = [2, 0]  (zero doubles count)
  After 0: dp = [4, 0]
  After 0: dp = [8, 0]
  After 1: dp[1] += dp[0] → dp = [8, 8]
```

**Answer: dp[1] = 8** ✓

Each zero doubled the count. Three zeros → 2³ = 8 multiplier.

---

### 🧠 Memory of the Signs of Destiny (Memoization) Law

**Approach 1 (Direct):**
-   State: `(index, currentSum)`. Two choices: add or subtract.
-   Base: `index == n` → `currentSum == target ? 1 : 0`
-   Use hash map for negative sums.
-   Time: O(n × sumRange).

**Approach 2 (Reduction):**
-   `subsetTarget = (total + target) / 2`
-   Count Subsets with Sum = subsetTarget
-   Impossible if `(total + target)` is odd or negative
-   Time: O(n × subsetTarget). More efficient.

**Both are valid.** Present Approach 1 for intuition, Approach 2 for optimization.

Thus is remembered the saga of **Target Sum (Memoization)**,
where the Oracle assigned `+` or `-` to every element --
first by direct recursion, adding or subtracting at each step,
then by algebraic reduction to Count Subsets --
both paths converging on the same count
of ways to reach the target. ➕➖🧠✨
