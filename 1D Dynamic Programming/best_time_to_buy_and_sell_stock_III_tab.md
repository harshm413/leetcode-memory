## 📈📈📋 _The Merchant's Two Trades (Tabulation): The Best Time to Buy and Sell Stock III Saga_

> \_"The Oracle had solved two trades with memoization.
> Now she built the answer bottom-up.
>
> **`dp[day][txn]` = max profit starting from `day`
> with transaction state `txn`.**
>
> Fill from the last day backward to day 0.
> Even txn = buy opportunity. Odd txn = sell opportunity.
>
> But the Oracle saw something even better --
> the entire 2D table could be collapsed
> into just 4 variables.
> One for each transaction state.
> Updated day by day."\_

---

This is the saga of **Best Time to Buy and Sell Stock III (Tabulation)**.

Same problem:
-   At most 2 transactions. Maximize total profit.

```
Input:  prices = [3,3,5,0,0,3,1,4]  →  Output: 6
Input:  prices = [1,2,3,4,5]         →  Output: 4
Input:  prices = [7,6,4,3,1]         →  Output: 0
```

---

## 🧠 The Bottom-Up Formulation

```
dp[day][txn] = max profit from day onward with transaction state txn.
```

**Base cases:**

```
dp[n][txn] = 0   for all txn.   ← past the last day, no profit
dp[day][4] = 0   for all day.   ← all transactions used, no profit
```

**Transition (same as memoization, just iterative):**

```
Even txn (buy):
  dp[day][txn] = max(-prices[day] + dp[day+1][txn+1], dp[day+1][txn])

Odd txn (sell):
  dp[day][txn] = max(+prices[day] + dp[day+1][txn+1], dp[day+1][txn])
```

**Fill order:** day from `n-1` down to `0`, txn from `3` down to `0`.
Why backward? Because `dp[day]` depends on `dp[day+1]`.

---

### 📜 The Scroll of the Bottom-Up Two Trades

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The 2D Tabulation

### Create the Table

```cpp
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    vector<vector<int>> dp(n + 1, vector<int>(5, 0));
```

`(n+1) × 5` table. Row `n` and column `4` are all zeros (base cases).
Already initialized to 0 — base cases handled by default.

---

### 🔁 Fill Backward -- Day by Day

```cpp
    for (int day = n - 1; day >= 0; day--) {
        for (int txn = 3; txn >= 0; txn--) {
```

Start from the last day, work backward.
At each day, process all 4 transaction states.

Why `txn` from 3 down to 0? Because `dp[day][txn]` uses `dp[day+1][txn+1]`.
`txn+1` is a higher index — already computed if we go high to low.
Actually, since we read from `day+1` (already filled), the txn order
within the same day doesn't matter. But going 3→0 is natural.

---

### 🔀 Even txn — BUY, Odd txn — SELL

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

Same logic as memoization. Buy = pay now + future.
Sell = gain now + future. Skip = same state tomorrow.

---

```cpp
        }
    }
    return dp[0][0];
}
```

`dp[0][0]` = max profit starting from day 0 with no transactions done.

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> p1 = {3, 3, 5, 0, 0, 3, 1, 4};
    cout << maxProfit(p1) << endl; // expected: 6

    vector<int> p2 = {1, 2, 3, 4, 5};
    cout << maxProfit(p2) << endl; // expected: 4

    vector<int> p3 = {7, 6, 4, 3, 1};
    cout << maxProfit(p3) << endl; // expected: 0

    return 0;
}
```

---

**Partial table trace for prices = [3,3,5,0,0,3,1,4]:**

```
         txn=0   txn=1   txn=2   txn=3   txn=4
day 8:     0       0       0       0       0     ← base case
day 7:     4       4       4       4       0
day 6:     4       4       3       3       0
day 5:     4       4       3       3       0
day 4:     7       4       3       3       0
day 3:     7       4       3       4       0
day 2:     6       4       3       4       0
day 1:     6       4       3       4       0
day 0:     6       4       3       4       0
```

**Answer: dp[0][0] = 6** ✓

---

## 🔧 Space Optimization -- From 2D to 1D

Each row `day` only depends on row `day+1`.
We only need two rows: `next` (day+1) and `curr` (day).

```cpp
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    vector<int> next(5, 0), curr(5, 0);
```

---

```cpp
    for (int day = n - 1; day >= 0; day--) {
        for (int txn = 3; txn >= 0; txn--) {
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

`next[txn]` = `dp[day+1][txn]`. `next[txn+1]` = `dp[day+1][txn+1]`.
Both from the next row, already computed.

After each day, `curr` becomes `next` for the previous day.

```
Time:  O(N × 4) = O(N)
Space: O(1) — just two arrays of size 5
```

---

## 🔧 The 4-Variable Solution -- Ultimate Optimization

Since there are only 4 transaction states, we can use 4 named variables
instead of arrays. This is the classic "interview-friendly" form:

```cpp
int maxProfit(vector<int>& prices) {
    int buy1 = INT_MIN, sell1 = 0;
    int buy2 = INT_MIN, sell2 = 0;
```

`buy1` = best profit after 1st buy (negative, since we paid).
`sell1` = best profit after 1st sell.
`buy2` = best profit after 2nd buy.
`sell2` = best profit after 2nd sell.

All sells start at 0 (doing nothing is valid).
All buys start at INT_MIN (haven't bought yet — worst possible).

---

```cpp
    for (int price : prices) {
        buy1  = max(buy1, -price);
```

**1st buy:** either keep the old buy, or buy today at `-price`.
We want the LEAST negative (cheapest buy).

---

```cpp
        sell1 = max(sell1, buy1 + price);
```

**1st sell:** either keep the old sell profit, or sell today.
Selling today gives `buy1 + price` — what we paid (buy1) plus today's price.

---

```cpp
        buy2  = max(buy2, sell1 - price);
```

**2nd buy:** either keep the old buy, or buy today using 1st trade's profit.
`sell1 - price` means: profit from 1st trade minus today's buy price.
This is the key — the 2nd buy BUILDS ON the 1st trade's profit.

---

```cpp
        sell2 = max(sell2, buy2 + price);
```

**2nd sell:** either keep the old sell, or sell today.
`buy2 + price` = accumulated profit from both trades.

---

```cpp
    }
    return sell2;
}
```

`sell2` holds the maximum profit from at most 2 transactions.

If only 1 trade was profitable, `buy2` and `sell2` effectively
replicate `sell1` (the 2nd trade adds zero profit).

---

**Trace for prices = [3,3,5,0,0,3,1,4]:**

| Price | buy1 | sell1 | buy2 | sell2 |
| ----- | ---- | ----- | ---- | ----- |
| 3     | -3   | 0     | -3   | 0     |
| 3     | -3   | 0     | -3   | 0     |
| 5     | -3   | 2     | -3   | 2     |
| 0     | 0    | 2     | 2    | 2     |
| 0     | 0    | 2     | 2    | 2     |
| 3     | 0    | 3     | 2    | 5     |
| 1     | 0    | 3     | 2    | 5     |
| 4     | 0    | 4     | 2    | **6** |

**Answer: sell2 = 6** ✓

Key moment: at price 0, `buy2 = max(-3, sell1 - 0) = max(-3, 2) = 2`.
The 2nd buy "carries forward" the 1st trade's profit of 2.
Then at price 4, `sell2 = buy2 + 4 = 2 + 4 = 6`.

---

**Trace for prices = [1,2,3,4,5]:**

| Price | buy1 | sell1 | buy2 | sell2 |
| ----- | ---- | ----- | ---- | ----- |
| 1     | -1   | 0     | -1   | 0     |
| 2     | -1   | 1     | -1   | 1     |
| 3     | -1   | 2     | -1   | 2     |
| 4     | -1   | 3     | -1   | 3     |
| 5     | -1   | 4     | -1   | **4** |

**Answer: 4** ✓ (one trade is enough — buy2/sell2 mirror buy1/sell1)

---

## 🔍 Why the 4-Variable Approach Works

The variables form a chain of dependencies:

```
buy1 depends on: nothing (just -price)
sell1 depends on: buy1
buy2 depends on: sell1
sell2 depends on: buy2
```

Each variable builds on the previous one.
Updating them LEFT TO RIGHT within the same day is safe
because each uses the JUST-UPDATED value of its predecessor.

This is NOT a bug — it's correct. Using today's `buy1` in today's `sell1`
means "buy and sell on the same day" which gives profit 0.
That's equivalent to "don't trade" — harmless.

---

## 🔍 Connecting the Three Approaches

| Approach       | Time  | Space  | Clarity          |
| -------------- | ----- | ------ | ---------------- |
| 2D DP table    | O(N)  | O(N)   | Clearest         |
| Two-row DP     | O(N)  | O(1)   | Good             |
| 4 variables    | O(N)  | O(1)   | Most compact     |

Present the 2D table first. Show the 4-variable as the optimization.

---

### 🧠 Memory of the Merchant's Two Trades (Tabulation) Law

-   **2D table:** `dp[day][txn]`, fill backward, even=buy, odd=sell
-   **Space optimization:** two rows → 4 variables
-   **4-variable form:** `buy1, sell1, buy2, sell2` updated left-to-right
-   **Key insight:** `buy2 = max(buy2, sell1 - price)` — 2nd buy carries 1st profit
-   **Generalizes to K:** use `2K` variables in a loop
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Best Time to Buy and Sell Stock III (Tabulation)**,
where the Oracle built the trading table backward --
then collapsed it into four variables,
each building on the last --
buy1 found the cheapest entry,
sell1 locked the first profit,
buy2 reinvested that profit into a second trade,
and sell2 captured the final harvest --
all in a single forward pass. 📈📈📋✨
