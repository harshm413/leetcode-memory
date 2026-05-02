## 🪙🔢📋 _Counting the Combinations (Tabulation): The Coin Change II Saga_

> \_"The Oracle had counted combinations with memoization.
> Now she built the answer bottom-up.
>
> The tabulation had a subtle but critical structure:
>
> **The OUTER loop iterates over COINS.
> The INNER loop iterates over AMOUNTS.**
>
> This order ensures combinations, not permutations.
> If the loops were swapped -- amounts outer, coins inner --
> we'd count permutations instead."\_

---

This is the saga of **Coin Change II (Tabulation)**.

Same problem:
-   Count combinations of coins summing to amount.
-   Unlimited supply. Order doesn't matter.

```
Input:  coins = [1, 2, 5], amount = 5  →  Output: 4
Input:  coins = [2], amount = 3         →  Output: 0
```

---

## 🧠 The Bottom-Up Formulation

```
dp[a] = number of combinations that sum to amount a.
```

**Base case:**

```
dp[0] = 1   (one way to make 0: use no coins)
```

**Transition:**

For each coin, for each amount ≥ coin:

```
dp[a] += dp[a - coin]
```

"The number of combinations for amount `a` increases by
the number of combinations for `a - coin`
(those combinations, plus one more of this coin, now sum to `a`)."

**Critical: coins in the OUTER loop, amounts in the INNER loop.**

---

## 🧠 Why Loop Order Matters -- The Most Important Insight

### Coins outer, amounts inner → COMBINATIONS ✓

```cpp
for (int coin : coins) {          // outer: one coin type at a time
    for (int a = coin; a <= amount; a++) {
        dp[a] += dp[a - coin];
    }
}
```

Each coin type is processed completely before moving to the next.
When processing coin 2, coin 1 is already "done."
Combinations are built in coin order: 1s first, then 2s, then 5s.
`{1, 2}` is generated. `{2, 1}` is NOT.

### Amounts outer, coins inner → PERMUTATIONS ✗

```cpp
for (int a = 1; a <= amount; a++) {    // outer: one amount at a time
    for (int coin : coins) {
        if (coin <= a) dp[a] += dp[a - coin];
    }
}
```

At each amount, ALL coins are tried.
`dp[3]` gets contributions from coin 1 (via `dp[2]`)
AND coin 2 (via `dp[1]`).
But `dp[2]` already includes paths that used coin 2.
So `{2, 1}` and `{1, 2}` are both counted. Permutations!

**The outer loop determines what we're counting.**
Coins outer = combinations. Amounts outer = permutations.

> _"The order of the loops is the order of the counting.
> Coins first means each coin gets its turn --
> and once its turn is over, it never disrupts the others.
> Amounts first means every coin fights for every amount --
> creating duplicate orderings."_

---

### 📜 The Scroll of the Bottom-Up Combinations

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Array

```cpp
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount + 1, 0);
```

`dp[a]` = number of combinations summing to `a`.
All start at 0.

---

### 🏁 Base Case

```cpp
    dp[0] = 1;
```

One way to make amount 0: use no coins.
This is the seed from which all combinations grow.

---

### 🔁 Outer Loop -- One Coin Type at a Time

```cpp
    for (int coin : coins) {
```

Process each coin denomination completely
before moving to the next.

This is what makes it COMBINATIONS.

---

### 🔁 Inner Loop -- All Amounts This Coin Can Reach

```cpp
        for (int a = coin; a <= amount; a++) {
```

For each amount from `coin` to `amount`.
(Amounts less than `coin` can't use this coin.)

**Left to right** -- because this is UNBOUNDED knapsack.
We WANT to reuse the same coin multiple times.
Left to right allows `dp[a - coin]` to include
uses of this coin from earlier in this same loop iteration.

In 0/1 knapsack, we go right to left (prevent reuse).
In unbounded, we go left to right (allow reuse).

---

### 🪙 Add Combinations That Use This Coin

```cpp
            dp[a] += dp[a - coin];
        }
    }
```

`dp[a - coin]` = combinations that sum to `a - coin`.
Each of those, plus one more of this coin, sums to `a`.
Add them to `dp[a]`.

---

### 📤 The Answer

```cpp
    return dp[amount];
}
```

---

### 🎺 The Trial of the Bottom-Up Combinations

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

**After processing coin 1:**

| a  | dp[a-1] (used) | dp[a] |
| -- | --------------- | ----- |
| 0  | --              | 1     |
| 1  | dp[0]=1         | 1     |
| 2  | dp[1]=1         | 1     |
| 3  | dp[2]=1         | 1     |
| 4  | dp[3]=1         | 1     |
| 5  | dp[4]=1         | 1     |

dp = [1, 1, 1, 1, 1, 1]

With only coin 1: every amount has exactly 1 combination (all 1s).

**After processing coin 2:**

| a  | dp[a-2] (used) | dp[a] before | dp[a] after |
| -- | --------------- | ------------ | ----------- |
| 2  | dp[0]=1         | 1            | 1+1 = 2    |
| 3  | dp[1]=1         | 1            | 1+1 = 2    |
| 4  | dp[2]=2         | 1            | 1+2 = 3    |
| 5  | dp[3]=2         | 1            | 1+2 = 3    |

dp = [1, 1, 2, 2, 3, 3]

Amount 2: {1,1} and {2}. Two combinations.
Amount 4: {1,1,1,1}, {1,1,2}, {2,2}. Three combinations.

**After processing coin 5:**

| a  | dp[a-5] (used) | dp[a] before | dp[a] after |
| -- | --------------- | ------------ | ----------- |
| 5  | dp[0]=1         | 3            | 3+1 = **4** |

dp = [1, 1, 2, 2, 3, 4]

Amount 5: {1,1,1,1,1}, {1,1,1,2}, {1,2,2}, {5}. Four combinations.

**Answer: dp[5] = 4** ✓

---

**Trace for coins = [2], amount = 3:**

After processing coin 2:
dp = [1, 0, 1, 0, ...]

dp[3] = 0. Can't make odd amounts with only even coins.

**Answer: 0** ✓

---

## 🔍 The Loop Order Cheat Sheet

| Outer loop | Inner loop | What it counts | Use case              |
| ---------- | ---------- | -------------- | --------------------- |
| Coins      | Amounts    | Combinations   | Coin Change II        |
| Amounts    | Coins      | Permutations   | Combination Sum IV    |

**This is the single most important thing to remember
about unbounded knapsack counting problems.**

Same code. Swapped loops. Completely different answer.

---

## 🔍 Left-to-Right vs Right-to-Left

| Direction    | Knapsack type | Reuse?                |
| ------------ | ------------- | --------------------- |
| Left to right| Unbounded     | YES (coin reused)     |
| Right to left| 0/1           | NO (each item once)   |

Coin Change II: left to right (unlimited coins).
Count Subsets / Target Sum: right to left (each element once).

---

## 🔍 The Unbounded Knapsack Counting Family

| Problem              | Question                    | Outer loop | Inner direction |
| -------------------- | --------------------------- | ---------- | --------------- |
| Coin Change II       | Count combinations          | Coins      | Left to right   |
| Combination Sum IV   | Count permutations          | Amounts    | Left to right   |
| Rod Cutting          | Max value (unbounded)       | Lengths    | Left to right   |

---

### 🧠 Memory of the Counted Combinations (Tabulation) Law

-   **`dp[a]` = number of combinations summing to `a`**
-   **Base:** `dp[0] = 1`
-   **Outer loop: COINS. Inner loop: AMOUNTS.** → combinations
-   **Swapping loops** would count permutations instead
-   **Left to right** inner loop → unbounded (coin reuse allowed)
-   **Transition:** `dp[a] += dp[a - coin]`
-   **Time:** O(n × amount). **Space:** O(amount).

Thus is remembered the saga of **Coin Change II (Tabulation)**,
where the Oracle counted combinations by processing
one coin type at a time --
the outer loop over coins ensuring order,
the inner loop left to right allowing reuse --
until every unordered selection of coins
that summed to the amount was counted exactly once. 🪙🔢📋✨
