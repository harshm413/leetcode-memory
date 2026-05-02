## 🪵💰📋 _The Merchant's Rod (Tabulation): The Rod Cutting Saga_

> \_"The Oracle had solved the rod cutting with memoization.
> Now she built the answer bottom-up.
>
> **`dp[len]` = maximum revenue from a rod of length `len`.**
>
> Start at length 0 (zero revenue).
> For each length, try every possible cut.
> Take the maximum.
>
> Pure iteration. No recursion."\_

---

This is the saga of **Rod Cutting (Tabulation)**.

Same problem:
-   Rod of length N. Prices for each piece length.
-   Maximize revenue by cutting optimally.

```
Input:  price = [1, 5, 8, 9], N = 4  →  Output: 10
Input:  price = [1, 5, 8, 9, 10, 17, 17, 20], N = 8  →  Output: 22
```

---

## 🧠 The Bottom-Up Formulation

```
dp[0] = 0                    (no rod, no revenue)
dp[len] = max over cut = 1 to len:
    price[cut - 1] + dp[len - cut]
```

For each rod length, try every possible first cut.
The revenue = price of that piece + best revenue from the remainder.
Take the maximum.

```
Time:  O(N²)
Space: O(N)
```

---

### 📜 The Scroll of the Bottom-Up Merchant

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Array

```cpp
int rodCutting(vector<int>& price, int N) {
    vector<int> dp(N + 1, 0);
```

`dp[len]` = max revenue from a rod of length `len`.
All start at 0.

---

### 🔁 Fill Length by Length

```cpp
    for (int len = 1; len <= N; len++) {
```

For each rod length from 1 to N.

---

### 🔁 Try Every Possible First Cut

```cpp
        for (int cut = 1; cut <= len; cut++) {
```

Try cutting a piece of length `cut` (from 1 to `len`).

---

### 🪵 Revenue from This Cut

```cpp
            dp[len] = max(dp[len], price[cut - 1] + dp[len - cut]);
        }
    }
```

**`price[cut - 1]`** -- price of a piece of length `cut`.
(0-indexed: length 1 → `price[0]`, length 2 → `price[1]`, etc.)

**`dp[len - cut]`** -- best revenue from the remaining rod.
Already computed (since `len - cut < len` and we fill in order).

**`max`** -- keep the best cut seen so far.

Why is `dp[len - cut]` already correct?
Because `len - cut < len`, and we fill lengths in increasing order.
Smaller lengths are always solved before larger ones.

---

### 📤 The Answer

```cpp
    return dp[N];
}
```

---

### 🎺 The Trial of the Bottom-Up Merchant

```cpp
int main() {
    vector<int> p1 = {1, 5, 8, 9};
    cout << rodCutting(p1, 4) << endl; // expected: 10

    vector<int> p2 = {1, 5, 8, 9, 10, 17, 17, 20};
    cout << rodCutting(p2, 8) << endl; // expected: 22

    return 0;
}
```

---

**Full table for price = [1, 5, 8, 9], N = 4:**

| len | cut=1          | cut=2          | cut=3          | cut=4          | dp[len] |
| --- | -------------- | -------------- | -------------- | -------------- | ------- |
| 0   | --             | --             | --             | --             | 0       |
| 1   | 1+dp[0]=1      | --             | --             | --             | **1**   |
| 2   | 1+dp[1]=2      | 5+dp[0]=5      | --             | --             | **5**   |
| 3   | 1+dp[2]=6      | 5+dp[1]=6      | 8+dp[0]=8      | --             | **8**   |
| 4   | 1+dp[3]=9      | 5+dp[2]=10     | 8+dp[1]=9      | 9+dp[0]=9      | **10**  |

**dp = [0, 1, 5, 8, 10]**

**Answer: dp[4] = 10** ✓

At length 4: cutting into two pieces of length 2 gives 5+5=10.
That beats: four 1s (4), one 3 + one 1 (9), or one whole piece (9).

---

**Table for price = [1, 5, 8, 9, 10, 17, 17, 20], N = 8:**

| len | Best cut                          | dp[len] |
| --- | --------------------------------- | ------- |
| 0   | --                                | 0       |
| 1   | cut=1: 1                          | 1       |
| 2   | cut=2: 5                          | 5       |
| 3   | cut=3: 8                          | 8       |
| 4   | cut=2: 5+5=10                     | 10      |
| 5   | cut=2: 5+8=13 or cut=3: 8+5=13   | 13      |
| 6   | cut=6: 17                         | 17      |
| 7   | cut=1: 1+17=18                    | 18      |
| 8   | cut=2: 5+17=22                    | **22**  |

**Answer: dp[8] = 22** ✓

Best: cut length 2 (price 5) + length 6 (price 17) = 22.

---

## 🔍 Rod Cutting vs Coin Change -- Same Structure

| Rod Cutting                       | Coin Change I                     |
| --------------------------------- | --------------------------------- |
| Piece lengths 1 to N              | Coin denominations                |
| Price of each length              | Cost = 1 per coin                 |
| Maximize revenue                  | Minimize coin count               |
| `max(price[cut-1] + dp[len-cut])` | `min(1 + dp[amt-coin])`          |
| Unbounded (reuse lengths)         | Unbounded (reuse coins)           |

Same loop structure. Different operation (max vs min)
and different "value" (price vs constant 1).

---

## 🔍 The 2D Knapsack Formulation (Alternative)

You can also solve rod cutting as explicit unbounded knapsack:

```cpp
// Items: piece lengths 1 to N
// weight[i] = i+1, value[i] = price[i]
// Capacity = N

for (int i = 0; i < N; i++) {           // each piece type
    for (int c = i + 1; c <= N; c++) {   // left to right (unbounded)
        dp[c] = max(dp[c], price[i] + dp[c - (i + 1)]);
    }
}
```

Outer loop = piece types. Inner loop = capacities, left to right.
Same answer. Matches the unbounded knapsack template exactly.

---

## 🔍 When NOT to Cut

Sometimes the whole rod is worth more than any combination of cuts.

Example: price = [2, 6, 7, 10, 13], N = 5.
Whole rod: price[4] = 13.
Best cuts: two 2s + one 1 = 6+6+2=14... wait.
Actually: length 2 + length 3 = 6+7=13. Same as whole.
Length 2 + length 2 + length 1 = 6+6+2=14. Better!

So the answer is 14, not 13. The "don't cut" option
is just one of the choices -- the loop naturally considers it
when `cut = len` (the whole rod as one piece).

---

### 🧠 Memory of the Merchant's Rod (Tabulation) Law

-   **State:** `dp[len]` = max revenue from rod of length `len`
-   **Base:** `dp[0] = 0`
-   **Transition:** `dp[len] = max over cut=1..len of: price[cut-1] + dp[len-cut]`
-   **0-indexed price:** `price[cut-1]` for piece of length `cut`
-   **Fill order:** length 1 to N (smaller lengths first)
-   **Rod Cutting = Unbounded Knapsack** (pieces = items, length = capacity)
-   **Time:** O(N²). **Space:** O(N).

Thus is remembered the saga of **Rod Cutting (Tabulation)**,
where the Oracle filled the table length by length --
at each length trying every possible first cut,
selling the piece and adding the best revenue from the rest --
always keeping the maximum --
until the full rod's optimal revenue was known. 🪵💰📋✨
