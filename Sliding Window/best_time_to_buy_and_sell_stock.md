## 💰 _The Merchant’s Fortune: The Best Time to Buy and Sell Stock Saga_

> _"Riches are not made by every trade,
> but by patience: buying at the valley,
> selling at the crest,
> and never forgetting the highest profit the tides allow."_

---

In the bustling marketplace, the price of gold shifted each dawn. Some days it rose, some days it sank. Merchants longed to know: if they could buy once and sell once, what was the greatest profit they could earn?

Many tried brute force — testing every pair of days — but the Wise Merchant knew better:

> _“Keep an eye always on the lowest valley,
> and measure every new peak against it.
> Thus shall you find the richest trade.”_

Thus began the saga of the **Best Time to Buy and Sell Stock**.

---

### 📜 The Merchant’s Ledger

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The scribes recorded each day’s price on parchment (`vector<int>`). The Merchant carried two numbers: the lowest price seen so far (the valley) and the greatest profit ever gained (the fortune).

---

### 🏦 The Ritual of Watching Prices

```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = INT_MAX;
    int maxProfit = 0;
```

The Merchant began with no knowledge — the valley set impossibly high (`INT_MAX`) and the fortune at zero.

---

```cpp
    for (int p : prices) {
        if (p < minPrice) {
            minPrice = p; // found a deeper valley
        } else if (p - minPrice > maxProfit) {
            maxProfit = p - minPrice; // found a higher profit peak
        }
    }
    return maxProfit;
}
```

Day after day, the Merchant watched:

-   If today’s price was lower than all before, it became the new **valley** — the best buying price so far.
-   Otherwise, the Merchant asked: _“What if I had bought at the valley and sold today?”_ If the profit surpassed his fortune, he recorded it.

Thus, with one pass of the market, the greatest profit emerged.

---

### 🎺 The Trial of Prices

```cpp
int main() {
    vector<int> prices = {7,1,5,3,6,4};
    cout << maxProfit(prices) << endl; // expected: 5 (buy at 1, sell at 6)
    return 0;
}
```

In this market, the valley was 1, and the crest after it was 6. The Merchant bought low, sold high, and earned a fortune of 5.

---

### 🧠 Memory of the Fortune

-   **Track minPrice** → the valley, best buying point so far.
-   **Track maxProfit** → the fortune, best trade profit so far.
-   **Update on each day:**

    -   If `p < minPrice`, record new valley.
    -   Else, check `p - minPrice` and update fortune if higher.

-   **Time:** O(n), **Space:** O(1).

Thus is remembered the saga of the **Best Time to Buy and Sell Stock**, where fortunes are won not by rushing every trade, but by waiting for the valley and selling at the crest. 💰📈
