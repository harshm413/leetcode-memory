## ⚔️ _The Battle of Bits: The Sum of Two Integers Saga_

> \*"In the Binary Kingdom,
> two warriors stood—
> each made not of flesh,
> but of bits: 0s and 1s.
>
> They wished to unite their strengths,
> yet the laws of the land forbade ordinary addition.
> No '+'… no arithmetic scrolls…
> Only **bitwise magic** could bind them.
>
> Thus the Oracle taught them
> the ancient dance of XOR and Carry—
> a ritual where sums and carries
> march separately until peace is found."\*

---

In the Realm of Pure Bits,
two integer spirits, `a` and `b`,
sought to join their powers into a single number.

But the King forbade the use of the **+** or **-** sigils.
Only the ancient ways of **bitwise operations**
could be invoked to achieve their union.

The Oracle knew the truth:

-   **XOR** (`a ^ b`) gives the _sum without carries_.
-   **AND + shift** (`(a & b) << 1`) gives the _carries themselves_.
-   Repeating this ritual merges all carries
    until nothing remains but the true sum.

Thus began the saga of **Sum of Two Integers**.

---

### 📜 The Scroll of Bit-Magic

```cpp
#include <iostream>
using namespace std;
```

The battlefield of bits was prepared.

---

## ⚡ The Oracle’s Ritual of Bitwise Union

_Repeat until no carry remains_

```cpp
int getSum(int a, int b) {
    while (b != 0) {
        unsigned carry = (unsigned)(a & b) << 1;
        a = a ^ b;
        b = carry;
    }
    return a;
}
```

### 🔍 Step 1 — The Clash of Bits

`a ^ b`
The XOR ritual combined the two warriors
**without accounting for carries**—
a raw sum, bit by bit.

### 🔥 Step 2 — Summon the Carries

`(a & b) << 1`
Where both had a ‘1’,
a carry was born
and shifted left to its rightful place.

### 🔄 Step 3 — Repeat the Dance

The sum became the new `a`.
The carries became the new `b`.
The ritual repeated
until all carries were consumed
and harmony prevailed.

When `b` became zero,
all conflicts resolved,
and `a` alone held the true sum.

---

### 🎺 The Trial of the Binary Warriors

```cpp
int main() {
    cout << getSum(1, 2) << endl;   // 3
    cout << getSum(-5, 7) << endl;  // 2
    return 0;
}
```

The Oracle tested:

-   1 + 2 → the dance produced **3**
-   -5 + 7 → the dance produced **2**

Both unions succeeded
without a single use of forbidden arithmetic.

---

### 🧠 Memory of the Bitwise Dance

-   **XOR** → adds bits without carry
-   **AND + left shift** → computes carries
-   Repeat until no carry remains
-   Works with negative numbers via 2’s complement
-   **Time:** O(1) for fixed bit-width,
    O(log n) conceptually due to carry propagation
-   **Space:** O(1)

Thus is remembered the saga of **Sum of Two Integers**,
where two bit-warriors unite through XOR and Carry,
and the Oracle weaves their powers
into a single harmonious number—
without ever touching the forbidden symbol '+'. ⚔️✨
