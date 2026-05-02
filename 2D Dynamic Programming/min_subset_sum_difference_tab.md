## ⚖️🎯📋 _The Closest Split (Tabulation): The Minimum Subset Sum Difference Saga_

> \_"The Oracle had found the closest split with memoization.
> Now she built the Subset Sum table bottom-up --
> then scanned its last row for the best achievable sum.
>
> The tabulation approach was cleaner here
> because the final step (scanning the last row)
> naturally uses the completed table."\_

---

This is the saga of **Minimum Subset Sum Difference (Tabulation)**.

Same problem:
-   Split array into two subsets.
-   Minimize `|sum1 - sum2|`.

```
Input:  arr = [1, 6, 11, 5]  →  Output: 1
Input:  arr = [1, 2, 3, 4]   →  Output: 0
```

---

## 🧠 The Strategy

```
1. Compute total. target = total / 2.
2. Build Subset Sum table: dp[i][t] = can we hit sum t using elements 0..i?
3. Scan dp[n-1][t] from t = target down to 0.
   First true = best achievable sum s1.
4. Minimum difference = total - 2 * s1.
```

---

### 📜 The Scroll of the Bottom-Up Closest Split

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Setup

```cpp
int minSubsetSumDifference(vector<int>& arr) {
    int n = arr.size();
    int total = accumulate(arr.begin(), arr.end(), 0);
    int target = total / 2;
```

---

### Build the Subset Sum Table

```cpp
    vector<vector<bool>> dp(n, vector<bool>(target + 1, false));
```

`dp[i][t]` = can we achieve sum `t` using elements 0 to i?

---

### Base Case -- Sum 0 Always Achievable

```cpp
    for (int i = 0; i < n; i++) {
        dp[i][0] = true;
    }
```

Empty subset sums to 0. Always true.

---

### Base Case -- First Element Alone

```cpp
    if (arr[0] <= target) {
        dp[0][arr[0]] = true;
    }
```

---

### Fill the Table -- Standard Subset Sum

```cpp
    for (int i = 1; i < n; i++) {
        for (int t = 1; t <= target; t++) {
            bool exclude = dp[i - 1][t];
            bool include = false;
            if (arr[i] <= t) {
                include = dp[i - 1][t - arr[i]];
            }
            dp[i][t] = exclude || include;
        }
    }
```

Identical to Subset Sum tabulation.
Each element: include (target shrinks) or exclude (target stays).

---

### Scan the Last Row for the Best Sum

```cpp
    int minDiff = total;

    for (int t = target; t >= 0; t--) {
        if (dp[n - 1][t]) {
            minDiff = total - 2 * t;
            break;
        }
    }
    return minDiff;
}
```

The last row `dp[n-1]` tells us which sums are achievable
using ALL elements.

Scan from `target` (= total/2) downward.
The first `true` is the largest achievable sum ≤ total/2.

`minDiff = total - 2 * t`.

Why scan downward?
We want the LARGEST `t` that's achievable.
Larger `t` → closer to total/2 → smaller difference.
The first `true` from the top is the best.

---

### 🎺 The Trial of the Bottom-Up Closest Split

```cpp
int main() {
    vector<int> a1 = {1, 6, 11, 5};
    cout << minSubsetSumDifference(a1) << endl; // expected: 1

    vector<int> a2 = {1, 2, 3, 4};
    cout << minSubsetSumDifference(a2) << endl; // expected: 0

    return 0;
}
```

---

**Full table for arr = [1, 6, 11, 5], target = 11:**

```
        t=0  t=1  t=2  t=3  t=4  t=5  t=6  t=7  t=8  t=9  t=10 t=11
i=0(1):  T    T    F    F    F    F    F    F    F    F    F    F
i=1(6):  T    T    F    F    F    F    T    T    F    F    F    F
i=2(11): T    T    F    F    F    F    T    T    F    F    F    T
i=3(5):  T    T    F    F    F    T    T    T    F    F    F    T
```

**Last row achievable sums:** 0, 1, 5, 6, 7, 11.

**Scan from t=11 downward:**
-   t=11: dp[3][11] = T → **FOUND!**

`minDiff = 23 - 2*11 = 1`.

**Answer: 1** ✓

---

**Table for arr = [1, 2, 3, 4], target = 5:**

```
        t=0  t=1  t=2  t=3  t=4  t=5
i=0(1):  T    T    F    F    F    F
i=1(2):  T    T    T    T    F    F
i=2(3):  T    T    T    T    T    T
i=3(4):  T    T    T    T    T    T
```

Scan: t=5 → T. `minDiff = 10 - 2*5 = 0`.

**Answer: 0** ✓ -- perfect split.

---

**Table for arr = [8, 6, 5], target = 9:**

```
total = 19. target = 9.

        t=0  t=1  t=2  t=3  t=4  t=5  t=6  t=7  t=8  t=9
i=0(8):  T    F    F    F    F    F    F    F    T    F
i=1(6):  T    F    F    F    F    F    T    F    T    F
i=2(5):  T    F    F    F    F    T    T    F    T    F
```

Scan: t=9 → F. t=8 → T.

`minDiff = 19 - 2*8 = 3`.

**Answer: 3** ✓

Subsets: {8} = 8 and {6, 5} = 11. Diff = |8 - 11| = 3.

---

## 🔧 Space Optimization -- 1D Array

Same as Subset Sum optimization:

```cpp
int minSubsetSumDifference(vector<int>& arr) {
    int n = arr.size();
    int total = accumulate(arr.begin(), arr.end(), 0);
    int target = total / 2;

    vector<bool> dp(target + 1, false);
    dp[0] = true;

    for (int i = 0; i < n; i++) {
        for (int t = target; t >= arr[i]; t--) {
            dp[t] = dp[t] || dp[t - arr[i]];
        }
    }

    for (int t = target; t >= 0; t--) {
        if (dp[t]) return total - 2 * t;
    }
    return total;
}
```

1D array, right to left. Then scan for the best sum.

Present the 2D table first. Mention this as a follow-up.

```
Time:  O(N × total/2)
Space: O(total/2)
```

---

## 🔍 The Subset Sum Family -- Complete Picture

| Problem                    | What we ask of the table           | Answer from table          |
| -------------------------- | ---------------------------------- | -------------------------- |
| Subset Sum                 | Can I hit target?                  | `dp[n-1][target]`         |
| Partition Equal Subset     | Can I hit total/2?                 | `dp[n-1][total/2]`        |
| Min Subset Sum Diff        | What's closest to total/2?         | Scan last row, find best t |
| Count Subsets with Sum     | How many subsets hit target?       | `dp[n-1][target]` (int, not bool) |
| 0/1 Knapsack               | Max value within capacity?         | `dp[n-1][capacity]` (int, max) |

Same table. Different questions. Different answer extraction.

---

## 🔍 Why This Is the Natural Next Step After Partition Equal Subset

```
Partition Equal Subset: "Can I hit total/2 exactly?"
  → YES/NO answer from one cell.

Min Subset Sum Diff: "What's the closest I can get to total/2?"
  → Scan the entire last row for the best achievable sum.
```

Partition Equal is a special case of Min Difference
where the minimum difference is 0.

---

### 🧠 Memory of the Closest Split (Tabulation) Law

-   **Build Subset Sum table** for target = total/2
-   **Scan last row** from total/2 downward for first `true`
-   **Minimum difference** = `total - 2 * bestAchievableSum`
-   **Same Subset Sum DP** -- different question asked of the table
-   **Space optimization:** 1D array, right to left, then scan
-   **Time:** O(N × total/2). **Space:** O(total/2) optimized.
-   **Edge cases:**
    -   Perfect split exists → diff = 0
    -   Single element → diff = that element
    -   All elements equal → diff = 0 (even count) or element (odd count)

Thus is remembered the saga of **Minimum Subset Sum Difference (Tabulation)**,
where the Oracle built the Subset Sum table
and scanned its last row from the top --
finding the largest achievable sum within half the total --
and the gap between that sum and its complement
was the smallest possible split. ⚖️🎯📋✨
