## 🗡️📜 _The Cleansing of Repeated Echoes: The Remove Duplicates Saga_

> \*"In the Hall of Ordered Numbers,
> values stood already in **sorted order**,
> yet some voices echoed more than once.
>
> The Oracle was commanded:
>
> **‘Remove the echoes.
> Let each number speak only once.
> Do it in place,
> and return how many unique voices remain.’**
>
> The hall must stay ordered.
> No new hall may be built.
>
> Thus the Oracle chose
> the Rite of the Two Walkers —
> one to explore,
> and one to preserve purity."\*

---

This is the saga of **Remove Duplicates from Sorted Array**.

You are given a **sorted** array `nums`.
Your task:

-   Remove duplicates **in-place**
-   Keep only **unique elements** at the front
-   Return the count of unique elements
-   Order must remain intact

Thus began the cleansing.

---

## 🧠 The Oracle’s Core Insight — Order Is Power

Because the array is already **sorted**:

-   All duplicates stand **next to each other**
-   A new number is unique **only when it differs from the last kept one**

So the Oracle walks forward,
keeping only the first occurrence of every value.

---

### 📜 The Scroll of Ordered Purity

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Two-Walker Cleansing Ritual

_One scans, one writes_

```cpp
int removeDuplicates(vector<int>& nums) {
    if (nums.empty()) return 0;
```

If the hall was empty,
there was nothing to cleanse.

---

### 🧭 Initialize the Keeper of Uniqueness

```cpp
    int k = 1;
```

The first number is always unique.
`k` marks where the next unique value should be placed.

---

### 👁️ Walk the Hall and Judge

```cpp
    for (int i = 1; i < nums.size(); i++) {
        if (nums[i] != nums[i - 1]) {
            nums[k] = nums[i];
            k++;
        }
    }
```

At each step:

-   If the current number differs from the previous
    → it is **unique**
-   The Oracle writes it forward
-   `k` advances

Repeated echoes are ignored.

---

### 🏁 Return the Count of Pure Voices

```cpp
    return k;
}
```

The first `k` positions now hold
all unique elements.

---

### 🎺 The Trial of the Cleansed Hall

```cpp
int main() {
    vector<int> nums = {1, 1, 2, 2, 3};
    int k = removeDuplicates(nums);

    cout << k << endl;        // expected: 3
    for (int i = 0; i < k; i++)
        cout << nums[i] << " ";
    cout << endl;

    return 0;
}
```

The Oracle purified the hall into:

`1 2 3`

The count of unique voices was **3**.

---

### 🧠 Memory of the Cleansing Rite

-   Array is already sorted
-   Duplicates appear consecutively
-   Keep first occurrence only
-   Two pointers:

    -   `i` scans
    -   `k` writes uniques

-   In-place, no extra space
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Remove Duplicates**,
where the Oracle walks a hall already in order,
silencing repeated echoes,
and allowing each number
to speak only once in the realm of purity. 🗡️✨
