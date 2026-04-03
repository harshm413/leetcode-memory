## 🔢⚡ _The Census of the Lit Torches: The Number of 1 Bits Saga_

> \_"In the Kingdom of Bits,
> every number was secretly a sequence of torches —
> thirty-two of them, standing in a row.
>
> Each torch was either **lit** (1) or **dark** (0).
>
> The Royal Census Taker was summoned and commanded:
>
> **'Count how many torches are lit
> in the binary representation of this number.'**
>
> This count had a name — the **Hamming Weight** —
> the number of `1` bits in a number.
>
> The Census Taker considered two ways:
>
> **The Shifting Way:**
> Check each torch one by one —
> look at the rightmost torch,
> record whether it is lit,
> then shift the entire row right by one position,
> bringing the next torch into view.
> Repeat until darkness.
>
> **The Elegant Way — Brian Kernighan's Trick:**
> Do not check every torch.
> Instead, in one operation, extinguish the rightmost lit torch —
> and count how many operations it takes
> until all torches are dark.
>
> The elegant way is faster —
> it runs in O(number of 1 bits) steps,
> not always O(32).
>
> The Census Taker learned both ways —
> for the elegant way reveals a beautiful truth
> about how bits interact with subtraction."\_

---

This is the saga of **Number of 1 Bits**.

You are given a positive integer `n`.

Return the **number of set bits** (1 bits) in its binary representation.

This is also called the **Hamming Weight**.

```
Input:  n = 11  →  binary: 1011  →  three 1 bits  →  Output: 3
Input:  n = 128 →  binary: 10000000 → one 1 bit  →  Output: 1
Input:  n = 2147483645 → binary: 1111111111111111111111111111101 → 30 ones → Output: 30
```

---

## 🧠 The Oracle's Core Insight — Two Paths to Count the Lit Torches

### Path One — The Shift and Check

```
Check the rightmost bit with (n & 1).
If it is 1 → increment count.
Shift n right by 1 → (n >>= 1).
Repeat until n becomes 0.
```

Simple. Reads every bit. At most 32 iterations for a 32-bit integer.

### Path Two — Brian Kernighan's Trick

```
n & (n - 1)  →  turns off the rightmost 1 bit of n.
```

Why? Because subtracting 1 from `n` flips all bits
from the rightmost `1` bit down to bit 0:

```
n     = ...1 0 0 0 0  (rightmost 1 at some position, zeros below it)
n - 1 = ...0 1 1 1 1  (that 1 flips to 0, all zeros below become 1)
n & (n-1) = ...0 0 0 0 0  (the rightmost 1 is gone, everything below also zeroed)
```

So `n = n & (n-1)` extinguishes exactly **one lit torch** per iteration —
the rightmost one.

Count how many times until `n` becomes 0 — that count is the answer.

This runs in exactly **as many steps as there are 1 bits** —
much better than always running 32 steps when there are few set bits.

---

### 📜 The Scroll of the Torch Census

```cpp
#include <iostream>
using namespace std;
```

---

## 🔦 Path One — The Shift and Check

_Look at every torch, one by one_

```cpp
int hammingWeight_shift(uint32_t n) {
    int count = 0;
```

The Census Taker prepared their tally — `count` at zero.

`uint32_t` was used — an unsigned 32-bit integer —
to ensure right shifts filled with zeros, not sign bits.

---

```cpp
    while (n != 0) {
        count += (n & 1);
        n >>= 1;
    }
    return count;
}
```

At every step:

-   `n & 1` — peeked at the **rightmost torch**.
    If lit (`1`), it contributed `1` to the count.
    If dark (`0`), it contributed `0`.

-   `n >>= 1` — the entire row of torches shifted right by one.
    The next torch slid into the rightmost position.

The loop stopped when `n` became `0` — all torches had been inspected
and none remained lit.

> _"Shift. Check. Shift. Check.
> Every torch passes through the rightmost window exactly once."_

---

## ✨ Path Two — Brian Kernighan's Elegant Extinguishing Trick

_Extinguish one lit torch per step — only visit lit torches_

```cpp
int hammingWeight_kernighan(uint32_t n) {
    int count = 0;
```

The Census Taker prepared their tally.

---

```cpp
    while (n != 0) {
        n = n & (n - 1);
        count++;
    }
    return count;
}
```

Each iteration performed one magical operation:

**`n = n & (n - 1)`**

This single line turned off the **rightmost lit torch** in `n`.

The Census Taker incremented the count —
recording that one lit torch had just been extinguished.

The loop continued until `n` was `0` — all torches dark.

The count of extinguishing operations
was exactly the count of torches that had been lit.

**Visual proof on `n = 1011` (decimal 11):**

```
n = 1011   (3 lit torches)
n - 1 = 1010
n & (n-1) = 1010    → rightmost 1 (at bit 0) extinguished. count=1

n = 1010
n - 1 = 1001
n & (n-1) = 1000    → rightmost 1 (at bit 1) extinguished. count=2

n = 1000
n - 1 = 0111
n & (n-1) = 0000    → rightmost 1 (at bit 3) extinguished. count=3

n = 0 → stop.  Answer: 3
```

Three lit torches. Three extinguishing operations. Three iterations.
No wasted steps on dark torches. ✓

---

### 🎺 The Trial of the Torch Census

```cpp
int main() {
    uint32_t n1 = 11;         // binary: 1011
    uint32_t n2 = 128;        // binary: 10000000
    uint32_t n3 = 2147483645; // binary: 1111111111111111111111111111101

    cout << hammingWeight_shift(n1)      << endl; // 3
    cout << hammingWeight_kernighan(n1)  << endl; // 3

    cout << hammingWeight_shift(n2)      << endl; // 1
    cout << hammingWeight_kernighan(n2)  << endl; // 1

    cout << hammingWeight_shift(n3)      << endl; // 30
    cout << hammingWeight_kernighan(n3)  << endl; // 30
    return 0;
}
```

**Shift trace for `n = 11` (binary: `1011`):**

| Step | n (binary) | n & 1 | count | n after >>= 1 |
| ---- | ---------- | ----- | ----- | ------------- |
| 1    | 1011       | 1     | 1     | 0101          |
| 2    | 0101       | 1     | 2     | 0010          |
| 3    | 0010       | 0     | 2     | 0001          |
| 4    | 0001       | 1     | 3     | 0000          |

n = 0 → stop. Answer: **3** ✓

**Kernighan trace for `n = 11` (binary: `1011`):**

| Step | n (binary) | n-1 (binary) | n & (n-1) | count |
| ---- | ---------- | ------------ | --------- | ----- |
| 1    | 1011       | 1010         | 1010      | 1     |
| 2    | 1010       | 1001         | 1000      | 2     |
| 3    | 1000       | 0111         | 0000      | 3     |

n = 0 → stop. Answer: **3** ✓

The Shift Way took 4 steps. Kernighan's Way took 3 steps.
For sparse numbers (few 1 bits) Kernighan's elegance shines brighter.

---

### 🧠 Memory of the Torch Census Law

**The Shift and Check Way:**

-   `count += (n & 1)` — check rightmost bit
-   `n >>= 1` — shift right to expose the next bit
-   Loop until `n == 0` — stop when no lit torches remain
-   O(32) worst case — always processes all bit positions
-   Use `uint32_t` to avoid signed shift behavior

**Brian Kernighan's Elegant Way:**

-   `n = n & (n - 1)` — extinguishes the rightmost lit torch
-   `count++` — record the extinguishing
-   Loop until `n == 0` — stop when no torches remain
-   O(k) where k = number of 1 bits — only visits lit torches
-   **Why it works:** `n - 1` flips the rightmost 1 and all zeros below it;
    AND-ing cancels those bits, turning off exactly the rightmost 1

**The critical bit trick to memorize:**

```
n & (n - 1)  removes the rightmost set bit from n
```

This trick appears in many bit problems — memorize it for life.

Thus is remembered the saga of **Number of 1 Bits**,
where the Census Taker counted the lit torches
of the Kingdom of Bits —
either by checking every torch one by one through rightward shifting,
or by Brian Kernighan's elegant law:
subtract one, AND together, watch the rightmost torch go dark —
and count how many times the darkness spreads
until no torches remain lit. 🔢⚡✨
