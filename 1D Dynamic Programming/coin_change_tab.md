## 🪙📜 _The Stone Tablet of Sums: The Coin Change (Tabulation) Saga_

> \*"In the Kingdom of Currency,
> the Oracle once solved the forging of coins
> by descending through recursion and memory.
>
> But now she faced a new trial:
>
> **to carve the answer into a great stone tablet,
> filling it from the smallest amounts upward
> until the final amount revealed its destiny.**
>
> Each slot in the tablet held the minimum number of coins
> needed to create that sum —
> a rising ladder of tributes built from the ground up."\*

---

The Oracle now used **bottom-up tabulation**,
a method where she filled a DP array of size `amount + 1`
with the minimal number of coins for every intermediate sum.

The table began at `0`,
for which the answer was always `0 coins`.

Then, building upward,
each future amount used earlier entries
to compute its fate.

Thus began the saga of **Coin Change (Tabulation)**.

---

### 📜 The Scroll of the Coin-Forge Tablet

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

The empty tablet awaited its carvings.

---

## 🧱 The Oracle’s Bottom-Up Ritual

_Forge all amounts from 1 to target_

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, INT_MAX);
    dp[0] = 0;
```

-   `dp[x]` will mean:
    **minimum number of coins needed to make amount x**
-   `dp[0] = 0` is the foundation of the forge.
-   All other sums begin as impossible (`INT_MAX`)
    until proven forgeable.

---

### 🪨 For Each Amount, Try Each Coin

```cpp
    for (int amt = 1; amt <= amount; amt++) {
        for (int coin : coins) {
            if (amt - coin >= 0 && dp[amt - coin] != INT_MAX) {
                dp[amt] = min(dp[amt], dp[amt - coin] + 1);
            }
        }
    }
```

For each amount `amt`:

-   The Oracle inspected every coin.
-   If `amt - coin` was a valid previously-forged amount,
    she updated the current cell with
    **1 coin + dp[amt - coin]**.
-   The smallest of these attempts
    became the final tribute count for `amt`.

Each dp entry rose from the stone
like a step toward the full amount.

---

### 🧿 Return the Destiny of the Final Amount

```cpp
    return dp[amount] == INT_MAX ? -1 : dp[amount];
}
```

If the final cell remained impossible,
no tribute could match the target.
Otherwise, the number carved into the stone
was the minimum coins required.

---

### 🎺 The Trial of the Stone Tablet

```cpp
int main() {
    vector<int> coins = {1, 2, 5};
    int amount = 11;
    cout << coinChange(coins, amount) << endl; // expected: 3
    return 0;
}
```

The rising tablet revealed:

-   Amount 1 → 1 coin
-   Amount 2 → 1 coin
-   Amount 5 → 1 coin
-   Amount 10 → 2 coins
-   Amount 11 → 3 coins (5 + 5 + 1)

The carved destiny was **3**.

---

### 🧠 Memory of the Stone-Tablet Law

-   Build `dp` from 0 → amount
-   `dp[x] = min(dp[x], dp[x - coin] + 1)`
-   Use `INT_MAX` to represent unreachable states
-   Final answer is `dp[amount]`, or `-1` if impossible
-   **Time:** O(amount × number_of_coins)
-   **Space:** O(amount)

Thus is remembered the saga of **Coin Change (Tabulation)**,
where the Oracle no longer dives into recursion
but instead builds a mighty towering stone tablet,
each engraved cell bringing her closer to
the exact tribute demanded by fate. 🪙✨
