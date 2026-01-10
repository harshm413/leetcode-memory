## 🧮📜 _The Broken Ledger of the Realm: The Missing Number (Without Bit Magic) Saga_

> \*"In the Kingdom of Consecutive Numbers,
> a perfect census was promised —
> every number from **0 to n**
> written exactly once in the royal ledger.
>
> But when the scroll was unrolled,
> the Oracle noticed something unsettling:
>
> **the ledger was lighter than it should be.**
>
> One number had vanished.
>
> No arcane bit magic was allowed.
> No tricks of cancellation or XOR.
>
> The Oracle instead trusted
> the oldest law of mathematics —
>
> **the law of sums.**
>
> If the total expected weight of the ledger was known,
> and the actual weight was measured,
> then the missing number would reveal itself
> by simple subtraction."\*

---

This is the saga of **Missing Number**
— solved **without bit manipulation**.

You are given an array `nums` of length `n`
containing **distinct numbers from `0` to `n`**,
with **exactly one number missing**.

Your task:

-   Find the missing number
-   **O(n) time**
-   **O(1) extra space**

---

## 🧠 The Oracle’s Core Insight — The Law of Total Sum

In a perfect realm, the sum of numbers from `0` to `n` is:

```
expectedSum = n * (n + 1) / 2
```

If one number is missing, then:

```
missing = expectedSum - actualSum
```

No tricks.
No swaps.
Just pure arithmetic truth.

---

### 📜 The Scroll of Measured Totals

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚖️ The Oracle’s Ledger-Weighing Ritual

_Compare what should exist with what does_

```cpp
int missingNumber(vector<int>& nums) {
    int n = nums.size();
```

The Oracle counted how many numbers **should** exist.

---

### 📐 Compute the Expected Total

```cpp
    int expectedSum = n * (n + 1) / 2;
```

This was the weight of a **complete ledger**
with no missing entries.

---

### 📊 Measure the Actual Ledger

```cpp
    int actualSum = 0;
    for (int x : nums) {
        actualSum += x;
    }
```

The Oracle summed all numbers that were truly present.

---

### 👑 Reveal the Missing Heir

```cpp
    return expectedSum - actualSum;
}
```

The difference exposed the vanished number.

---

### 🎺 The Trial of the Broken Ledger

```cpp
int main() {
    vector<int> nums = {3, 0, 1};
    cout << missingNumber(nums) << endl; // expected: 2
    return 0;
}
```

The ledger was lighter by **2**.
Thus the missing number was revealed.

---

### 🧠 Memory of the Ledger Law

-   Sum of `0…n` is `n(n+1)/2`
-   Subtract actual sum from expected sum
-   No extra memory required
-   No bit manipulation
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Missing Number (Without Bit Manipulation)**,
where the Oracle does not rely on arcane tricks,
but instead weighs the ledger of the realm itself —
and lets simple arithmetic expose
the number that slipped into silence. 🧮✨
