## 💹🌊 _The Market of Endless Tides: The Best Time to Buy and Sell Stock II Saga_

> \_"Beyond the eastern gates lay the Market of Many Days —
> where prices rose with the morning sun
> and fell with the evening dusk.
>
> The Oracle had once been bound by a cruel law:
> **one trade, one lifetime.**
>
> But the King had changed the rules.
>
> He summoned her and declared:
>
> **'The old law is lifted.
> Trade as many times as you wish.
> Buy today, sell tomorrow.
> Sell today, buy again the next.
> But never hold two stocks at once —
> you must sell before you buy again.'**
>
> The Oracle stood before the price scroll —
> a long list of numbers, one per day.
>
> The question was no longer
> _'when is the single best moment?'_
>
> The question was now:
>
> **'How much total profit can be gathered
> from every rising wave of the market?'**
>
> She studied the scroll.
> She saw rises. She saw falls.
>
> And then — the insight struck her like lightning:
>
> **'I do not need to find the perfect valley and the perfect peak.
> I only need to harvest every single rise —
> no matter how small.
> Every day the price climbs,
> that climb belongs to me.'**
>
> She walked through the market,
> day by day,
> pocketing every upward step,
> ignoring every fall —
>
> and by the end,
> she had taken everything the market had to offer."\_

---

This is the saga of **Best Time to Buy and Sell Stock II**.

You are given an integer array `prices`,
where `prices[i]` is the price of a stock on day `i`.

Rules of the market:

-   You may complete **as many transactions as you like**
-   You must **sell before you buy again** (no holding two stocks simultaneously)
-   Goal: **maximize total profit**

```
Input:  prices = [7, 1, 5, 3, 6, 4]
Output: 7

Input:  prices = [1, 2, 3, 4, 5]
Output: 4

Input:  prices = [7, 6, 4, 3, 1]
Output: 0
```

---

## 🧠 The Oracle's Core Insight — Harvest Every Rise

This problem looks complex at first.
With unlimited trades, should we track every valley and every peak?
Should we simulate every possible buy-sell combination?

No.

The Oracle discovered a profound simplification:

> **Every upward step from one day to the next is profit you can take.**

Why does this work?

Consider prices `[1, 2, 3]`:

```
Option A: Buy day 0 (price 1), sell day 2 (price 3) → profit = 2
Option B: Buy day 0 (price 1), sell day 1 (price 2) → profit = 1
          Buy day 1 (price 2), sell day 2 (price 3) → profit = 1
          Total = 2
```

Both options yield the **same profit**.

Holding across multiple days is mathematically identical
to collecting each daily rise individually.

So instead of tracking peaks and valleys,
the Oracle simply walks through the array
and adds every positive difference `prices[i] - prices[i-1]`.

This is the **Greedy** approach:
take every gain available, skip every loss.

```
Time:  O(N) — one pass through the array
Space: O(1) — no extra data structures
```

---

### 📜 The Scroll of the Market

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔮 The Oracle's Greedy Trading Ritual

```cpp
int maxProfit(vector<int>& prices) {
    int profit = 0;
```

The Oracle began with empty coffers.

`profit` was the running total of all gains harvested.
It started at zero — no trades made, no money earned.

---

## 🧭 Walk Through the Market — Day by Day

```cpp
    for (int i = 1; i < prices.size(); i++) {
```

The Oracle began on **day 1** (index 1),
always comparing today's price against yesterday's.

Day 0 had no "yesterday" — no comparison possible, no trade to make.
The loop started at 1.

---

## 💰 Harvest Every Rise — Ignore Every Fall

```cpp
        if (prices[i] > prices[i - 1]) {
            profit += prices[i] - prices[i - 1];
        }
    }
```

Each day, the Oracle asked one question:

> **"Did the price rise from yesterday to today?"**

**If yes** — she bought yesterday, sold today.
The difference `prices[i] - prices[i-1]` was pocketed.

**If no** (price fell or stayed flat) —
she held no stock, made no trade, and waited.
No loss was ever taken.

The Oracle never bought into a falling market.
She only moved when the tide was rising.

---

## 🏁 Return the Total Harvest

```cpp
    return profit;
}
```

Every rising wave had been captured.
Every falling tide had been avoided.

The total profit was the sum of all upward steps —
the maximum possible from unlimited trades.

---

### 🎺 The Trial of the Endless Market

```cpp
int main() {
    vector<int> prices1 = {7, 1, 5, 3, 6, 4};
    cout << maxProfit(prices1) << endl; // expected: 7

    vector<int> prices2 = {1, 2, 3, 4, 5};
    cout << maxProfit(prices2) << endl; // expected: 4

    vector<int> prices3 = {7, 6, 4, 3, 1};
    cout << maxProfit(prices3) << endl; // expected: 0

    return 0;
}
```

---

**Trace for `[7, 1, 5, 3, 6, 4]`:**

| Day i | prices[i] | prices[i-1] | Rise? | Profit Added | Running Profit |
| ----- | --------- | ----------- | ----- | ------------ | -------------- |
| 1     | 1         | 7           | No    | 0            | 0              |
| 2     | 5         | 1           | Yes   | +4           | 4              |
| 3     | 3         | 5           | No    | 0            | 4              |
| 4     | 6         | 3           | Yes   | +3           | 7              |
| 5     | 4         | 6           | No    | 0            | 7              |

**Answer: 7** ✓

The Oracle captured:

-   Buy at 1 → sell at 5 → profit **4**
-   Buy at 3 → sell at 6 → profit **3**
-   Total: **7**

---

**Trace for `[1, 2, 3, 4, 5]`:**

| Day i | prices[i] | prices[i-1] | Rise? | Profit Added | Running Profit |
| ----- | --------- | ----------- | ----- | ------------ | -------------- |
| 1     | 2         | 1           | Yes   | +1           | 1              |
| 2     | 3         | 2           | Yes   | +1           | 2              |
| 3     | 4         | 3           | Yes   | +1           | 3              |
| 4     | 5         | 4           | Yes   | +1           | 4              |

**Answer: 4** ✓

A perfectly rising market — every single day was a gain.
The Oracle harvested every step of the climb.

---

**Trace for `[7, 6, 4, 3, 1]`:**

| Day i | prices[i] | prices[i-1] | Rise? | Profit Added | Running Profit |
| ----- | --------- | ----------- | ----- | ------------ | -------------- |
| 1     | 6         | 7           | No    | 0            | 0              |
| 2     | 4         | 6           | No    | 0            | 0              |
| 3     | 3         | 4           | No    | 0            | 0              |
| 4     | 1         | 3           | No    | 0            | 0              |

**Answer: 0** ✓

A market in pure decline — no rise ever came.
The Oracle never traded. She lost nothing.

---

## ⚔️ Why Not Track Peaks and Valleys Explicitly?

One might think the correct approach is:

```
Find every local valley → buy there
Find every local peak   → sell there
Sum all (peak - valley) pairs
```

This works — but it is unnecessary complexity.

The greedy daily-difference approach is provably equivalent:

```
Valley at index v, Peak at index p (v < p):
  peak - valley = (prices[v+1] - prices[v])
                + (prices[v+2] - prices[v+1])
                + ...
                + (prices[p]   - prices[p-1])
```

The profit from one valley-to-peak trade
equals the sum of all daily rises within that range.

The Oracle does not need to find valleys and peaks.
She just collects every positive daily step.
The math is identical.

---

### 🧠 Memory of the Market Law

-   **Unlimited trades** → greedy is optimal: take every rise
-   Walk from `i = 1` to `i = n-1`, compare `prices[i]` vs `prices[i-1]`
-   If `prices[i] > prices[i-1]` → add `prices[i] - prices[i-1]` to profit
-   If price fell or stayed flat → skip, no trade
-   **No need to track peaks and valleys** — daily differences are equivalent
-   **Time:** O(N) — single pass
-   **Space:** O(1) — only one variable `profit`
-   **Edge case:** strictly decreasing prices → profit = 0, never trade

Thus is remembered the saga of **Best Time to Buy and Sell Stock II**,
where the Oracle abandoned the search for the perfect single trade
and instead walked the market day by day —
pocketing every upward step,
ignoring every fall —
harvesting the full bounty of an unlimited market
in a single O(N) pass,
with nothing but a running sum and a greedy eye
for every rising tide. 💹🌊✨
