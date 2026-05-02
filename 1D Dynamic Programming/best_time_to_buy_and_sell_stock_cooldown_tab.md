## 📈❄️📋 _The Merchant's Cooldown (Tabulation): The Best Time to Buy and Sell Stock with Cooldown Saga_

> \_"The Oracle had solved cooldown with memoization.
> Now she built the answer bottom-up.
>
> **`dp[day][state]` = max profit from `day` onward
> in the given state.**
>
> Three states. Fill backward.
> Then collapse to just 3 variables --
> free, holding, cooldown --
> updated day by day."\_

---

This is the saga of **Best Time to Buy and Sell Stock with Cooldown (Tabulation)**.

Same problem:
-   Unlimited trades. One-day cooldown after selling.

```
Input:  prices = [1,2,3,0,2]  →  Output: 3
Input:  prices = [1]           →  Output: 0
```

---

## 📋 The 2D Tabulation

### Create the table

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

```cpp
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    if (n <= 1) return 0;
    vector<vector<int>> dp(n + 1, vector<int>(3, 0));
```

`(n+1) × 3` table. Row `n` is all zeros (base case).
States: FREE (0), HOLDING (1), COOLDOWN (2).

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

Buy (pay, become HOLDING tomorrow) or skip (stay FREE).

---

### 🔴 State 1 — HOLDING

```cpp
        dp[day][1] = max(
            prices[day] + dp[day + 1][2],
            dp[day + 1][1]
        );
```

Sell (gain, enter COOLDOWN tomorrow) or skip (stay HOLDING).

---

### ❄️ State 2 — COOLDOWN

```cpp
        dp[day][2] = dp[day + 1][0];
```

Forced wait. Tomorrow becomes FREE.

---

```cpp
    }
    return dp[0][0];
}
```

Start from day 0, state FREE.

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> p1 = {1, 2, 3, 0, 2};
    cout << maxProfit(p1) << endl; // expected: 3

    vector<int> p2 = {1};
    cout << maxProfit(p2) << endl; // expected: 0

    vector<int> p3 = {2, 1, 4, 5, 2, 9, 7};
    cout << maxProfit(p3) << endl; // expected: 11

    return 0;
}
```

---

**Full table for prices = [1,2,3,0,2]:**

```
         FREE(0)  HOLD(1)  COOL(2)
day 5:     0        0        0       ← base case
day 4:     0        2        0
day 3:     2        2        0
day 2:     2        4        2
day 1:     3        4        2
day 0:     3        3        3
```

**Reading key cells:**

| Day | State | Calculation | Value |
| --- | ----- | ----------- | ----- |
| 4   | FREE  | max(-2+0, 0) = max(-2, 0) | 0 |
| 4   | HOLD  | max(2+0, 0) = max(2, 0) | 2 |
| 3   | FREE  | max(-0+2, 0) = max(2, 0) | 2 |
| 3   | HOLD  | max(0+0, 2) = max(0, 2) | 2 |
| 2   | HOLD  | max(3+2, 2) = max(5, 2)... |  |

Wait, let me recompute carefully:

```
day 4 (price=2):
  FREE:  max(-2 + dp[5][1], dp[5][0]) = max(-2+0, 0) = 0
  HOLD:  max(2 + dp[5][2], dp[5][1])  = max(2+0, 0) = 2
  COOL:  dp[5][0] = 0

day 3 (price=0):
  FREE:  max(-0 + dp[4][1], dp[4][0]) = max(0+2, 0) = 2
  HOLD:  max(0 + dp[4][2], dp[4][1])  = max(0+0, 2) = 2
  COOL:  dp[4][0] = 0

day 2 (price=3):
  FREE:  max(-3 + dp[3][1], dp[3][0]) = max(-3+2, 2) = max(-1, 2) = 2
  HOLD:  max(3 + dp[3][2], dp[3][1])  = max(3+0, 2) = max(3, 2) = 3
  COOL:  dp[3][0] = 2

day 1 (price=2):
  FREE:  max(-2 + dp[2][1], dp[2][0]) = max(-2+3, 2) = max(1, 2) = 2
  HOLD:  max(2 + dp[2][2], dp[2][1])  = max(2+2, 3) = max(4, 3) = 4
  COOL:  dp[2][0] = 2

day 0 (price=1):
  FREE:  max(-1 + dp[1][1], dp[1][0]) = max(-1+4, 2) = max(3, 2) = 3
  HOLD:  max(1 + dp[1][2], dp[1][1])  = max(1+2, 4) = max(3, 4) = 4
  COOL:  dp[1][0] = 2
```

Corrected table:

```
         FREE(0)  HOLD(1)  COOL(2)
day 5:     0        0        0
day 4:     0        2        0
day 3:     2        2        0
day 2:     2        3        2
day 1:     2        4        2
day 0:     3        4        2
```

**Answer: dp[0][0] = 3** ✓

Optimal path: day 0 FREE → buy (−1 + HOLD).
Day 1 HOLD → sell (+2 + COOL). Profit so far: 1.
Day 2 COOL → forced wait → FREE.
Day 3 FREE → buy (−0 + HOLD).
Day 4 HOLD → sell (+2). Profit: 1 + 2 = 3. ✓

---

**Table for prices = [2,1,4,5,2,9,7]:**

The optimal: buy at 1, sell at 5 (+4), cool, buy at 2, sell at 9 (+7) = 11.

**Answer: 11** ✓

---

## 🔧 Space Optimization -- 3 Variables

Each day depends only on the next day.
We need just 3 variables: `nextFree`, `nextHold`, `nextCool`.

```cpp
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    if (n <= 1) return 0;

    int nextFree = 0, nextHold = 0, nextCool = 0;
```

All start at 0 — base case (past the last day).

---

```cpp
    for (int day = n - 1; day >= 0; day--) {
        int currFree = max(-prices[day] + nextHold, nextFree);
        int currHold = max(prices[day] + nextCool, nextHold);
        int currCool = nextFree;
```

**currFree:** buy (pay, tomorrow HOLD) or skip (tomorrow FREE).
**currHold:** sell (gain, tomorrow COOL) or skip (tomorrow HOLD).
**currCool:** forced wait (tomorrow FREE).

We compute all three BEFORE updating, so they use the "next day" values.

---

```cpp
        nextFree = currFree;
        nextHold = currHold;
        nextCool = currCool;
    }
    return nextFree;
}
```

After processing all days, `nextFree` = max profit starting free on day 0.

```
Time:  O(N)
Space: O(1)
```

Present the 2D table first. Show this as the optimization.

---

## 🔍 The Forward Formulation (Alternative)

Some prefer a forward DP where `dp[day]` = max profit UP TO day:

```
free[i]  = max(free[i-1], cool[i-1])
hold[i]  = max(hold[i-1], free[i-1] - prices[i])
cool[i]  = hold[i-1] + prices[i]
```

This is equivalent but thinks forward instead of backward.
`free[i]` = best profit on day i while free (didn't just sell).
`hold[i]` = best profit on day i while holding.
`cool[i]` = best profit on day i having just sold (entering cooldown).

Both formulations give the same answer. The backward version
matches our memoization directly. The forward version is
sometimes seen in editorial solutions.

---

## 🔍 The Stock Cooldown Family

| Problem          | After sell      | States              |
| ---------------- | --------------- | ------------------- |
| Stock II         | Immediately free| 2 (free, hold)      |
| **With Cooldown**| **Wait 1 day**  | **3 (free, hold, cool)** |
| With Fee         | Immediately free| 2 (free, hold) + fee|

Cooldown adds one state. Fee modifies the sell profit.
Both are variations on the same state machine framework.

---

### 🧠 Memory of the Merchant's Cooldown (Tabulation) Law

-   **2D table:** `dp[day][state]`, 3 states, fill backward
-   **FREE:** `max(-price + dp[day+1][HOLD], dp[day+1][FREE])`
-   **HOLDING:** `max(+price + dp[day+1][COOL], dp[day+1][HOLD])`
-   **COOLDOWN:** `dp[day+1][FREE]`
-   **Space optimization:** 3 variables (`nextFree, nextHold, nextCool`)
-   **Compute all curr values BEFORE updating** (avoid using stale data)
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Best Time to Buy and Sell Stock with Cooldown (Tabulation)**,
where the Oracle filled the three-state table backward --
free days offered choices,
holding days awaited the right sell,
cooldown days passed in forced silence --
then collapsed the table into three variables,
updated day by day,
until the maximum profit emerged
from the rhythm of trade, rest, and trade again. 📈❄️📋✨
