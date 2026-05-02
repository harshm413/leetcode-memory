## 🪙🔢🧠 _Counting the Combinations (Memoization): The Coin Change II Saga_

> \_"The Oracle had already found the FEWEST coins
> to make an amount (Coin Change I).
>
> Now the King asked a different question:
>
> **'How many DIFFERENT COMBINATIONS of coins
> make up this amount?'**
>
> Not the fewest. Not the best.
> The COUNT of all possible ways.
>
> And there was a subtle but critical rule:
>
> **Order does NOT matter.**
> `{1, 2, 2}` and `{2, 1, 2}` and `{2, 2, 1}`
> are the SAME combination -- counted only ONCE.
>
> This was NOT the same as Count Subsets.
> In Count Subsets, each element is used once (0/1 knapsack).
> Here, each coin has **unlimited supply** (unbounded knapsack).
>
> And it was NOT the same as a permutation count.
> Permutations count different orderings as different.
> Combinations don't.
>
> The Oracle needed to count COMBINATIONS --
> unordered selections with unlimited use.
>
> The key to avoiding duplicate orderings:
> **process coins in a fixed order.**
> Once you move past a coin, you never go back to it.
> This ensures each combination is generated exactly once."\_

---

This is the saga of **Coin Change II (Memoization)**.

Given:
-   `coins[]` -- available denominations.
-   `amount` -- the target sum.

Return the **number of combinations** that make up `amount`.
Each coin can be used **unlimited** times.
Order doesn't matter.

```
Input:  coins = [1, 2, 5], amount = 5
Output: 4   (combinations: {5}, {2,2,1}, {2,1,1,1}, {1,1,1,1,1})

Input:  coins = [2], amount = 3
Output: 0   (can't make 3 with only 2s)

Input:  coins = [10], amount = 10
Output: 1   (only {10})
```

---

## 🧠 Building the Intuition -- Why Index Matters Here

In Coin Change I (fewest coins), we used a 1D state: just the amount.
At each amount, we tried ALL coins. That worked for minimization.

But for COUNTING combinations, trying all coins at every amount
would count `{1, 2}` and `{2, 1}` as DIFFERENT ways.
That's permutations, not combinations.

**To count combinations (unordered), we need an INDEX.**

The index ensures we process coins in order:
once we move past coin `i`, we never use it again.
This prevents generating the same combination in different orders.

```
solve(index, remaining):
  "How many combinations using coins[index..n-1] sum to remaining?"
```

At each coin, two choices:
-   **USE it** (stay at same index -- unlimited supply).
-   **SKIP it** (move to next coin -- never come back).

This is the **unbounded knapsack counting** pattern.

---

## 🧠 Why "Stay at Same Index" Prevents Permutations

Consider coins = [1, 2], amount = 3.

**Without index (permutations):**
```
solve(3): try coin 1 → solve(2): try coin 2 → solve(0) → 1.  Path: {1, 2}
solve(3): try coin 2 → solve(1): try coin 1 → solve(0) → 1.  Path: {2, 1}
```
Both counted. But {1, 2} and {2, 1} are the same combination!

**With index (combinations):**
```
solve(0, 3): use coin 0 (=1) → solve(0, 2) → ... → eventually uses 1s and 2s in ORDER.
             skip coin 0 → solve(1, 3): use coin 1 (=2) → solve(1, 1) → can't use 2. skip → done.
```
Coin 1 is always used BEFORE coin 2 in any combination.
{1, 2} is generated. {2, 1} is NOT. No duplicates.

The index enforces a canonical ordering.

---

### 📜 The Scroll of the Counted Combinations

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🪙 Setting Up the Cache

```cpp
int change(int amount, vector<int>& coins) {
    int n = coins.size();
    vector<vector<int>> memo(n, vector<int>(amount + 1, -1));
```

2D cache: `memo[index][remaining]`.
`-1` = not yet computed.

---

```cpp
    return solve(0, amount, coins, memo);
}
```

Start at coin 0 with the full amount.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int index, int remaining, vector<int>& coins,
          vector<vector<int>>& memo) {
```

"How many combinations using coins `index` to `n-1`
sum to `remaining`?"

---

### 🎯 Amount is zero -- one valid combination found

```cpp
    if (remaining == 0) return 1;
```

The coins already chosen sum to the target.
One combination found. Count it.

---

### 🛑 No more coin types

```cpp
    if (index == (int)coins.size()) return 0;
```

No more denominations to try. Amount still > 0.
No combination possible from here.

---

### 📖 Cache check

```cpp
    if (memo[index][remaining] != -1) return memo[index][remaining];
```

---

### 🪙 Choice 1 -- USE This Coin (Stay at Same Index)

```cpp
    int use = 0;
    if (coins[index] <= remaining) {
        use = solve(index, remaining - coins[index], coins, memo);
    }
```

Use one of this coin. Amount shrinks.
**Stay at the same index** -- can use this coin again (unlimited supply).

Why stay? Because the coin has infinite copies.
Using it once doesn't exhaust it.

Why does staying prevent permutations?
Because we can only use coins[index] or later coins.
We never go BACK to an earlier coin.
The combination is built in non-decreasing coin order.

---

### 🚶 Choice 2 -- SKIP This Coin Type (Move to Next)

```cpp
    int skip = solve(index + 1, remaining, coins, memo);
```

Done with this denomination forever.
Move to the next coin type. Amount unchanged.

Once skipped, this coin is never used again in this branch.
This is what prevents going back and creating duplicate orderings.

---

### ➕ Total Combinations

```cpp
    memo[index][remaining] = use + skip;
    return memo[index][remaining];
}
```

Combinations that USE this coin + combinations that SKIP it.
Both sets are distinct (one includes this coin type, the other doesn't).
Total = sum.

---

### 🎺 The Trial of the Counted Combinations

```cpp
int main() {
    vector<int> c1 = {1, 2, 5};
    cout << change(5, c1) << endl; // expected: 4

    vector<int> c2 = {2};
    cout << change(3, c2) << endl; // expected: 0

    vector<int> c3 = {10};
    cout << change(10, c3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for coins = [1, 2, 5], amount = 5:**

```
solve(0, 5): "Combinations using [1,2,5] for 5"
  USE coin 1: solve(0, 4)
    USE coin 1: solve(0, 3)
      USE coin 1: solve(0, 2)
        USE coin 1: solve(0, 1)
          USE coin 1: solve(0, 0) → 1  ← {1,1,1,1,1}
          SKIP coin 1: solve(1, 1)
            USE coin 2: 2>1, can't.
            SKIP: solve(2, 1): 5>1, can't. skip: solve(3,1) → 0.
          memo[1][1] = 0
        memo[0][1] = 1 + 0 = 1
        SKIP coin 1: solve(1, 2)
          USE coin 2: solve(1, 0) → 1  ← {1,1,1,2}... wait
```

This gets complex. Let me just verify the answer:

The 4 combinations for amount 5 with coins [1, 2, 5]:
1. {1, 1, 1, 1, 1}
2. {1, 1, 1, 2}
3. {1, 2, 2}
4. {5}

**Answer: 4** ✓

---

**Trace for coins = [2], amount = 3:**

```
solve(0, 3): USE coin 2 → solve(0, 1): 2>1, can't use. skip → solve(1,1) → 0.
             SKIP → solve(1, 3) → 0.
memo[0][3] = 0.
```

**Answer: 0** ✓ (can't make odd amount with only even coins)

---

## 🔍 Coin Change I vs Coin Change II

| Coin Change I (fewest)            | Coin Change II (count)            |
| --------------------------------- | --------------------------------- |
| "Fewest coins for amount"         | "How many combinations for amount"|
| Minimize → `min`                  | Count → `+`                       |
| 1D state OK (try all coins)       | 2D state needed (index for order) |
| `min(1 + solve(amt - coin))`     | `use + skip` with index           |
| Unbounded (reuse coins)           | Unbounded (reuse coins)           |

The key difference: Coin Change II needs the index
to avoid counting permutations as different combinations.

---

## 🔍 Count Subsets vs Coin Change II

| Count Subsets (0/1)               | Coin Change II (unbounded)        |
| --------------------------------- | --------------------------------- |
| Each element used once            | Each coin used unlimited          |
| After include: `index + 1`       | After include: `index` (stay)     |
| 0/1 knapsack                      | Unbounded knapsack                |

The "stay at same index" is the unbounded signature.

---

### 🧠 Memory of the Counted Combinations (Memoization) Law

-   **State:** `(index, remaining)` -- which coin type, how much left
-   **USE coin:** `solve(index, remaining - coin)` -- stay (unlimited)
-   **SKIP coin:** `solve(index + 1, remaining)` -- move on (never return)
-   **Combine:** `use + skip` (count combinations)
-   **Base:** `remaining == 0` → 1. `index == n` → 0.
-   **Index prevents permutations** -- coins used in fixed order
-   **Time:** O(n × amount). **Space:** O(n × amount).

Thus is remembered the saga of **Coin Change II (Memoization)**,
where the Oracle counted combinations, not permutations --
using an index to enforce order,
staying at the same coin for unlimited use,
skipping forward to prevent going back --
until every unordered selection of coins
that summed to the amount was counted exactly once. 🪙🔢🧠✨
