## ⚖️🔢📋 _Counting the Splits (Tabulation): The Count Partitions with Given Difference Saga_

> \_"The Oracle had reduced the partition problem
> to Count Subsets with Sum K.
>
> Now she solved it bottom-up --
> the same counting table, filled element by element."\_

---

This is the saga of **Count Partitions with Given Difference (Tabulation)**.

Same problem:
-   Split array into S1 and S2 where S1 - S2 = d.
-   Count the number of ways.

```
Input:  arr = [5, 2, 6, 4], d = 3  →  Output: 1
Input:  arr = [1, 1, 1, 1], d = 0  →  Output: 6
```

---

## 🧠 The Strategy

```
1. target = (total + d) / 2. Check impossible cases.
2. Count Subsets with Sum = target (tabulation).
```

---

### 📜 The Scroll of the Bottom-Up Counted Splits

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### The Wrapper -- Reduce and Validate

```cpp
int countPartitions(vector<int>& arr, int d) {
    int total = accumulate(arr.begin(), arr.end(), 0);
```

---

### 🛑 Impossible Cases

```cpp
    if ((total + d) % 2 != 0) return 0;
    if (total + d < 0) return 0;
```

Odd sum → fractional target → impossible.
Negative sum → negative target → impossible.

---

### 🎯 Compute Target

```cpp
    int target = (total + d) / 2;
    int n = arr.size();
```

---

### Build the Count Subsets Table

```cpp
    vector<vector<int>> dp(n, vector<int>(target + 1, 0));
```

`dp[i][t]` = number of subsets from arr[0..i] summing to t.

---

### 🏁 Base Case -- Sum 0

```cpp
    for (int i = 0; i < n; i++) {
        dp[i][0] = 1;
    }
```

One way to achieve sum 0: the empty subset.

---

### 🏁 Base Case -- First Element

```cpp
    if (arr[0] <= target) {
        dp[0][arr[0]] += 1;
    }
```

`+=` handles the case where `arr[0] == 0`
(both empty subset and {0} sum to 0 → count = 2).

---

### 🔁 Fill the Table

```cpp
    for (int i = 1; i < n; i++) {
        for (int t = 0; t <= target; t++) {
            int exclude = dp[i - 1][t];
            int include = 0;
            if (arr[i] <= t) {
                include = dp[i - 1][t - arr[i]];
            }
            dp[i][t] = exclude + include;
        }
    }
    return dp[n - 1][target];
}
```

Standard Count Subsets tabulation. Nothing new.

Exclude: ways without this element.
Include: ways using this element (if it fits).
Total: sum of both.

---

### 🎺 The Trial of the Bottom-Up Counted Splits

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

**Full table for arr = [5, 2, 6, 4], d = 3, target = 10:**

```
        t=0  t=1  t=2  t=3  t=4  t=5  t=6  t=7  t=8  t=9  t=10
i=0(5):  1    0    0    0    0    1    0    0    0    0    0
i=1(2):  1    0    1    0    0    1    0    1    0    0    0
i=2(6):  1    0    1    0    0    1    1    1    1    0    0
i=3(4):  1    0    1    0    1    1    1    1    1    1    1
```

**dp[3][10] = 1** ✓

One subset sums to 10: {6, 4}.
S1 = 10, S2 = 7, diff = 3. ✓

---

**Table for arr = [1, 1, 1, 1], d = 0, target = 2:**

```
        t=0  t=1  t=2
i=0(1):  1    1    0
i=1(1):  1    2    1
i=2(1):  1    3    3
i=3(1):  1    4    6
```

**dp[3][2] = 6** ✓

6 ways to pick 2 ones from 4 = C(4, 2) = 6.

---

## 🔧 Space Optimization -- 1D Array

```cpp
int countPartitions(vector<int>& arr, int d) {
    int total = accumulate(arr.begin(), arr.end(), 0);
    if ((total + d) % 2 != 0 || total + d < 0) return 0;
    int target = (total + d) / 2;

    vector<int> dp(target + 1, 0);
    dp[0] = 1;

    for (int i = 0; i < (int)arr.size(); i++) {
        for (int t = target; t >= arr[i]; t--) {
            dp[t] += dp[t - arr[i]];
        }
    }
    return dp[target];
}
```

Right to left. Same 0/1 knapsack optimization.

**Zero handling:** when `arr[i] = 0`, the inner loop
`for (t = target; t >= 0; t--)` would run,
and `dp[t] += dp[t - 0] = dp[t] += dp[t]` → doubles every cell.
This is correct (each zero doubles the count).

But with `t >= arr[i]` and `arr[i] = 0`, the loop runs for ALL `t`.
Make sure the loop condition handles this.

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 The Complete Reduction Family

| Problem                          | Reduction                              |
| -------------------------------- | -------------------------------------- |
| Partition Equal Subset Sum       | Subset Sum on total/2                  |
| Min Subset Sum Difference        | Subset Sum on total/2, scan for best   |
| Count Partitions with Diff d     | Count Subsets on (total + d) / 2       |
| Target Sum (LeetCode 494)        | Count Subsets on (total + target) / 2  |

All reduce to Subset Sum or Count Subsets with a transformed target.
The algebra is the hard part. The DP is always the same.

---

## 🔍 Why This Is Also Target Sum (LeetCode 494)

Target Sum: assign `+` or `-` to each element to reach `target`.

Let P = sum of `+` elements, N = sum of `-` elements.
`P - N = target` and `P + N = total`.
→ `P = (total + target) / 2`.

**Identical to Count Partitions with d = target.**

Same reduction. Same DP. Different problem statement.

---

### 🧠 Memory of the Counted Splits (Tabulation) Law

-   **Reduction:** `target = (total + d) / 2`
-   **Impossible:** `(total + d)` odd or negative → return 0
-   **DP:** Count Subsets with Sum = target (standard tabulation)
-   **Also solves Target Sum** (LeetCode 494) with same reduction
-   **Space optimization:** 1D, right to left
-   **Time:** O(N × target). **Space:** O(target) optimized.

Thus is remembered the saga of **Count Partitions with Given Difference (Tabulation)**,
where the Oracle reduced the partition problem
to a single algebraic formula --
`target = (total + d) / 2` --
then counted the subsets hitting that target
with the familiar include-or-exclude table,
knowing each counted subset
was a valid partition with exactly the required difference. ⚖️🔢📋✨
