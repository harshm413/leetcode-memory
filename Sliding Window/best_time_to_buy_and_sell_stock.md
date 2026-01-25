## 💰⚔️ _The Single Trade of Destiny: The Best Time to Buy and Sell Stock Saga_

> \*"In the Market of Passing Days,
> prices rose and fell like tides.
>
> The Oracle was given a strict decree:
>
> \*\*‘You may buy once.
> You may sell once.
>
> Choose wisely,
> for one poor choice ruins all profit.’\*\*
>
> The Oracle understood this was not a tale
> of many trades and endless gains,
> but of **patience** and **perfect timing** —
>
> to buy at the deepest valley
> and sell at the highest peak that follows."\*

---

This is the saga of **Best Time to Buy and Sell Stock** (Single Transaction).

You are given an array `prices`,
where `prices[i]` is the stock price on day `i`.

Your task:

-   Choose **one day to buy**
-   Choose **a later day to sell**
-   Maximize profit
-   If no profit is possible, return `0`

---

## 🧠 The Oracle’s Core Insight — Valley Before Peak

The Oracle discovered a timeless rule:

> **For every day,
> the best profit is made by selling today
> after buying at the lowest price seen so far.**

So she carried with her:

-   the **lowest price so far**
-   the **best profit so far**

No future knowledge required.
Just a single, disciplined walk through time.

---

### 📜 The Scroll of Market Days

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔮 The Oracle’s One-Pass Trading Ritual

_Track the valley, test the peak_

```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = prices[0];
    int maxProfit = 0;
```

The Oracle began by assuming
the first day as the earliest valley.

---

### 🧭 Walk the Market Day by Day

```cpp
    for (int i = 1; i < prices.size(); i++) {
```

Each day revealed a new price.

---

### 🏞️ Update the Deepest Valley

```cpp
        minPrice = min(minPrice, prices[i]);
```

If a new lower price appeared,
the Oracle marked it as a better buying opportunity.

---

### 🏔️ Test the Profit of Selling Today

```cpp
        maxProfit = max(maxProfit, prices[i] - minPrice);
    }
```

Selling today after buying at the best valley
might yield a new maximum profit.

The Oracle recorded the best outcome.

---

### 🏁 Return the Best Possible Gain

```cpp
    return maxProfit;
}
```

If no rise ever followed a valley,
profit remained zero.

---

### 🎺 The Trial of the Market

```cpp
int main() {
    vector<int> prices = {7, 1, 5, 3, 6, 4};
    cout << maxProfit(prices) << endl; // expected: 5
    return 0;
}
```

The Oracle chose:

-   Buy at **1**
-   Sell at **6**

Profit = **5**

One trade.
Perfect timing.

---

### 🧠 Memory of the Single-Trade Law

-   Only one buy and one sell allowed
-   Buy must occur before sell
-   Track minimum price so far
-   Maximize `price - minPrice`
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Best Time to Buy and Sell Stock**,
where the Oracle does not chase every rise,
but waits patiently for the deepest valley —
and strikes once, at the perfect peak,
to claim the greatest possible gain. 💰✨
