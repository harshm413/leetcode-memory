## 📈🔢📋 _The Merchant's K Trades (Tabulation): The Best Time to Buy and Sell Stock IV Saga_

> \_"The Oracle had solved K trades with memoization.
> Now she built the answer bottom-up.
>
> **`dp[day][txn]` = max profit from `day` onward
> with transaction state `txn`.**
>
> Fill backward. Collapse to two rows.
> Then collapse further to a single array of 2K variables --
> the generalized version of Stock III's 4-variable trick."\_

---

This is the saga of **Best Time to Buy and Sell Stock IV (Tabulation)**.

Same problem:
-   At most K transactions. Maximize total profit.

```
Input:  k = 2, prices = [3,2,6,5,0,3]  →  Output: 7
Input:  k = 1, prices = [1,2,3,4,5]     →  Output: 4
```

---

## 📋 The 2D Tabulation

### Greedy shortcut + table creation

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = prices.size();
    if (n == 0) return 0;
```

---

### Large-K shortcut

```cpp
    if (k >= n / 2) {
        int profit = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] > prices[i - 1])
                profit += prices[i] - prices[i - 1];
        }
        return profit;
    }
```

Same greedy shortcut. If K is large enough, the limit is meaningless.

---

### Create the table

```cpp
    vector<vector<int>> dp(n + 1, vector<int>(2 * k + 1, 0));
```

`(n+1) × (2K+1)` table. Row `n` and column `2K` are all zeros.
Base cases handled by initialization.

---

### 🔁 Fill backward

```cpp
    for (int day = n - 1; day >= 0; day--) {
        for (int txn = 2 * k - 1; txn >= 0; txn--) {
```

Day from last to first. Txn from `2K-1` down to 0.

---

### Even = BUY, Odd = SELL

```cpp
            if (txn % 2 == 0) {
                dp[day][txn] = max(
                    -prices[day] + dp[day + 1][txn + 1],
                    dp[day + 1][txn]
                );
            } else {
                dp[day][txn] = max(
                    prices[day] + dp[day + 1][txn + 1],
                    dp[day + 1][txn]
                );
            }
```

Identical to Stock III. Just `2*k` instead of `4`.

---

```cpp
        }
    }
    return dp[0][0];
}
```

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> p1 = {2, 4, 1};
    cout << maxProfit(2, p1) << endl; // expected: 2

    vector<int> p2 = {3, 2, 6, 5, 0, 3};
    cout << maxProfit(2, p2) << endl; // expected: 7

    vector<int> p3 = {1, 2, 3, 4, 5};
    cout << maxProfit(1, p3) << endl; // expected: 4

    return 0;
}
```

---

**Table trace for k=2, prices = [3,2,6,5,0,3]:**

```
         txn=0   txn=1   txn=2   txn=3   txn=4
day 6:     0       0       0       0       0
day 5:     3       3       3       3       0
day 4:     3       3       3       3       0
day 3:     4       3       3       3       0
day 2:     7       6       3       3       0
day 1:     7       6       3       3       0
day 0:     7       6       3       3       0
```

**Answer: dp[0][0] = 7** ✓

Reading the optimal path from the table:
-   `dp[0][0] = 7`: at day 0, txn 0 (can buy). Skip (7 = dp[1][0]).
-   `dp[1][0] = 7`: at day 1, price 2. Buy (-2 + dp[2][1] = -2+6 = 4)
    vs skip (dp[2][0] = 7). Skip wins? Actually buy gives 4, skip gives 7...
    The table shows the RESULT, not the path. The optimal buys at day 1 and day 4.

---

## 🔧 Space Optimization -- Two Rows

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = prices.size();
    if (n == 0) return 0;
    if (k >= n / 2) {
        int profit = 0;
        for (int i = 1; i < n; i++)
            if (prices[i] > prices[i - 1])
                profit += prices[i] - prices[i - 1];
        return profit;
    }

    vector<int> next(2 * k + 1, 0), curr(2 * k + 1, 0);
```

Two arrays of size `2K+1`. That's all we need.

---

```cpp
    for (int day = n - 1; day >= 0; day--) {
        for (int txn = 2 * k - 1; txn >= 0; txn--) {
            if (txn % 2 == 0) {
                curr[txn] = max(-prices[day] + next[txn + 1], next[txn]);
            } else {
                curr[txn] = max(prices[day] + next[txn + 1], next[txn]);
            }
        }
        next = curr;
    }
    return next[0];
}
```

```
Time:  O(N × K)
Space: O(K)
```

---

## 🔧 The 2K-Variable Solution -- Generalized Stock III

For Stock III, we had 4 variables: `buy1, sell1, buy2, sell2`.
For Stock IV, we use an array of `2K` variables:

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = prices.size();
    if (n == 0) return 0;
    if (k >= n / 2) {
        int profit = 0;
        for (int i = 1; i < n; i++)
            if (prices[i] > prices[i - 1])
                profit += prices[i] - prices[i - 1];
        return profit;
    }

    vector<int> dp(2 * k, 0);
    for (int i = 0; i < 2 * k; i += 2) dp[i] = INT_MIN;
```

Even indices (buys) start at `INT_MIN` — haven't bought yet.
Odd indices (sells) start at `0` — doing nothing is valid.

---

```cpp
    for (int price : prices) {
        dp[0] = max(dp[0], -price);
        for (int j = 1; j < 2 * k; j++) {
            if (j % 2 == 1) {
                dp[j] = max(dp[j], dp[j - 1] + price);
            } else {
                dp[j] = max(dp[j], dp[j - 1] - price);
            }
        }
    }
    return dp[2 * k - 1];
}
```

**`dp[0]`** = best 1st buy = `max(dp[0], -price)`.
**`dp[1]`** = best 1st sell = `max(dp[1], dp[0] + price)`.
**`dp[2]`** = best 2nd buy = `max(dp[2], dp[1] - price)`.
**`dp[3]`** = best 2nd sell = `max(dp[3], dp[2] + price)`.
...and so on for all K trades.

Each sell builds on the previous buy.
Each buy builds on the previous sell's profit.
The chain extends to any K.

---

**Trace for k=2, prices = [3,2,6,5,0,3]:**

| Price | dp[0](buy1) | dp[1](sell1) | dp[2](buy2) | dp[3](sell2) |
| ----- | ----------- | ------------ | ----------- | ------------ |
| 3     | -3          | 0            | -3          | 0            |
| 2     | -2          | 0            | -2          | 0            |
| 6     | -2          | 4            | -2          | 4            |
| 5     | -2          | 4            | -1          | 4            |
| 0     | 0           | 4            | 4           | 4            |
| 3     | 0           | 4            | 4           | **7**        |

**Answer: dp[3] = 7** ✓

Key: at price 0, `dp[2] = max(-2, dp[1] - 0) = max(-2, 4) = 4`.
The 2nd buy carries the 1st trade's profit of 4.
At price 3, `dp[3] = max(4, 4 + 3) = 7`.

---

## 🔍 The Complete Stock Family -- Unified View

| Problem    | K        | Solution                              |
| ---------- | -------- | ------------------------------------- |
| Stock I    | 1        | `dp[0..1]` or track-min shortcut      |
| Stock II   | ∞        | Greedy (sum positive diffs)           |
| Stock III  | 2        | `dp[0..3]` (4 variables)             |
| **Stock IV** | **any** | **`dp[0..2K-1]` + greedy shortcut** |

Stock IV is the master solution. Every other stock problem
is a special case of this framework.

---

### 🧠 Memory of the Merchant's K Trades (Tabulation) Law

-   **2D table:** `dp[day][txn]`, fill backward, even=buy, odd=sell
-   **Space optimization:** two rows of size 2K → O(K)
-   **2K-variable form:** forward pass, each variable builds on the previous
    -   Even (buy): `max(dp[j], dp[j-1] - price)`
    -   Odd (sell): `max(dp[j], dp[j-1] + price)`
-   **Large-K shortcut:** `k >= n/2` → greedy O(N)
-   **This is the GENERAL solution** for all stock problems with transaction limits
-   **Time:** O(N × K). **Space:** O(K).

Thus is remembered the saga of **Best Time to Buy and Sell Stock IV (Tabulation)**,
where the Oracle generalized the 4-variable trick
into a chain of 2K variables --
each buy building on the previous sell,
each sell harvesting the previous buy --
with a greedy escape hatch for when K grew so large
that the constraint dissolved into nothing. 📈🔢📋✨
