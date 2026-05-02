## 📈💸📋 _The Merchant's Tax (Tabulation): The Best Time to Buy and Sell Stock with Transaction Fee Saga_

> \_"The Oracle had solved the fee problem with memoization.
> Now she built the answer bottom-up.
>
> **`dp[day][state]` = max profit from `day` onward.**
>
> Two states. Fill backward.
> Then collapse to just 2 variables --
> the simplest stock DP of them all."\_

---

This is the saga of **Best Time to Buy and Sell Stock with Transaction Fee (Tabulation)**.

Same problem:
-   Unlimited trades. Fee per transaction.

```
Input:  prices = [1,3,2,8,4,9], fee = 2  →  Output: 8
Input:  prices = [1,3,7,5,10,3], fee = 3  →  Output: 6
```

---

## 📋 The 2D Tabulation

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

### Create the table

```cpp
int maxProfit(vector<int>& prices, int fee) {
    int n = prices.size();
    vector<vector<int>> dp(n + 1, vector<int>(2, 0));
```

`(n+1) × 2` table. Row `n` is all zeros.
States: FREE (0), HOLDING (1).

---

### 🔁 Fill backward

```cpp
    for (int day = n - 1; day >= 0; day--) {
```

---

### 🟢 State 0 — FREE

```cpp
        dp[day][0] = max(
            -prices[day] + dp[day + 1][1],
            dp[day + 1][0]
        );
```

Buy (pay, become HOLDING) or skip (stay FREE).

---

### 🔴 State 1 — HOLDING

```cpp
        dp[day][1] = max(
            prices[day] - fee + dp[day + 1][0],
            dp[day + 1][1]
        );
```

Sell (gain minus fee, become FREE) or skip (stay HOLDING).

The `-fee` is the only difference from Stock II tabulation.

---

```cpp
    }
    return dp[0][0];
}
```

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> p1 = {1, 3, 2, 8, 4, 9};
    cout << maxProfit(p1, 2) << endl; // expected: 8

    vector<int> p2 = {1, 3, 7, 5, 10, 3};
    cout << maxProfit(p2, 3) << endl; // expected: 6

    return 0;
}
```

---

**Full table for prices = [1,3,2,8,4,9], fee = 2:**

```
         FREE(0)  HOLD(1)
day 6:     0        0       ← base case
day 5:     0        7       ← sell 9-2=7 vs skip 0 → 7
day 4:     3        7       ← buy -4+7=3 vs skip 0 → 3; sell 4-2+0=2 vs skip 7 → 7
day 3:     6        7       ← buy -2+7=5 vs skip 3 → 5... 
```

Let me compute carefully:

```
day 5 (price=9):
  FREE:  max(-9 + dp[6][1], dp[6][0]) = max(-9+0, 0) = 0
  HOLD:  max(9-2 + dp[6][0], dp[6][1]) = max(7+0, 0) = 7

day 4 (price=4):
  FREE:  max(-4 + dp[5][1], dp[5][0]) = max(-4+7, 0) = 3
  HOLD:  max(4-2 + dp[5][0], dp[5][1]) = max(2+0, 7) = 7

day 3 (price=8):
  FREE:  max(-8 + dp[4][1], dp[4][0]) = max(-8+7, 3) = max(-1, 3) = 3
  HOLD:  max(8-2 + dp[4][0], dp[4][1]) = max(6+3, 7) = max(9, 7) = 9

day 2 (price=2):
  FREE:  max(-2 + dp[3][1], dp[3][0]) = max(-2+9, 3) = max(7, 3) = 7
  HOLD:  max(2-2 + dp[3][0], dp[3][1]) = max(0+3, 9) = max(3, 9) = 9

day 1 (price=3):
  FREE:  max(-3 + dp[2][1], dp[2][0]) = max(-3+9, 7) = max(6, 7) = 7
  HOLD:  max(3-2 + dp[2][0], dp[2][1]) = max(1+7, 9) = max(8, 9) = 9

day 0 (price=1):
  FREE:  max(-1 + dp[1][1], dp[1][0]) = max(-1+9, 7) = max(8, 7) = 8
  HOLD:  max(1-2 + dp[1][0], dp[1][1]) = max(-1+7, 9) = max(6, 9) = 9
```

Corrected table:

```
         FREE(0)  HOLD(1)
day 6:     0        0
day 5:     0        7
day 4:     3        7
day 3:     3        9
day 2:     7        9
day 1:     7        9
day 0:     8        9
```

**Answer: dp[0][0] = 8** ✓

Reading the path: day 0 FREE, buy at 1 (−1 + HOLD value 9 = 8).
Day 3 HOLD, sell at 8 − 2 = 6, + FREE value 3 = 9.
Day 4 FREE, buy at 4 (−4 + HOLD value 7 = 3).
Day 5 HOLD, sell at 9 − 2 = 7.
Total: (8−1−2) + (9−4−2) = 5 + 3 = 8. ✓

---

## 🔧 Space Optimization -- 2 Variables

Each day depends only on the next day. Two variables suffice.

```cpp
int maxProfit(vector<int>& prices, int fee) {
    int n = prices.size();
    int nextFree = 0, nextHold = 0;
```

Base case: past the last day, both states = 0.

---

```cpp
    for (int day = n - 1; day >= 0; day--) {
        int currFree = max(-prices[day] + nextHold, nextFree);
        int currHold = max(prices[day] - fee + nextFree, nextHold);
```

Compute both BEFORE updating. `currFree` uses `nextHold`,
`currHold` uses `nextFree` — both from the next day.

---

```cpp
        nextFree = currFree;
        nextHold = currHold;
    }
    return nextFree;
}
```

```
Time:  O(N)
Space: O(1)
```

---

## 🔧 The Forward Formulation (Alternative)

Some prefer forward DP where values represent "best profit UP TO day i":

```cpp
int maxProfit(vector<int>& prices, int fee) {
    int n = prices.size();
    int free = 0;
    int hold = -prices[0];
```

`free` = best profit on day i while not holding.
`hold` = best profit on day i while holding.

`hold` starts at `-prices[0]` (bought on day 0).

---

```cpp
    for (int i = 1; i < n; i++) {
        int newFree = max(free, hold + prices[i] - fee);
        int newHold = max(hold, free - prices[i]);
        free = newFree;
        hold = newHold;
    }
    return free;
}
```

**`newFree`:** either stay free, or sell today (hold's profit + price − fee).
**`newHold`:** either stay holding, or buy today (free's profit − price).

Both formulations (forward and backward) give the same answer.
The forward version is often seen in editorial solutions.

---

## 🔍 Fee = 0 Reduces to Stock II

When `fee = 0`, the sell line becomes `prices[day] + solve(FREE)`.
That's exactly Stock II. Every rise is worth taking.

As fee increases, fewer trades are profitable.
At extreme fees, only one big trade (or none) is worth it.

The DP handles this spectrum automatically.

---

## 🔍 The Complete Stock Family -- Final View

| Problem          | States | Sell transition                    |
| ---------------- | ------ | ---------------------------------- |
| Stock II         | 2      | `+price → FREE`                   |
| With Cooldown    | 3      | `+price → COOLDOWN`               |
| **With Fee**     | **2**  | **`+price - fee → FREE`**         |

All three are unlimited-trade variants.
Cooldown adds a state. Fee modifies the profit. That's all.

---

### 🧠 Memory of the Merchant's Tax (Tabulation) Law

-   **2D table:** `dp[day][state]`, 2 states, fill backward
-   **FREE:** `max(-price + dp[HOLD], dp[FREE])`
-   **HOLDING:** `max(price - fee + dp[FREE], dp[HOLD])`
-   **Space optimization:** 2 variables, compute both before updating
-   **Forward alternative:** `free = max(free, hold+price-fee)`, `hold = max(hold, free-price)`
-   **Fee = 0** reduces to Stock II exactly
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Best Time to Buy and Sell Stock with Fee (Tabulation)**,
where the Oracle filled the two-state table backward --
free days weighed the cost of entry,
holding days weighed the net gain after the taxman's cut --
then collapsed everything into two variables,
proving that the simplest stock DP
needed nothing more than one subtraction
to account for the merchant's tax. 📈💸📋✨
