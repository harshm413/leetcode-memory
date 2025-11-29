## 🔥 _The Flames Within the Number: The Number of 1 Bits Saga_

> _"Every number is a silent torch,
> its binary form a row of flames and shadows.
> Some torches burn brightly with many sparks of ‘1’,
> while others glow faintly.
> The Oracle was tasked to count these flames —
> the bits of fire —
> to know the true heat within the number."_

---

In the Realm of Binary,
every integer hid its soul in a sequence of bits:
`0`s as darkness,
`1`s as sparks of living flame.

The Oracle received a number `n`
and was asked:

> _“How many flames — how many ‘1’ bits —
> burn inside its binary form?”_

To answer this, she used bit magic —
shifting, masking,
and even a legendary trick known as **n & (n - 1)**
that extinguishes the _lowest_ burning flame each time.

Thus began the saga of **Number of 1 Bits**,
also known as the **Hamming Weight**.

---

### 📜 The Scroll of Binary Flame Rituals

```cpp
#include <iostream>
using namespace std;
```

The number would be inspected bit by bit,
until all sparks were counted.

---

## 🔥 Method 1 — The Oracle’s Bit-By-Bit Inspection

_Shift through all bits_

```cpp
int hammingWeight(uint32_t n) {
    int count = 0;
```

She began with a count of zero flames.

---

```cpp
    while (n != 0) {
        count += (n & 1);
        n >>= 1;
    }
    return count;
}
```

One by one, she checked the **least significant bit**:

-   If `(n & 1)` was `1`, a flame burned → count it.
-   Shift right to inspect the next bit.

When all bits faded,
the count revealed the number’s inner fire.

---

## ⚡ Method 2 — The Legendary Flame-Extinguishing Trick

_Use n & (n - 1) to remove one ‘1’ at a time_

```cpp
int hammingWeightFast(uint32_t n) {
    int count = 0;
    while (n) {
        n &= (n - 1); // extinguish the lowest flame
        count++;
    }
    return count;
}
```

With this ancient spell,
each iteration removed **exactly one burning bit**,
making the loop run once per `1` in the number.

A technique revered by bit-masters.

---

### 🎺 The Trial of the Binary Torch

```cpp
int main() {
    uint32_t n = 0b0000000000000000000000000001011; // 11 in binary
    cout << hammingWeight(n) << endl;      // expected: 3
    cout << hammingWeightFast(n) << endl;  // expected: 3
    return 0;
}
```

The torch `000...1011` held three sparks of fire.
Both methods revealed the same truth: **3 flames**.

---

### 🧠 Memory of the Bit-Flame Ritual

-   **Method 1:** shift and check each bit

    -   Simple, checks all 32 bits
    -   Time: O(32)

-   **Method 2:** extinguish flames using `n &= (n - 1)`

    -   Runs once per `1` bit
    -   Time: O(number of 1 bits)

-   Both produce the number’s Hamming Weight.

Thus is remembered the saga of **Number of 1 Bits**,
where numbers reveal their secret flames,
one spark at a time—
until the Oracle counts the burning truth hidden within. 🔥✨
