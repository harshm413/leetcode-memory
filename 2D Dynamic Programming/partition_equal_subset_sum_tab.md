## ⚖️📋 _The Perfect Split (Tabulation): The Partition Equal Subset Sum Saga_

> \_"The Oracle had reduced the partition problem
> to Subset Sum with target = total / 2.
>
> Now she solved it bottom-up --
> a boolean dp table, filled element by element,
> asking at each cell: can I hit this sum
> by including or excluding this element?"\_

---

This is the saga of **Partition Equal Subset Sum (Tabulation)**.

Same problem:
-   Can the array be split into two subsets with equal sum?
-   Reduces to: Subset Sum with `target = total / 2`.

```
Input:  nums = [1, 5, 11, 5]  →  true
Input:  nums = [1, 2, 3, 5]   →  false (odd total)
```

---

## 🧠 The Strategy

```
1. Compute total. If odd → false.
2. target = total / 2.
3. Subset Sum tabulation on target.
```

---

### 📜 The Scroll of the Bottom-Up Perfect Split

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## ⚖️ The Wrapper

```cpp
bool canPartition(vector<int>& nums) {
    int total = accumulate(nums.begin(), nums.end(), 0);
```

---

### 🛑 Odd total → impossible

```cpp
    if (total % 2 != 0) return false;
```

---

### 🎯 Reduce to Subset Sum

```cpp
    int target = total / 2;
    int n = nums.size();
```

---

## 📋 The Subset Sum Table

```cpp
    vector<vector<bool>> dp(n, vector<bool>(target + 1, false));
```

`dp[i][t]` = can we hit sum `t` using elements 0 to i?

---

### 🏁 Base Case -- Sum 0 Is Always Achievable

```cpp
    for (int i = 0; i < n; i++) {
        dp[i][0] = true;
    }
```

The empty subset sums to 0. Always possible.

---

### 🏁 Base Case -- First Element Alone

```cpp
    if (nums[0] <= target) {
        dp[0][nums[0]] = true;
    }
```

With only element 0, the only achievable non-zero sum is `nums[0]`.

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i < n; i++) {
        for (int t = 1; t <= target; t++) {
```

---

### Exclude or Include

```cpp
            bool exclude = dp[i - 1][t];
```

Can we hit `t` without element `i`?

---

```cpp
            bool include = false;
            if (nums[i] <= t) {
                include = dp[i - 1][t - nums[i]];
            }
```

Can we hit `t` by using element `i`?
Only if it fits (`nums[i] <= t`).
Then we need `t - nums[i]` from previous elements.

---

```cpp
            dp[i][t] = exclude || include;
        }
    }
    return dp[n - 1][target];
}
```

---

### 🎺 The Trial of the Bottom-Up Perfect Split

```cpp
int main() {
    vector<int> n1 = {1, 5, 11, 5};
    cout << canPartition(n1) << endl; // expected: 1 (true)

    vector<int> n2 = {1, 2, 3, 5};
    cout << canPartition(n2) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full table for nums = [1, 5, 11, 5], target = 11:**

```
        t=0  t=1  t=2  t=3  t=4  t=5  t=6  t=7  t=8  t=9  t=10 t=11
i=0(1):  T    T    F    F    F    F    F    F    F    F    F    F
i=1(5):  T    T    F    F    F    T    T    F    F    F    F    F
i=2(11): T    T    F    F    F    T    T    F    F    F    F    T
i=3(5):  T    T    F    F    F    T    T    F    F    F    T    T
```

**Row 0 (element 1):** sums 0 and 1 achievable.

**Row 1 (elements 1, 5):**
-   t=5: exclude=F, include=dp[0][5-5]=dp[0][0]=T → T.
-   t=6: exclude=F, include=dp[0][6-5]=dp[0][1]=T → T.

**Row 2 (elements 1, 5, 11):**
-   t=11: exclude=F, include=dp[1][11-11]=dp[1][0]=T → **T!**

**dp[2][11] = true** already! But let's fill row 3 too:

**Row 3 (elements 1, 5, 11, 5):**
-   t=11: exclude=dp[2][11]=T → T.

**Answer: dp[3][11] = true** ✓

Subset {11} sums to 11. Remaining {1, 5, 5} also sums to 11.

---

**Trace for nums = [1, 2, 3, 5]:**

```
total = 11. Odd! → return false immediately.
```

**Answer: false** ✓. No table needed.

---

## 🔧 Space Optimization -- 1D Array, Right to Left

Same trick as Subset Sum:

```cpp
bool canPartition(vector<int>& nums) {
    int total = accumulate(nums.begin(), nums.end(), 0);
    if (total % 2 != 0) return false;
    int target = total / 2;

    vector<bool> dp(target + 1, false);
    dp[0] = true;

    for (int i = 0; i < (int)nums.size(); i++) {
        for (int t = target; t >= nums[i]; t--) {
            dp[t] = dp[t] || dp[t - nums[i]];
        }
    }
    return dp[target];
}
```

**Right to left** prevents using the same element twice.

`dp[t]` before update = "can I hit `t` without this element?" (exclude).
`dp[t - nums[i]]` = "can I hit `t - nums[i]` without this element?" (include).
`dp[t] = exclude || include`.

Present the 2D table first. Mention this as a follow-up.

```
Time:  O(N × target)
Space: O(target)
```

---

## 🔍 The Reduction Chain

```
Partition Equal Subset Sum
  ↓ "Can I split into two equal halves?"
  ↓ "Can any subset sum to total/2?"
Subset Sum with target = total/2
  ↓ "Include or exclude each element"
Standard 0/1 Knapsack DP
```

Three levels of abstraction. One underlying algorithm.

---

## 🔍 Related Problems Built on This Reduction

| Problem                    | Reduction                          |
| -------------------------- | ---------------------------------- |
| Partition Equal Subset Sum | Subset Sum on total/2              |
| Target Sum (+/-)           | Subset Sum on (total + target) / 2 |
| Last Stone Weight II       | Subset Sum on total/2 (minimize remainder) |
| Minimum Subset Sum Diff    | Subset Sum on total/2 (closest achievable) |

All reduce to Subset Sum with a transformed target.
Master Subset Sum and the entire family falls.

---

### 🧠 Memory of the Perfect Split (Tabulation) Law

-   **Reduce:** total/2 = target. Odd total → false.
-   **Subset Sum table:** `dp[i][t] = dp[i-1][t] || dp[i-1][t - nums[i]]`
-   **Base:** `dp[i][0] = true`. `dp[0][nums[0]] = true`.
-   **Answer:** `dp[n-1][target]`
-   **Space optimization:** 1D array, right to left → O(target)
-   **Time:** O(N × target). **Space:** O(target) optimized.

Thus is remembered the saga of **Partition Equal Subset Sum (Tabulation)**,
where the Oracle computed the total,
checked for oddness,
then filled a Subset Sum table for half the total --
and the final cell revealed
whether the perfect split existed. ⚖️📋✨
