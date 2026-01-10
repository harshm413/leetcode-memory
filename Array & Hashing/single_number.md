## 🏹📜 _The Ledger of Twin Shadows: The Single Number (Without Bit Magic) Saga_

> \*"In the Assembly of Numbers,
> every warrior was sworn to arrive with a twin.
> Two by two they stood —
> mirrored, equal, inseparable.
>
> Yet the Oracle sensed imbalance.
> One warrior stood alone,
> casting a shadow unmatched by any other.
>
> She was commanded:
>
> **‘Find the lone warrior —
> but do not invoke the forbidden art of bit magic.
> Use only counting, memory, and truth.’**
>
> Thus the Oracle reached not for cancellation spells,
> but for a **ledger of uniqueness**,
> where each warrior’s name was written only once."\*

---

This is the saga of **Single Number**
— solved **without bit manipulation**.

You are given an array `nums` where:

-   Every element appears **exactly twice**
-   Except **one element**, which appears **once**

Your task:

-   Find the single element
-   **O(n) time**
-   Extra space is allowed

---

## 🧠 The Oracle’s Core Insight — The Law of Double Weight

The Oracle observed a powerful truth:

-   If every number appears **twice**,
    then the **sum of all numbers × 2**
    equals the **sum of unique numbers × 2**
-   Except for the lone warrior,
    who is counted **only once** in the array

So the missing balance is revealed by:

```
single = (sum of unique elements × 2) − (sum of all elements)
```

The Oracle would use a **set**
to ensure each warrior was written only once.

---

### 📜 The Scroll of Honest Counting

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## 📘 The Oracle’s Ledger Ritual

_Count each warrior only once_

```cpp
int singleNumber(vector<int>& nums) {
    unordered_set<int> seen;
    int uniqueSum = 0;
    int totalSum = 0;
```

Two tallies were prepared:

-   `uniqueSum` → sum of all unique warriors
-   `totalSum` → sum of everyone present

---

### 🧭 Walk Through the Assembly

```cpp
    for (int x : nums) {
        totalSum += x;
        if (seen.insert(x).second) {
            uniqueSum += x;
        }
    }
```

As each number appeared:

-   It was added to the total sum
-   If it had never been seen before,
    it was added to the unique ledger

Duplicates were ignored in the unique count.

---

### 👑 Reveal the Lone Warrior

```cpp
    return 2 * uniqueSum - totalSum;
}
```

The arithmetic exposed the imbalance —
the lone survivor.

---

### 🎺 The Trial of the Twin Assembly

```cpp
int main() {
    vector<int> nums = {4, 1, 2, 1, 2};
    cout << singleNumber(nums) << endl; // expected: 4
    return 0;
}
```

The Oracle counted:

-   Unique sum = `4 + 1 + 2 = 7`
-   Total sum = `4 + 1 + 2 + 1 + 2 = 10`

So the lone warrior was:

```
2 × 7 − 10 = 4
```

Truth revealed.

---

### 🧠 Memory of the Honest Ledger Law

-   Use a set to track unique elements
-   Maintain two sums
-   Formula: `(2 × uniqueSum) − totalSum`
-   No bit manipulation
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Single Number (Without Bit Magic)**,
where the Oracle abandons arcane tricks,
and instead balances the books of existence itself —
letting pure arithmetic reveal
the lone warrior who stood without a twin. 🏹✨
