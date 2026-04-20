## 🕳️⚡ _The Vanished Heir of the Counting Line: The Missing Number (Bit Manipulation) Saga_

> \_"In the Kingdom of Consecutive Numbers,
> a perfect census was promised --
> every number from 0 to n
> written exactly once in the royal ledger.
>
> But when the scroll was unrolled,
> one number had vanished.
> One throne stood empty.
>
> The Oracle knew two ancient methods --
> the Law of Sums and the Law of XOR.
>
> But this saga belongs to the **Law of XOR** --
> the purest bit manipulation approach.
>
> **XOR every index from 0 to n.
> XOR every element in the array.
> Every number that exists in BOTH sets
> cancels itself: x ^ x = 0.
> The one number that exists only in the index set
> -- the missing number -- survives alone.**
>
> No subtraction. No overflow risk.
> Just XOR."\_

---

This is the saga of **Missing Number (Bit Manipulation Approach)**.

Given an array `nums` of length `n`
containing **distinct numbers from 0 to n**,
with **exactly one number missing**:

-   Find the missing number.
-   **O(N) time, O(1) space.**

```
Input:  [3, 0, 1]
Output: 2

Input:  [0, 1]
Output: 2

Input:  [9,6,4,2,3,5,7,0,1]
Output: 8
```

---

## 🧠 The Oracle's Core Insight -- XOR Creates Pairs

The complete set of numbers is `{0, 1, 2, ..., n}`.
The array contains all of them except one.

If we XOR every number from 0 to n
AND every element in the array:

```
Every number that appears in BOTH sets → appears twice → cancels (x ^ x = 0)
The missing number appears only in {0..n} → appears once → survives
```

```
result = (0 ^ 1 ^ 2 ^ ... ^ n) ^ (nums[0] ^ nums[1] ^ ... ^ nums[n-1])
       = missing number
```

This works because XOR is commutative and associative --
order doesn't matter, pairs cancel, the lone survivor remains.

**Why XOR over the Sum approach?**

The sum approach (`n*(n+1)/2 - sum(nums)`) risks integer overflow
for very large `n`. XOR never overflows --
it operates within the bit width of the operands.

```
Time:  O(N) -- two passes (or one combined pass)
Space: O(1) -- one variable
```

---

### 📜 The Scroll of the Vanished Heir

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's XOR Ritual

```cpp
int missingNumber(vector<int>& nums) {
    int n = nums.size();
    int xorAll = 0;
```

`xorAll` would accumulate the XOR of everything --
both the complete set and the array.

---

## 🔁 XOR All Indices from 0 to n

```cpp
    for (int i = 0; i <= n; i++) {
        xorAll ^= i;
    }
```

The Oracle XORed every number from 0 to n --
the complete set that SHOULD exist.

For `n = 3`: `xorAll = 0 ^ 1 ^ 2 ^ 3`.

---

## 🔁 XOR All Elements in the Array

```cpp
    for (int x : nums) {
        xorAll ^= x;
    }
```

Then she XORed every number that actually existed.

Now `xorAll` contained:

```
(0 ^ 1 ^ 2 ^ ... ^ n) ^ (nums[0] ^ nums[1] ^ ... ^ nums[n-1])
```

Every number present in both sets cancelled: `x ^ x = 0`.
Only the missing number had no partner. It survived.

---

## 🏁 The Missing Heir Revealed

```cpp
    return xorAll;
}
```

The lone survivor was the missing number.

> _"When every present number finds its twin and vanishes,
> the one without a twin stands exposed --
> the missing heir, revealed by annihilation."_

---

### 🎺 The Trial of the Vanished Heir

```cpp
int main() {
    vector<int> nums1 = {3, 0, 1};
    cout << missingNumber(nums1) << endl; // expected: 2

    vector<int> nums2 = {0, 1};
    cout << missingNumber(nums2) << endl; // expected: 2

    vector<int> nums3 = {9,6,4,2,3,5,7,0,1};
    cout << missingNumber(nums3) << endl; // expected: 8

    return 0;
}
```

---

**Full trace for `[3, 0, 1]` (n=3):**

**XOR indices 0 to 3:**

| Step | i | xorAll (before) | xorAll ^= i |
| ---- | - | --------------- | ----------- |
| 1    | 0 | 0               | 0           |
| 2    | 1 | 0               | 1           |
| 3    | 2 | 1               | 3           |
| 4    | 3 | 3               | 0           |

After indices: `xorAll = 0 ^ 1 ^ 2 ^ 3 = 0`

**XOR array elements:**

| Step | x | xorAll (before) | xorAll ^= x |
| ---- | - | --------------- | ----------- |
| 1    | 3 | 0               | 3           |
| 2    | 0 | 3               | 3           |
| 3    | 1 | 3               | 2           |

**Answer: 2** ✓

The `0`s cancelled (index 0 ^ array 0). The `1`s cancelled. The `3`s cancelled.
Only `2` had no partner in the array.

---

**Full trace for `[9,6,4,2,3,5,7,0,1]` (n=9):**

XOR of indices 0-9: `0^1^2^3^4^5^6^7^8^9`

Using the XOR-up-to-N shortcut (`n%4 == 1 → result = 1`):
`0^1^...^9 = 1` (since 9%4 = 1).

XOR of array: `9^6^4^2^3^5^7^0^1`

All numbers 0-9 except 8 are in the array.
So: `(0^1^2^...^9) ^ (9^6^4^2^3^5^7^0^1) = 8`

Every number except 8 appeared twice (once in indices, once in array) and cancelled.

**Answer: 8** ✓

---

**Trace for `[0, 1]` (n=2):**

XOR indices: `0 ^ 1 ^ 2 = 3`
XOR array: `0 ^ 1 = 1`
Result: `3 ^ 1 = 2`

**Answer: 2** ✓ -- the missing number is at the end.

---

**Trace for `[1]` (n=1):**

XOR indices: `0 ^ 1 = 1`
XOR array: `1`
Result: `1 ^ 1 = 0`

**Answer: 0** ✓ -- the missing number is 0.

---

**Trace for `[0]` (n=1):**

XOR indices: `0 ^ 1 = 1`
XOR array: `0`
Result: `1 ^ 0 = 1`

**Answer: 1** ✓ -- the missing number is n itself.

---

## 🔄 One-Pass Optimization

Instead of two separate loops, combine them:

```cpp
int missingNumber(vector<int>& nums) {
    int n = nums.size();
    int xorAll = n;  // start with n (the last index)
    for (int i = 0; i < n; i++) {
        xorAll ^= i ^ nums[i];
    }
    return xorAll;
}
```

Initialize `xorAll = n` (since the loop only goes 0 to n-1).
Each iteration XORs both the index `i` and the array element `nums[i]`.

Same result, one loop instead of two.

---

## 🔍 XOR vs Sum -- Which Is Better?

| XOR Approach                      | Sum Approach                      |
| --------------------------------- | --------------------------------- |
| `xor(0..n) ^ xor(nums)`          | `n*(n+1)/2 - sum(nums)`          |
| No overflow risk                  | Can overflow for large n          |
| O(N) time, O(1) space            | O(N) time, O(1) space            |
| Bit manipulation                  | Arithmetic                        |
| Works for any integer range       | Needs exact formula               |

Both are O(N) time, O(1) space.
XOR is safer (no overflow) and more elegant.
Sum is more intuitive for most people.

In interviews, knowing both shows depth.

---

## 🌐 The XOR Pairing Pattern

This problem is a variation of the **Single Number** pattern:

| Problem        | What pairs cancel?                    | What survives?     |
| -------------- | ------------------------------------- | ------------------ |
| Single Number  | Duplicate elements cancel             | The lone element   |
| Missing Number | Index-element pairs cancel            | The missing index  |
| Find Duplicate | Index-element pairs cancel            | The extra element  |

The core idea is always the same:
create pairs that cancel via XOR, leaving the answer alone.

---

### 🧠 Memory of the Vanished Heir Law

-   **XOR all indices 0 to n** AND **XOR all array elements**
-   Every number present in both sets cancels: `x ^ x = 0`
-   The missing number has no partner → it survives
-   **One-pass variant:** `xorAll = n; for i: xorAll ^= i ^ nums[i]`
-   **No overflow** -- XOR operates within bit width, unlike sum
-   **Time:** O(N)
-   **Space:** O(1)
-   **Edge cases:**
    -   Missing 0 → XOR of indices dominates
    -   Missing n → initialized `xorAll = n` handles it
    -   Single element [0] → missing is 1
    -   Single element [1] → missing is 0

Thus is remembered the saga of **Missing Number**,
where the Oracle did not subtract sums
but instead XORed every index with every element --
letting every present number find its twin and vanish,
until the missing heir stood alone
in the silence of perfect cancellation --
revealed not by arithmetic,
but by the annihilation of all that was present. 🕳️⚡✨
