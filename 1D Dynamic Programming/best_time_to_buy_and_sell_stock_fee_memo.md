## 📈💸🧠 _The Merchant's Tax (Memoization): The Best Time to Buy and Sell Stock with Transaction Fee Saga_

> \_"The merchant had mastered unlimited trades.
> He had mastered cooldown.
>
> Now the market imposed a different cost:
>
> **'Every time you complete a transaction,
> you must pay a fee.
> Buy and sell — that's one transaction, one fee.
> The fee is deducted from your profit.'**
>
> With no fee, the greedy approach worked —
> harvest every tiny rise.
> But with a fee, small rises might NOT be worth trading.
>
> A rise of +2 with a fee of 3? That's a LOSS of 1.
> Better to skip it and wait for a bigger wave.
>
> The merchant needed DP again.
> But this time, only TWO states —
> no cooldown, no transaction limit.
> Just free and holding.
> The fee was simply subtracted when selling."\_

---

This is the saga of **Best Time to Buy and Sell Stock with Transaction Fee (LeetCode 714)**.

Given an array `prices` and an integer `fee`:
-   You may complete **as many transactions as you like**.
-   Each transaction (buy + sell) costs `fee`.
-   Find the **maximum profit**.

```
Input:  prices = [1,3,2,8,4,9], fee = 2
Output: 8   (buy at 1, sell at 8 = +7-2=5; buy at 4, sell at 9 = +5-2=3; total=8)

Input:  prices = [1,3,7,5,10,3], fee = 3
Output: 6   (buy at 1, sell at 7 = +6-3=3; buy at 5, sell at 10 = +5-3=2... 
             actually: buy at 1, sell at 10 = +9-3=6. One trade is better.)
```

---

## 🧠 Building the Intuition -- Two States, One Fee

This is the simplest stock DP after Stock II.
Only two states:

**State 0 — FREE:** not holding. Can BUY or SKIP.
**State 1 — HOLDING:** holding a stock. Can SELL or SKIP.

No cooldown. No transaction limit. Just a fee on each sell.

The fee can be applied at either the buy or the sell.
Convention: **subtract the fee when selling.**
(Subtracting at buy works too — same result.)

---

## 🧠 The Recurrence

```
solve(day, state):
  If day == n: return 0

  If state == 0 (FREE):
    buy  = -prices[day] + solve(day+1, 1)
    skip = solve(day+1, 0)
    return max(buy, skip)

  If state == 1 (HOLDING):
    sell = prices[day] - fee + solve(day+1, 0)    ← fee deducted here
    skip = solve(day+1, 1)
    return max(sell, skip)
```

The ONLY difference from Stock II:
`prices[day] - fee` instead of `prices[day]` when selling.

That's it. One subtraction. The entire problem.

> _"The fee makes small trades unprofitable.
> The merchant will only sell when the gain exceeds the fee.
> The DP naturally handles this — if selling gives less profit
> than holding, the merchant holds."_

---

## 🧠 Why Greedy Fails with Fees

Prices = [1, 3, 2, 8], fee = 2.

Greedy (harvest every rise):
-   Day 0→1: +2, minus fee 2 = 0. Not worth it.
-   Day 2→3: +6, minus fee 2 = 4. Worth it.
-   Total: 4.

Optimal:
-   Buy at 1, sell at 8: +7, minus fee 2 = 5.
-   Total: 5.

Greedy splits into small trades and pays the fee multiple times.
The optimal holds through the dip and pays the fee only once.

---

### 📜 The Scroll of the Merchant's Tax

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 💸 Setting Up the Cache

```cpp
int maxProfit(vector<int>& prices, int fee) {
    int n = prices.size();
    vector<vector<int>> memo(n, vector<int>(2, -1));
```

`n × 2` cache. Two states: FREE (0), HOLDING (1).

---

```cpp
    return solve(0, 0, prices, fee, memo);
}
```

Start from day 0, state FREE.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int day, int state, vector<int>& prices, int fee,
          vector<vector<int>>& memo) {
```

---

### 🛑 No more days

```cpp
    if (day == prices.size()) return 0;
```

---

### 📖 Cache check

```cpp
    if (memo[day][state] != -1) return memo[day][state];
```

---

### 🟢 State 0 — FREE: buy or skip

```cpp
    if (state == 0) {
        int buy  = -prices[day] + solve(day + 1, 1, prices, fee, memo);
```

**BUY today.** Pay `prices[day]`. Tomorrow HOLDING.

No fee on buying. The fee is paid when the transaction completes (at sell).

---

```cpp
        int skip = solve(day + 1, 0, prices, fee, memo);
```

**SKIP.** Stay free. Wait for a better entry.

---

```cpp
        memo[day][state] = max(buy, skip);
    }
```

---

### 🔴 State 1 — HOLDING: sell or skip

```cpp
    else {
        int sell = prices[day] - fee + solve(day + 1, 0, prices, fee, memo);
```

**SELL today.** Gain `prices[day]` but pay `fee`.
Net gain = `prices[day] - fee`.
Tomorrow FREE — can buy again immediately (no cooldown).

This is the key line. The fee makes the merchant think twice.
If `prices[day] - fee` is small, holding might be better.

> _"The merchant imagines selling today.
> But the taxman takes his cut.
> Is the net gain worth it?
> Or should he hold and wait for a bigger wave?"_

---

```cpp
        int skip = solve(day + 1, 1, prices, fee, memo);
```

**SKIP.** Keep holding. Maybe tomorrow's price minus fee is better.

---

```cpp
        memo[day][state] = max(sell, skip);
    }
    return memo[day][state];
}
```

---

### 🎺 The Trial of the Merchant's Tax

```cpp
int main() {
    vector<int> p1 = {1, 3, 2, 8, 4, 9};
    cout << maxProfit(p1, 2) << endl; // expected: 8

    vector<int> p2 = {1, 3, 7, 5, 10, 3};
    cout << maxProfit(p2, 3) << endl; // expected: 6

    vector<int> p3 = {1, 4, 2};
    cout << maxProfit(p3, 1) << endl; // expected: 2

    return 0;
}
```

---

**Trace for prices = [1,3,2,8,4,9], fee = 2:**

```
solve(0, FREE): price 1
  buy: -1 + solve(1, HOLD)
    solve(1, HOLD): price 3
      sell: 3-2 + solve(2, FREE) = 1 + solve(2, FREE)
      skip: solve(2, HOLD)
      ... recursion explores both ...

Optimal path:
  Day 0: BUY at 1
  Day 3: SELL at 8, pay fee 2. Net: +5. Total: 5.
  Day 4: BUY at 4
  Day 5: SELL at 9, pay fee 2. Net: +3. Total: 8.
```

**Answer: 8** ✓

Why not sell at 3 (day 1) and rebuy at 2 (day 2)?
Sell at 3: gain 3-2=1 (after fee). Buy at 2. Sell at 8: gain 8-2=6. Total: 7.
Vs hold through: sell at 8: gain 8-1-2=5. Then buy 4, sell 9: 9-4-2=3. Total: 8.
Holding through the dip saves one fee payment.

---

**Trace for prices = [1,3,7,5,10,3], fee = 3:**

```
Optimal: buy at 1, sell at 10. Profit = 10-1-3 = 6.
One trade. The fee makes splitting into two trades worse:
  Trade 1: buy 1, sell 7 = 6-3 = 3.
  Trade 2: buy 5, sell 10 = 5-3 = 2.
  Total: 5. Worse than 6.
```

**Answer: 6** ✓

---

## 🔍 The Fee's Effect -- When to Split vs Hold

Without fee: always split (harvest every rise). Stock II greedy.
With fee: splitting costs an extra fee each time.

**Split when:** the dip is deep enough that rebuying saves more
than the fee costs.

**Hold when:** the dip is shallow. Paying the fee to exit and re-enter
costs more than just riding through the dip.

The DP handles this automatically. No manual threshold needed.

---

## 🔍 Where to Apply the Fee -- Buy vs Sell

Both are equivalent:

```
Fee on sell: sell = prices[day] - fee + solve(FREE)
Fee on buy:  buy  = -(prices[day] + fee) + solve(HOLD)
```

The total fee per transaction is the same either way.
Convention: apply at sell (more intuitive — you pay when you cash out).

---

## 🔍 The Complete Stock Family

| Problem          | Constraint         | States | Key difference        |
| ---------------- | ------------------ | ------ | --------------------- |
| Stock I          | 1 trade            | —      | Track min             |
| Stock II         | Unlimited          | 2      | Greedy works          |
| Stock III        | At most 2          | 4 txn  | Even/odd encoding     |
| Stock IV         | At most K          | 2K txn | Generalized III       |
| With Cooldown    | Unlimited + 1 day  | 3      | COOL state after sell |
| **With Fee**     | **Unlimited + fee**| **2**  | **-fee when selling** |

Stock with Fee is the simplest DP variant — same as Stock II
but with `-fee` on the sell line. Two states, no extra complexity.

---

### 🧠 Memory of the Merchant's Tax (Memoization) Law

-   **Two states:** FREE (0), HOLDING (1)
-   **FREE:** `max(-price + solve(HOLD), solve(FREE))`
-   **HOLDING:** `max(price - fee + solve(FREE), solve(HOLD))`
-   **The key:** `-fee` when selling. That's the only change from Stock II.
-   **Fee makes small trades unprofitable** — DP naturally skips them
-   **Base:** `day == n → 0`
-   **Time:** O(N × 2) = O(N). **Space:** O(N × 2) = O(N).

Thus is remembered the saga of **Best Time to Buy and Sell Stock with Fee (Memoization)**,
where the merchant faced the taxman at every sale --
small rises that once were free profit
now cost more than they earned --
and the DP learned to hold through shallow dips,
paying the fee only when the wave was big enough
to make the trade worthwhile. 📈💸🧠✨
