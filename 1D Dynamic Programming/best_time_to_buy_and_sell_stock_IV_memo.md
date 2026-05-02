## 📈🔢🧠 _The Merchant's K Trades (Memoization): The Best Time to Buy and Sell Stock IV Saga_

> \_"The merchant had mastered two trades.
>
> Now the King generalized the constraint:
>
> **'You may make AT MOST K transactions.
> K is given. It could be 1, 2, 100, or a million.
> Maximize your total profit.'**
>
> The merchant smiled.
> He had already built the machinery for 2 trades.
> The even/odd transaction encoding.
> The (day, txn) state.
>
> For 2 trades, txn went from 0 to 4.
> For K trades, txn goes from 0 to 2K.
>
> **Same code. Replace 4 with 2K. Done.**
>
> But the Oracle added one optimization:
>
> **If K ≥ n/2, we have effectively unlimited trades.**
> With n days, the maximum useful transactions is n/2
> (buy one day, sell the next, repeat).
> Beyond that, extra transactions are wasted.
> In that case — fall back to the greedy O(N) solution."\_

---

This is the saga of **Best Time to Buy and Sell Stock IV (LeetCode 188)**.

Given an array `prices` and an integer `k`:
-   You may complete **at most k transactions**.
-   You must sell before you buy again.
-   Find the **maximum profit**.

```
Input:  k = 2, prices = [2,4,1]
Output: 2   (buy at 2, sell at 4)

Input:  k = 2, prices = [3,2,6,5,0,3]
Output: 7   (buy at 2, sell at 6 = +4; buy at 0, sell at 3 = +3)
```

---

## 🧠 The Generalization -- From 2 to K

In Stock III, we had 4 transaction states (2 trades × 2 steps each):

```
txn=0: 1st buy, txn=1: 1st sell, txn=2: 2nd buy, txn=3: 2nd sell
```

For K trades, we have `2K` transaction states:

```
txn=0: 1st buy,  txn=1: 1st sell
txn=2: 2nd buy,  txn=3: 2nd sell
...
txn=2K-2: Kth buy, txn=2K-1: Kth sell
txn=2K: DONE
```

Even txn = buy. Odd txn = sell. Same pattern. Just more states.

---

## 🧠 The Large-K Optimization

If `k >= n/2`, we can make a transaction every two days.
That's more trades than we could ever use.
Effectively unlimited — same as Stock II.

In that case, skip the DP entirely and use the greedy approach:
sum all positive daily differences.

This prevents creating a massive `n × 2K` table when K is huge.

---

## 🧠 The Recurrence (Identical to Stock III)

```
solve(day, txn):
  If day == n OR txn == 2*k: return 0

  If txn is even (buy):
    max(-prices[day] + solve(day+1, txn+1), solve(day+1, txn))

  If txn is odd (sell):
    max(+prices[day] + solve(day+1, txn+1), solve(day+1, txn))
```

---

### 📜 The Scroll of the Merchant's K Trades

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📈 The Large-K Greedy Shortcut

```cpp
int greedyProfit(vector<int>& prices) {
    int profit = 0;
    for (int i = 1; i < prices.size(); i++) {
        if (prices[i] > prices[i - 1]) {
            profit += prices[i] - prices[i - 1];
        }
    }
    return profit;
}
```

Stock II's greedy solution. Harvest every rise.
Used when K is large enough that the limit doesn't matter.

---

## 📈 Setting Up the Cache

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = prices.size();
    if (n == 0) return 0;
```

Empty prices — no trading possible.

---

### Check for unlimited trades

```cpp
    if (k >= n / 2) return greedyProfit(prices);
```

If K is large enough, the constraint is meaningless.
Fall back to O(N) greedy. This avoids creating a huge DP table.

Why `n/2`? In n days, the maximum number of non-overlapping
buy-sell pairs is `n/2` (buy day 0, sell day 1, buy day 2, sell day 3...).
Any K beyond that adds nothing.

---

### Create the memo table

```cpp
    vector<vector<int>> memo(n, vector<int>(2 * k, -1));
```

`n × 2K` table. Transaction states go from 0 to `2K-1`.
State `2K` is the base case (return 0).

---

```cpp
    return solve(0, 0, k, prices, memo);
}
```

---

## 🔮 The Recursive Thinking

```cpp
int solve(int day, int txn, int k, vector<int>& prices,
          vector<vector<int>>& memo) {
```

---

### 🛑 Base cases

```cpp
    if (day == prices.size()) return 0;
    if (txn == 2 * k) return 0;
```

No more days, or all K transactions used.

---

### 📖 Cache check

```cpp
    if (memo[day][txn] != -1) return memo[day][txn];
```

---

### 🔀 Even = BUY, Odd = SELL

```cpp
    if (txn % 2 == 0) {
        memo[day][txn] = max(
            -prices[day] + solve(day + 1, txn + 1, k, prices, memo),
            solve(day + 1, txn, k, prices, memo)
        );
    }
```

Buy today (pay, advance txn) or skip (stay).

---

```cpp
    else {
        memo[day][txn] = max(
            prices[day] + solve(day + 1, txn + 1, k, prices, memo),
            solve(day + 1, txn, k, prices, memo)
        );
    }
```

Sell today (gain, advance txn) or skip (stay).

---

```cpp
    return memo[day][txn];
}
```

**Identical to Stock III.** The only difference: `4` became `2*k`.

---

### 🎺 The Trial of the Merchant's K Trades

```cpp
int main() {
    vector<int> p1 = {2, 4, 1};
    cout << maxProfit(2, p1) << endl; // expected: 2

    vector<int> p2 = {3, 2, 6, 5, 0, 3};
    cout << maxProfit(2, p2) << endl; // expected: 7

    vector<int> p3 = {1, 2, 3, 4, 5};
    cout << maxProfit(1, p3) << endl; // expected: 4

    vector<int> p4 = {7, 6, 4, 3, 1};
    cout << maxProfit(3, p4) << endl; // expected: 0

    return 0;
}
```

---

**Trace for k=2, prices = [3,2,6,5,0,3]:**

```
Optimal path:
  Day 1: BUY at 2   (txn 0→1)
  Day 2: SELL at 6   (txn 1→2, profit +4)
  Day 4: BUY at 0   (txn 2→3)
  Day 5: SELL at 3   (txn 3→4, profit +3)
  Total: 4 + 3 = 7
```

**Answer: 7** ✓

---

**Trace for k=1, prices = [1,2,3,4,5]:**

```
Only 1 trade allowed. txn goes 0→1→2 (done).
Best: BUY at 1 (day 0), SELL at 5 (day 4). Profit = 4.
```

**Answer: 4** ✓

---

## 🔍 The Complete Stock Problem Progression

| Problem   | K value   | Approach                          |
| --------- | --------- | --------------------------------- |
| Stock I   | K = 1     | Track min, one pass (or this DP)  |
| Stock II  | K = ∞     | Greedy — harvest every rise       |
| Stock III | K = 2     | DP with txn=0..4                  |
| **Stock IV** | **K = any** | **DP with txn=0..2K + greedy shortcut** |

Stock IV is the GENERAL solution. Stocks I, II, III are special cases.

---

## 🔍 Why the Greedy Shortcut Matters

Without it, k=1000000 and n=10 would create a 10×2000000 table.
That's 20 million cells for a problem that needs at most 5 trades.

The `k >= n/2` check catches this and solves it in O(N) instead.

---

### 🧠 Memory of the Merchant's K Trades (Memoization) Law

-   **State:** `(day, txn)` where txn = 0 to 2K-1
-   **Even txn = buy:** `max(-price + solve(day+1, txn+1), solve(day+1, txn))`
-   **Odd txn = sell:** `max(+price + solve(day+1, txn+1), solve(day+1, txn))`
-   **Base:** `day == n → 0`, `txn == 2K → 0`
-   **Large-K shortcut:** if `k >= n/2`, use greedy (Stock II)
-   **This is the GENERAL solution** — Stocks I, II, III are special cases
-   **Time:** O(N × K). **Space:** O(N × K).

Thus is remembered the saga of **Best Time to Buy and Sell Stock IV (Memoization)**,
where the merchant generalized his two-trade machinery
to handle any number of trades --
the same even/odd encoding,
the same buy-or-skip and sell-or-skip choices,
just with `2K` states instead of 4 --
and a greedy shortcut for when K was so large
that the constraint ceased to matter. 📈🔢🧠✨
