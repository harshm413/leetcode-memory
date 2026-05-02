## 🪙🧠 _The Fewest Coins (Memoization): The Coin Change Saga_

> \_"The Oracle was given a set of coin denominations
> and a target amount.
>
> She was commanded:
>
> **'What is the FEWEST number of coins needed
> to make exactly this amount?
> If it's impossible -- return -1.'**
>
> The Oracle had already mastered Subset Sum --
> where each element was used AT MOST ONCE (0/1 knapsack).
>
> But coins were different.
>
> **Each coin could be used UNLIMITED times.**
>
> This was the **Unbounded Knapsack** --
> the infinite supply version.
>
> The choice at each coin was no longer 'include once or exclude.'
> It was 'use this coin (and STAY to use it again) or move on.'
>
> And instead of asking 'does a subset exist?' (boolean),
> the Oracle was MINIMIZING the count of coins used.
>
> Two shifts from Subset Sum:
> 1. Unlimited use → stay at the same index after including.
> 2. Minimize count → use `min` instead of `||` or `+`."\_

---

This is the saga of **Coin Change (Memoization)**.

Given:
-   `coins[]` -- available denominations.
-   `amount` -- the target sum.

Return the **fewest coins** to make `amount`.
If impossible, return `-1`.
Each coin can be used **unlimited** times.

```
Input:  coins = [1, 2, 5], amount = 11
Output: 3   (5 + 5 + 1 = 11)

Input:  coins = [2], amount = 3
Output: -1  (can't make 3 with only 2s)

Input:  coins = [1], amount = 0
Output: 0   (no coins needed for amount 0)
```

---

## 🧠 Building the Intuition -- The Choice at Each Coin

The Oracle looks at each coin denomination and asks:

> **"Should I USE this coin, or SKIP to the next denomination?"**

**If she USES coin `coins[i]`:**
She spends one coin. The remaining amount shrinks by `coins[i]`.
But she STAYS at the same coin -- she can use it again.
`1 + solve(index, remaining - coins[index])`

**If she SKIPS coin `coins[i]`:**
She moves to the next denomination. Amount unchanged.
`solve(index + 1, remaining)`

She takes the MINIMUM of both choices
(fewest coins).

**Why STAY at the same index when using a coin?**

In 0/1 knapsack (Subset Sum), each element is used once.
After including it, you move to `index + 1`.

In unbounded knapsack (Coin Change), each coin has infinite supply.
After using it, you STAY at `index` -- you might use it again.

This is the KEY difference between 0/1 and unbounded knapsack.

---

## 🧠 The Base Cases

**Amount becomes 0:**
No more money needed. Zero coins. Return 0.

**Past the last coin (`index == n`) and amount > 0:**
No more denominations to try. Can't make the amount.
Return a very large number (infinity) -- so it never wins the `min`.

Why infinity and not -1?
Because we're minimizing. `min(validAnswer, -1)` would pick -1.
We need `min(validAnswer, infinity)` to pick the valid answer.
We'll convert infinity to -1 at the very end.

---

### 📜 The Scroll of the Fewest Coins

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 🪙 Setting Up the Cache

```cpp
int coinChange(vector<int>& coins, int amount) {
    int n = coins.size();
    vector<vector<int>> memo(n, vector<int>(amount + 1, -1));
```

`memo[index][remaining]` = fewest coins to make `remaining`
using coins from `index` to `n-1`.
`-1` = not yet computed.

---

```cpp
    int result = solve(0, amount, coins, memo);
    return result >= 1e9 ? -1 : result;
}
```

Start at coin 0 with the full amount.
If the result is infinity (≥ 1e9) → impossible → return -1.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int index, int remaining, vector<int>& coins,
          vector<vector<int>>& memo) {
```

"What's the fewest coins to make `remaining`
using coins from `index` to `n-1`?"

---

### 🎯 "Amount is zero!"

```cpp
    if (remaining == 0) return 0;
```

No more money needed. Zero coins. Done.

---

### 🛑 "No more coin types"

```cpp
    if (index == (int)coins.size()) return 1e9;
```

No more denominations available. Amount still > 0.
Impossible from here. Return infinity.

Why `1e9` and not `INT_MAX`?
Because we'll add 1 to it (`1 + solve(...)`).
`INT_MAX + 1` overflows. `1e9 + 1` doesn't.

---

### 📖 Check the cache

```cpp
    if (memo[index][remaining] != -1) return memo[index][remaining];
```

---

### 🪙 Choice 1 -- USE This Coin (and Stay)

```cpp
    int use = 1e9;
    if (coins[index] <= remaining) {
        use = 1 + solve(index, remaining - coins[index], coins, memo);
    }
```

**Can I use this coin?** Only if it doesn't exceed the remaining amount.

**If I use it:**
I spend 1 coin (`+ 1`).
The remaining amount shrinks by this coin's value.
I STAY at the same index -- I can use this coin again.

**Why `solve(index, ...)` and not `solve(index + 1, ...)`?**

This is the unbounded knapsack twist.
The coin has infinite supply. Using it once doesn't exhaust it.
I stay at `index` to potentially use it again.

In 0/1 knapsack: `solve(index + 1, ...)` -- move on after using.
In unbounded: `solve(index, ...)` -- stay to reuse.

---

### 🚶 Choice 2 -- SKIP This Coin Type

```cpp
    int skip = solve(index + 1, remaining, coins, memo);
```

Move to the next denomination. Amount unchanged.
Maybe a different coin works better.

---

### ⚖️ Take the Minimum

```cpp
    memo[index][remaining] = min(use, skip);
    return memo[index][remaining];
}
```

The Oracle picks whichever choice uses fewer coins.

---

### 🎺 The Trial of the Fewest Coins

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

**Full trace for coins = [1, 2, 5], amount = 11:**

```
solve(0, 11): "Fewest coins for 11 using [1, 2, 5]?"
  USE coin 1: 1 + solve(0, 10) → ... eventually 11 coins (all 1s)
  SKIP coin 1: solve(1, 11)
    USE coin 2: 1 + solve(1, 9)
      USE coin 2: 1 + solve(1, 7)
        ... eventually many 2s + leftover
    SKIP coin 2: solve(2, 11)
      USE coin 5: 1 + solve(2, 6)
        USE coin 5: 1 + solve(2, 1)
          USE coin 5: 5 > 1, can't.
          SKIP: solve(3, 1) → index==3 → 1e9
        memo[2][1] = 1e9 (can't make 1 with only 5s)
      memo[2][6] = 1 + 1e9 = 1e9... hmm.
```

Actually the recursion explores ALL combinations.
The optimal path: use coin 5 twice (solve stays at index 2),
then skip to coin 1, use it once.

Let me trace the winning path:

```
solve(0, 11):
  skip coin 1 → solve(1, 11):
    skip coin 2 → solve(2, 11):
      use coin 5: 1 + solve(2, 6):
        use coin 5: 1 + solve(2, 1):
          can't use 5 (5 > 1). skip → solve(3, 1) → 1e9.
        memo[2][1] = 1e9.
      Hmm, this path fails.
```

The issue: after using two 5s (remaining=1), only coin 5 is available.
The recursion needs to ALSO try coins 1 and 2 for the remainder.

But wait -- `solve(2, 1)` only has coin 5 (index 2).
The coins before index 2 (coins 1 and 2) are already "skipped."

**This is the problem with the index-based approach for Coin Change.**

Actually, the standard Coin Change memoization uses a DIFFERENT formulation:
**just `solve(remaining)` -- try ALL coins at every step.**

Let me rewrite with the correct approach.

---

Actually, the index-based approach DOES work, but the winning path is:

```
solve(0, 11):
  USE coin 1: 1 + solve(0, 10):
    SKIP coin 1 → solve(1, 10):
      SKIP coin 2 → solve(2, 10):
        USE coin 5: 1 + solve(2, 5):
          USE coin 5: 1 + solve(2, 0) → 0
        memo[2][5] = 2
      memo[2][10] = 1 + 2 = 3... wait that's 3 coins for 10? No.
```

This is getting confusing. Let me use the SIMPLER and more standard formulation.

---

## 🧠 The Simpler Formulation -- No Index, Just Amount

The more common (and cleaner) Coin Change memoization:

**State: just the remaining amount.**

At each amount, try EVERY coin. Take the one that gives the fewest total coins.

```
solve(remaining) = min over all coins c where c <= remaining:
    1 + solve(remaining - c)
```

No index needed. Every coin is available at every step.
This naturally handles unlimited use.

Let me rewrite:

---

## 🪙 The Cleaner Memoization (Amount-Only State)

### The Cache

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> memo(amount + 1, -1);
    int result = solve(amount, coins, memo);
    return result >= 1e9 ? -1 : result;
}
```

1D cache: `memo[remaining]` = fewest coins to make `remaining`.

---

### The Recursion

```cpp
int solve(int remaining, vector<int>& coins, vector<int>& memo) {
```

"What's the fewest coins to make `remaining`?"

---

### 🎯 Amount is zero

```cpp
    if (remaining == 0) return 0;
```

No money needed. Zero coins.

---

### 🛑 Negative amount

```cpp
    if (remaining < 0) return 1e9;
```

Overshot. This path is invalid.

---

### 📖 Cache check

```cpp
    if (memo[remaining] != -1) return memo[remaining];
```

---

### 🔁 Try Every Coin

```cpp
    int best = 1e9;

    for (int coin : coins) {
        if (coin <= remaining) {
            int result = 1 + solve(remaining - coin, coins, memo);
            best = min(best, result);
        }
    }
```

For each coin denomination:
-   If the coin fits (doesn't exceed remaining):
-   Use it: 1 coin + solve the reduced amount.
-   Track the minimum across all coins.

**Why try ALL coins, not just one?**

Because we don't know which coin leads to the optimal solution.
Coin 5 might seem great, but maybe using coin 2 three times
and coin 1 once gives fewer total coins for a specific amount.

The recursion explores all options. `min` picks the best.

---

### Cache and return

```cpp
    memo[remaining] = best;
    return best;
}
```

---

### 🎺 Correct Trace for coins = [1, 2, 5], amount = 11:

```
solve(11):
  coin 5: 1 + solve(6)
    coin 5: 1 + solve(1)
      coin 1: 1 + solve(0) → 0
    solve(1) = 1
  solve(6) = 1 + 1 = 2
solve(11) via coin 5: 1 + 2 = 3

  coin 2: 1 + solve(9)
    ... eventually more than 3 coins

  coin 1: 1 + solve(10)
    ... eventually more than 3 coins

memo[11] = min(3, ...) = 3
```

**Answer: 3** ✓ (5 + 5 + 1)

---

**Trace for coins = [2], amount = 3:**

```
solve(3):
  coin 2: 1 + solve(1)
    solve(1): coin 2 → 2 > 1, can't. best = 1e9.
  1 + 1e9 = 1e9.
memo[3] = 1e9 → return -1.
```

**Answer: -1** ✓ (can't make 3 with only 2s)

---

**Trace for amount = 0:**

```
solve(0) → 0.
```

**Answer: 0** ✓

---

## 🔍 Two Formulations Compared

| Index-based (2D)                  | Amount-only (1D)                  |
| --------------------------------- | --------------------------------- |
| State: `(index, remaining)`       | State: `(remaining)` only         |
| Include: stay at index (unbounded)| Try all coins at every step       |
| Skip: move to index+1            | No skip -- all coins always available |
| 2D memo: O(n × amount)           | 1D memo: O(amount)               |
| Matches knapsack framework        | Simpler, more common              |

The 1D formulation is standard for Coin Change.
The 2D formulation is useful for understanding the knapsack connection.

Both give the same answer. The 1D version is cleaner.

---

## 🔍 0/1 Knapsack vs Unbounded Knapsack

| 0/1 Knapsack (Subset Sum)        | Unbounded (Coin Change)           |
| --------------------------------- | --------------------------------- |
| Each item used at most once       | Each item used unlimited times    |
| After include: `index + 1`       | After include: `index` (stay)     |
| Or: no index, just try each once  | Or: no index, try all every time  |

The "stay at same index" is the signature of unbounded knapsack.

---

### 🧠 Memory of the Fewest Coins (Memoization) Law

-   **State:** `remaining` amount (1D is cleaner than 2D)
-   **Try every coin:** `1 + solve(remaining - coin)` for each coin that fits
-   **Take the min** across all coins
-   **Base:** `remaining == 0` → 0. `remaining < 0` → infinity.
-   **Infinity = 1e9** (not INT_MAX, to avoid overflow on `1 + ...`)
-   **Convert infinity to -1** at the end
-   **Unbounded:** each coin can be used unlimited times
-   **Time:** O(amount × numCoins). **Space:** O(amount).

Thus is remembered the saga of **Coin Change (Memoization)**,
where the Oracle tried every coin at every amount --
using one coin, reducing the amount,
and asking "what's the fewest from here?" --
always picking the coin that led to the smallest total --
until the amount reached zero
or no coin could help. 🪙🧠✨
