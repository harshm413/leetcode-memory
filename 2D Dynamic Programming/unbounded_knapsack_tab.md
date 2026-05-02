## 🎒♾️📋 _The Infinite Supply (Tabulation): The Unbounded Knapsack Saga_

> \_"The Oracle had solved the unbounded knapsack with memoization.
> Now she built the answer bottom-up.
>
> The tabulation had the same critical difference
> from 0/1 knapsack:
>
> **Inner loop goes LEFT TO RIGHT (not right to left).**
>
> Left to right allows reusing the same item.
> Right to left prevents reuse.
> The direction encodes the supply policy."\_

---

This is the saga of **Unbounded Knapsack (Tabulation)**.

Same problem:
-   Items with weight and value. Unlimited copies.
-   Maximize value within capacity W.

```
Input:  weight = [2, 4, 6], value = [5, 11, 13], W = 10  →  Output: 27
Input:  weight = [1, 3, 4, 5], value = [1, 4, 5, 7], W = 8  →  Output: 11
```

---

## 🧠 Two Tabulation Approaches

### Approach 1 -- 2D Table (Matches the Memoization)

```
dp[i][c] = max value using items 0..i with capacity c.

dp[i][c] = max(
    value[i] + dp[i][c - weight[i]],    ← USE (stay at i -- unbounded!)
    dp[i-1][c]                            ← SKIP (previous items only)
)
```

Note: `dp[i][c - weight[i]]` reads from the SAME row `i`.
In 0/1 knapsack, it would read from row `i-1`.
This is the unbounded signature in 2D.

### Approach 2 -- 1D Table (Simpler)

```
dp[c] = max value achievable with capacity c.

For each item:
    for c = weight[i] to W:    ← LEFT TO RIGHT
        dp[c] = max(dp[c], value[i] + dp[c - weight[i]])
```

Left to right allows `dp[c - weight[i]]` to include
uses of this item from earlier in the same iteration.
That's reuse. That's unbounded.

We'll present the 2D approach first (clearer),
then the 1D optimization.

---

### 📜 The Scroll of the Bottom-Up Infinite Supply

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The 2D Tabulation

### Create the Table

```cpp
int unboundedKnapsack(vector<int>& weight, vector<int>& value, int W) {
    int n = weight.size();
    vector<vector<int>> dp(n, vector<int>(W + 1, 0));
```

`dp[i][c]` = max value using items 0 to i with capacity c.
All start at 0 (no value with no items or no capacity).

---

### 🏁 Fill Row 0 -- Only Item 0 Available

```cpp
    for (int c = 0; c <= W; c++) {
        if (weight[0] <= c) {
            dp[0][c] = (c / weight[0]) * value[0];
        }
    }
```

With only item 0 and unlimited copies:
how many copies fit? `c / weight[0]` (integer division).
Total value = copies × value[0].

Why `c / weight[0]`? Because we can use item 0
as many times as the capacity allows.
This is the unbounded base case.

In 0/1 knapsack, the base case would be:
`dp[0][c] = value[0]` if `weight[0] <= c`, else 0.
(At most one copy.)

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i < n; i++) {
        for (int c = 0; c <= W; c++) {
```

---

### 🚶 Skip This Item

```cpp
            int skip = dp[i - 1][c];
```

Max value using items 0 to i-1 with capacity c.
This item is not used.

---

### 🎒 Use This Item (Read from SAME Row)

```cpp
            int use = 0;
            if (weight[i] <= c) {
                use = value[i] + dp[i][c - weight[i]];
            }
```

**`dp[i][c - weight[i]]`** -- reading from the SAME row `i`.

Why same row? Because after using item `i`,
it's still available (unlimited).
`dp[i][c - weight[i]]` already accounts for
potentially using item `i` multiple times
(it was filled earlier in this same row, left to right).

In 0/1 knapsack: `dp[i-1][c - weight[i]]` -- previous row.
In unbounded: `dp[i][c - weight[i]]` -- SAME row.

> _"The thief reads from his own row --
> the row where this item is available.
> Because the item never runs out."_

---

### ⚖️ Take the Maximum

```cpp
            dp[i][c] = max(use, skip);
        }
    }
    return dp[n - 1][W];
}
```

---

### 🎺 The Trial of the 2D Infinite Supply

```cpp
int main() {
    vector<int> w1 = {2, 4, 6}, v1 = {5, 11, 13};
    cout << unboundedKnapsack(w1, v1, 10) << endl; // expected: 27

    vector<int> w2 = {1, 3, 4, 5}, v2 = {1, 4, 5, 7};
    cout << unboundedKnapsack(w2, v2, 8) << endl; // expected: 11

    return 0;
}
```

---

**Full 2D table for weight=[2,4,6], value=[5,11,13], W=10:**

**Row 0 (item 0: w=2, v=5):**

| c  | copies = c/2 | dp[0][c] |
| -- | ------------ | -------- |
| 0  | 0            | 0        |
| 1  | 0            | 0        |
| 2  | 1            | 5        |
| 3  | 1            | 5        |
| 4  | 2            | 10       |
| 5  | 2            | 10       |
| 6  | 3            | 15       |
| 7  | 3            | 15       |
| 8  | 4            | 20       |
| 9  | 4            | 20       |
| 10 | 5            | 25       |

**Row 1 (items 0,1: adding w=4, v=11):**

| c  | skip=dp[0][c] | use=11+dp[1][c-4] | dp[1][c] |
| -- | ------------- | ------------------ | -------- |
| 0  | 0             | --                 | 0        |
| 1  | 0             | --                 | 0        |
| 2  | 5             | --                 | 5        |
| 3  | 5             | --                 | 5        |
| 4  | 10            | 11+dp[1][0]=11     | 11       |
| 5  | 10            | 11+dp[1][1]=11     | 11       |
| 6  | 15            | 11+dp[1][2]=16     | 16       |
| 7  | 15            | 11+dp[1][3]=16     | 16       |
| 8  | 20            | 11+dp[1][4]=22     | 22       |
| 9  | 20            | 11+dp[1][5]=22     | 22       |
| 10 | 25            | 11+dp[1][6]=27     | **27**   |

At c=10: use = 11 + dp[1][6] = 11 + 16 = 27.
dp[1][6] = 11 + dp[1][2] = 11 + 5 = 16 (item 1 + item 0).
So: item 1 + item 1 + item 0 = 11+11+5 = 27. Weight: 4+4+2 = 10. ✓

**Row 2 (adding item 2: w=6, v=13):** doesn't improve on 27.

**Answer: dp[2][10] = 27** ✓

---

## 🔧 Space Optimization -- 1D Array, Left to Right

```cpp
int unboundedKnapsack(vector<int>& weight, vector<int>& value, int W) {
    int n = weight.size();
    vector<int> dp(W + 1, 0);
```

---

```cpp
    for (int i = 0; i < n; i++) {
        for (int c = weight[i]; c <= W; c++) {
            dp[c] = max(dp[c], value[i] + dp[c - weight[i]]);
        }
    }
    return dp[W];
}
```

**LEFT TO RIGHT** -- the unbounded signature.

`dp[c - weight[i]]` may have been updated EARLIER in this same iteration.
That means it already includes uses of item `i`.
So `value[i] + dp[c - weight[i]]` can use item `i` multiple times.
That's exactly what we want -- unlimited supply.

In 0/1 knapsack: RIGHT TO LEFT (prevent reuse).
In unbounded: LEFT TO RIGHT (allow reuse).

**The direction of the inner loop is the ONLY difference
between 0/1 and unbounded knapsack in 1D tabulation.**

Present the 2D table first. Mention 1D as a follow-up.

---

## 🔍 0/1 vs Unbounded -- The Complete Comparison

| Aspect              | 0/1 Knapsack                | Unbounded Knapsack          |
| ------------------- | --------------------------- | --------------------------- |
| Item supply         | Each item once              | Each item unlimited         |
| Memo: after include | `solve(i+1, ...)`          | `solve(i, ...)` (stay)      |
| 2D tab: include reads | `dp[i-1][c-w]` (prev row) | `dp[i][c-w]` (same row)   |
| 1D tab: inner loop  | Right to left               | **Left to right**           |
| Base case (row 0)   | 0 or value[0] (one copy)   | `(c/w[0]) * v[0]` (many)   |

**Everything else is identical.** Same structure. Same choices.
The supply policy changes one index, one direction, one base case.

---

### 🧠 Memory of the Infinite Supply (Tabulation) Law

**2D Table:**
-   `dp[i][c] = max(value[i] + dp[i][c-w[i]], dp[i-1][c])`
-   USE reads from **same row** `i` (unbounded)
-   Base row 0: `(c / weight[0]) * value[0]`

**1D Optimization:**
-   `dp[c] = max(dp[c], value[i] + dp[c - weight[i]])`
-   Inner loop: **LEFT TO RIGHT** (allows reuse)
-   0/1 would be right to left

-   **Time:** O(n × W). **Space:** O(n × W) or O(W) optimized.

Thus is remembered the saga of **Unbounded Knapsack (Tabulation)**,
where the Oracle filled the table item by item --
reading from the same row when including (unlimited supply),
iterating left to right to allow reuse --
until the maximum value within the capacity was found. 🎒♾️📋✨
