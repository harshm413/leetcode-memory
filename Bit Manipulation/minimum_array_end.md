## 🔢🏁 _The Smallest Final Stone: The Minimum Array End Saga_

> \_"The Oracle was given two numbers -- `n` and `x`.
>
> She was commanded:
>
> **'Construct an array of n positive integers
> such that the bitwise AND of ALL elements equals x.
> The array must be sorted in strictly increasing order.
> Minimize the LAST element.'**
>
> The Oracle thought carefully.
>
> For the AND of all elements to equal `x`,
> every element must have ALL bits of `x` set.
> If even one element has a bit of `x` cleared,
> the AND would lose that bit.
>
> So every element must be a **superset** of `x`'s bits.
>
> To minimize the last element,
> the Oracle needed to make the elements
> as small as possible while:
> 1. Keeping all bits of `x` set in every element.
> 2. Making them strictly increasing.
>
> The insight:
>
> **The bits of `x` are FIXED -- they must be 1 in every element.
> The ZERO bits of `x` are FREE -- they can be used
> to create the smallest possible increasing sequence.**
>
> Fill the free bit positions with the binary representation
> of 0, 1, 2, ..., n-1 (the smallest n distinct values).
>
> The last element uses `n-1` in the free positions --
> giving the minimum possible final value."\_

---

This is the saga of **Minimum Array End** (LeetCode 3133).

Given integers `n` and `x`:

-   Construct an array of `n` positive integers, strictly increasing.
-   The bitwise AND of all elements must equal `x`.
-   Return the **minimum possible** value of the last element.

```
Input:  n = 3, x = 4
Output: 6

Input:  n = 2, x = 7
Output: 15
```

---

## 🧠 The Oracle's Core Insight -- Fixed Bits and Free Bits

For the AND of all elements to be `x`:

-   Every bit that is `1` in `x` must be `1` in ALL elements.
    These are **fixed bits** -- untouchable.

-   Every bit that is `0` in `x` is a **free bit** --
    it can be 0 or 1 in each element without affecting the AND
    (since `x` already has 0 there, and `0 AND anything = 0`).

To build n strictly increasing elements with minimum last value:

```
Element 0: x with free bits encoding 0  (= x itself)
Element 1: x with free bits encoding 1
Element 2: x with free bits encoding 2
...
Element n-1: x with free bits encoding n-1
```

The free bits act as a **counter** from 0 to n-1.
Since the counter values are distinct and increasing,
the elements are strictly increasing.

The last element encodes `n-1` in the free bit positions of `x`.

```
Time:  O(number of bits) = O(64)
Space: O(1)
```

---

### 📜 The Scroll of the Free Bits

```cpp
#include <iostream>
using namespace std;
```

---

## ⚔️ The Oracle's Bit-Filling Ritual

```cpp
long long minEnd(int n, int x) {
    long long result = x;
    long long remaining = n - 1;
    long long bit = 1;
```

The Oracle started with `result = x` --
all fixed bits already set.

`remaining = n - 1` -- the value to encode in the free bits.
(Element 0 is `x` itself with free bits = 0.
 The last element needs free bits = `n - 1`.)

`bit` -- a mask that walks through bit positions one by one.

---

## 🔁 Fill the Free Bit Positions

```cpp
    while (remaining > 0) {
        if (!(x & bit)) {
            if (remaining & 1) {
                result |= bit;
            }
            remaining >>= 1;
        }
        bit <<= 1;
    }
    return result;
}
```

The Oracle walked through every bit position from 0 upward.

**If the bit is FIXED** (`x & bit` is non-zero) --
skip it. This bit must stay 1 and cannot be used for the counter.

**If the bit is FREE** (`x & bit` is zero) --
use it to encode the next bit of `remaining`:
-   If the lowest bit of `remaining` is 1 → set this bit in `result`.
-   If 0 → leave it cleared.
-   Shift `remaining` right to expose the next bit.

This effectively **interleaves** the bits of `n-1`
into the free positions of `x`.

When `remaining` becomes 0 -- all bits of `n-1` have been placed.

> _"The fixed bits of x are sacred -- never touched.
> The free bits are a canvas --
> and the Oracle paints the smallest possible counter
> into those empty positions."_

---

### 🎺 The Trial of the Smallest End

```cpp
int main() {
    cout << minEnd(3, 4) << endl;  // expected: 6
    cout << minEnd(2, 7) << endl;  // expected: 15
    return 0;
}
```

---

**Full trace for n = 3, x = 4:**

```
x = 4 = 100
n - 1 = 2 = 10 (binary)
```

Fixed bits of x: bit 2 (the `1` in `100`).
Free bits: bit 0, bit 1, bit 3, bit 4, ...

| bit position | x has this bit? | Free? | remaining | remaining & 1 | Action          | result  |
| ------------ | --------------- | ----- | --------- | -------------- | --------------- | ------- |
| 0            | 0               | Yes   | 10        | 0              | Leave cleared   | 100     |
| 1            | 0               | Yes   | 1         | 1              | Set bit 1       | 110     |
| 2            | 1               | No    | 1         | --             | Skip (fixed)    | 110     |

remaining = 1 >> 1 = 0 → done.

**Result: 110 = 6** ✓

The three elements would be:
-   Free bits = 0: `100` = 4
-   Free bits = 1: `101` = 5
-   Free bits = 2: `110` = 6

AND: `100 & 101 & 110 = 100 = 4 = x` ✓
Strictly increasing: 4 < 5 < 6 ✓
Last element minimized: 6.

---

**Full trace for n = 2, x = 7:**

```
x = 7 = 111
n - 1 = 1 = 1 (binary)
```

Fixed bits: bits 0, 1, 2 (all set in `111`).
Free bits: bit 3, bit 4, bit 5, ...

| bit position | x has this bit? | Free? | remaining | remaining & 1 | Action        | result |
| ------------ | --------------- | ----- | --------- | -------------- | ------------- | ------ |
| 0            | 1               | No    | 1         | --             | Skip          | 0111   |
| 1            | 1               | No    | 1         | --             | Skip          | 0111   |
| 2            | 1               | No    | 1         | --             | Skip          | 0111   |
| 3            | 0               | Yes   | 1         | 1              | Set bit 3     | 1111   |

remaining = 1 >> 1 = 0 → done.

**Result: 1111 = 15** ✓

The two elements: 7 (`0111`) and 15 (`1111`).
AND: `0111 & 1111 = 0111 = 7 = x` ✓
Last element: 15.

When x has many bits set, the free positions are sparse --
the counter must use higher bit positions, making the result larger.

---

**Trace for n = 4, x = 2:**

```
x = 2 = 010
n - 1 = 3 = 11 (binary)
```

Free bits: 0, 2, 3, 4, ...

| bit pos | Fixed? | remaining | Action      | result |
| ------- | ------ | --------- | ----------- | ------ |
| 0       | Free   | 11        | Set (1&1=1) | 011    |
| 1       | Fixed  | 1         | Skip        | 011    |
| 2       | Free   | 1         | Set (1&1=1) | 111    |

remaining = 0 → done.

**Result: 111 = 7** ✓

Elements: 2 (`010`), 3 (`011`), 6 (`110`), 7 (`111`).
AND: `010 & 011 & 110 & 111 = 010 = 2 = x` ✓

---

**Trace for n = 1, x = 5:**

`n - 1 = 0`. No free bits to fill. Result = x = 5.

**Answer: 5** ✓ -- single element, the array is just `[5]`.

---

## 🔍 Why Interleave Into Free Bits?

If we simply did `x + 0, x + 1, x + 2, ...`,
adding to `x` might flip a fixed bit:

```
x = 6 = 110
x + 1 = 111  ← bit 0 set, fine
x + 2 = 1000 ← bit 1 and 2 cleared! AND would lose bits of x.
```

Simple addition doesn't preserve the fixed bits.

By interleaving into ONLY the free positions,
every fixed bit stays set in every element.
The AND is guaranteed to equal `x`.

---

## 🔄 Why `long long`?

The result can exceed 32-bit range.
If `x` has many bits set and `n` is large,
the free bits are sparse and the counter
must reach into high bit positions.

`long long` (64-bit) ensures no overflow.

---

### 🧠 Memory of the Smallest End Law

-   **Fixed bits** (1 in x) → must be 1 in every element → untouchable
-   **Free bits** (0 in x) → used as a counter from 0 to n-1
-   **Last element** = x with `n-1` encoded in the free bit positions
-   **Algorithm:** walk bit positions, skip fixed, fill free with bits of `n-1`
-   This is **bit interleaving** -- placing one number's bits into another's gaps
-   Simple addition (`x + i`) does NOT work -- it can flip fixed bits
-   Use `long long` -- result can exceed 32-bit range
-   **Time:** O(64) = O(1)
-   **Space:** O(1)
-   **Edge cases:**
    -   n = 1 → result = x (no free bits needed)
    -   x = 0 → all bits free → result = n - 1
    -   x = all 1s → no free bits → result grows very large

Thus is remembered the saga of **Minimum Array End**,
where the Oracle saw every integer as a mix
of fixed bits and free bits --
the fixed bits sacred to the AND,
the free bits a canvas for the smallest counter --
and by interleaving `n-1` into the free positions of `x`,
she built the minimum possible last element
of an array whose AND was exactly `x`. 🔢🏁✨
