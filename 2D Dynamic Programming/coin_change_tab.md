## 🪙📋 _The Fewest Coins (Tabulation): The Coin Change Saga_

> \_"The Oracle had solved Coin Change with memoization.
> Now she built the answer bottom-up.
>
> **`dp[a]` = fewest coins to make amount `a`.**
>
> Start at amount 0 (zero coins needed).
> For each amount from 1 to target,
> try every coin. Take the minimum.
>
> Pure iteration. No recursion."\_

---

This is the saga of **Coin Change (Tabulation)**.

Same problem:
-   Coin denominations with unlimited supply.
-   Fewest coins to make the target amount.
-   Return -1 if impossible.

```
Input:  coins = [1, 2, 5], amount = 11  →  Output: 3
Input:  coins = [2], amount = 3          →  Output: -1
```

---

## 🧠 The Bottom-Up Formulation

```
dp[0] = 0                    (zero coins for amount 0)
dp[a] = min over all coins c where c <= a:
    1 + dp[a - c]            (use coin c, solve the rest)
```

For each amount `a`, try every coin.
If using coin `c` leaves amount `a - c`,
and we already know `dp[a - c]`,
then `1 + dp[a - c]` is one option.
Take the minimum across all coins.

If no coin works → `dp[a]` stays at infinity → impossible.

---

### 📜 The Scroll of the Bottom-Up Fewest Coins

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Array

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, amount + 1);
```

`dp[a]` = fewest coins to make amount `a`.

Why initialize to `amount + 1` (not INT_MAX)?

`amount + 1` is a safe "infinity" because
the maximum possible coins is `amount` (all 1-cent coins).
Any value > `amount` means "impossible."
And `amount + 1` won't overflow when we do `1 + dp[...]`.

---

### 🏁 Base Case -- Amount 0

```cpp
    dp[0] = 0;
```

Zero coins needed for amount 0.
This is where every successful path ends.

---

### 🔁 Fill Amount by Amount

```cpp
    for (int a = 1; a <= amount; a++) {
```

For each amount from 1 to the target.

---

### 🔁 Try Every Coin

```cpp
        for (int coin : coins) {
```

For each denomination.

---

### 🪙 Can This Coin Be Used?

```cpp
            if (coin <= a) {
```

The coin must not exceed the current amount.
A 5-cent coin can't help with amount 3.

---

### 📐 Use It and Compare

```cpp
                dp[a] = min(dp[a], 1 + dp[a - coin]);
            }
        }
    }
```

**`1 + dp[a - coin]`:**
Use this coin (1 coin spent).
The remaining amount is `a - coin`.
We already know the fewest coins for `a - coin` (filled earlier).

**`min(dp[a], ...)`:**
Keep the best option seen so far across all coins.

Why is `dp[a - coin]` already correct when we read it?
Because `a - coin < a`, and we fill amounts in increasing order.
Smaller amounts are always filled before larger ones.

> _"I try every coin that fits.
> For each, I ask: if I use this coin,
> how many more do I need for the remainder?
> I pick the coin that leads to the fewest total."_

---

### 📤 The Answer

```cpp
    return dp[amount] > amount ? -1 : dp[amount];
}
```

If `dp[amount]` is still > `amount` → no combination of coins works.
Return -1.

Otherwise → return the fewest coins.

---

### 🎺 The Trial of the Bottom-Up Fewest Coins

```cpp
int main() {
    vector<int> c1 = {1, 2, 5};
    cout << coinChange(c1, 11) << endl; // expected: 3

    vector<int> c2 = {2};
    cout << coinChange(c2, 3) << endl; // expected: -1

    vector<int> c3 = {1};
    cout << coinChange(c3, 0) << endl; // expected: 0

    return 0;
}
```

---

**Full table for coins = [1, 2, 5], amount = 11:**

| a  | Try coin 1       | Try coin 2       | Try coin 5       | dp[a]  |
| -- | ---------------- | ---------------- | ---------------- | ------ |
| 0  | --               | --               | --               | 0      |
| 1  | 1+dp[0]=1        | 2>1, skip        | 5>1, skip        | **1**  |
| 2  | 1+dp[1]=2        | 1+dp[0]=1        | 5>2, skip        | **1**  |
| 3  | 1+dp[2]=2        | 1+dp[1]=2        | 5>3, skip        | **2**  |
| 4  | 1+dp[3]=3        | 1+dp[2]=2        | 5>4, skip        | **2**  |
| 5  | 1+dp[4]=3        | 1+dp[3]=3        | 1+dp[0]=1        | **1**  |
| 6  | 1+dp[5]=2        | 1+dp[4]=3        | 1+dp[1]=2        | **2**  |
| 7  | 1+dp[6]=3        | 1+dp[5]=2        | 1+dp[2]=2        | **2**  |
| 8  | 1+dp[7]=3        | 1+dp[6]=3        | 1+dp[3]=3        | **3**  |
| 9  | 1+dp[8]=4        | 1+dp[7]=3        | 1+dp[4]=3        | **3**  |
| 10 | 1+dp[9]=4        | 1+dp[8]=4        | 1+dp[5]=2        | **2**  |
| 11 | 1+dp[10]=3       | 1+dp[9]=4        | 1+dp[6]=3        | **3**  |

**dp[11] = 3** ✓ (5 + 5 + 1)

At amount 11: coin 1 gives 3, coin 2 gives 4, coin 5 gives 3. Min = 3.

---

**Table for coins = [2], amount = 3:**

| a | Try coin 2       | dp[a]  |
|---|------------------|--------|
| 0 | --               | 0      |
| 1 | 2>1, skip        | **12** (stays at infinity) |
| 2 | 1+dp[0]=1        | **1**  |
| 3 | 1+dp[1]=13       | **12** (stays at infinity) |

`dp[3] = 12 > 3` → return **-1** ✓

Odd amounts are impossible with only coin 2.

---

## 🔍 Why `amount + 1` as Infinity?

The maximum coins possible = `amount` (all 1-cent coins).
Any value > `amount` is impossible.

`amount + 1` is the smallest "impossible" sentinel.
It's safe for `1 + dp[...]` (no overflow).
And the final check `dp[amount] > amount` catches it cleanly.

Using `INT_MAX` would overflow on `1 + INT_MAX`. Dangerous.

---

## 🔍 0/1 vs Unbounded in Tabulation

| 0/1 Knapsack (Subset Sum)        | Unbounded (Coin Change)           |
| --------------------------------- | --------------------------------- |
| 2D table: `dp[i][t]`            | 1D table: `dp[a]`               |
| Each item used once               | Each coin used unlimited          |
| Fill: right to left (1D optim.)  | Fill: left to right (reuse OK)    |

In 0/1 knapsack 1D optimization, we iterate RIGHT to LEFT
to prevent using an item twice.

In unbounded knapsack, we iterate LEFT to RIGHT
BECAUSE we WANT to reuse coins.

The iteration direction encodes the reuse policy.

---

## 🔍 The Coin Change Family

| Problem              | Question                    | Operation |
| -------------------- | --------------------------- | --------- |
| Coin Change (this)   | Fewest coins for amount     | min       |
| Coin Change II       | Number of ways for amount   | +         |
| Unbounded Knapsack   | Max value within capacity   | max       |

Same unbounded structure. Different operation.

---

### 🧠 Memory of the Fewest Coins (Tabulation) Law

-   **State:** `dp[a]` = fewest coins to make amount `a`
-   **Base:** `dp[0] = 0`. All others = `amount + 1` (infinity).
-   **Transition:** `dp[a] = min(dp[a], 1 + dp[a - coin])` for each coin ≤ a
-   **Fill order:** left to right (allows coin reuse -- unbounded)
-   **Infinity:** `amount + 1` (safe, no overflow)
-   **Answer:** `dp[amount] > amount ? -1 : dp[amount]`
-   **Time:** O(amount × numCoins). **Space:** O(amount).

Thus is remembered the saga of **Coin Change (Tabulation)**,
where the Oracle filled the table amount by amount --
at each amount trying every coin,
asking "if I use this coin, how many more for the rest?" --
always keeping the minimum --
until the target amount revealed
the fewest coins needed,
or infinity declared it impossible. 🪙📋✨
