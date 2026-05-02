## 📈📈🧠 _The Merchant's Two Trades (Memoization): The Best Time to Buy and Sell Stock III Saga_

> \_"The merchant had mastered the single trade.
> He had mastered unlimited trades.
>
> Now the King imposed a new constraint:
>
> **'You may make AT MOST two transactions.
> Buy, sell. Buy again, sell again.
> No more than two complete trades.
> Maximize your total profit.'**
>
> The merchant realized this was fundamentally different.
>
> With one trade — track the minimum, maximize the difference.
> With unlimited trades — harvest every rise.
> With exactly two trades — neither trick works.
>
> He needed to track WHERE he was in the trading process:
>
> **Am I holding a stock or not?
> How many transactions have I completed?**
>
> At each day, his choices depended on his STATE.
> And that meant — Dynamic Programming."\_

---

This is the saga of **Best Time to Buy and Sell Stock III (LeetCode 123)**.

Given an array `prices`:
-   You may complete **at most 2 transactions**.
-   You must sell before you buy again.
-   Find the **maximum profit**.

```
Input:  prices = [3,3,5,0,0,3,1,4]
Output: 6   (buy at 0, sell at 3 = +3; buy at 1, sell at 4 = +3; total = 6)

Input:  prices = [1,2,3,4,5]
Output: 4   (buy at 1, sell at 5 = +4; one trade is enough)

Input:  prices = [7,6,4,3,1]
Output: 0   (no profitable trade exists)
```

---

## 🧠 Building the Intuition -- State Machine Thinking

At any point in time, the merchant is in one of these states:

**Transaction count:** how many COMPLETE buy-sell pairs has he done?
0, 1, or 2. Once he hits 2, he's done.

**Holding:** is he currently holding a stock?
Yes (bought but not yet sold) or No (free to buy).

We can encode this as a number. Think of the trading process
as a sequence of actions:

```
buy1 → sell1 → buy2 → sell2
```

That's 4 possible actions. We can number them 0, 1, 2, 3.
Even numbers (0, 2) = BUY actions.
Odd numbers (1, 3) = SELL actions.

When the transaction number reaches 4, all trades are done.

So the state is `(day, transactionNumber)` where:
-   `transactionNumber = 0`: haven't done anything yet, can buy (1st buy)
-   `transactionNumber = 1`: holding 1st stock, can sell (1st sell)
-   `transactionNumber = 2`: sold once, can buy again (2nd buy)
-   `transactionNumber = 3`: holding 2nd stock, can sell (2nd sell)
-   `transactionNumber = 4`: done, no more trades

---

## 🧠 The Recurrence

At each day with transaction number `txn`:

**If `txn` is EVEN (0 or 2) — it's a BUY opportunity:**

```
Choice 1: BUY today.  Pay prices[day]. Move to txn+1 (now holding).
           -prices[day] + solve(day+1, txn+1)

Choice 2: SKIP today. Don't buy. Stay at same txn.
           solve(day+1, txn)

Take the max.
```

**If `txn` is ODD (1 or 3) — it's a SELL opportunity:**

```
Choice 1: SELL today. Gain prices[day]. Move to txn+1 (trade complete).
           +prices[day] + solve(day+1, txn+1)

Choice 2: SKIP today. Don't sell. Stay at same txn.
           solve(day+1, txn)

Take the max.
```

> _"Even transaction numbers mean the merchant is free — he can buy.
> Odd transaction numbers mean he's holding — he can sell.
> At each day, he either acts or waits.
> The recursion explores both futures."_

**Base cases:**

```
If day == n: return 0          ← no more days, no more profit
If txn == 4: return 0          ← all 2 transactions used up
```

---

### 📜 The Scroll of the Merchant's Two Trades

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📈 Setting Up the Cache

```cpp
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    vector<vector<int>> memo(n, vector<int>(4, -1));
```

2D cache: `memo[day][txn]`.
4 transaction states (0 through 3). State 4 is the base case.

---

```cpp
    return solve(0, 0, prices, memo);
}
```

Start from day 0, transaction 0 (haven't done anything yet).

---

## 🔮 The Recursive Thinking

```cpp
int solve(int day, int txn, vector<int>& prices,
          vector<vector<int>>& memo) {
```

"Starting from `day` with transaction state `txn`,
what's the maximum profit I can earn?"

---

### 🛑 No more days

```cpp
    if (day == prices.size()) return 0;
```

Past the last day. No more trading possible. Profit = 0.

---

### 🛑 All transactions used

```cpp
    if (txn == 4) return 0;
```

Two complete buy-sell pairs done. Can't trade anymore.

Why 4? Because each transaction has two steps (buy + sell).
2 transactions × 2 steps = 4 total actions.

---

### 📖 Cache check

```cpp
    if (memo[day][txn] != -1) return memo[day][txn];
```

---

### 🔀 Even txn — BUY opportunity

```cpp
    if (txn % 2 == 0) {
        int buy  = -prices[day] + solve(day + 1, txn + 1, prices, memo);
```

**BUY today.** The merchant pays `prices[day]` (negative profit).
He moves to `txn + 1` (now holding a stock).
Tomorrow he'll be in a SELL state.

Why negative? Buying costs money. It reduces profit.
The profit comes later when he sells.

---

```cpp
        int skip = solve(day + 1, txn, prices, memo);
```

**SKIP today.** Don't buy. Stay in the same state.
Maybe tomorrow's price is cheaper.

---

```cpp
        memo[day][txn] = max(buy, skip);
    }
```

Whichever future is more profitable — buy or wait.

---

### 🔀 Odd txn — SELL opportunity

```cpp
    else {
        int sell = prices[day] + solve(day + 1, txn + 1, prices, memo);
```

**SELL today.** The merchant gains `prices[day]` (positive profit).
He moves to `txn + 1` (trade complete, free again).

---

```cpp
        int skip = solve(day + 1, txn, prices, memo);
```

**SKIP today.** Don't sell yet. Stay holding.
Maybe tomorrow's price is higher.

---

```cpp
        memo[day][txn] = max(sell, skip);
    }
```

---

```cpp
    return memo[day][txn];
}
```

---

### 🎺 The Trial of the Merchant's Two Trades

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

**Trace for prices = [3,3,5,0,0,3,1,4]:**

```
solve(0, 0): day 0, price 3, BUY opportunity
  buy:  -3 + solve(1, 1) = ?
  skip: solve(1, 0) = ?

  ... recursion explores all paths ...

Optimal path:
  Day 3: BUY at 0  (txn 0→1)
  Day 5: SELL at 3  (txn 1→2, profit +3)
  Day 6: BUY at 1  (txn 2→3)
  Day 7: SELL at 4  (txn 3→4, profit +3)
  Total: 3 + 3 = 6
```

**Answer: 6** ✓

---

**Trace for prices = [1,2,3,4,5]:**

```
Optimal: BUY at 1 (day 0), SELL at 5 (day 4). One trade. Profit = 4.
The second trade isn't needed — "at most 2" means 1 is fine.
```

**Answer: 4** ✓

---

**Trace for prices = [7,6,4,3,1]:**

```
Every buy leads to a loss. The merchant never buys.
Every solve returns skip → skip → ... → 0.
```

**Answer: 0** ✓

---

## 🔍 Why the Even/Odd Trick Works

Instead of tracking two separate variables (holding, txn_count),
we encode them into a single number:

```
txn=0: not holding, 0 trades done  → can BUY  (1st buy)
txn=1: holding,     0 trades done  → can SELL (1st sell)
txn=2: not holding, 1 trade done   → can BUY  (2nd buy)
txn=3: holding,     1 trade done   → can SELL (2nd sell)
txn=4: not holding, 2 trades done  → DONE
```

Even = free to buy. Odd = holding, free to sell.
This collapses a 3D state `(day, txn_count, holding)` into 2D `(day, txn)`.

---

## 🔍 Generalizing to K Transactions

This approach generalizes perfectly to **at most K transactions**:

```
txn ranges from 0 to 2*K.
Even txn = buy. Odd txn = sell.
Base case: txn == 2*K → done.
```

For K=2: txn goes 0 to 4 (this problem).
For K=1: txn goes 0 to 2 (Stock I with DP).
For K=∞: no txn limit (Stock II — greedy is better).

---

## 🔍 The Stock Problem Family

| Problem       | Constraint        | Approach                    |
| ------------- | ----------------- | --------------------------- |
| Stock I       | 1 trade           | Track min, one pass         |
| Stock II      | Unlimited trades  | Greedy — harvest every rise |
| **Stock III** | **At most 2**     | **DP with txn state**       |
| Stock IV      | At most K         | Same DP, generalized        |
| With Cooldown | Unlimited + 1 day | State machine DP            |
| With Fee      | Unlimited + fee   | State machine DP            |

---

### 🧠 Memory of the Merchant's Two Trades (Memoization) Law

-   **State:** `(day, txn)` where txn = 0,1,2,3 (even=buy, odd=sell)
-   **Even txn (buy):** `max(-prices[day] + solve(day+1, txn+1), solve(day+1, txn))`
-   **Odd txn (sell):** `max(+prices[day] + solve(day+1, txn+1), solve(day+1, txn))`
-   **Base:** `day == n → 0`, `txn == 4 → 0`
-   **Generalizes to K transactions:** txn goes 0 to 2K
-   **Time:** O(N × 4) = O(N). **Space:** O(N × 4) = O(N).

Thus is remembered the saga of **Best Time to Buy and Sell Stock III (Memoization)**,
where the merchant tracked his position in the trading sequence --
even states meant freedom to buy,
odd states meant holding and waiting to sell --
and at each day he chose to act or wait,
exploring both futures through recursion,
until the maximum profit from at most two trades
was found and remembered. 📈📈🧠✨
