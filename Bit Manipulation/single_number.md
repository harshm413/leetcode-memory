## 🏹⚡ _The Lone Warrior Revealed by Annihilation: The Single Number (Bit Manipulation) Saga_

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
> while all others appear exactly twice.
> Do it in O(N) time and O(1) space.'**
>
> The Oracle did not reach for a hash map.
> She did not sort. She did not count.
>
> She invoked the **Law of XOR** --
> the most elegant weapon in all of bit manipulation:
>
> **XOR every number together.
> Pairs cancel: x ^ x = 0.
> The lone survivor remains: 0 ^ x = x.**
>
> One pass. One variable. Zero extra space.
> The purest solution to any problem in all of algorithms."\_

---

This is the saga of **Single Number (Bit Manipulation Approach)**.

Given a non-empty array `nums` where every element appears
**exactly twice** except for **one element** which appears **exactly once**:

-   Find the single element.
-   **O(N) time, O(1) space.**

```
Input:  [2, 2, 1]
Output: 1

Input:  [4, 1, 2, 1, 2]
Output: 4

Input:  [1]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- The Three Laws of XOR

XOR has three magical properties that make this problem trivial:

```
Law 1:  x ^ x = 0       (self-cancellation -- any number XORed with itself vanishes)
Law 2:  x ^ 0 = x       (identity -- XOR with zero changes nothing)
Law 3:  XOR is commutative and associative (order doesn't matter)
```

If we XOR every element in the array:

```
[4, 1, 2, 1, 2]
= 4 ^ 1 ^ 2 ^ 1 ^ 2
= (1 ^ 1) ^ (2 ^ 2) ^ 4     (reorder by Law 3)
= 0 ^ 0 ^ 4                   (cancel pairs by Law 1)
= 4                            (identity by Law 2)
```

Every number appearing twice cancels itself to 0.
The lone number has no partner -- it survives.

No hash map. No sorting. No counting.
Just XOR.

```
Time:  O(N) -- single pass
Space: O(1) -- one variable
```

---

### 📜 The Scroll of the Twin Assembly

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's XOR Ritual

```cpp
int singleNumber(vector<int>& nums) {
    int result = 0;
```

The Oracle began with `result = 0` --
the neutral element of XOR.

XORing with 0 changes nothing: `x ^ 0 = x`.
This was the blank slate.

---

## 🔁 XOR Every Element

```cpp
    for (int x : nums) {
        result ^= x;
    }
```

Every number in the assembly was XORed into `result`.

Pairs cancelled: `x ^ x = 0`.
The lone warrior had no twin to cancel with.

After the loop, only the lone warrior's value remained.

> _"When every twin finds its mirror and vanishes,
> the one without a twin stands exposed --
> revealed by the silence of annihilation."_

---

## 🏁 The Survivor Speaks

```cpp
    return result;
}
```

The survivor was returned. O(N) time, O(1) space.
No set. No map. No extra memory. Just XOR.

---

### 🎺 The Trial of the Twin Assembly

```cpp
int main() {
    vector<int> nums1 = {2, 2, 1};
    cout << singleNumber(nums1) << endl; // expected: 1

    vector<int> nums2 = {4, 1, 2, 1, 2};
    cout << singleNumber(nums2) << endl; // expected: 4

    vector<int> nums3 = {1};
    cout << singleNumber(nums3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `[4, 1, 2, 1, 2]`:**

| Step | x | result (before) | result ^= x | Binary            |
| ---- | - | --------------- | ------------ | ----------------- |
| 1    | 4 | 0               | 0 ^ 4 = 4   | 000 ^ 100 = 100   |
| 2    | 1 | 4               | 4 ^ 1 = 5   | 100 ^ 001 = 101   |
| 3    | 2 | 5               | 5 ^ 2 = 7   | 101 ^ 010 = 111   |
| 4    | 1 | 7               | 7 ^ 1 = 6   | 111 ^ 001 = 110   |
| 5    | 2 | 6               | 6 ^ 2 = 4   | 110 ^ 010 = 100   |

**Answer: 4** ✓

The `1`s cancelled (steps 2 and 4): `1 ^ 1 = 0`.
The `2`s cancelled (steps 3 and 5): `2 ^ 2 = 0`.
Only `4` survived.

---

**Full trace for `[2, 2, 1]`:**

| Step | x | result ^= x |
| ---- | - | ------------ |
| 1    | 2 | 0 ^ 2 = 2   |
| 2    | 2 | 2 ^ 2 = 0   |
| 3    | 1 | 0 ^ 1 = 1   |

**Answer: 1** ✓

The pair of `2`s cancelled completely at step 2. `1` stood alone.

---

**Trace for `[1]`:**

| Step | x | result ^= x |
| ---- | - | ------------ |
| 1    | 1 | 0 ^ 1 = 1   |

**Answer: 1** ✓ -- single element, trivially the lone warrior.

---

**Trace for `[-1, -1, -2]` (negative numbers):**

| Step | x  | result ^= x  |
| ---- | -- | ------------- |
| 1    | -1 | 0 ^ -1 = -1  |
| 2    | -1 | -1 ^ -1 = 0  |
| 3    | -2 | 0 ^ -2 = -2  |

**Answer: -2** ✓

XOR works identically on negative numbers.
In two's complement, `-1 ^ -1 = 0` just like `1 ^ 1 = 0`.

---

## 🔍 Why XOR and Not Other Approaches?

### Hash Map Approach -- O(N) time, O(N) space

```cpp
unordered_set<int> seen;
for (int x : nums) {
    if (seen.count(x)) seen.erase(x);
    else seen.insert(x);
}
return *seen.begin();
```

Works, but uses O(N) extra space. The problem asks for O(1) space.

### Sorting Approach -- O(N log N) time, O(1) space

```cpp
sort(nums.begin(), nums.end());
for (int i = 0; i < n - 1; i += 2) {
    if (nums[i] != nums[i + 1]) return nums[i];
}
return nums[n - 1];
```

Works, but O(N log N) is slower than O(N).

### XOR Approach -- O(N) time, O(1) space ✓

The only approach that achieves both optimal time AND optimal space.
No data structure. No sorting. Just one variable and one pass.

---

## 🔄 Why Does Order Not Matter?

XOR is **commutative** (`a ^ b = b ^ a`)
and **associative** (`(a ^ b) ^ c = a ^ (b ^ c)`).

This means the order of elements in the array is irrelevant.
Whether the twins are adjacent or scattered across the array --
they will always cancel when XORed together.

```
[1, 2, 1] → 1 ^ 2 ^ 1 = (1 ^ 1) ^ 2 = 0 ^ 2 = 2
[2, 1, 1] → 2 ^ 1 ^ 1 = 2 ^ (1 ^ 1) = 2 ^ 0 = 2
[1, 1, 2] → 1 ^ 1 ^ 2 = (1 ^ 1) ^ 2 = 0 ^ 2 = 2
```

All produce the same result regardless of arrangement.

---

## 🌐 XOR Beyond Single Number

This XOR-cancellation pattern appears in many problems:

| Problem                  | XOR Application                        |
| ------------------------ | -------------------------------------- |
| Single Number            | Pairs cancel, lone survives            |
| Missing Number           | XOR 0..n with array, missing survives  |
| Single Number II (×3)    | Bit counting per position (not pure XOR) |
| Single Number III (two singles) | XOR all, split by differing bit  |
| Find Duplicate           | XOR indices and values                 |

The single-number XOR trick is the gateway to all of these.

---

### 🧠 Memory of the Annihilation Law

-   **XOR every element:** `result ^= x` for all x in nums
-   **Pairs cancel:** `x ^ x = 0` -- every duplicate vanishes
-   **Lone survivor:** `0 ^ x = x` -- the single element remains
-   **Order irrelevant:** XOR is commutative and associative
-   **Works on negatives:** two's complement XOR behaves identically
-   **Start with 0:** the neutral element of XOR
-   **Time:** O(N) -- single pass
-   **Space:** O(1) -- one variable
-   **No hash map, no sorting, no counting** -- pure bit manipulation
-   **Edge cases:**
    -   Single element → return it (0 ^ x = x)
    -   Negative numbers → handled identically
    -   Large arrays → still O(1) space

Thus is remembered the saga of **Single Number**,
where the Oracle did not search, did not sort, did not count --
but simply XORed every warrior in the assembly together,
letting every twin annihilate its mirror,
until the lone warrior stood alone
in the silence of perfect cancellation --
revealed not by what was found,
but by what refused to vanish. 🏹⚡✨
