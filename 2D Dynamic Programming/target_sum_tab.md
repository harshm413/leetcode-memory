## ➕➖📋 _The Signs of Destiny (Tabulation): The Target Sum Saga_

> \_"The Oracle had solved Target Sum two ways with memoization:
> direct recursion and subset reduction.
>
> Now she built the optimized version bottom-up --
> the subset reduction with tabulation.
>
> **`dp[t]` = number of subsets summing to `t`.**
>
> One 1D array. Right to left. Clean and fast."\_

---

This is the saga of **Target Sum (Tabulation)**.

Same problem:
-   Assign `+` or `-` to each element.
-   Count ways to reach the target.

```
Input:  nums = [1, 1, 1, 1, 1], target = 3  →  Output: 5
Input:  nums = [0, 0, 0, 1], target = 1     →  Output: 8
```

---

## 🧠 The Strategy -- Subset Reduction + Tabulation

```
1. subsetTarget = (total + target) / 2.
2. Check impossible cases.
3. Count Subsets with Sum = subsetTarget (1D tabulation).
```

---

### 📜 The Scroll of the Bottom-Up Signs

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Compute and Validate

```cpp
int findTargetSumWays(vector<int>& nums, int target) {
    int total = 0;
    for (int x : nums) total += x;
```

---

### 🛑 Impossible Cases

```cpp
    if (abs(target) > total) return 0;
```

If the target's magnitude exceeds the total,
even assigning all `+` or all `-` can't reach it.

---

```cpp
    if ((total + target) % 2 != 0) return 0;
```

Odd sum → fractional subset target → impossible.

---

### 🎯 The Subset Target

```cpp
    int subsetTarget = (total + target) / 2;
```

Count subsets summing to this value.

---

### Build the 1D DP Array

```cpp
    vector<int> dp(subsetTarget + 1, 0);
    dp[0] = 1;
```

`dp[t]` = number of subsets summing to `t`.
`dp[0] = 1` -- one way to sum to 0: the empty subset.

---

### 🔁 Process Each Element

```cpp
    for (int i = 0; i < (int)nums.size(); i++) {
```

For each element in the array.

---

### 🔁 Fill Right to Left (0/1 Knapsack)

```cpp
        for (int t = subsetTarget; t >= nums[i]; t--) {
            dp[t] += dp[t - nums[i]];
        }
    }
```

**`dp[t] += dp[t - nums[i]]`:**
The number of subsets summing to `t` increases by
the number of subsets that summed to `t - nums[i]`
(those subsets, plus this element, now sum to `t`).

**Right to left** ensures each element is used at most once.
`dp[t - nums[i]]` hasn't been updated yet in this iteration,
so it reflects the count WITHOUT this element.

**When `nums[i] = 0`:**
The loop condition `t >= nums[i]` becomes `t >= 0` → runs for ALL `t`.
`dp[t] += dp[t - 0]` → `dp[t] += dp[t]` → doubles every cell.
Correct: each zero doubles the number of valid assignments
(+0 and -0 both contribute the same sum).

---

### 📤 The Answer

```cpp
    return dp[subsetTarget];
}
```

---

### 🎺 The Trial of the Bottom-Up Signs

```cpp
int main() {
    vector<int> n1 = {1, 1, 1, 1, 1};
    cout << findTargetSumWays(n1, 3) << endl; // expected: 5

    vector<int> n2 = {0, 0, 0, 1};
    cout << findTargetSumWays(n2, 1) << endl; // expected: 8

    vector<int> n3 = {1};
    cout << findTargetSumWays(n3, 1) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for nums = [1, 1, 1, 1, 1], target = 3:**

```
total = 5. subsetTarget = (5 + 3) / 2 = 4.
```

| Element | dp[0] | dp[1] | dp[2] | dp[3] | dp[4] |
| ------- | ----- | ----- | ----- | ----- | ----- |
| Start   | 1     | 0     | 0     | 0     | 0     |
| 1       | 1     | 1     | 0     | 0     | 0     |
| 1       | 1     | 2     | 1     | 0     | 0     |
| 1       | 1     | 3     | 3     | 1     | 0     |
| 1       | 1     | 4     | 6     | 4     | 1     |
| 1       | 1     | 5     | 10    | 10    | **5** |

**Answer: dp[4] = 5** ✓

This is C(5, 4) = 5. Pick 4 elements to be positive (sum = 4).
The remaining 1 element is negative (sum = -1).
Total = 4 - 1 = 3 = target. ✓

---

**Full trace for nums = [0, 0, 0, 1], target = 1:**

```
total = 1. subsetTarget = (1 + 1) / 2 = 1.
```

| Element | dp[0] | dp[1] |
| ------- | ----- | ----- |
| Start   | 1     | 0     |
| 0       | 2     | 0     |
| 0       | 4     | 0     |
| 0       | 8     | 0     |
| 1       | 8     | **8** |

**Answer: dp[1] = 8** ✓

Three zeros each doubled the count (1 → 2 → 4 → 8).
Then element 1 created 8 subsets summing to 1.

---

**Trace for nums = [1], target = 1:**

```
total = 1. subsetTarget = (1 + 1) / 2 = 1.
dp = [1, 0]. After element 1: dp = [1, 1].
```

**Answer: 1** ✓ (assign +1)

---

**Trace for nums = [1], target = 2:**

```
total = 1. |2| > 1 → return 0.
```

**Answer: 0** ✓ (can't reach 2 with just [1])

---

## 🔍 The Two Approaches -- Complete Comparison

| Direct Recursion                  | Subset Reduction (this)           |
| --------------------------------- | --------------------------------- |
| State: `(index, currentSum)`      | State: `dp[subsetTarget]`         |
| Sum can be negative → hash map    | Sum always ≥ 0 → array            |
| 2ⁿ branches (with memo: n×range) | O(n × subsetTarget)               |
| More intuitive to derive          | Requires algebraic insight         |
| Good for explaining the problem   | Good for optimal solution          |

**Present the direct approach to show understanding.**
**Present the reduction for the optimal solution.**

---

## 🔍 The Subset Sum Reduction Family -- Complete

| Problem                          | Reduction target                   |
| -------------------------------- | ---------------------------------- |
| Partition Equal Subset Sum       | total / 2                          |
| Min Subset Sum Difference        | total / 2 (scan for closest)       |
| Count Partitions with Diff d     | (total + d) / 2                    |
| **Target Sum** (this)            | **(total + target) / 2**           |

All four reduce to Subset Sum or Count Subsets
with a transformed target.
The algebra is the hard part. The DP is always the same.

---

### 🧠 Memory of the Signs of Destiny (Tabulation) Law

-   **Reduction:** `subsetTarget = (total + target) / 2`
-   **Impossible:** `|target| > total` or `(total + target)` is odd
-   **1D DP:** `dp[t] += dp[t - nums[i]]`, right to left
-   **Zeros:** each zero doubles every cell (correct behavior)
-   **Base:** `dp[0] = 1`
-   **Answer:** `dp[subsetTarget]`
-   **Same as Count Partitions with Difference = target**
-   **Time:** O(N × subsetTarget). **Space:** O(subsetTarget).

Thus is remembered the saga of **Target Sum (Tabulation)**,
where the Oracle reduced the sign-assignment problem
to counting subsets --
`(total + target) / 2` was the magic number --
and a single 1D array, filled right to left,
revealed how many ways the signs could align
to reach the target. ➕➖📋✨
