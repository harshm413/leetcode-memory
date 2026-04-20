## 🔢🔥 _The Count of Burning Flames: The Number of 1 Bits Saga_

> \_"In the Binary Kingdom,
> every integer was a row of torches --
> some lit (1), some dark (0).
>
> The Oracle was commanded:
>
> **'Count the burning flames --
> how many bits are set to 1
> in this number's binary representation?'**
>
> This was the **Hamming Weight** --
> the population count of a binary number.
>
> The naive approach would check each of the 32 bits
> one by one. Correct, but always 32 iterations.
>
> The Oracle knew a faster ritual:
>
> **Brian Kernighan's Algorithm.**
>
> `n & (n - 1)` clears the LOWEST set bit.
> Repeat until n becomes 0.
> The number of iterations = the number of set bits.
>
> If only 3 bits are set in a 32-bit number,
> the loop runs only 3 times -- not 32.
>
> One trick. One loop.
> The flames counted."\_

---

This is the saga of **Number of 1 Bits** (Hamming Weight).

Given an unsigned integer `n`:

-   Return the number of `1` bits in its binary representation.

```
Input:  n = 11  (binary: 1011)
Output: 3

Input:  n = 128  (binary: 10000000)
Output: 1

Input:  n = 2147483645  (binary: 1111111111111111111111111111101)
Output: 30
```

---

## 🧠 Approach One -- Check Each Bit (Naive)

Check the last bit with `n & 1`, then shift right. Repeat 32 times.

```cpp
int hammingWeight_naive(uint32_t n) {
    int count = 0;
    while (n) {
        count += n & 1;
        n >>= 1;
    }
    return count;
}
```

`n & 1` extracts the last bit (0 or 1).
`n >>= 1` shifts right, bringing the next bit into position.

This always runs until `n` becomes 0.
For a 32-bit number, at most 32 iterations.

```
Time:  O(32) = O(1) -- fixed number of bits
Space: O(1)
```

Simple and correct. But we can do better.

---

## 🧠 Approach Two -- Brian Kernighan's Algorithm (Optimal)

The key trick:

> **`n & (n - 1)` clears the lowest set bit of n.**

```
n   = 12 = 1100
n-1 = 11 = 1011

n & (n-1) = 1100 & 1011 = 1000
```

The lowest `1` (at position 2) was cleared.
Everything else stayed the same.

**Why it works:**
`n - 1` flips the lowest set bit to 0
and turns all zeros below it into 1s.
ANDing with `n` clears exactly that lowest set bit
and all the flipped bits below it.

**The algorithm:**

```cpp
int hammingWeight(uint32_t n) {
    int count = 0;
    while (n) {
        n = n & (n - 1);
        count++;
    }
    return count;
}
```

Each iteration removes exactly one set bit.
The loop runs exactly `popcount(n)` times --
the number of 1-bits, not the total number of bits.

If `n` has only 2 set bits in 32 bits → only 2 iterations.

```
Time:  O(k) where k = number of set bits (at most 32)
Space: O(1)
```

---

### 📜 The Scroll of the Burning Torches

```cpp
#include <iostream>
using namespace std;
```

---

## ⚔️ The Oracle's Flame-Counting Ritual

```cpp
int hammingWeight(uint32_t n) {
    int count = 0;
```

The Oracle began with zero flames counted.

---

## 🔁 Clear One Flame at a Time

```cpp
    while (n) {
        n = n & (n - 1);
        count++;
    }
```

Each iteration:
1. `n & (n - 1)` cleared the lowest set bit.
2. `count++` recorded that one flame was extinguished.

The loop continued until `n` became 0 --
all flames had been counted and cleared.

> _"Each pass through the loop
> extinguishes exactly one flame.
> When darkness falls -- all flames are counted."_

---

## 🏁 Return the Count

```cpp
    return count;
}
```

The total number of set bits was returned.

---

### 🎺 The Trial of the Burning Torches

```cpp
int main() {
    cout << hammingWeight(11)  << endl; // expected: 3
    cout << hammingWeight(128) << endl; // expected: 1
    cout << hammingWeight(2147483645) << endl; // expected: 30
    cout << hammingWeight(0)   << endl; // expected: 0
    cout << hammingWeight(15)  << endl; // expected: 4
    return 0;
}
```

---

**Full trace for n = 11 (binary 1011):**

| Iteration | n (binary) | n - 1 (binary) | n & (n-1) | Bit cleared | count |
| --------- | ---------- | --------------- | --------- | ----------- | ----- |
| 1         | 1011       | 1010            | 1010      | bit 0       | 1     |
| 2         | 1010       | 1001            | 1000      | bit 1       | 2     |
| 3         | 1000       | 0111            | 0000      | bit 3       | 3     |
| 4         | 0000       | --              | exit loop | --          | **3** |

**Answer: 3** ✓

Three set bits in `1011`. Three iterations. Not 32.

---

**Full trace for n = 128 (binary 10000000):**

| Iteration | n (binary) | n & (n-1) | count |
| --------- | ---------- | --------- | ----- |
| 1         | 10000000   | 00000000  | 1     |
| 2         | 00000000   | exit loop | **1** |

**Answer: 1** ✓

Only one bit set. One iteration. Brian Kernighan shines here --
the naive approach would still check all 8 (or 32) positions.

---

**Full trace for n = 15 (binary 1111):**

| Iteration | n (binary) | n & (n-1) | count |
| --------- | ---------- | --------- | ----- |
| 1         | 1111       | 1110      | 1     |
| 2         | 1110       | 1100      | 2     |
| 3         | 1100       | 1000      | 3     |
| 4         | 1000       | 0000      | 4     |
| 5         | 0000       | exit loop | **4** |

**Answer: 4** ✓

All 4 bits set. Four iterations.

---

**Trace for n = 0:**

Loop never runs (n is already 0). **Answer: 0** ✓

---

## 🔍 Naive vs Brian Kernighan -- When Does It Matter?

| n (decimal) | Binary              | Set bits | Naive iterations | Kernighan iterations |
| ----------- | ------------------- | -------- | ---------------- | -------------------- |
| 128         | 10000000            | 1        | 8                | **1**                |
| 255         | 11111111            | 8        | 8                | 8                    |
| 1024        | 10000000000         | 1        | 11               | **1**                |
| 2^31        | 10000...0 (32 bits) | 1        | 32               | **1**                |

When few bits are set, Kernighan is dramatically faster.
When all bits are set, both are the same.

For 32-bit integers, the difference is at most 32 vs k.
Both are O(1) in the big picture -- but Kernighan is elegant.

---

## 🔄 Built-in Alternative

C++ provides a hardware-accelerated popcount:

```cpp
int count = __builtin_popcount(n);       // for 32-bit
int count = __builtin_popcountll(n);     // for 64-bit
```

This compiles to a single CPU instruction on modern hardware.
O(1) in the truest sense.

Use it in competitive programming.
Implement Kernighan's in interviews to show understanding.

---

## 🌐 Where Hamming Weight Appears

| Problem              | Connection                              |
| -------------------- | --------------------------------------- |
| Number of 1 Bits     | Direct application                      |
| Counting Bits (338)  | Hamming weight for every number 0 to n  |
| Hamming Distance     | `popcount(x ^ y)` = differing bits      |
| Power of Two         | `popcount(n) == 1`                      |
| Reverse Bits         | Bit-by-bit construction                 |

Hamming weight is the foundation for all bit-counting problems.

---

### 🧠 Memory of the Flame-Counting Law

-   **Brian Kernighan's:** `n = n & (n - 1)` clears the lowest set bit
-   Loop runs exactly `k` times where k = number of set bits
-   **Naive:** `count += n & 1; n >>= 1;` -- checks every bit position
-   **Built-in:** `__builtin_popcount(n)` -- single CPU instruction
-   `n & (n-1)` works because `n-1` flips the lowest set bit
    and all bits below it
-   **Time:** O(k) for Kernighan, O(32) for naive, O(1) for built-in
-   **Space:** O(1) for all approaches
-   **Edge cases:**
    -   n = 0 → 0 bits set
    -   n = all 1s (0xFFFFFFFF) → 32 bits set
    -   Power of 2 → exactly 1 bit set

Thus is remembered the saga of **Number of 1 Bits**,
where the Oracle did not inspect every torch in the row
but instead used Brian Kernighan's elegant trick --
clearing the lowest burning flame with each pass,
counting each extinction --
until all flames were dark
and the total was known,
in exactly as many steps
as there were flames to count. 🔢🔥✨
