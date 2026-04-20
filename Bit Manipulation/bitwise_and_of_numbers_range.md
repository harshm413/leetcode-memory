## 🏔️🔗 _The Common Crown of the Range: The Bitwise AND of Numbers Range Saga_

> \_"In the Binary Kingdom,
> a range of consecutive numbers marched in a line --
> from `left` to `right`, shoulder to shoulder.
>
> The King commanded:
>
> **'AND every number in this range together.
> What bits survive?'**
>
> The naive approach would AND all numbers one by one.
> For a range of millions -- far too slow.
>
> The Oracle studied the binary patterns.
>
> She noticed something profound:
>
> **As numbers increment through a range,
> the lower bits FLIP constantly.
> Only the UPPER bits that are COMMON to both
> `left` and `right` survive the AND.**
>
> The bits that differ between `left` and `right`
> will have been both 0 and 1 at some point in the range --
> and ANDing a 0 with anything gives 0.
>
> **The answer is the common prefix
> of `left` and `right` in binary --
> the bits that never changed across the range.**
>
> Shift both numbers right until they are equal.
> That equal value is the common prefix.
> Shift it back left by the number of shifts.
> Done."\_

---

This is the saga of **Bitwise AND of Numbers Range**.

Given two integers `left` and `right` (`left <= right`):

-   Return the bitwise AND of all numbers in `[left, right]`.

```
Input:  left = 5, right = 7
Output: 4

Input:  left = 0, right = 0
Output: 0

Input:  left = 1, right = 2147483647
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Common Prefix Survives

Consider the range `[5, 7]` in binary:

```
5 = 101
6 = 110
7 = 111
```

AND them all: `101 & 110 & 111 = 100 = 4`.

The last two bits flipped across the range.
Only the leading `1` (bit 2) was common to all three.

**The general rule:**

> **The AND of a range [left, right] equals
> the common binary prefix of `left` and `right`,
> with all differing lower bits set to 0.**

Why? Because between `left` and `right`,
every bit position below the first differing bit
will have been both 0 and 1 at some point.
ANDing a 0 kills that bit permanently.

Only the bits that are identical in BOTH `left` and `right`
-- and therefore identical in every number between them --
survive the AND.

---

## 🔧 The Algorithm -- Shift Until Equal

```
Shift both left and right to the right until they are equal.
Count the number of shifts.
Shift the common value back left by that count.
```

When `left == right`, we've found the common prefix.
The number of shifts tells us how many lower bits were different.
Shifting back restores the prefix to its original position.

```
Time:  O(32) = O(1) -- at most 32 shifts
Space: O(1)
```

---

### 📜 The Scroll of the Common Crown

```cpp
#include <iostream>
using namespace std;
```

---

## ⚔️ The Oracle's Prefix-Finding Ritual

```cpp
int rangeBitwiseAnd(int left, int right) {
    int shifts = 0;

    while (left != right) {
        left >>= 1;
        right >>= 1;
        shifts++;
    }

    return left << shifts;
}
```

**`while (left != right)`** -- keep shifting until the two numbers
have the same value. At that point, all differing lower bits
have been shifted away. What remains is the common prefix.

**`shifts`** -- counts how many bit positions were different.

**`left << shifts`** -- restores the common prefix
to its original position, with zeros filling the lower bits.

> _"Strip away the noise -- the bits that flicker.
> What remains when both numbers agree
> is the only truth that survives the AND."_

---

### 🎺 The Trial of the Common Crown

```cpp
int main() {
    cout << rangeBitwiseAnd(5, 7)          << endl; // expected: 4
    cout << rangeBitwiseAnd(0, 0)          << endl; // expected: 0
    cout << rangeBitwiseAnd(1, 2147483647) << endl; // expected: 0
    cout << rangeBitwiseAnd(12, 15)        << endl; // expected: 12
    cout << rangeBitwiseAnd(6, 7)          << endl; // expected: 6
    return 0;
}
```

---

**Full trace for left = 5, right = 7:**

```
5 = 101
7 = 111
```

| Iteration | left | right | Equal? | Action       | shifts |
| --------- | ---- | ----- | ------ | ------------ | ------ |
| 1         | 101  | 111   | No     | Shift both   | 1      |
| 2         | 10   | 11    | No     | Shift both   | 2      |
| 3         | 1    | 1     | Yes    | Stop         | 2      |

Common prefix = `1`. Shift left by 2: `1 << 2 = 100 = 4`.

**Answer: 4** ✓

Bits 0 and 1 differed across the range (5,6,7).
Only bit 2 was common to all.

---

**Full trace for left = 12, right = 15:**

```
12 = 1100
13 = 1101
14 = 1110
15 = 1111
```

| Iteration | left | right | Equal? | shifts |
| --------- | ---- | ----- | ------ | ------ |
| 1         | 1100 | 1111  | No     | 1      |
| 2         | 110  | 111   | No     | 2      |
| 3         | 11   | 11    | Yes    | 2      |

Common prefix = `11`. Shift left by 2: `11 << 2 = 1100 = 12`.

**Answer: 12** ✓

The top two bits (`11`) were common. The bottom two flickered.

---

**Full trace for left = 6, right = 7:**

```
6 = 110
7 = 111
```

| Iteration | left | right | Equal? | shifts |
| --------- | ---- | ----- | ------ | ------ |
| 1         | 110  | 111   | No     | 1      |
| 2         | 11   | 11    | Yes    | 1      |

Common prefix = `11`. Shift left by 1: `11 << 1 = 110 = 6`.

**Answer: 6** ✓

Only the last bit differed (6 has 0, 7 has 1).

---

**Trace for left = 0, right = 0:**

Already equal. shifts = 0. Answer = `0 << 0 = 0`.

**Answer: 0** ✓

---

**Trace for left = 1, right = 2147483647:**

```
1          = 00000000000000000000000000000001
2147483647 = 01111111111111111111111111111111
```

These differ in every bit except the sign bit (which is 0 for both).
After shifting, both become 0 at shift count 31.

Common prefix = `0`. Answer = `0 << 31 = 0`.

**Answer: 0** ✓

The range is so vast that every bit position
has been both 0 and 1 at some point.

---

## 🔄 Alternative -- Brian Kernighan's Approach

Instead of shifting both numbers, clear the lowest set bit
of `right` until `right <= left`:

```cpp
int rangeBitwiseAnd(int left, int right) {
    while (right > left) {
        right = right & (right - 1);
    }
    return right;
}
```

`right & (right - 1)` clears the lowest set bit of `right`.
Each iteration makes `right` smaller.
When `right <= left`, the remaining bits of `right`
are exactly the common prefix.

**Why this works:**
The bits that differ between `left` and `right`
are the lower bits. Clearing them from `right`
(which has more of them set) converges `right` toward `left`.
When they meet, only the common prefix remains.

Same O(32) time. Elegant use of Brian Kernighan's trick.

---

## 🔍 Why Do Lower Bits Always Flip?

Between any two numbers that differ in bit position `k`,
there exists a number in the range where bit `k` is 0
and another where bit `k` is 1.

Specifically, if `left` and `right` differ at bit `k`:
the number `left` with bits 0 to k-1 all set to 1
is still <= `right` (since they differ at bit k).
And the number with bit k flipped is in the range.

So bit `k` has been both 0 and 1 → AND kills it.

Every bit below the highest differing bit also flips
(they cycle through all combinations as numbers increment).

Only the common prefix -- bits above the highest differing bit --
survives.

---

### 🧠 Memory of the Common Crown Law

-   **AND of a range = common binary prefix of left and right**
-   **Shift approach:** shift both right until equal, count shifts, shift back left
-   **Kernighan approach:** clear lowest set bit of right until `right <= left`
-   Lower bits flip across any range → AND kills them
-   Only bits identical in BOTH left and right survive
-   **Time:** O(32) = O(1) -- at most 32 shifts or clears
-   **Space:** O(1)
-   **Edge cases:**
    -   left == right → answer is left (no range, no flipping)
    -   left == 0 → answer is 0 (0 AND anything = 0)
    -   Huge range → answer is 0 (all bits flip)
    -   Adjacent numbers (n, n+1) → common prefix with last bit zeroed

Thus is remembered the saga of **Bitwise AND of Numbers Range**,
where the Oracle did not AND millions of numbers one by one
but instead found the common crown --
the binary prefix shared by both endpoints --
by shifting away the flickering lower bits
until only the unchanging truth remained,
then restoring it to its rightful position --
the bits that never wavered
across the entire range. 🏔️🔗✨
