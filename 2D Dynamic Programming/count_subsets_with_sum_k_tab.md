## 🎒🔢📋 _Counting the Ways (Tabulation): The Count Subsets with Sum K Saga_

> \_"The Oracle had counted subsets with memoization.
> Now she built the count table bottom-up.
>
> **`dp[i][t]` = number of subsets from elements 0 to i
> that sum to exactly t.**
>
> Same Subset Sum table.
> But instead of booleans (true/false),
> the cells hold INTEGERS (counts).
> And instead of OR, the cells use ADDITION."\_

---

This is the saga of **Count Subsets with Sum K (Tabulation)**.

Same problem:
-   Count the number of subsets summing to exactly `k`.

```
Input:  arr = [1, 2, 3, 3], k = 6  →  Output: 3
Input:  arr = [1, 1, 1], k = 2     →  Output: 3
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][t] = number of subsets from arr[0..i] that sum to t.
```

**Base cases:**

```
dp[i][0] = 1 for all i.
```

Why 1? Because there's exactly ONE subset that sums to 0:
the empty subset. It always exists. One way.

In Subset Sum (bool), this was `true`.
Here, it's `1` -- one way to achieve sum 0.

```
dp[0][arr[0]] = 1   (if arr[0] <= k)
```

With only element 0, the only non-zero achievable sum is `arr[0]`.
One way to achieve it: pick that element.

**Special case: arr[0] == 0:**
`dp[0][0]` should be `2` (empty subset OR {0} -- both sum to 0).
Handle this carefully.

**Transition:**

```
dp[i][t] = dp[i-1][t] + dp[i-1][t - arr[i]]   (if arr[i] <= t)
         = dp[i-1][t]                            (if arr[i] > t)
```

Exclude: `dp[i-1][t]` ways without element `i`.
Include: `dp[i-1][t - arr[i]]` ways using element `i`.
Total: sum of both.

---

### 📜 The Scroll of the Bottom-Up Counting

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int countSubsets(vector<int>& arr, int k) {
    int n = arr.size();
    vector<vector<int>> dp(n, vector<int>(k + 1, 0));
```

`dp[i][t]` = number of subsets from `arr[0..i]` summing to `t`.
All start at 0.

---

### 🏁 Base Case -- Sum 0

```cpp
    for (int i = 0; i < n; i++) {
        dp[i][0] = 1;
    }
```

One way to achieve sum 0: pick nothing.

---

### 🏁 Base Case -- First Element

```cpp
    if (arr[0] <= k) {
        dp[0][arr[0]] += 1;
    }
```

Why `+=` and not `=`?

If `arr[0] == 0`, then `dp[0][0]` was already set to 1 (empty subset).
Adding 1 makes it 2: the empty subset AND the subset {0}.
Both sum to 0. Two ways.

If `arr[0] != 0`, `dp[0][arr[0]]` was 0, so `+= 1` makes it 1. Correct.

This `+=` handles the zero case naturally.

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i < n; i++) {
        for (int t = 0; t <= k; t++) {
```

Note: `t` starts from 0 (not 1) because we need to handle
the case where `arr[i] == 0` and `t == 0`.

---

### Exclude + Include

```cpp
            int exclude = dp[i - 1][t];
```

Ways to hit `t` WITHOUT element `i`.

---

```cpp
            int include = 0;
            if (arr[i] <= t) {
                include = dp[i - 1][t - arr[i]];
            }
```

Ways to hit `t` BY USING element `i`.
Only if element `i` fits (doesn't exceed `t`).

---

### ➕ Add Both

```cpp
            dp[i][t] = exclude + include;
        }
    }
    return dp[n - 1][k];
}
```

Total ways = exclude ways + include ways.

---

### 🎺 The Trial of the Bottom-Up Counting

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 3};
    cout << countSubsets(a1, 6) << endl; // expected: 3

    vector<int> a2 = {1, 1, 1};
    cout << countSubsets(a2, 2) << endl; // expected: 3

    return 0;
}
```

---

**Full table for arr = [1, 2, 3, 3], k = 6:**

```
        t=0  t=1  t=2  t=3  t=4  t=5  t=6
i=0(1):  1    1    0    0    0    0    0
i=1(2):  1    1    1    1    0    0    0
i=2(3):  1    1    1    2    1    1    1
i=3(3):  1    1    1    3    2    2    3
```

**Row 0 (element 1):** sum 0 = 1 way (empty). sum 1 = 1 way ({1}).

**Row 1 (elements 1, 2):**
-   t=2: exclude=0, include=dp[0][0]=1 → 1.
-   t=3: exclude=0, include=dp[0][1]=1 → 1.

**Row 2 (elements 1, 2, 3):**
-   t=3: exclude=dp[1][3]=1, include=dp[1][0]=1 → 2.
-   t=6: exclude=dp[1][6]=0, include=dp[1][3]=1 → 1.

**Row 3 (elements 1, 2, 3, 3):**
-   t=6: exclude=dp[2][6]=1, include=dp[2][3]=2 → **3**.

**Answer: dp[3][6] = 3** ✓

---

**Table for arr = [1, 1, 1], k = 2:**

```
        t=0  t=1  t=2
i=0(1):  1    1    0
i=1(1):  1    2    1
i=2(1):  1    3    3
```

**Row 1:** t=1: exclude=1, include=dp[0][0]=1 → 2. t=2: exclude=0, include=dp[0][1]=1 → 1.
**Row 2:** t=2: exclude=1, include=dp[1][1]=2 → **3**.

**Answer: 3** ✓

---

## 🔧 Space Optimization -- 1D Array, Right to Left

```cpp
int countSubsets(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> dp(k + 1, 0);
    dp[0] = 1;

    for (int i = 0; i < n; i++) {
        for (int t = k; t >= arr[i]; t--) {
            dp[t] = dp[t] + dp[t - arr[i]];
        }
    }
    return dp[k];
}
```

Right to left prevents using the same element twice.
`dp[t]` before update = exclude count.
`dp[t - arr[i]]` = include count (not yet overwritten).

**Note:** if zeros exist in the array, this simplified version
needs adjustment (the inner loop skips `t < arr[i]`,
but when `arr[i] = 0`, the loop doesn't run at all).
Handle zeros separately or use the 2D version.

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 Subset Sum Family -- The Complete Operator Table

| Problem              | Cell type | Combine | Base (t=0) | Base (index=n, t>0) |
| -------------------- | --------- | ------- | ---------- | ------------------- |
| Subset Sum (exist)   | bool      | OR      | true       | false               |
| Count Subsets        | int       | +       | 1          | 0                   |
| 0/1 Knapsack (max)   | int       | max     | 0          | 0                   |

Same skeleton. Same include/exclude. Different operator and type.

---

## 🔍 Why `t` Starts from 0 (Not 1) in the Inner Loop

In Subset Sum (bool), we could skip `t = 0` because it was always `true`.

In Count Subsets, when `arr[i] = 0`:
-   `dp[i][0] = dp[i-1][0] + dp[i-1][0-0] = dp[i-1][0] + dp[i-1][0] = 2 * dp[i-1][0]`.
-   Each zero doubles the count for sum 0.

If we skipped `t = 0`, we'd miss this doubling.
So the inner loop must include `t = 0`.

---

### 🧠 Memory of the Counting Ways (Tabulation) Law

-   **State:** `dp[i][t]` = number of subsets from arr[0..i] summing to t
-   **Base:** `dp[i][0] = 1`. `dp[0][arr[0]] += 1`.
-   **Transition:** `dp[i][t] = dp[i-1][t] + dp[i-1][t - arr[i]]`
-   **`+=` for first element** handles the zero case naturally
-   **Inner loop includes t=0** to handle zeros correctly
-   **Space optimization:** 1D, right to left (careful with zeros)
-   **Time:** O(N × K). **Space:** O(N × K), optimizable to O(K).

Thus is remembered the saga of **Count Subsets with Sum K (Tabulation)**,
where the Oracle filled the counting table element by element --
at each cell adding the exclude count and the include count --
until the final cell revealed
how many subsets summed to exactly K. 🎒🔢📋✨
