## 💹⚔️ _The Market of Endless Trades: The Best Time to Buy and Sell Stock II Saga_

> \*"Beyond the eastern gates lay the Market of Many Days —
> prices rose with the sun
> and fell with the dusk.
>
> The Oracle was no longer bound by the rule of a single trade.
> She was told:
>
> **‘Trade as many times as you wish.
> Buy today, sell tomorrow,
> sell today, buy again —
> but never hold more than one stock at a time.’**
>
> The question was no longer
> _when is the single best moment_,
> but rather:
>
> **‘How much total profit can be gathered
> from every rising wave of the market?’**
>
> And so the Oracle learned to harvest
> **every ascent**,
> ignoring the valleys,
> and claiming profit wherever the price climbed."\*

---

This is the saga of **Best Time to Buy and Sell Stock II**.

You are given an array `prices`,
where `prices[i]` is the stock price on day `i`.

Rules of the market:

-   You may complete **as many transactions as you like**
-   You must **sell before you buy again**
-   Goal: **maximize total profit**

---

## 🌊 The Oracle’s Insight: Capture Every Rise

Unlike the first stock saga,
there is no need to search for a single perfect trade.

The Oracle discovered a simple truth:

> **Every time the price rises from one day to the next,
> that increase can be taken as profit.**

Why?

Because:

-   Buying at the start of a rise
-   Selling at the end of that rise
    is equivalent to summing **all daily increases** in between.

Thus, profit becomes the sum of all positive differences.

---

### 📜 The Scroll of Market Days

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔮 The Oracle’s Greedy Trading Ritual

_Harvest every upward step_

```cpp
int maxProfit(vector<int>& prices) {
    int profit = 0;
```

The Oracle began with empty coffers.

---

### 🧭 Walk Through the Market Day by Day

```cpp
    for (int i = 1; i < prices.size(); i++) {
```

Each day was compared with the day before.

---

### 💰 Take Profit from Every Rise

```cpp
        if (prices[i] > prices[i - 1]) {
            profit += prices[i] - prices[i - 1];
        }
    }
```

Whenever today’s price exceeded yesterday’s:

-   Buy yesterday
-   Sell today
-   Add the difference to total profit

If the price fell or stayed flat,
the Oracle waited patiently.

---

### 🏁 Return the Total Harvest

```cpp
    return profit;
}
```

All rising waves had been captured.

---

### 🎺 The Trial of the Endless Market

```cpp
int main() {
    vector<int> prices = {7, 1, 5, 3, 6, 4};
    cout << maxProfit(prices) << endl; // expected: 7
    return 0;
}
```

The Oracle claimed:

-   Buy at 1 → sell at 5 → profit 4
-   Buy at 3 → sell at 6 → profit 3

Total profit = **7**

She never missed a rise.

---

### 🧠 Memory of the Market Law

-   Unlimited transactions allowed
-   Never hold more than one stock
-   Add **every positive price difference**
-   Equivalent to buying at every valley
    and selling at every peak
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Best Time to Buy and Sell Stock II**,
where the Oracle does not chase the perfect moment,
but calmly harvests every upward motion of the market —
turning many small gains
into a great fortune. 💹✨
