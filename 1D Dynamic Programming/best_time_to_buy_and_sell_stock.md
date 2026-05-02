## 📈💰🧠 _The Merchant's One Trade: The Best Time to Buy and Sell Stock Saga_

> \_"A merchant traveled through time,
> witnessing the price of a stock each day.
>
> He was allowed exactly ONE trade:
> buy on one day, sell on a LATER day.
>
> The Oracle was commanded:
>
> **'What is the maximum profit from one buy and one sell?
> If no profit is possible, return 0.'**
>
> The merchant could not sell before he bought.
> He could not go back in time.
> He had to buy FIRST, then sell LATER.
>
> The Oracle realized:
>
> **At each day, the best sell price is TODAY's price.
> The best buy price is the CHEAPEST price seen SO FAR.
> The best profit at each day = today - cheapest so far.
> The answer = the maximum of all daily profits.**
>
> One pass. Two variables. That's all."\_

---

This is the saga of **Best Time to Buy and Sell Stock (LeetCode 121)**.

Given an array `prices` where `prices[i]` is the stock price on day `i`:
-   Find the maximum profit from one buy and one sell.
-   You must buy before you sell.
-   If no profit is possible, return 0.

```
Input:  prices = [7, 1, 5, 3, 6, 4]
Output: 5   (buy at 1, sell at 6)

Input:  prices = [7, 6, 4, 3, 1]
Output: 0   (prices only decrease, no profit possible)

Input:  prices = [2, 4, 1]
Output: 2   (buy at 2, sell at 4)
```

---

## 🧠 Building the Intuition -- What Makes This Problem Tick?

The brute force is obvious: try every pair (buy day, sell day)
where sell > buy. That's O(N²). Can we do better?

Think about what you need to maximize profit on day `i`:

**You want to sell at the HIGHEST price.**
But you can only sell today or earlier -- you can't see the future.

**You want to have bought at the LOWEST price BEFORE today.**
The cheapest price from day 0 to day i-1.

So at each day, the best possible profit is:

```
profit_today = prices[i] - min_price_so_far
```

If we track `min_price_so_far` as we walk through the array,
we can compute the best profit at each day in O(1).

The answer = the maximum `profit_today` across all days.

> _"The merchant doesn't need to see the future.
> He just needs to remember the cheapest price he's seen.
> Every day, he asks: 'If I sell today,
> what's the best I could have done?'
> The answer is always: today's price minus the cheapest so far."_

---

## 🧠 Why This Works -- The Key Insight

For any optimal pair (buy_day, sell_day):
-   `buy_day` must be the minimum price in `prices[0..sell_day-1]`.
-   `sell_day` is some day after `buy_day`.

By tracking `min_price_so_far`, we guarantee that at every day,
we're considering the BEST possible buy price for that sell day.

We don't need to know WHICH day was the minimum.
We just need its value.

---

### 📜 The Scroll of the Merchant's One Trade

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📈 The Single-Pass Solution

### Initialize the Trackers

```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = prices[0];
    int maxProfit = 0;
```

`minPrice` starts at the first day's price.
On day 0, the cheapest price seen so far IS day 0.

`maxProfit` starts at 0.
If we never find a profitable trade, 0 is the correct answer
(the problem says return 0 if no profit is possible).

> _"The merchant begins his journey.
> The first price he sees becomes his benchmark --
> the cheapest so far. His best profit so far is nothing."_

---

### Walk Through Each Day

```cpp
    for (int i = 1; i < prices.size(); i++) {
```

Start from day 1. Day 0 is already captured in `minPrice`.
We can't sell on day 0 (no day before it to buy).

---

### Update the Cheapest Price Seen

```cpp
        minPrice = min(minPrice, prices[i]);
```

Before computing today's profit, check if today's price
is cheaper than anything we've seen before.

Why update BEFORE computing profit? Because `minPrice`
represents the best buy price available up to and including today.
But wait -- if today IS the new minimum, then selling today
would give profit = 0 (buy and sell same day).
That's fine -- `maxProfit` won't decrease.

Actually, the order doesn't matter here. Whether you update
`minPrice` first or compute profit first, the result is the same.
If today is the new minimum, `prices[i] - minPrice = 0`,
which won't beat any existing `maxProfit > 0`.

> _"The merchant checks: 'Is today's price the cheapest I've ever seen?'
> If so, he updates his memory. This might be the best day to buy."_

---

### Compute Today's Best Profit

```cpp
        int profitToday = prices[i] - minPrice;
```

If the merchant sells today, his profit is today's price
minus the cheapest price he's seen so far.

This is the BEST possible profit with today as the sell day.
No earlier buy price could be better than `minPrice`.

> _"The merchant imagines selling today.
> His profit would be today's price minus the cheapest he remembers.
> That's the best he could do if today were his last chance."_

---

### Update the Best Profit Overall

```cpp
        maxProfit = max(maxProfit, profitToday);
```

If today's profit beats the best we've seen, update.

> _"Is this the most profitable day so far?
> If so, remember it. The journey continues."_

---

```cpp
    }
    return maxProfit;
}
```

After walking through all days, `maxProfit` holds the answer.

---

### 🎺 The Trial of the Merchant's One Trade

```cpp
int main() {
    vector<int> p1 = {7, 1, 5, 3, 6, 4};
    cout << maxProfit(p1) << endl; // expected: 5

    vector<int> p2 = {7, 6, 4, 3, 1};
    cout << maxProfit(p2) << endl; // expected: 0

    vector<int> p3 = {2, 4, 1};
    cout << maxProfit(p3) << endl; // expected: 2

    vector<int> p4 = {1};
    cout << maxProfit(p4) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for prices = [7, 1, 5, 3, 6, 4]:**

| Day | Price | minPrice | profitToday | maxProfit | Thinking |
| --- | ----- | -------- | ----------- | --------- | -------- |
| 0   | 7     | 7        | —           | 0         | Starting point |
| 1   | 1     | 1        | 0           | 0         | New cheapest! Buy here. Sell today = 0. |
| 2   | 5     | 1        | 4           | 4         | Sell at 5, bought at 1 = profit 4. Best so far! |
| 3   | 3     | 1        | 2           | 4         | Sell at 3, bought at 1 = profit 2. Not better. |
| 4   | 6     | 1        | 5           | **5**     | Sell at 6, bought at 1 = profit 5. New best! |
| 5   | 4     | 1        | 3           | 5         | Sell at 4, bought at 1 = profit 3. Not better. |

**Answer: 5** ✓ (buy at price 1 on day 1, sell at price 6 on day 4)

---

**Trace for prices = [7, 6, 4, 3, 1]:**

| Day | Price | minPrice | profitToday | maxProfit | Thinking |
| --- | ----- | -------- | ----------- | --------- | -------- |
| 0   | 7     | 7        | —           | 0         | Starting point |
| 1   | 6     | 6        | 0           | 0         | New cheapest. Profit = 0. |
| 2   | 4     | 4        | 0           | 0         | New cheapest. Profit = 0. |
| 3   | 3     | 3        | 0           | 0         | New cheapest. Profit = 0. |
| 4   | 1     | 1        | 0           | 0         | New cheapest. Profit = 0. |

**Answer: 0** ✓ (prices only decrease — every day is a new minimum,
profit is always 0, no trade is profitable)

---

**Trace for prices = [2, 4, 1]:**

| Day | Price | minPrice | profitToday | maxProfit |
| --- | ----- | -------- | ----------- | --------- |
| 0   | 2     | 2        | —           | 0         |
| 1   | 4     | 2        | 2           | **2**     |
| 2   | 1     | 1        | 0           | 2         |

**Answer: 2** ✓ (buy at 2, sell at 4. Day 2 is cheaper but too late.)

Notice: the cheapest price (1) comes AFTER the best sell (4).
The merchant can't go back in time. The best trade was 2→4.

---

## 🔍 Why Not DP with a Full Array?

You COULD create a dp array:

```
dp[i] = max profit considering days 0 to i
dp[i] = max(dp[i-1], prices[i] - minPrice)
```

But `dp[i]` only depends on `dp[i-1]` and `minPrice`.
Both are single variables. The array is unnecessary.

The two-variable solution IS the space-optimized DP.
We just skipped the array entirely because it was never needed.

---

## 🔍 Connection to Other Stock Problems

| Problem                    | Trades allowed | Approach              |
| -------------------------- | -------------- | --------------------- |
| **Buy & Sell I (this)**    | 1 trade        | Track min, one pass   |
| Buy & Sell II              | Unlimited      | Sum all upward moves  |
| Buy & Sell III             | At most 2      | State machine DP      |
| Buy & Sell IV              | At most k      | Generalized state DP  |
| Buy & Sell with Cooldown   | Unlimited + CD | State machine DP      |
| Buy & Sell with Fee        | Unlimited + fee| State machine DP      |

This problem is the simplest. One trade. One pass.
The harder variants build on this foundation.

---

## 🔍 Common Mistakes

**Mistake 1: Selling before buying.**
Iterating backward or not enforcing the buy-before-sell order.
Our approach naturally handles this -- `minPrice` only contains
prices from BEFORE the current day.

**Mistake 2: Returning negative profit.**
If prices only decrease, the answer is 0 (don't trade at all).
Our `maxProfit` starts at 0, so it never goes negative.

**Mistake 3: Using max price instead of tracking min.**
You might think "find the max and min" -- but the max must come
AFTER the min. Global max and min might be in the wrong order.
Tracking `minPrice` as we go ensures the order is correct.

---

### 🧠 Memory of the Merchant's One Trade Law

-   **Track two variables:** `minPrice` (cheapest seen so far), `maxProfit` (best profit so far)
-   **At each day:** `profitToday = prices[i] - minPrice`
-   **Update:** `minPrice = min(minPrice, prices[i])`, `maxProfit = max(maxProfit, profitToday)`
-   **Why it works:** at every day, we consider the best possible buy price for that sell day
-   **No profit possible?** `maxProfit` stays at 0 (initialized value)
-   **Time:** O(N) — single pass. **Space:** O(1) — two variables.
-   **This is the foundation** for all harder stock problems.

Thus is remembered the saga of **Best Time to Buy and Sell Stock**,
where the merchant walked through time with two memories --
the cheapest price he'd ever seen
and the best profit he'd ever imagined --
updating both at every step,
never looking back, never seeing the future,
yet always knowing the optimal trade
by the time his journey ended. 📈💰🧠✨
