## ➕⚡ _Addition Without Arithmetic: The Sum of Two Integers Saga_

> \_"In the Binary Kingdom,
> the Oracle was given a cruel constraint:
>
> **'Add two integers together.
> But you may NOT use the + or - operator.'**
>
> No addition. No subtraction.
> Only bitwise operations and shifts.
>
> The Oracle smiled.
>
> She knew that addition at the hardware level
> was never done with a `+` symbol.
> It was done with **gates** -- AND gates and XOR gates.
>
> **XOR gives the sum without the carry.**
> `1 ^ 1 = 0` (like 1+1=0 with carry).
> `1 ^ 0 = 1` (like 1+0=1 no carry).
>
> **AND gives the carry bits.**
> `1 & 1 = 1` (carry generated).
> `1 & 0 = 0` (no carry).
>
> **Shift the carry left by 1** (carry goes to the next column).
>
> **Repeat until no carry remains.**
>
> This is how CPUs actually add numbers.
> The Oracle was simply doing what the hardware does --
> one layer of gates at a time."\_

---

This is the saga of **Sum of Two Integers**.

Given two integers `a` and `b`:

-   Return their **sum** without using `+` or `-`.

```
Input:  a = 1, b = 2
Output: 3

Input:  a = 2, b = 3
Output: 5

Input:  a = -1, b = 1
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- XOR is Sum, AND is Carry

In binary addition of two bits:

```
0 + 0 = 0, carry 0     →  0 XOR 0 = 0,  0 AND 0 = 0
0 + 1 = 1, carry 0     →  0 XOR 1 = 1,  0 AND 1 = 0
1 + 0 = 1, carry 0     →  1 XOR 0 = 1,  1 AND 0 = 0
1 + 1 = 0, carry 1     →  1 XOR 1 = 0,  1 AND 1 = 1
```

XOR behaves exactly like addition **ignoring carries**.
AND detects exactly where carries are **generated**.

But carries propagate to the next column (one position left).
So the carry must be **shifted left by 1**.

The algorithm:

```
While carry is not zero:
  sum_without_carry = a XOR b
  carry             = (a AND b) << 1
  a = sum_without_carry
  b = carry
  Repeat (the carry might generate new carries)

When carry = 0, a holds the final sum.
```

This is a **ripple carry adder** -- the same circuit
that lives inside every CPU's arithmetic unit.

```
Time:  O(32) = O(1) -- carry propagates at most 32 positions
Space: O(1)
```

---

### 📜 The Scroll of the Gate Circuit

```cpp
#include <iostream>
using namespace std;
```

---

## ⚔️ The Oracle's Gateless Addition Ritual

```cpp
int getSum(int a, int b) {
    while (b != 0) {
        int carry = a & b;
        a = a ^ b;
        b = carry << 1;
    }
    return a;
}
```

Each iteration simulated one layer of the adder circuit:

**`a & b`** -- the AND of `a` and `b` found every position
where BOTH bits were 1. These are the carry bits.

**`a ^ b`** -- the XOR of `a` and `b` computed the sum
at every position, ignoring carries.
This became the new `a`.

**`carry << 1`** -- the carry bits were shifted left by 1,
because a carry from column `i` affects column `i + 1`.
This became the new `b`.

The loop repeated because the shifted carry
might create NEW carries when added to `a`.

When `b` (the carry) became 0 -- no more carries to propagate.
`a` held the final sum.

> _"XOR adds without carrying.
> AND finds the carries.
> Shift the carries left.
> Repeat until silence --
> when no carry remains,
> the sum is complete."_

---

### 🎺 The Trial of the Gate Circuit

```cpp
int main() {
    cout << getSum(1, 2)   << endl; // expected: 3
    cout << getSum(2, 3)   << endl; // expected: 5
    cout << getSum(-1, 1)  << endl; // expected: 0
    cout << getSum(0, 5)   << endl; // expected: 5
    cout << getSum(-2, -3) << endl; // expected: -5
    return 0;
}
```

---

**Full trace for a = 2, b = 3:**

```
a = 2 = 010
b = 3 = 011
```

| Iteration | a (binary) | b (binary) | carry = a & b | a = a ^ b | b = carry << 1 |
| --------- | ---------- | ---------- | ------------- | --------- | -------------- |
| 1         | 010        | 011        | 010           | 001       | 100            |
| 2         | 001        | 100        | 000           | 101       | 000            |
| 3         | 101        | 000        | b=0 → exit    | --        | --             |

**Answer: a = 101 = 5** ✓

Iteration 1: XOR gives `001` (sum without carry). AND gives `010` (carry at position 1). Shift carry left → `100`.
Iteration 2: XOR gives `101`. AND gives `000` (no carry). Done.

---

**Full trace for a = 1, b = 2:**

```
a = 1 = 001
b = 2 = 010
```

| Iteration | a      | b      | carry  | a = a^b | b = carry<<1 |
| --------- | ------ | ------ | ------ | ------- | ------------ |
| 1         | 001    | 010    | 000    | 011     | 000          |

**Answer: 011 = 3** ✓

No carry at all -- the bits don't overlap. One iteration.

---

**Full trace for a = 7, b = 5:**

```
a = 7 = 0111
b = 5 = 0101
```

| Iter | a      | b      | carry  | a = a^b | b = carry<<1 |
| ---- | ------ | ------ | ------ | ------- | ------------ |
| 1    | 0111   | 0101   | 0101   | 0010    | 1010         |
| 2    | 0010   | 1010   | 0010   | 1000    | 0100         |
| 3    | 1000   | 0100   | 0000   | 1100    | 0000         |

**Answer: 1100 = 12** ✓ (7 + 5 = 12)

The carry rippled through three iterations before settling.

---

**Full trace for a = -1, b = 1 (32-bit two's complement):**

```
a = -1 = 11111111111111111111111111111111
b =  1 = 00000000000000000000000000000001
```

| Iter | carry = a & b                      | a = a ^ b                          | b = carry << 1                     |
| ---- | ---------------------------------- | ---------------------------------- | ---------------------------------- |
| 1    | 00000000000000000000000000000001   | 11111111111111111111111111111110   | 00000000000000000000000000000010   |
| 2    | 00000000000000000000000000000010   | 11111111111111111111111111111100   | 00000000000000000000000000000100   |
| ...  | carry ripples through all 32 bits  | ...                                | ...                                |
| 32   | 10000000000000000000000000000000   | 01111111111111111111111111111111   | 00000000000000000000000000000000   |

Wait -- actually the carry propagates all the way through 32 bits
and overflows out of the 32-bit range, leaving `a = 0`.

**Answer: 0** ✓ (-1 + 1 = 0)

In two's complement, `-1` is all 1s. Adding 1 flips every bit
through carry propagation, resulting in all 0s (with overflow discarded).

---

## 🔍 Why Does This Work for Negative Numbers?

Two's complement makes it work automatically.

In two's complement:
-   `-1` = `11111111111111111111111111111111`
-   `-5` = `11111111111111111111111111111011`

XOR and AND operate on the raw bits.
Two's complement ensures that the bit patterns
represent the correct signed values.

The algorithm doesn't need to know if numbers are positive or negative.
It just manipulates bits -- and two's complement handles the rest.

---

## 🔄 Why the Loop Always Terminates

Each iteration, the carry shifts left by 1.
In a 32-bit integer, the carry can shift at most 32 times
before it shifts entirely out of the 32-bit range (becomes 0).

In practice, the carry often dies much sooner --
whenever no two adjacent bits both need to carry.

Worst case: 32 iterations (carry ripples through all bits).
This is O(1) for fixed-width integers.

---

## 🌐 The Hardware Connection

This algorithm IS how hardware adds numbers:

```
Half Adder:   sum = a XOR b,  carry = a AND b
Full Adder:   sum = a XOR b XOR carry_in
              carry_out = (a AND b) OR (carry_in AND (a XOR b))
```

A **ripple carry adder** chains full adders together.
Each iteration of our loop simulates one ripple of the carry.

Modern CPUs use **carry-lookahead adders** that compute
all carries in parallel -- O(log N) gate depth instead of O(N).
But the logical principle is the same.

---

### 🧠 Memory of the Gateless Addition Law

-   **XOR** = sum without carry (`a ^ b`)
-   **AND** = carry bits (`a & b`)
-   **Shift carry left** by 1 (`carry << 1`) -- carry affects next column
-   **Loop** until carry = 0 -- no more propagation needed
-   Works for **positive and negative** numbers via two's complement
-   **Terminates** in at most 32 iterations (carry shifts out)
-   This IS how hardware adds -- XOR gates + AND gates + shift
-   **Time:** O(32) = O(1)
-   **Space:** O(1)
-   **Edge cases:**
    -   One operand is 0 → XOR returns the other, AND returns 0, done in 1 step
    -   Both negative → two's complement handles it
    -   Result overflows 32 bits → same as hardware overflow behavior

Thus is remembered the saga of **Sum of Two Integers**,
where the Oracle added two numbers
without ever using the `+` operator --
instead simulating the CPU's own adder circuit,
XORing for the sum, ANDing for the carry,
shifting the carry left and repeating --
until the carry rippled to silence
and the sum stood complete
in nothing but gates and shifts. ➕⚡✨
