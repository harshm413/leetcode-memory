## 🔢⚡ _The Census of the Set Soldiers: The Number of 1 Bits Saga_

> \_"In the Binary Kingdom,
> every number was an army of 32 soldiers —
> each soldier standing in a position,
> carrying either a `0` (absent) or a `1` (present).
>
> The Census Keeper was commanded:
>
> **'Count how many soldiers carry a `1`.
> This is called the Hamming Weight —
> the number of set bits in the binary representation.'**
>
> The naive Keeper walked through all 32 positions
> one by one, checking each soldier.
> Correct — but inspected every position,
> even those carrying `0`.
>
> A second Keeper discovered a smarter approach —
> checking only the last bit each time,
> then shifting the number right to expose the next.
>
> But the Wisest Keeper knew the most elegant trick of all —
> the **Brian Kernighan trick**:
>
> **'When you AND a number with itself minus one —
> `n & (n-1)` —
> the rightmost `1` bit is erased. Instantly.
> Jump directly from one set soldier to the next.
> Count only the ones that exist.
> Skip every zero entirely.'**
>
> Three ways to count the same soldiers.
> One truth — always the Hamming Weight.
> The Kernighan trick the most beautiful of all —
> it runs in as many steps
> as there are `1` bits in the number,
> never wasting a step on a `0`."\_

---

This is the saga of **Number of 1 Bits**.

You are given a positive integer `n`.

Return the **number of set bits** (bits equal to `1`) in its binary representation.
This is also called the **Hamming Weight**.

```
Input:  n = 11   (binary: 1011)   → Output: 3
Input:  n = 128  (binary: 10000000) → Output: 1
Input:  n = 2147483645             → Output: 30
```

---

## 🧠 The Oracle's Core Insight — Three Weapons to Count Set Bits

Before the approaches, understand the key operations:

```
n & 1         → isolates the last bit (1 if set, 0 if not)
n >> 1        → shifts all bits right by 1 (moves to next bit)
n & (n-1)     → erases the rightmost 1 bit (Brian Kernighan)
__builtin_popcount(n) → compiler intrinsic, counts set bits in one call
```

**Why does `n & (n-1)` erase the rightmost 1 bit?**

```
n     =  ...1 0 0 0  (the rightmost 1 followed by zeros)
n - 1 =  ...0 1 1 1  (subtracting 1 flips the rightmost 1 to 0 and all zeros after it to 1)
n & (n-1) = ...0 0 0 0  (AND clears the rightmost 1 and all below it)
```

Example:

```
n     = 1100  (12 in decimal)
n-1   = 1011  (11)
n&(n-1)= 1000  (8) → rightmost 1 was at position 2, now gone
```

This single operation lets us skip all zero bits and land
directly on the next set bit — making the loop run exactly
as many times as there are `1` bits.

---

### 📜 The Scroll of the Binary Kingdom

```cpp
#include <iostream>
using namespace std;
```

---

## ⚡ Approach 1 — Brian Kernighan's Trick (Optimal & Elegant)

_Erase the rightmost 1 bit each step — loop runs exactly k times where k = number of 1 bits_

```cpp
int hammingWeight_Kernighan(uint32_t n) {
    int count = 0;
    while (n != 0) {
        n = n & (n - 1);
        count++;
    }
    return count;
}
```

The Census Keeper erased one set soldier per step.

`n & (n - 1)` wiped the rightmost `1` from `n`.
`count` was incremented once per erasure.

When `n` became `0` — no soldiers remained.
`count` held the exact number of set bits.

**The beauty:** if `n` has only three `1` bits,
the loop runs exactly **3 times** —
not 32, not the number of digits —
just the number of `1`s.

For a number like `128 = 10000000`,
the loop runs just once:
`128 & 127 = 10000000 & 01111111 = 0` → done in one step.

-   **Time:** O(k) where k = number of set bits (at most O(32) = O(1))
-   **Space:** O(1)

---

## 🔍 Approach 2 — Bit Shift: The Steady Walk

_Check the last bit, shift right, repeat for all 32 positions_

```cpp
int hammingWeight_Shift(uint32_t n) {
    int count = 0;
    while (n != 0) {
        count += (n & 1);
        n >>= 1;
    }
    return count;
}
```

The Keeper walked through the army from the right:

-   `n & 1` checked if the **last soldier** carried a `1`.
    If yes — `count` incremented.
-   `n >>= 1` shifted the entire army one position right —
    exposing the next soldier for inspection.
-   Loop continued until `n` became `0` (all soldiers passed).

This is the most intuitive approach —
each bit inspected in sequence, left to right of the traversal.

Note: the loop only runs until `n = 0` —
so leading zeros are not iterated.
For `n = 11 = 1011`, the loop runs 4 times (not 32).

-   **Time:** O(log n) — runs until the highest set bit is passed
-   **Space:** O(1)

---

## 🏛️ Approach 3 — Fixed 32-Bit Loop: The Full March

_Check all 32 bit positions, one by one, regardless of the number_

```cpp
int hammingWeight_Fixed(uint32_t n) {
    int count = 0;
    for (int i = 0; i < 32; i++) {
        if (n & (1u << i)) count++;
    }
    return count;
}
```

The Keeper marched through all 32 positions —
from position 0 (least significant) to position 31 (most significant).

`1u << i` created a mask with a single `1` at position `i`.
`n & (1u << i)` tested if that specific position in `n` was set.

Always exactly 32 iterations.
Simple, predictable, easy to understand.

Used when you want exhaustive bit checking —
or when the problem involves a fixed-width integer.

-   **Time:** O(32) = O(1)
-   **Space:** O(1)

---

## 🚀 Approach 4 — Built-in Function: The Compiler's Gift

_Let the hardware do it in one instruction_

```cpp
int hammingWeight_Builtin(uint32_t n) {
    return __builtin_popcount(n);
}
```

The compiler intrinsic `__builtin_popcount(n)` counts set bits
using the CPU's native `POPCNT` instruction where available —
effectively one hardware operation.

In competitive programming and production code,
this is the pragmatic choice.

In interviews, always explain the manual approaches first —
then mention this as the practical shortcut.

-   **Time:** O(1) — single CPU instruction on modern hardware
-   **Space:** O(1)

---

### 🎺 The Trial of the Binary Kingdom

```cpp
int main() {
    uint32_t n1 = 11;          // binary: 1011       → 3 set bits
    uint32_t n2 = 128;         // binary: 10000000   → 1 set bit
    uint32_t n3 = 4294967293u; // binary: 11111111 11111111 11111111 11111101 → 30 set bits

    cout << "Kernighan: " << hammingWeight_Kernighan(n1) << endl; // 3
    cout << "Shift:     " << hammingWeight_Shift(n2)     << endl; // 1
    cout << "Fixed:     " << hammingWeight_Fixed(n1)     << endl; // 3
    cout << "Builtin:   " << hammingWeight_Builtin(n3)   << endl; // 30
    return 0;
}
```

**Kernighan trace for `n = 11 = 1011`:**

| Step | n (binary) | n-1 (binary) | n & (n-1) | count |
| ---- | ---------- | ------------ | --------- | ----- |
| 1    | `1011`     | `1010`       | `1010`    | 1     |
| 2    | `1010`     | `1001`       | `1000`    | 2     |
| 3    | `1000`     | `0111`       | `0000`    | 3     |
| end  | `0000`     | loop ends    | —         | **3** |

Three `1` bits → loop ran exactly three times. ✓

**Shift trace for `n = 11 = 1011`:**

| Step | n (binary) | n & 1     | count | n >>= 1 |
| ---- | ---------- | --------- | ----- | ------- |
| 1    | `1011`     | 1         | 1     | `0101`  |
| 2    | `0101`     | 1         | 2     | `0010`  |
| 3    | `0010`     | 0         | 2     | `0001`  |
| 4    | `0001`     | 1         | 3     | `0000`  |
| end  | `0000`     | loop ends | **3** |

Four iterations (length of binary representation), same answer. ✓

---

### 🧠 Memory of the Set Soldier Census Law

| Approach             | Time     | Space | Key Operation                                |
| -------------------- | -------- | ----- | -------------------------------------------- |
| **Kernighan**        | O(k)     | O(1)  | `n = n & (n-1)` erases rightmost 1 each step |
| Bit Shift            | O(log n) | O(1)  | `count += (n&1)`, then `n >>= 1`             |
| Fixed 32-bit         | O(1)     | O(1)  | `n & (1u << i)` checks each position         |
| `__builtin_popcount` | O(1)     | O(1)  | Single CPU instruction                       |

**The three XOR/bit laws to know:**

```
n & 1         → test if last bit is set
n >> 1        → shift right (divide by 2, expose next bit)
n & (n-1)     → erase rightmost 1 bit (Brian Kernighan's key)
```

**Interview answer order:**

1. Start with the bit-shift approach (most intuitive, easy to explain).
2. Then reveal the Kernighan trick (shows deep bit manipulation knowledge).
3. Mention `__builtin_popcount` as a practical note.

**Why `uint32_t` and not `int`?**
Signed integers have undefined behavior on right shift if negative.
`uint32_t` ensures safe, predictable bit operations.

Thus is remembered the saga of **Number of 1 Bits**,
where the Census Keeper counted the set soldiers
using three increasingly clever weapons —
the steady shift that inspected every position,
the fixed march that checked all 32 regardless,
and the Kernighan trick that skipped all zeros entirely —
jumping directly from one set soldier to the next
with the single ancient operation `n & (n-1)`,
erasing the rightmost `1` at each step
until only silence remained. 🔢⚡✨
