## 🎒✅📋 _The Knapsack of Exact Weight (Tabulation): The Subset Sum Equal to Target Saga_

> \_"The Oracle had solved the subset sum with memoization.
> Now she built the answer bottom-up.
>
> **A 2D boolean table: `dp[i][t]` = can I hit sum `t`
> using stones from index `0` to `i`?**
>
> Fill it row by row (stone by stone).
> Each stone either joins the selection or doesn't.
> The answer is at `dp[n-1][target]`."\_

---

This is the saga of **Subset Sum Equal to Target (Tabulation)**.

Same problem:
-   Array of positive integers. Target sum.
-   Can any subset sum to exactly the target?

```
Input:  arr = [1, 2, 3, 4], target = 4  →  true
Input:  arr = [3, 34, 4, 12, 5, 2], target = 9  →  true
Input:  arr = [3, 34, 4, 12, 5, 2], target = 30  →  false
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][t] = true if there exists a subset of arr[0..i] that sums to t.
```

**Base cases:**

```
dp[i][0] = true for all i.
```

Why? Because sum 0 can ALWAYS be achieved -- by picking NOTHING.
The empty subset has sum 0. It always exists.

```
dp[0][arr[0]] = true   (if arr[0] <= target)
```

With only the first stone, the only non-zero sum achievable is `arr[0]` itself.

**Transition:**

```
dp[i][t] = dp[i-1][t]                          ← EXCLUDE stone i
         OR dp[i-1][t - arr[i]]                 ← INCLUDE stone i (if arr[i] <= t)
```

If we can hit `t` WITHOUT stone `i` → true (exclude).
If we can hit `t - arr[i]` WITHOUT stone `i` → adding stone `i` hits `t` (include).
Either way → `dp[i][t] = true`.

---

### 📜 The Scroll of the Bottom-Up Exact Weight

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
bool subsetSumToTarget(vector<int>& arr, int target) {
    int n = arr.size();
    vector<vector<bool>> dp(n, vector<bool>(target + 1, false));
```

`dp[i][t]` = can we achieve sum `t` using stones `0` to `i`?
All start `false` -- no sums proven achievable yet.

---

### 🏁 Base Case -- Sum 0 Is Always Achievable

```cpp
    for (int i = 0; i < n; i++) {
        dp[i][0] = true;
    }
```

Every row's column 0 is `true`.
No matter which stones are available,
the empty subset sums to 0.

Why is this important?
Because when a stone is included and the remaining target
becomes 0, the recursion needs to return true.
In tabulation, that's `dp[i-1][0] = true`.

---

### 🏁 Base Case -- First Stone Alone

```cpp
    if (arr[0] <= target) {
        dp[0][arr[0]] = true;
    }
```

With only stone 0, the only achievable non-zero sum is `arr[0]`.

The guard `arr[0] <= target` prevents writing outside the table
(if the first stone is heavier than the target).

---

### 🔁 Fill Row by Row -- Stone by Stone

```cpp
    for (int i = 1; i < n; i++) {
```

For each stone from 1 to n-1.
(Stone 0 was handled in the base case.)

---

### 🔁 For Each Possible Sum

```cpp
        for (int t = 1; t <= target; t++) {
```

For each target sum from 1 to `target`.
(Sum 0 was handled in the base case.)

---

### 🚶 Exclude This Stone

```cpp
            bool exclude = dp[i - 1][t];
```

Can we hit sum `t` WITHOUT stone `i`?
That's `dp[i-1][t]` -- the answer from the previous row
(which only had stones 0 to i-1).

If the previous stones could already hit `t` → we don't need stone `i`.

---

### 🎒 Include This Stone

```cpp
            bool include = false;
            if (arr[i] <= t) {
                include = dp[i - 1][t - arr[i]];
            }
```

Can we hit sum `t` BY USING stone `i`?

If stone `i` weighs more than `t` → can't include it. `include = false`.

If stone `i` fits (`arr[i] <= t`):
We need the remaining `t - arr[i]` to be achievable
from stones 0 to i-1. That's `dp[i-1][t - arr[i]]`.

Why `dp[i-1]` and not `dp[i]`?
Because each stone can be used AT MOST ONCE (0/1 knapsack).
We look at the row WITHOUT this stone to avoid using it twice.

---

### ✅ Either Works → True

```cpp
            dp[i][t] = exclude || include;
        }
    }
```

If excluding works OR including works → this sum is achievable.

---

### 📤 The Answer

```cpp
    return dp[n - 1][target];
}
```

Can we hit `target` using all stones 0 to n-1?

---

### 🎺 The Trial of the Bottom-Up Exact Weight

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 4};
    cout << subsetSumToTarget(a1, 4) << endl; // expected: 1 (true)

    vector<int> a2 = {3, 34, 4, 12, 5, 2};
    cout << subsetSumToTarget(a2, 9) << endl; // expected: 1 (true)

    cout << subsetSumToTarget(a2, 30) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full table for arr = [1, 2, 3, 4], target = 4:**

```
       t=0  t=1  t=2  t=3  t=4
i=0(1): T    T    F    F    F
i=1(2): T    T    T    T    F
i=2(3): T    T    T    T    T
i=3(4): T    T    T    T    T
```

**Row 0 (stone 1):** sum 0 = T (empty). sum 1 = T (pick stone 1). Rest = F.

**Row 1 (stones 1, 2):**
-   t=1: exclude=dp[0][1]=T → T.
-   t=2: exclude=dp[0][2]=F. include=dp[0][2-2]=dp[0][0]=T → T.
-   t=3: exclude=dp[0][3]=F. include=dp[0][3-2]=dp[0][1]=T → T.
-   t=4: exclude=dp[0][4]=F. include=dp[0][4-2]=dp[0][2]=F → F.

**Row 2 (stones 1, 2, 3):**
-   t=4: exclude=dp[1][4]=F. include=dp[1][4-3]=dp[1][1]=T → **T!**

**Answer: dp[2][4] = true** (already found at row 2, but dp[3][4] also true)

Subset: {1, 3} = 4. ✓

---

**Table for arr = [3, 34, 4, 12, 5, 2], target = 9:**

The table is 6 × 10. Key cells:

-   dp[0][3] = T (stone 3).
-   dp[2][4] = T (stone 4). dp[2][7] = T (stones 3+4).
-   dp[4][9] = T (stones 4+5 = 9). **Answer: true** ✓

---

## 🔧 Space Optimization -- Single Row

Each row `i` depends only on row `i-1`.
We can use a single 1D array.

**Critical: iterate `t` from RIGHT to LEFT.**

```cpp
bool subsetSumToTarget(vector<int>& arr, int target) {
    int n = arr.size();
    vector<bool> dp(target + 1, false);
    dp[0] = true;
```

Sum 0 is always achievable.

---

```cpp
    for (int i = 0; i < n; i++) {
        for (int t = target; t >= arr[i]; t--) {
            dp[t] = dp[t] || dp[t - arr[i]];
        }
    }
    return dp[target];
}
```

**Why RIGHT to LEFT?**

If we went left to right, `dp[t - arr[i]]` might have been
updated EARLIER in this same row -- meaning we'd use stone `i` TWICE.

Right to left ensures `dp[t - arr[i]]` still holds the value
from the PREVIOUS row (before stone `i` was considered).

This is the classic 0/1 knapsack space optimization trick.

> _"Right to left preserves the past.
> Left to right would contaminate it.
> The direction of iteration is the difference
> between using each stone once and using it infinitely."_

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 The 0/1 Knapsack Family -- Built on Subset Sum

| Problem                    | What changes?                      |
| -------------------------- | ---------------------------------- |
| Subset Sum (this)          | Can I hit target? (bool, OR)       |
| Count Subsets with Sum     | How many subsets hit target? (int, +) |
| Partition Equal Subset     | Subset Sum on total/2              |
| 0/1 Knapsack               | Max value within weight limit (int, max) |
| Target Sum (+/-)           | Count ways to assign +/- to hit target |

All use the same include/exclude skeleton.
The ONLY thing that changes is the combine operation:
`||` for existence, `+` for counting, `max` for optimization.

---

### 🧠 Memory of the Exact Weight (Tabulation) Law

-   **State:** `dp[i][t]` = can we hit sum `t` using stones 0 to i?
-   **Base:** `dp[i][0] = true` for all i. `dp[0][arr[0]] = true`.
-   **Transition:** `dp[i][t] = dp[i-1][t] || dp[i-1][t - arr[i]]`
    -   Exclude: can we hit `t` without stone `i`?
    -   Include: can we hit `t - arr[i]` without stone `i`?
-   **Answer:** `dp[n-1][target]`
-   **Space optimization:** 1D array, iterate `t` RIGHT to LEFT
    -   Right-to-left prevents using the same stone twice
-   **Time:** O(N × target). **Space:** O(N × target), optimizable to O(target).

Thus is remembered the saga of **Subset Sum (Tabulation)**,
where the Oracle filled a table stone by stone, sum by sum --
at each cell asking "can I hit this sum
by including or excluding this stone?" --
until the final cell revealed
whether the exact target was achievable
from the bag of stones. 🎒✅📋✨
