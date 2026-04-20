# 🧠⚡ The Complete Guide to Bit Manipulation — From Zero to Mastery

> _"Numbers are not just values. They are patterns of bits —
> ones and zeros dancing in binary.
> To master bit manipulation is to see numbers
> not as humans see them, but as machines do."_

---

## Table of Contents

1. [Binary Number System — The Foundation](#1-binary-number-system--the-foundation)
2. [How Integers Are Stored — Signed vs Unsigned](#2-how-integers-are-stored--signed-vs-unsigned)
3. [The Six Bitwise Operators](#3-the-six-bitwise-operators)
4. [Shift Operators — Left and Right](#4-shift-operators--left-and-right)
5. [Essential Bit Tricks — The Toolkit](#5-essential-bit-tricks--the-toolkit)
6. [Checking, Setting, Clearing, Toggling Bits](#6-checking-setting-clearing-toggling-bits)
7. [Counting Bits — Hamming Weight and Beyond](#7-counting-bits--hamming-weight-and-beyond)
8. [Powers of Two — The Bit Perspective](#8-powers-of-two--the-bit-perspective)
9. [XOR — The Swiss Army Knife](#9-xor--the-swiss-army-knife)
10. [Common Patterns in LeetCode Problems](#10-common-patterns-in-leetcode-problems)
11. [Bit Manipulation Cheat Sheet](#11-bit-manipulation-cheat-sheet)

---

## 1. Binary Number System — The Foundation

Every integer is stored as a sequence of **bits** (binary digits).
Each bit is either `0` or `1`.

### Decimal to Binary

Each position represents a power of 2 (right to left):

```
Position:   7    6    5    4    3    2    1    0
Power:     128   64   32   16   8    4    2    1
```

**Example: 13 in binary**

```
13 = 8 + 4 + 1 = 2³ + 2² + 2⁰

Position:  3  2  1  0
Bits:      1  1  0  1

13 in binary = 1101
```

**Example: 42 in binary**

```
42 = 32 + 8 + 2 = 2⁵ + 2³ + 2¹

Position:  5  4  3  2  1  0
Bits:      1  0  1  0  1  0

42 in binary = 101010
```

### Binary to Decimal

Multiply each bit by its position's power of 2 and sum:

```
1011 = 1×2³ + 0×2² + 1×2¹ + 1×2⁰
     = 8 + 0 + 2 + 1
     = 11
```

### Quick Reference — First 16 Numbers

```
 0 = 0000      8 = 1000
 1 = 0001      9 = 1001
 2 = 0010     10 = 1010
 3 = 0011     11 = 1011
 4 = 0100     12 = 1100
 5 = 0101     13 = 1101
 6 = 0110     14 = 1110
 7 = 0111     15 = 1111
```

Notice: powers of 2 have exactly ONE bit set: `1, 10, 100, 1000, ...`

---

## 2. How Integers Are Stored — Signed vs Unsigned

### 32-bit Integers (C++ `int`)

A 32-bit integer uses 32 bits. The leftmost bit is the **sign bit**:

```
Positive: sign bit = 0
Negative: sign bit = 1
```

**Range of 32-bit signed int:**
```
-2,147,483,648  to  2,147,483,647
(-2³¹)          to  (2³¹ - 1)
```

### Two's Complement — How Negatives Work

Negative numbers are stored in **two's complement**:

```
To get -x from x:
  1. Flip all bits (bitwise NOT)
  2. Add 1
```

**Example: -5 in 8-bit**

```
 5 = 00000101
~5 = 11111010  (flip all bits)
+1 = 11111011  (add 1)

-5 = 11111011
```

**Why two's complement?**
Because addition works the same for positive and negative numbers.
The hardware doesn't need separate circuits for subtraction.

```
  5 + (-5):
  00000101
+ 11111011
----------
 100000000  (9 bits — the overflow bit is discarded)
= 00000000  = 0  ✓
```

### Key Insight for Problems

```
-1 in binary (32-bit) = 11111111111111111111111111111111  (all 1s)
 0 in binary (32-bit) = 00000000000000000000000000000000  (all 0s)
```

`-1` has ALL bits set. This is useful in many bit tricks.

---

## 3. The Six Bitwise Operators

### AND (`&`) — Both Must Be 1

```
1 & 1 = 1
1 & 0 = 0
0 & 1 = 0
0 & 0 = 0
```

**Example:**
```
  1010  (10)
& 1100  (12)
------
  1000  (8)
```

**Use cases:**
- Check if a bit is set: `n & (1 << i)`
- Clear bits: `n & mask`
- Extract the lowest set bit: `n & (-n)`

---

### OR (`|`) — At Least One Must Be 1

```
1 | 1 = 1
1 | 0 = 1
0 | 1 = 1
0 | 0 = 0
```

**Example:**
```
  1010  (10)
| 1100  (12)
------
  1110  (14)
```

**Use cases:**
- Set a bit: `n | (1 << i)`
- Combine flags: `flags | NEW_FLAG`

---

### XOR (`^`) — Exactly One Must Be 1

```
1 ^ 1 = 0
1 ^ 0 = 1
0 ^ 1 = 1
0 ^ 0 = 0
```

**Example:**
```
  1010  (10)
^ 1100  (12)
------
  0110  (6)
```

**Use cases:**
- Find the single number (pairs cancel)
- Swap without temp: `a ^= b; b ^= a; a ^= b;`
- Toggle a bit: `n ^ (1 << i)`

---

### NOT (`~`) — Flip Every Bit

```
~1 = 0
~0 = 1
```

**Example (8-bit):**
```
~ 00001010  (10)
= 11110101  (-11 in signed, 245 in unsigned)
```

**Key identity:** `~n = -(n + 1)`

```
~0  = -1
~5  = -6
~(-3) = 2
```

---

### Left Shift (`<<`) — Multiply by Powers of 2

```
n << k  =  n × 2ᵏ
```

**Example:**
```
3 << 1 = 6     (3 × 2)
3 << 2 = 12    (3 × 4)
1 << 5 = 32    (1 × 32)
```

Bits shift left, zeros fill from the right:
```
  0011  (3)
<< 2
  1100  (12)
```

**Use case:** `1 << i` creates a mask with only bit `i` set.

---

### Right Shift (`>>`) — Divide by Powers of 2

```
n >> k  =  n / 2ᵏ  (integer division, floor)
```

**Example:**
```
12 >> 1 = 6    (12 / 2)
12 >> 2 = 3    (12 / 4)
7  >> 1 = 3    (7 / 2, floor)
```

Bits shift right, behavior of leftmost bit depends on signed/unsigned:
- **Unsigned / positive:** zeros fill from the left (logical shift)
- **Negative signed:** sign bit fills from the left (arithmetic shift)

---

## 4. Shift Operators — Left and Right

### Creating Bit Masks with Shifts

The expression `1 << i` creates a number with ONLY bit `i` set:

```
1 << 0 = 0001 = 1
1 << 1 = 0010 = 2
1 << 2 = 0100 = 4
1 << 3 = 1000 = 8
```

This is the fundamental building block for all bit manipulation.

### Extracting the i-th Bit

```cpp
int bit = (n >> i) & 1;
```

Shift `n` right by `i` positions, then AND with 1.
This isolates the bit at position `i`.

```
n = 13 = 1101

Bit 0: (1101 >> 0) & 1 = 1101 & 1 = 1
Bit 1: (1101 >> 1) & 1 = 0110 & 1 = 0
Bit 2: (1101 >> 2) & 1 = 0011 & 1 = 1
Bit 3: (1101 >> 3) & 1 = 0001 & 1 = 1
```

### Multiplication and Division by Powers of 2

```cpp
n * 2   ==  n << 1
n * 4   ==  n << 2
n * 8   ==  n << 3
n / 2   ==  n >> 1
n / 4   ==  n >> 2
```

Shifts are faster than multiplication/division on hardware.
Compilers often optimize `n * 2` to `n << 1` automatically.

---

## 5. Essential Bit Tricks — The Toolkit

### Trick 1: Check if a Number is Even or Odd

```cpp
if (n & 1)  // odd  (last bit is 1)
else        // even (last bit is 0)
```

The last bit determines parity. No modulo needed.

```
5 = 101  → last bit 1 → odd
6 = 110  → last bit 0 → even
```

---

### Trick 2: Swap Two Numbers Without a Temp Variable

```cpp
a ^= b;
b ^= a;
a ^= b;
```

**How it works:**
```
a = 5 (101), b = 3 (011)

a ^= b  →  a = 101 ^ 011 = 110 (6)
b ^= a  →  b = 011 ^ 110 = 101 (5)  ← b is now original a
a ^= b  →  a = 110 ^ 101 = 011 (3)  ← a is now original b
```

Neat trick, but in practice `swap(a, b)` is clearer.

---

### Trick 3: Get the Lowest Set Bit

```cpp
int lowestBit = n & (-n);
```

This isolates the rightmost `1` bit.

```
n  = 12 = 1100
-n = -12 = ...0100 (two's complement)

n & (-n) = 1100 & 0100 = 0100 = 4
```

The lowest set bit of 12 is at position 2 (value 4).

**Why it works:** `-n = ~n + 1`. Flipping all bits and adding 1
turns all trailing zeros into ones and flips the lowest set bit's neighbors,
leaving only the lowest set bit in common.

---

### Trick 4: Remove the Lowest Set Bit

```cpp
n = n & (n - 1);
```

This clears the rightmost `1` bit.

```
n   = 12 = 1100
n-1 = 11 = 1011

n & (n-1) = 1100 & 1011 = 1000 = 8
```

The lowest `1` (at position 2) was cleared.

**Why it works:** `n - 1` flips the lowest set bit and all bits below it.
ANDing with `n` clears exactly that lowest set bit.

**Use case:** Count set bits by repeatedly clearing the lowest one:

```cpp
int count = 0;
while (n) {
    n = n & (n - 1);
    count++;
}
```

Each iteration removes one set bit. The loop runs exactly `popcount(n)` times.

---

### Trick 5: Check if n is a Power of 2

```cpp
bool isPowerOf2 = (n > 0) && (n & (n - 1)) == 0;
```

A power of 2 has exactly ONE bit set: `1, 10, 100, 1000, ...`

`n & (n - 1)` removes the lowest set bit.
If the result is 0, there was only one bit set → power of 2.

```
8  = 1000,  8 & 7  = 1000 & 0111 = 0000 → power of 2 ✓
6  = 0110,  6 & 5  = 0110 & 0101 = 0100 → not power of 2 ✗
```

---

### Trick 6: Turn Off All Bits Except the Highest

```cpp
// For 32-bit integers:
int highestBit(int n) {
    n |= (n >> 1);
    n |= (n >> 2);
    n |= (n >> 4);
    n |= (n >> 8);
    n |= (n >> 16);
    return (n + 1) >> 1;
}
```

This fills all bits below the highest set bit with 1s,
then extracts just the highest bit.

---

## 6. Checking, Setting, Clearing, Toggling Bits

These four operations are the bread and butter of bit manipulation.
All use `1 << i` as the mask for bit position `i`.

### Check if Bit i is Set

```cpp
bool isSet = (n >> i) & 1;
// OR
bool isSet = n & (1 << i);
```

```
n = 13 = 1101
Check bit 2: 1101 & 0100 = 0100 (non-zero → set)
Check bit 1: 1101 & 0010 = 0000 (zero → not set)
```

---

### Set Bit i (Turn ON)

```cpp
n = n | (1 << i);
```

OR with the mask forces bit `i` to 1, leaving others unchanged.

```
n = 9 = 1001
Set bit 1: 1001 | 0010 = 1011 = 11
```

---

### Clear Bit i (Turn OFF)

```cpp
n = n & ~(1 << i);
```

AND with the inverted mask forces bit `i` to 0, leaving others unchanged.

```
n = 13 = 1101
Clear bit 2: 1101 & 1011 = 1001 = 9
```

`~(1 << 2) = ~0100 = 1011` — all bits ON except position 2.

---

### Toggle Bit i (Flip)

```cpp
n = n ^ (1 << i);
```

XOR with the mask flips bit `i`: 0→1 or 1→0.

```
n = 13 = 1101
Toggle bit 1: 1101 ^ 0010 = 1111 = 15
Toggle bit 1 again: 1111 ^ 0010 = 1101 = 13 (back to original)
```

---

### Summary Table

| Operation      | Code                | Effect                    |
| -------------- | ------------------- | ------------------------- |
| Check bit i    | `(n >> i) & 1`      | Returns 0 or 1            |
| Set bit i      | `n \| (1 << i)`     | Forces bit i to 1         |
| Clear bit i    | `n & ~(1 << i)`     | Forces bit i to 0         |
| Toggle bit i   | `n ^ (1 << i)`      | Flips bit i               |
| Lowest set bit | `n & (-n)`          | Isolates rightmost 1      |
| Clear lowest   | `n & (n - 1)`       | Removes rightmost 1       |

---

## 7. Counting Bits — Hamming Weight and Beyond

### Count Set Bits (Population Count / Hamming Weight)

**Method 1 — Check each bit:**

```cpp
int countBits(int n) {
    int count = 0;
    while (n) {
        count += n & 1;
        n >>= 1;
    }
    return count;
}
```

Check the last bit, shift right, repeat. O(32) for 32-bit int.

**Method 2 — Brian Kernighan's Algorithm:**

```cpp
int countBits(int n) {
    int count = 0;
    while (n) {
        n = n & (n - 1);  // clear lowest set bit
        count++;
    }
    return count;
}
```

Each iteration removes exactly one set bit.
Runs in O(number of set bits) — faster when few bits are set.

**Method 3 — Built-in:**

```cpp
int count = __builtin_popcount(n);      // for int
int count = __builtin_popcountll(n);    // for long long
```

Hardware-accelerated on most platforms. O(1).

---

### Hamming Distance

The number of positions where two numbers differ:

```cpp
int hammingDistance(int x, int y) {
    return __builtin_popcount(x ^ y);
}
```

XOR gives 1 at every position where the bits differ.
Count those 1s = Hamming distance.

```
x = 1 (001), y = 4 (100)
x ^ y = 101 → popcount = 2
Hamming distance = 2
```

---

## 8. Powers of Two — The Bit Perspective

Powers of 2 are special in binary — they have exactly ONE bit set:

```
2⁰ = 1    = 00000001
2¹ = 2    = 00000010
2² = 4    = 00000100
2³ = 8    = 00001000
2⁴ = 16   = 00010000
2⁵ = 32   = 00100000
```

### Check if Power of 2

```cpp
bool isPow2 = (n > 0) && (n & (n - 1)) == 0;
```

### Next Power of 2 (>= n)

```cpp
int nextPow2(int n) {
    if (n <= 1) return 1;
    n--;
    n |= n >> 1;
    n |= n >> 2;
    n |= n >> 4;
    n |= n >> 8;
    n |= n >> 16;
    return n + 1;
}
```

### Log Base 2 (Position of Highest Set Bit)

```cpp
int log2(int n) {
    int pos = 0;
    while (n > 1) {
        n >>= 1;
        pos++;
    }
    return pos;
}
// Or: int pos = 31 - __builtin_clz(n);  // count leading zeros
```

---

## 9. XOR — The Swiss Army Knife

XOR is the most powerful bitwise operator for algorithm problems.

### The Three Laws of XOR

```
Law 1: x ^ x = 0        (self-cancellation)
Law 2: x ^ 0 = x        (identity)
Law 3: XOR is commutative and associative (order doesn't matter)
```

These three laws solve an enormous number of problems.

---

### Pattern 1: Find the Single Number

If every number appears twice except one:

```cpp
int single = 0;
for (int x : nums) single ^= x;
// Pairs cancel (x ^ x = 0), lone number survives
```

---

### Pattern 2: Find Two Single Numbers

If every number appears twice except TWO numbers `a` and `b`:

```cpp
int xorAll = 0;
for (int x : nums) xorAll ^= x;
// xorAll = a ^ b (pairs cancelled)

// Find any bit where a and b differ:
int diffBit = xorAll & (-xorAll);  // lowest set bit

int group1 = 0, group2 = 0;
for (int x : nums) {
    if (x & diffBit) group1 ^= x;
    else group2 ^= x;
}
// group1 = a, group2 = b
```

Split all numbers into two groups based on that differing bit.
Each group contains exactly one of the two singles.
XOR within each group reveals the single.

---

### Pattern 3: Find the Missing Number

Numbers 0 to n, one missing:

```cpp
int missing = 0;
for (int i = 0; i <= n; i++) missing ^= i;
for (int x : nums) missing ^= x;
// Every number except the missing one appears twice (once in 0..n, once in nums)
// Pairs cancel, missing survives
```

---

### Pattern 4: XOR from 0 to N (Without Loop)

```
XOR(0 to n) follows a pattern based on n % 4:

n % 4 == 0  →  n
n % 4 == 1  →  1
n % 4 == 2  →  n + 1
n % 4 == 3  →  0
```

```cpp
int xorUpToN(int n) {
    switch (n % 4) {
        case 0: return n;
        case 1: return 1;
        case 2: return n + 1;
        case 3: return 0;
    }
}
```

This is O(1) — no loop needed.

---

## 10. Common Patterns in LeetCode Problems

### Pattern: Bit-by-Bit Construction

Build the answer one bit at a time:

```cpp
int result = 0;
for (int i = 31; i >= 0; i--) {
    int bit = /* compute bit i somehow */;
    result |= (bit << i);
}
```

Used in: Reverse Bits, Maximum XOR problems.

---

### Pattern: Iterate Over All Subsets (Bitmask)

For an array of N elements, each subset can be represented
as a bitmask from `0` to `2^N - 1`:

```cpp
int n = arr.size();
for (int mask = 0; mask < (1 << n); mask++) {
    for (int i = 0; i < n; i++) {
        if (mask & (1 << i)) {
            // element i is in this subset
        }
    }
}
```

Total subsets = 2^N. Used in backtracking, DP with bitmask.

---

### Pattern: Sum Without Arithmetic Operators

```cpp
int getSum(int a, int b) {
    while (b != 0) {
        int carry = a & b;       // carry bits
        a = a ^ b;               // sum without carry
        b = carry << 1;          // shift carry left
    }
    return a;
}
```

XOR gives the sum ignoring carries.
AND gives the carry bits.
Shift carry left and repeat until no carry remains.

---

### Pattern: Counting Bits for Every Number 0 to N

```cpp
vector<int> countBits(int n) {
    vector<int> dp(n + 1, 0);
    for (int i = 1; i <= n; i++) {
        dp[i] = dp[i >> 1] + (i & 1);
    }
    return dp;
}
```

`dp[i] = dp[i/2] + last bit of i`

The number of set bits in `i` equals the set bits in `i/2`
plus whether `i` itself is odd.

---

## 11. Bit Manipulation Cheat Sheet

### Operators Quick Reference

| Operator | Symbol | Description              | Example (5, 3)     |
| -------- | ------ | ------------------------ | ------------------- |
| AND      | `&`    | Both bits must be 1      | `101 & 011 = 001`  |
| OR       | `\|`   | At least one bit is 1    | `101 \| 011 = 111` |
| XOR      | `^`    | Exactly one bit is 1     | `101 ^ 011 = 110`  |
| NOT      | `~`    | Flip all bits            | `~101 = ...010`     |
| L-Shift  | `<<`   | Shift left, fill 0s      | `011 << 1 = 110`   |
| R-Shift  | `>>`   | Shift right              | `110 >> 1 = 011`   |

### Essential Tricks Quick Reference

| Trick                    | Code                  | Result                    |
| ------------------------ | --------------------- | ------------------------- |
| Check odd/even           | `n & 1`               | 1 = odd, 0 = even        |
| Check bit i              | `(n >> i) & 1`        | 0 or 1                   |
| Set bit i                | `n \| (1 << i)`       | Bit i forced to 1         |
| Clear bit i              | `n & ~(1 << i)`       | Bit i forced to 0         |
| Toggle bit i             | `n ^ (1 << i)`        | Bit i flipped             |
| Lowest set bit           | `n & (-n)`            | Isolates rightmost 1      |
| Clear lowest set bit     | `n & (n - 1)`         | Removes rightmost 1       |
| Is power of 2?           | `n & (n-1) == 0`      | True if exactly one bit   |
| Count set bits           | `__builtin_popcount(n)`| Number of 1s              |
| All bits set (32-bit)    | `-1` or `0xFFFFFFFF`  | 11111...1                 |
| Multiply by 2^k          | `n << k`              | n × 2^k                  |
| Divide by 2^k            | `n >> k`              | n / 2^k (floor)           |
| Swap a and b             | `a^=b; b^=a; a^=b;`  | No temp variable          |

### XOR Properties

```
x ^ x = 0          (self-cancellation)
x ^ 0 = x          (identity)
x ^ y = y ^ x      (commutative)
(x ^ y) ^ z = x ^ (y ^ z)  (associative)
x ^ ~x = all 1s    (complement)
```

### Common Identities

```
~n = -(n + 1)
-n = ~n + 1                    (two's complement)
n & (-n) = lowest set bit
n & (n-1) = n with lowest bit cleared
n | (n-1) = n with all bits below lowest set bit turned ON
```

### Bit Count Patterns

```
Number of set bits in i = bits in (i/2) + (i & 1)
dp[i] = dp[i >> 1] + (i & 1)

XOR from 0 to n:
  n%4==0 → n
  n%4==1 → 1
  n%4==2 → n+1
  n%4==3 → 0
```

---

## Final Words

Bit manipulation is not magic — it's pattern recognition.

Every trick reduces to the same six operators
applied in clever combinations.

The most important things to internalize:

1. **`n & (n-1)`** clears the lowest set bit — used everywhere.
2. **`n & (-n)`** isolates the lowest set bit — used in BIT/Fenwick trees.
3. **XOR cancels pairs** — the foundation of "find the single number" problems.
4. **`1 << i`** creates a mask — the building block for all bit operations.
5. **Shifts are multiplication/division by powers of 2** — fast and clean.

Master these five, and every bit manipulation problem
becomes a combination of tools you already know. ⚡🧠
