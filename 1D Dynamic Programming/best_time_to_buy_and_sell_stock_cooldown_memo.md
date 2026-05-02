## 📈❄️🧠 _The Merchant's Cooldown (Memoization): The Best Time to Buy and Sell Stock with Cooldown Saga_

> \_"The merchant had mastered unlimited trades.
>
> But the market imposed a new rule:
>
> **'After you sell, you must wait one day
> before you can buy again.
> The day after a sell is a COOLDOWN day.
> You cannot buy during cooldown.'**
>
> The merchant could no longer chain trades back-to-back.
> Sell on Monday, buy on Tuesday? Forbidden.
> Sell on Monday, cooldown Tuesday, buy on Wednesday? Allowed.
>
> This changed everything.
>
> With unlimited trades and no cooldown,
> the greedy approach worked — harvest every rise.
> But cooldown meant skipping a rise could be BETTER
> if it set up a bigger trade later.
>
> The merchant needed to track his STATE:
>
> **Am I free to buy? Am I holding? Am I in cooldown?**
>
> Three states. That meant — Dynamic Programming."\_

---

This is the saga of **Best Time to Buy and Sell Stock with Cooldown (LeetCode 309)**.

Given an array `prices`:
-   You may complete **as many transactions as you like**.
-   After selling, you must wait **one day** before buying again.
-   Find the **maximum profit**.

```
Input:  prices = [1,2,3,0,2]
Output: 3   (buy at 1, sell at 3, cooldown, buy at 0, sell at 2)

Input:  prices = [1]
Output: 0
```

---

## 🧠 Building the Intuition -- Three States

At any day, the merchant is in one of three states:

**State 0 — FREE:** not holding, not in cooldown. Can BUY or SKIP.
**State 1 — HOLDING:** bought a stock, waiting to sell. Can SELL or SKIP.
**State 2 — COOLDOWN:** just sold yesterday. Must wait. Can only SKIP.

The transitions:

```
FREE (0):
  BUY  → pay prices[day], move to HOLDING (1)
  SKIP → stay FREE (0)

HOLDING (1):
  SELL → gain prices[day], move to COOLDOWN (2)
  SKIP → stay HOLDING (1)

COOLDOWN (2):
  SKIP → move to FREE (0)    ← cooldown ends, free again
  (no other choice — must wait)
```

> _"The cooldown state is the key difference from Stock II.
> After selling, the merchant is FORCED into cooldown.
> He can't buy the next day. He must wait one day,
> then he's free again."_

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
    sell = prices[day] + solve(day+1, 2)    ← sell → cooldown
    skip = solve(day+1, 1)
    return max(sell, skip)

  If state == 2 (COOLDOWN):
    return solve(day+1, 0)                  ← forced wait, then free
```

The critical line: selling leads to state 2 (cooldown), NOT state 0 (free).
In Stock II without cooldown, selling leads directly back to free.
That one difference — `solve(day+1, 2)` instead of `solve(day+1, 0)` —
is the entire problem.

---

### 📜 The Scroll of the Merchant's Cooldown

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ❄️ Setting Up the Cache

```cpp
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    vector<vector<int>> memo(n, vector<int>(3, -1));
```

`n × 3` cache. Three states: FREE (0), HOLDING (1), COOLDOWN (2).

---

```cpp
    return solve(0, 0, prices, memo);
}
```

Start from day 0, state FREE.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int day, int state, vector<int>& prices,
          vector<vector<int>>& memo) {
```

"Starting from `day` in `state`, what's the max profit?"

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

### 🟢 State 0 — FREE: can buy or skip

```cpp
    if (state == 0) {
        int buy  = -prices[day] + solve(day + 1, 1, prices, memo);
```

**BUY today.** Pay `prices[day]`. Tomorrow the merchant is HOLDING.

> _"The merchant is free. He sees today's price and decides:
> 'Should I buy now, or wait for a cheaper day?'"_

---

```cpp
        int skip = solve(day + 1, 0, prices, memo);
```

**SKIP today.** Stay free. Maybe tomorrow is better.

---

```cpp
        memo[day][state] = max(buy, skip);
    }
```

---

### 🔴 State 1 — HOLDING: can sell or skip

```cpp
    else if (state == 1) {
        int sell = prices[day] + solve(day + 1, 2, prices, memo);
```

**SELL today.** Gain `prices[day]`. Tomorrow is COOLDOWN.

This is the key line. After selling, the next state is 2 (cooldown),
NOT 0 (free). The merchant CANNOT buy tomorrow.
He must wait one day.

> _"The merchant sells and pockets the profit.
> But the market enforces its rule —
> tomorrow is a rest day. No buying allowed."_

---

```cpp
        int skip = solve(day + 1, 1, prices, memo);
```

**SKIP today.** Keep holding. Maybe tomorrow's price is higher.

---

```cpp
        memo[day][state] = max(sell, skip);
    }
```

---

### ❄️ State 2 — COOLDOWN: forced wait

```cpp
    else {
        memo[day][state] = solve(day + 1, 0, prices, memo);
    }
```

No choice. The merchant must wait.
Tomorrow he's FREE again.

There's no `max` here — only one option exists.
The cooldown day is wasted. That's the cost of selling.

> _"The merchant sits idle. The market moves without him.
> Tomorrow, the chains lift. He'll be free again."_

---

```cpp
    return memo[day][state];
}
```

---

### 🎺 The Trial of the Merchant's Cooldown

```cpp
int main() {
    vector<int> p1 = {1, 2, 3, 0, 2};
    cout << maxProfit(p1) << endl; // expected: 3

    vector<int> p2 = {1};
    cout << maxProfit(p2) << endl; // expected: 0

    vector<int> p3 = {1, 2, 4};
    cout << maxProfit(p3) << endl; // expected: 3

    vector<int> p4 = {2, 1, 4, 5, 2, 9, 7};
    cout << maxProfit(p4) << endl; // expected: 11

    return 0;
}
```

---

**Trace for prices = [1,2,3,0,2]:**

```
solve(0, FREE): price 1
  buy: -1 + solve(1, HOLD)
    solve(1, HOLD): price 2
      sell: 2 + solve(2, COOL)
        solve(2, COOL): → solve(3, FREE)
          solve(3, FREE): price 0
            buy: 0 + solve(4, HOLD)
              solve(4, HOLD): price 2
                sell: 2 + solve(5, COOL) = 2+0 = 2
                skip: solve(5, HOLD) = 0
              → 2
            buy: 0 + 2 = 2... wait, buy costs: -0 + 2 = 2
            skip: solve(4, FREE)
              solve(4, FREE): price 2
                buy: -2 + solve(5, HOLD) = -2+0 = -2
                skip: solve(5, FREE) = 0
              → 0
          solve(3, FREE) = max(2, 0) = 2
        solve(2, COOL) = 2
      sell: 2 + 2 = 4
      skip: solve(2, HOLD) → ...
    solve(1, HOLD) = 4 (sell wins)
  buy: -1 + 4 = 3
  skip: solve(1, FREE) → ...
solve(0, FREE) = 3
```

**Answer: 3** ✓

Optimal: buy at 1, sell at 3 (+2), cooldown day 2, buy at 0, sell at 2 (+2).
Wait — that's +2+2 = 4? Let me recheck...

Actually: buy at 1 (day 0), sell at 2 (day 1) = +1, cooldown (day 2),
buy at 0 (day 3), sell at 2 (day 4) = +2. Total = 3. ✓

Or: buy at 1 (day 0), sell at 3 (day 2) = +2, cooldown (day 3),
can't sell at day 4 because we'd need to buy first. Buy at 0 (day 3)?
No — day 3 is cooldown. Buy at day 4? But day 4 is the last day.
So: sell at 3 gives +2, cooldown day 3, buy day 4 at 2 but can't sell. Total = 2.

The DP finds: buy day 0, sell day 1 (+1), cool day 2, buy day 3, sell day 4 (+2) = 3. ✓

---

## 🔍 Cooldown vs No Cooldown -- The One Difference

| Aspect          | Stock II (no cooldown)     | With Cooldown              |
| --------------- | -------------------------- | -------------------------- |
| After selling   | Immediately FREE           | Enter COOLDOWN for 1 day   |
| States needed   | 2 (free, holding)          | 3 (free, holding, cooldown)|
| Greedy works?   | Yes                        | No — must use DP           |
| Sell transition | `solve(day+1, FREE)`       | `solve(day+1, COOLDOWN)`   |

One transition change. One extra state. That's the entire difference.

---

## 🔍 Why Greedy Fails with Cooldown

Prices = [1, 2, 3, 0, 2].

Greedy (harvest every rise): +1 (1→2) + +1 (2→3) + +2 (0→2) = 4?
But selling on day 1 means cooldown on day 2.
Selling on day 2 means cooldown on day 3.
You can't sell on BOTH day 1 and day 2 — you'd need to buy on day 2
but you're in cooldown from selling on day 1.

Greedy doesn't account for the cooldown constraint.
DP must explore which sells to skip to avoid wasting cooldown days.

---

### 🧠 Memory of the Merchant's Cooldown (Memoization) Law

-   **Three states:** FREE (0), HOLDING (1), COOLDOWN (2)
-   **FREE:** `max(-price + solve(HOLD), solve(FREE))`
-   **HOLDING:** `max(+price + solve(COOL), solve(HOLD))`
-   **COOLDOWN:** `solve(FREE)` — forced wait, no choice
-   **The key:** selling → COOLDOWN, not FREE
-   **Base:** `day == n → 0`
-   **Time:** O(N × 3) = O(N). **Space:** O(N × 3) = O(N).

Thus is remembered the saga of **Best Time to Buy and Sell Stock with Cooldown (Memoization)**,
where the merchant tracked three states --
free to act, holding and waiting, or frozen in cooldown --
and at each day chose to buy, sell, or wait,
knowing that every sell came with a price:
one day of forced rest
before the market would let him trade again. 📈❄️🧠✨
