## 🏹📜 _The Lone Warrior of the Twin Assembly: The Single Number Saga_

> \_"In the Assembly of Numbers,
> every warrior was sworn to arrive with a twin.
> Two by two they stood --
> mirrored, equal, inseparable.
>
> Yet the Oracle sensed imbalance.
> One warrior stood alone,
> casting a shadow unmatched by any other.
>
> She was commanded:
>
> **'Find the lone warrior --
> the one number that appears exactly once
> while all others appear exactly twice.'**
>
> The Oracle knew two ancient methods:
>
> **The Law of XOR:**
> XOR every number together.
> Pairs cancel (`x ^ x = 0`).
> The lone survivor remains.
> O(N) time, O(1) space -- the purest solution.
>
> **The Law of Double Weight:**
> Sum all unique numbers and double it.
> Subtract the total sum of the array.
> The difference is the lone warrior.
> O(N) time, O(N) space -- no bit magic needed.
>
> Two weapons. One answer.
> The Oracle mastered both."\_

---

This is the saga of **Single Number**.

You are given a non-empty array `nums` where:

-   Every element appears **exactly twice**,
    except for **one element** which appears **exactly once**.

Your task:

-   Find the single element.
-   Ideally in O(N) time and O(1) space.

```
Input:  [2, 2, 1]
Output: 1

Input:  [4, 1, 2, 1, 2]
Output: 4

Input:  [1]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Two Paths to the Lone Warrior

**Path One -- XOR (O(1) space):**

XOR has three magical properties:

```
x ^ x = 0     (any number XORed with itself vanishes)
x ^ 0 = x     (XOR with zero changes nothing)
XOR is commutative and associative (order doesn't matter)
```

If we XOR every element in the array:
- Every number appearing twice cancels itself to 0.
- The lone number has no partner -- it survives.

```
[4, 1, 2, 1, 2]
= 4 ^ 1 ^ 2 ^ 1 ^ 2
= (1 ^ 1) ^ (2 ^ 2) ^ 4
= 0 ^ 0 ^ 4
= 4
```

**Path Two -- Double Sum (O(N) space):**

```
single = 2 * sum(unique elements) - sum(all elements)
```

If every number appeared twice, `2 * uniqueSum` would equal `totalSum`.
The lone warrior creates an imbalance of exactly its own value.

---

### 📜 The Scroll of the Twin Assembly

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## 🔮 Path One -- The Law of XOR

_Let pairs annihilate -- the survivor is the answer_

```cpp
int singleNumber_xor(vector<int>& nums) {
    int result = 0;
```

The Oracle began with `result = 0` --
the neutral element of XOR.

XORing with 0 changes nothing: `x ^ 0 = x`.

---

```cpp
    for (int x : nums) {
        result ^= x;
    }
```

Every number in the assembly was XORed into `result`.

Pairs cancelled: `x ^ x = 0`.
The lone warrior had no twin to cancel with.

After the loop, only the lone warrior's value remained in `result`.

> _"When every twin finds its mirror and vanishes,
> the one without a twin stands exposed --
> the lone warrior, revealed by annihilation."_

---

```cpp
    return result;
}
```

The survivor was returned. O(N) time, O(1) space.
No set. No map. No extra memory. Just XOR.

---

## 📘 Path Two -- The Law of Double Weight

_Balance the books of existence_

```cpp
int singleNumber_sum(vector<int>& nums) {
    unordered_set<int> seen;
    int uniqueSum = 0;
    int totalSum = 0;
```

The Oracle opened two ledgers:
-   `uniqueSum` -- the sum of each distinct number, counted once.
-   `totalSum` -- the sum of every number in the array.

The set `seen` ensured each number was added to `uniqueSum` only once.

---

```cpp
    for (int x : nums) {
        totalSum += x;
        if (seen.insert(x).second) {
            uniqueSum += x;
        }
    }
```

Every number was added to `totalSum`.
Only first-time appearances were added to `uniqueSum`.

`seen.insert(x).second` returns `true` if the insertion succeeded
(the element was new) and `false` if it already existed.

---

```cpp
    return 2 * uniqueSum - totalSum;
}
```

If every number appeared exactly twice:
`2 * uniqueSum` would equal `totalSum` perfectly.

But the lone warrior appears only once in `totalSum`
while being doubled in `2 * uniqueSum`.
The difference is exactly the lone warrior's value.

---

### 🎺 The Trial of the Twin Assembly

```cpp
int main() {
    vector<int> nums1 = {2, 2, 1};
    cout << singleNumber_xor(nums1) << endl; // expected: 1
    cout << singleNumber_sum(nums1) << endl; // expected: 1

    vector<int> nums2 = {4, 1, 2, 1, 2};
    cout << singleNumber_xor(nums2) << endl; // expected: 4
    cout << singleNumber_sum(nums2) << endl; // expected: 4

    vector<int> nums3 = {1};
    cout << singleNumber_xor(nums3) << endl; // expected: 1

    return 0;
}
```

---

**XOR trace for `[4, 1, 2, 1, 2]`:**

| Step | x | result (before) | result ^= x | result (binary) |
| ---- | - | --------------- | ------------ | --------------- |
| 1    | 4 | 0               | 0 ^ 4 = 4   | 100             |
| 2    | 1 | 4               | 4 ^ 1 = 5   | 101             |
| 3    | 2 | 5               | 5 ^ 2 = 7   | 111             |
| 4    | 1 | 7               | 7 ^ 1 = 6   | 110             |
| 5    | 2 | 6               | 6 ^ 2 = 4   | 100             |

**Answer: 4** ✓

The `1`s cancelled (steps 2 and 4). The `2`s cancelled (steps 3 and 5).
Only `4` survived.

---

**Double Sum trace for `[4, 1, 2, 1, 2]`:**

| Step | x | totalSum | New to set? | uniqueSum |
| ---- | - | -------- | ----------- | --------- |
| 1    | 4 | 4        | Yes         | 4         |
| 2    | 1 | 5        | Yes         | 5         |
| 3    | 2 | 7        | Yes         | 7         |
| 4    | 1 | 8        | No          | 7         |
| 5    | 2 | 10       | No          | 7         |

```
2 * uniqueSum - totalSum = 2 * 7 - 10 = 14 - 10 = 4
```

**Answer: 4** ✓

---

**XOR trace for `[2, 2, 1]`:**

| Step | x | result ^= x |
| ---- | - | ------------ |
| 1    | 2 | 0 ^ 2 = 2   |
| 2    | 2 | 2 ^ 2 = 0   |
| 3    | 1 | 0 ^ 1 = 1   |

**Answer: 1** ✓

The pair of `2`s cancelled completely. `1` stood alone.

---

### 🧠 Memory of the Lone Warrior Law

-   **XOR approach (preferred):**
    -   `result = 0`, then `result ^= x` for every element
    -   Pairs cancel (`x ^ x = 0`), lone survivor remains
    -   O(N) time, O(1) space -- the optimal solution
-   **Double Sum approach:**
    -   `single = 2 * sum(unique elements) - sum(all elements)`
    -   Use a set to track unique elements
    -   O(N) time, O(N) space -- no bit manipulation needed
-   Both approaches handle negatives, zeros, and single-element arrays correctly
-   **Edge cases:**
    -   Single element `[x]` -- XOR returns `x`, double sum returns `2*x - x = x`
    -   Negative numbers -- XOR and arithmetic work identically on negatives
    -   Zero as the lone number -- `0 ^ 0 = 0` for pairs, lone `0` survives

Thus is remembered the saga of **Single Number**,
where the Oracle wielded two ancient laws --
the Law of XOR that let every twin annihilate its mirror,
and the Law of Double Weight that balanced the books of existence --
both converging on the same truth:
the lone warrior who stood without a twin,
exposed by the silence of its missing partner. 🏹📜✨
