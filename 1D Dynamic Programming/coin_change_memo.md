## 🪙🔥 _The Forge of Infinite Coins: The Coin Change Saga_

> \*"In the Kingdom of Currency,
> a mighty amount stood before the Oracle —
> a total sum that demanded exact tribute.
>
> Beside her lay coins of different values,
> each coin a spark of possibility.
>
> The Oracle was asked:
> **‘What is the minimum number of coins needed
> to forge this exact amount?’**
>
> Yet not all paths would reach the target,
> and some paths required far too many coins.
>
> Thus she turned to the Art of Memoization,
> exploring every amount from the target downward,
> remembering each result so no path of the forge
> would be repeated."\*

---

The task:
Given coin values (like `{1, 2, 5}`)
and a target amount,
find the **minimum number of coins** needed to make that amount.

If impossible, return **-1**.

The Oracle approached this as a **top-down recursion** problem,
where each sub-amount calls smaller sub-amounts,
and memoization stores fate already computed.

Thus began the saga of **Coin Change**.

---

### 📜 The Scroll of the Coin Forge

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

The forge glowed with molten value.

---

## 🔮 The Oracle’s Recursive Coin-Forge Ritual

_Return the fewest coins needed for amount `rem`_

```cpp
int dfs(int rem, vector<int>& coins, vector<int>& memo) {
    if (rem == 0) return 0;
    if (rem < 0) return INT_MAX;
```

-   When no amount remains,
    no more coins are needed.

-   If the remainder goes negative,
    this path is invalid — a dead end.

---

### 📒 The Ledger of Remembered Amounts

```cpp
    if (memo[rem] != -1)
        return memo[rem];
```

If this amount was forged before,
its fate is reclaimed.

---

### 🔥 Try All Coins, Seeking the Fewest

```cpp
    int best = INT_MAX;
    for (int coin : coins) {
        int res = dfs(rem - coin, coins, memo);
        if (res != INT_MAX)
            best = min(best, 1 + res);
    }
```

For each coin:

-   She spent the coin, reducing `rem`
-   The recursion told her how many coins were needed
-   If valid, she considered this path
-   She chose the **minimum** number across all coins

The forge accepted all possibilities
but kept only the most efficient transformation.

---

### 📓 Record the Best Forge Result

```cpp
    memo[rem] = best;
    return best;
}
```

The Oracle etched the minimum number of coins
for this amount into the ledger,
ensuring no repeated forging.

---

## 💰 Invocation of the Grand Coin Forge

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> memo(amount + 1, -1);
    int ans = dfs(amount, coins, memo);
    return ans == INT_MAX ? -1 : ans;
}
```

The Oracle began with the full amount.
If the best result remained `INT_MAX`,
no combination could produce it.

---

### 🎺 The Trial of the Golden Forge

```cpp
int main() {
    vector<int> coins = {1, 2, 5};
    int amount = 11;

    cout << coinChange(coins, amount) << endl; // expected: 3 (5 + 5 + 1)
    return 0;
}
```

To make **11**,
the Oracle discovered:

-   5 + 5 + 1 → **3 coins**

The forge glowed with triumph.

---

### 🧠 Memory of the Coin-Forge Law

-   If `rem == 0` → took exact amount
-   If `rem < 0` → invalid
-   Memoization prevents repeated subproblems
-   Try all coins; choose minimum valid result
-   Return `-1` if no combination works
-   **Time:** O(amount × number_of_coins)
-   **Space:** O(amount)

Thus is remembered the saga of **Coin Change**,
where the Oracle casts coins into molten sums,
trying every forging path,
and discovering the fewest sparks required
to craft the exact tribute. 🪙🔥✨
