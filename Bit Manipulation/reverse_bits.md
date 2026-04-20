## 🔄⚡ _The Mirror of the Binary Kingdom: The Reverse Bits Saga_

> \_"In the Binary Kingdom,
> every 32-bit integer was a row of torches --
> some lit, some dark, stretching 32 positions wide.
>
> The Oracle was commanded:
>
> **'Reverse the entire row.
> The leftmost torch becomes the rightmost.
> The rightmost becomes the leftmost.
> Every torch swaps with its mirror position.'**
>
> The Oracle devised a simple ritual:
>
> **Build the reversed number bit by bit.
> Extract the last bit of the input.
> Place it into the result from the left.
> Shift the input right. Shift the result left.
> Repeat 32 times.**
>
> One bit at a time,
> the mirror image was constructed --
> the first becoming the last,
> the last becoming the first."\_

---

This is the saga of **Reverse Bits**.

Given a 32-bit unsigned integer `n`:

-   Reverse all 32 bits and return the result.

```
Input:  n = 43261596
Binary: 00000010100101000001111010011100
Output: 964176192
Binary: 00111001011110000010100101000000
```

```
Input:  n = 4294967293
Binary: 11111111111111111111111111111101
Output: 3221225471
Binary: 10111111111111111111111111111111
```

---

## 🧠 The Oracle's Core Insight -- Extract Last, Place First

The algorithm processes one bit per iteration, 32 times:

```
For each of the 32 bits:
  1. Extract the last bit of n:       bit = n & 1
  2. Shift result left to make room:  result <<= 1
  3. Place the bit into result:       result |= bit
  4. Shift n right to expose next:    n >>= 1
```

After 32 iterations, every bit has been moved
from its original position to its mirror position.

**Why this works:**

The first bit extracted (position 0 of `n`)
is placed at position 0 of `result`.
But then `result` is shifted left 31 more times --
so that bit ends up at position 31.

The last bit extracted (position 31 of `n`)
is placed at position 0 of `result` with no further shifts.

Position `i` in `n` becomes position `31 - i` in `result`.
That's a perfect reversal.

```
Time:  O(32) = O(1) -- fixed 32 iterations
Space: O(1)
```

---

### 📜 The Scroll of the Mirror

```cpp
#include <iostream>
using namespace std;
```

---

## ⚔️ The Oracle's Bit-Reversal Ritual

```cpp
uint32_t reverseBits(uint32_t n) {
    uint32_t result = 0;
```

The Oracle began with a blank mirror -- all 32 bits dark.

---

## 🔁 Process All 32 Bits

```cpp
    for (int i = 0; i < 32; i++) {
        result <<= 1;
        result |= (n & 1);
        n >>= 1;
    }
    return result;
}
```

Each iteration performed three operations:

**`result <<= 1`** -- shift the result left by 1.
This made room for the next bit at position 0.
All previously placed bits moved one position higher.

**`result |= (n & 1)`** -- extract the last bit of `n`
and place it at position 0 of `result`.
`n & 1` gives 0 or 1. OR-ing it into `result` sets that bit.

**`n >>= 1`** -- shift `n` right by 1.
The next bit of `n` moved into position 0,
ready to be extracted in the next iteration.

> _"Shift the mirror left to make room.
> Pluck the last torch from the original.
> Plant it in the mirror.
> Shift the original right to expose the next.
> Repeat until all 32 torches have crossed."_

---

### 🎺 The Trial of the Mirror

```cpp
int main() {
    cout << reverseBits(43261596)  << endl; // expected: 964176192
    cout << reverseBits(4294967293u) << endl; // expected: 3221225471
    cout << reverseBits(0)         << endl; // expected: 0
    cout << reverseBits(1)         << endl; // expected: 2147483648
    return 0;
}
```

---

**Full trace for n = 13 (binary 00000000000000000000000000001101):**

Showing only the last 4 significant bits for clarity:

| Iter | n (last 4) | n & 1 | result (building) | Action                    |
| ---- | ---------- | ----- | ----------------- | ------------------------- |
| 1    | 1101       | 1     | 1                 | Extract 1, place at pos 0 |
| 2    | 0110       | 0     | 10                | Extract 0, place at pos 0 |
| 3    | 0011       | 1     | 101               | Extract 1, place at pos 0 |
| 4    | 0001       | 1     | 1011              | Extract 1, place at pos 0 |
| 5-32 | 0000       | 0     | 1011000...0       | 28 more zeros appended    |

Result: `10110000000000000000000000000000`

In decimal: `2952790016`

The original `1101` (13) became `1011...0` reversed across 32 bits.

---

**Trace for n = 1:**

```
Original: 00000000000000000000000000000001
Reversed: 10000000000000000000000000000000
```

The lone `1` at position 0 moved to position 31.
Result = 2^31 = **2147483648** ✓

---

**Trace for n = 0:**

All bits are 0. Reversing all zeros gives all zeros.
Result = **0** ✓

---

**Trace for n = 4294967295 (all 1s, 0xFFFFFFFF):**

All 32 bits are 1. Reversing all ones gives all ones.
Result = **4294967295** ✓ -- unchanged, a palindrome in binary.

---

## 🔍 Step-by-Step: Why Order Matters

The order of the three operations inside the loop matters:

```cpp
// CORRECT order:
result <<= 1;       // 1. Make room
result |= (n & 1);  // 2. Place the bit
n >>= 1;            // 3. Advance to next bit
```

If you placed the bit BEFORE shifting result:

```cpp
// WRONG:
result |= (n & 1);  // Place bit at pos 0
result <<= 1;       // Oops -- just shifted it to pos 1!
```

The first bit would end up at position 32 (shifted out),
and the last bit would be at position 1 instead of 0.
Off by one everywhere.

Always shift result first, then place the bit.

---

## 🔄 Alternative -- Swap Approach

Instead of building bit by bit, you can swap bits
from opposite ends:

```cpp
uint32_t reverseBits(uint32_t n) {
    uint32_t result = 0;
    for (int i = 0; i < 32; i++) {
        result |= ((n >> i) & 1) << (31 - i);
    }
    return result;
}
```

For each bit position `i`:
-   Extract bit `i` from `n`: `(n >> i) & 1`
-   Place it at position `31 - i` in result: `<< (31 - i)`

Same O(32) time. More explicit about the mirror mapping.

---

## 🌐 Where Reverse Bits Appears

| Problem              | Connection                              |
| -------------------- | --------------------------------------- |
| Reverse Bits         | Direct application                      |
| Reverse Integer      | Similar idea but decimal digits          |
| Number of 1 Bits     | Often paired -- reverse then count      |
| Bit manipulation DP  | Reversed number as a key/state          |

---

### 🧠 Memory of the Mirror Law

-   **32 iterations** -- one per bit, always exactly 32
-   **Each iteration:**
    1. `result <<= 1` -- shift left to make room
    2. `result |= (n & 1)` -- extract last bit of n, place in result
    3. `n >>= 1` -- advance n to expose the next bit
-   **Order matters:** shift result BEFORE placing the bit
-   Bit at position `i` in `n` ends up at position `31 - i` in result
-   **Alternative:** `result |= ((n >> i) & 1) << (31 - i)` -- explicit mapping
-   **Time:** O(32) = O(1)
-   **Space:** O(1)
-   **Edge cases:**
    -   n = 0 → 0 (all zeros reversed = all zeros)
    -   n = 1 → 2^31 (lone bit moves to the other end)
    -   All 1s → all 1s (binary palindrome)
    -   Already a palindrome → unchanged

Thus is remembered the saga of **Reverse Bits**,
where the Oracle built a mirror of the binary kingdom
one torch at a time --
plucking the last flame from the original,
planting it in the mirror,
shifting both sides to expose the next --
until all 32 torches had crossed
and the mirror image stood complete,
every bit in its reflected position. 🔄⚡✨
