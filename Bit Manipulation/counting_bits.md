## 📊⚡ _The Ledger of Every Flame: The Counting Bits Saga_

> \_"The Oracle had learned to count the flames
> in a single number -- the Hamming Weight.
>
> But now the King demanded more:
>
> **'For every number from 0 to n,
> tell me how many bits are set.
> Return the entire ledger.'**
>
> The naive approach would compute the Hamming Weight
> for each number independently.
> O(N × 32) -- correct, but wasteful.
>
> The Oracle saw a pattern hidden in the bits:
>
> **The number of set bits in `i`
> equals the number of set bits in `i / 2`
> plus whether `i` itself is odd.**
>
> `dp[i] = dp[i >> 1] + (i & 1)`
>
> Why? Because `i >> 1` is `i` with the last bit removed.
> The set bits in `i` are the same as in `i >> 1`,
> plus the last bit (which is 1 if `i` is odd).
>
> One pass from 0 to n.
> Each answer computed in O(1) from a previous answer.
> O(N) total. The entire ledger filled."\_

---

This is the saga of **Counting Bits**.

Given an integer `n`:

-   Return an array `ans` of length `n + 1`
    where `ans[i]` is the number of `1` bits in `i`.

```
Input:  n = 2
Output: [0, 1, 1]

Input:  n = 5
Output: [0, 1, 1, 2, 1, 2]
```

---

## 🧠 The Oracle's Core Insight -- DP on the Last Bit

The recurrence:

```
dp[i] = dp[i >> 1] + (i & 1)
```

**`i >> 1`** = `i` with the last bit chopped off (integer division by 2).
**`i & 1`** = the last bit of `i` (0 if even, 1 if odd).

The set bits in `i` = set bits in `i/2` + the last bit.

**Why this works:**

```
i   = ...b₃b₂b₁b₀     (some binary number)
i>>1 = ...b₃b₂b₁       (same bits, last one removed)

popcount(i) = popcount(i>>1) + b₀
```

Removing the last bit gives a smaller number we've already computed.
Adding back the last bit completes the count.

**Base case:** `dp[0] = 0` (zero has no set bits).

```
Time:  O(N) -- one pass, O(1) per number
Space: O(N) -- the output array (required by the problem)
```

---

### 📜 The Scroll of the Flame Ledger

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Ledger-Filling Ritual

```cpp
vector<int> countBits(int n) {
    vector<int> dp(n + 1, 0);
```

The Oracle prepared a ledger of `n + 1` entries,
all initialized to 0.

`dp[0] = 0` -- zero has no flames. The base case.

---

## 🔁 Fill the Ledger from 1 to n

```cpp
    for (int i = 1; i <= n; i++) {
        dp[i] = dp[i >> 1] + (i & 1);
    }
    return dp;
}
```

For each number `i` from 1 to n:

-   `i >> 1` = the number with the last bit removed.
    We already computed its popcount (it's a smaller number).
-   `i & 1` = the last bit (0 or 1).
-   Sum them = popcount of `i`.

One line. O(1) per number. The entire ledger filled in O(N).

> _"Every number's flame count
> is built from a number already counted --
> its right-shifted parent --
> plus the single flame at the end."_

---

### 🎺 The Trial of the Flame Ledger

```cpp
int main() {
    auto r1 = countBits(2);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 0 1 1

    auto r2 = countBits(5);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 0 1 1 2 1 2

    auto r3 = countBits(8);
    for (int x : r3) cout << x << " "; cout << endl;
    // expected: 0 1 1 2 1 2 2 3 1

    return 0;
}
```

---

**Full trace for n = 5:**

| i | Binary | i >> 1 | dp[i>>1] | i & 1 | dp[i] = dp[i>>1] + (i&1) |
|---|--------|--------|----------|-------|---------------------------|
| 0 | 000    | --     | --       | --    | 0 (base case)             |
| 1 | 001    | 0      | 0        | 1     | 0 + 1 = **1**            |
| 2 | 010    | 1      | 1        | 0     | 1 + 0 = **1**            |
| 3 | 011    | 1      | 1        | 1     | 1 + 1 = **2**            |
| 4 | 100    | 2      | 1        | 0     | 1 + 0 = **1**            |
| 5 | 101    | 2      | 1        | 1     | 1 + 1 = **2**            |

**Answer: [0, 1, 1, 2, 1, 2]** ✓

Each entry was computed in O(1) by looking up a previously computed value.

---

**Full trace for n = 8:**

| i | Binary | i >> 1 | dp[i>>1] | i & 1 | dp[i] |
|---|--------|--------|----------|-------|-------|
| 0 | 0000   | --     | --       | --    | 0     |
| 1 | 0001   | 0      | 0        | 1     | 1     |
| 2 | 0010   | 1      | 1        | 0     | 1     |
| 3 | 0011   | 1      | 1        | 1     | 2     |
| 4 | 0100   | 2      | 1        | 0     | 1     |
| 5 | 0101   | 2      | 1        | 1     | 2     |
| 6 | 0110   | 3      | 2        | 0     | 2     |
| 7 | 0111   | 3      | 2        | 1     | 3     |
| 8 | 1000   | 4      | 1        | 0     | 1     |

**Answer: [0, 1, 1, 2, 1, 2, 2, 3, 1]** ✓

Notice the pattern: powers of 2 (1, 2, 4, 8) always have exactly 1 bit set.
The count resets to 1 at every power of 2.

---

## 🔄 Alternative Recurrence -- Using `n & (n-1)`

Another valid DP relation:

```
dp[i] = dp[i & (i - 1)] + 1
```

`i & (i - 1)` clears the lowest set bit of `i`.
The result is a smaller number with one fewer set bit.
So `dp[i] = dp[that smaller number] + 1`.

```cpp
vector<int> countBits(int n) {
    vector<int> dp(n + 1, 0);
    for (int i = 1; i <= n; i++) {
        dp[i] = dp[i & (i - 1)] + 1;
    }
    return dp;
}
```

**Trace for i = 6:**
```
6 = 110
6 & 5 = 110 & 101 = 100 = 4
dp[6] = dp[4] + 1 = 1 + 1 = 2  ✓
```

Both recurrences are O(1) per number, O(N) total.
The `i >> 1` version is slightly more intuitive.
The `i & (i-1)` version connects directly to Brian Kernighan's trick.

---

## 🔍 Why Not Just Call popcount for Each Number?

```cpp
for (int i = 0; i <= n; i++) {
    dp[i] = __builtin_popcount(i);
}
```

This works and is O(N) with hardware popcount.
But the problem specifically asks for an O(N) solution
**without** using built-in functions --
to test your understanding of bit DP.

The DP approach also demonstrates a deeper insight:
**every number's bit count is related to a smaller number's bit count.**
This is the kind of thinking interviewers want to see.

---

## 🌐 The Pattern in the Bit Counts

```
n:     0  1  2  3  4  5  6  7  8  9  10  11  12  13  14  15
bits:  0  1  1  2  1  2  2  3  1  2   2   3   2   3   3   4
```

Notice:
-   The sequence for 0-7 is `[0,1,1,2,1,2,2,3]`.
-   The sequence for 8-15 is the SAME but with +1: `[1,2,2,3,2,3,3,4]`.

Every power-of-2 block repeats the previous block with +1.
This is because adding a leading `1` bit adds exactly 1 to the count.

This pattern is a consequence of the `dp[i>>1] + (i&1)` recurrence.

---

### 🧠 Memory of the Flame Ledger Law

-   **Recurrence 1:** `dp[i] = dp[i >> 1] + (i & 1)`
    -   `i >> 1` = remove last bit (already computed)
    -   `i & 1` = the last bit (0 or 1)
-   **Recurrence 2:** `dp[i] = dp[i & (i-1)] + 1`
    -   `i & (i-1)` = clear lowest set bit (Brian Kernighan)
    -   One fewer set bit → add 1
-   **Base case:** `dp[0] = 0`
-   Both recurrences are O(1) per number → O(N) total
-   Powers of 2 always have dp = 1 (single bit set)
-   The bit count sequence repeats in power-of-2 blocks with +1 offset
-   **Time:** O(N)
-   **Space:** O(N) -- the output array
-   **Edge cases:**
    -   n = 0 → [0]
    -   n = 1 → [0, 1]

Thus is remembered the saga of **Counting Bits**,
where the Oracle did not count each number's flames from scratch
but instead built a ledger where every entry
was computed in O(1) from a smaller entry --
right-shifting to remove the last bit,
adding back its flame if it burned --
filling the entire ledger from 0 to n
in a single elegant pass. 📊⚡✨
