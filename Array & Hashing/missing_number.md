## 🧮📜 _The Broken Ledger of the Realm: The Missing Number Saga_

> \_"In the Kingdom of Consecutive Numbers,
> a perfect census was promised —
> every number from **0 to n**
> written exactly once in the royal ledger.
>
> But when the scroll was unrolled,
> the Oracle noticed something unsettling:
>
> **The ledger was lighter than it should be.**
>
> One number had vanished.
> One throne stood empty.
>
> The Oracle knew two ancient methods
> to expose the missing heir —
> each elegant, each O(N) time, each O(1) space.
>
> **The Law of Sums:**
> If the total weight of a complete ledger is known,
> and the actual weight is measured,
> the difference reveals the missing number.
>
> **The Law of XOR:**
> If every number from 0 to n is XORed together,
> and every number in the ledger is XORed on top,
> all pairs cancel to zero —
> leaving only the missing number standing alone.
>
> Two weapons. One answer.
> The Oracle mastered both."\_

---

This is the saga of **Missing Number**.

You are given an array `nums` of length `n`
containing **distinct numbers from 0 to n**,
with **exactly one number missing**.

Your task:

-   Find the missing number.
-   **O(N) time, O(1) extra space.**

```
Input:  [3, 0, 1]
Output: 2   (numbers 0-3 expected, 2 is missing)

Input:  [0, 1]
Output: 2   (numbers 0-2 expected, 2 is missing)

Input:  [9,6,4,2,3,5,7,0,1]
Output: 8
```

---
## 🧠 The Oracle's Core Insight — Two Paths to the Same Truth

Both approaches exploit the same idea:

> **We know exactly what SHOULD exist (0 to n).
> We know what DOES exist (the array).
> The difference — by sum or by XOR — is the missing number.**

```
Path One — Sum:
  expectedSum = n*(n+1)/2
  actualSum   = sum of all elements in nums
  missing     = expectedSum - actualSum

Path Two — XOR:
  XOR all indices 0 to n  →  captures what SHOULD exist
  XOR all elements in nums →  captures what DOES exist
  Pairs cancel (x ^ x = 0), leaving only the missing number
```

Both are O(N) time, O(1) space.
The sum approach is simpler.
The XOR approach avoids any risk of integer overflow.

---

### 📜 The Scroll of the Census

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚖️ Path One — The Law of Sums

_Compare what should exist with what does_

```cpp
int missingNumber_sum(vector<int>& nums) {
    int n = nums.size();
    int expectedSum = n * (n + 1) / 2;
```

The Oracle computed the weight of a **complete ledger** —
the sum of all numbers from `0` to `n`.

The formula `n * (n + 1) / 2` is the ancient Gauss summation —
the total of any consecutive sequence starting from zero.

For `n = 3`: expected = `3 * 4 / 2 = 6` (0 + 1 + 2 + 3).

---

```cpp
    int actualSum = 0;
    for (int x : nums) {
        actualSum += x;
    }
```

The Oracle then weighed the actual ledger —
summing every number that was truly present.

For `[3, 0, 1]`: actual = `3 + 0 + 1 = 4`.

---

```cpp
    return expectedSum - actualSum;
}
```

The difference exposed the vanished number.

`6 - 4 = 2` — the missing heir was **2**.

> _"The ledger was lighter by exactly the weight
> of the number that slipped into silence."_

---

## 🔮 Path Two — The Law of XOR

_Let pairs annihilate each other — the survivor is the answer_

```cpp
int missingNumber_xor(vector<int>& nums) {
    int n = nums.size();
    int xorAll = 0;
```

The Oracle opened a different ritual —
one that used the ancient property of XOR:

```
x ^ x = 0     (any number XORed with itself vanishes)
x ^ 0 = x     (XOR with zero changes nothing)
XOR is commutative and associative (order doesn't matter)
```

`xorAll` would accumulate the XOR of everything —
both what should exist and what does exist.

---

### 🔢 XOR All Indices from 0 to n

```cpp
    for (int i = 0; i <= n; i++) {
        xorAll ^= i;
    }
```

The Oracle XORed every number from `0` to `n` —
the complete set of numbers that should appear.

For `n = 3`: `xorAll = 0 ^ 1 ^ 2 ^ 3`.

---

### 📊 XOR All Elements in the Array

```cpp
    for (int x : nums) {
        xorAll ^= x;
    }
```

Then she XORed every number that actually existed in the ledger.

Now `xorAll` contained:
```
(0 ^ 1 ^ 2 ^ 3) ^ (3 ^ 0 ^ 1)
```

Every number that appeared in BOTH sets cancelled itself:
`0 ^ 0 = 0`, `1 ^ 1 = 0`, `3 ^ 3 = 0`.

Only `2` had no partner. It survived alone.

---

```cpp
    return xorAll;
}
```

The lone survivor was the missing number.

> _"When every present number finds its twin and vanishes,
> the one without a twin stands exposed —
> the missing heir, revealed by annihilation."_

---

### 🎺 The Trial of the Broken Ledger

```cpp
int main() {
    vector<int> nums1 = {3, 0, 1};
    cout << missingNumber_sum(nums1) << endl; // expected: 2
    cout << missingNumber_xor(nums1) << endl; // expected: 2

    vector<int> nums2 = {0, 1};
    cout << missingNumber_sum(nums2) << endl; // expected: 2
    cout << missingNumber_xor(nums2) << endl; // expected: 2

    vector<int> nums3 = {9,6,4,2,3,5,7,0,1};
    cout << missingNumber_sum(nums3) << endl; // expected: 8
    cout << missingNumber_xor(nums3) << endl; // expected: 8

    return 0;
}
```

---

**Sum trace for `[3, 0, 1]` (n=3):**

| Step         | Value                    |
| ------------ | ------------------------ |
| expectedSum  | 3 × 4 / 2 = **6**       |
| actualSum    | 3 + 0 + 1 = **4**       |
| missing      | 6 - 4 = **2** ✓         |

---

**XOR trace for `[3, 0, 1]` (n=3):**

| Step              | xorAll (binary)       | xorAll (decimal) |
| ----------------- | --------------------- | ---------------- |
| Initial           | 000                   | 0                |
| ^ 0               | 000                   | 0                |
| ^ 1               | 001                   | 1                |
| ^ 2               | 011                   | 3                |
| ^ 3               | 000                   | 0                |
| ^ 3 (from array)  | 011                   | 3                |
| ^ 0 (from array)  | 011                   | 3                |
| ^ 1 (from array)  | 010                   | **2** ✓          |

Every number except `2` appeared twice and cancelled.
`2` stood alone.

---

**Sum trace for `[9,6,4,2,3,5,7,0,1]` (n=9):**

| Step         | Value                              |
| ------------ | ---------------------------------- |
| expectedSum  | 9 × 10 / 2 = **45**               |
| actualSum    | 9+6+4+2+3+5+7+0+1 = **37**        |
| missing      | 45 - 37 = **8** ✓                  |

---

## ⚠️ Sum vs XOR — When to Choose Which

**Sum approach:**
- Simpler to write and understand
- Risk: for very large `n`, `n*(n+1)/2` could overflow 32-bit integers
- Mitigation: use `long long` for safety

**XOR approach:**
- No overflow risk — XOR never grows beyond the bit-width of the largest number
- Slightly less intuitive but mathematically elegant
- Perfect when overflow is a concern

Both are equally valid for interview settings.

---

### 🧠 Memory of the Broken Ledger Law

-   **Sum approach:** `missing = n*(n+1)/2 - sum(nums)`
    -   Gauss formula gives expected sum of 0 to n
    -   Subtract actual sum → the difference is the missing number
-   **XOR approach:** `XOR(0..n) ^ XOR(nums) = missing`
    -   Every number appearing twice cancels (`x ^ x = 0`)
    -   The lone unpaired number survives
-   Both approaches: **O(N) time, O(1) space**
-   Array contains distinct numbers from 0 to n with exactly one missing
-   **Edge cases:**
    -   Missing number is 0 → both approaches handle it naturally
    -   Missing number is n (the largest) → sum: actualSum < expectedSum; XOR: n has no pair
    -   Single element `[0]` → missing is 1; `[1]` → missing is 0

Thus is remembered the saga of **Missing Number**,
where the Oracle wielded two ancient laws —
the Law of Sums and the Law of XOR —
to expose the one number that vanished from the ledger,
either by weighing the realm and finding it lighter,
or by letting every present number annihilate its twin
until the missing heir stood alone,
revealed by the silence of its absence. 🧮📜✨
