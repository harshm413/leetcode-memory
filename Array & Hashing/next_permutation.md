## 🔁👑 _The Turning of Lexicographic Fate: The Next Permutation Saga_

> \*"In the Kingdom of Arrangements,
> numbers stood in a precise order —
> one of many possible destinies.
>
> The Oracle was commanded:
>
> **‘Transform this arrangement
> into the very next one
> in lexicographic order.’**
>
> No new realm could be created.
> No list of all destinies could be written.
>
> Only a subtle shift —
> the smallest change
> that would move the sequence
> just one step forward in fate.
>
> And if no such future existed,
> the Oracle must return the world
> to its very first beginning."\*

---

This is the saga of **Next Permutation**.

You are given an array `nums` representing a permutation.
Your task:

-   Rearrange it into the **next lexicographically greater permutation**
-   If no such permutation exists,
    rearrange it into the **lowest possible order**
-   Do everything **in-place**

---

## 🧠 The Oracle’s Core Insight — Find Where Order Breaks

Lexicographic order follows a strict rule:

> From right to left,
> find the **first position where the sequence increases**.

This position is called the **pivot**.

Beyond this pivot,
the suffix is already the **largest possible**,
so only a precise correction can advance fate.

---

## ⚔️ The Oracle’s Three-Step Ritual of Destiny

### 📜 Step 1 — Find the Pivot

```cpp
int i = nums.size() - 2;
while (i >= 0 && nums[i] >= nums[i + 1]) {
    i--;
}
```

The Oracle walked backward,
searching for the first place
where order could still rise.

If none was found,
the sequence was already at its final destiny.

---

### 🔍 Step 2 — Find the Just-Larger Successor

```cpp
if (i >= 0) {
    int j = nums.size() - 1;
    while (nums[j] <= nums[i]) {
        j--;
    }
    swap(nums[i], nums[j]);
}
```

From the rightmost end,
the Oracle found the smallest number
that was **greater than the pivot**,
and swapped them.

This ensured the next arrangement
was the smallest possible step forward.

---

### 🔁 Step 3 — Reverse the Suffix

```cpp
reverse(nums.begin() + i + 1, nums.end());
```

The suffix beyond the pivot
was reversed into its **lowest order**,
completing the transition to the next permutation.

---

## 🧾 The Complete Spell

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

void nextPermutation(vector<int>& nums) {
    int n = nums.size();
    int i = n - 2;

    while (i >= 0 && nums[i] >= nums[i + 1]) i--;

    if (i >= 0) {
        int j = n - 1;
        while (nums[j] <= nums[i]) j--;
        swap(nums[i], nums[j]);
    }

    reverse(nums.begin() + i + 1, nums.end());
}
```

---

### 🎺 The Trial of Changing Fate

```cpp
int main() {
    vector<int> nums = {1, 2, 3};
    nextPermutation(nums);

    for (int x : nums) cout << x << " ";
    cout << endl; // expected: 1 3 2
    return 0;
}
```

The Oracle advanced the sequence by one destiny:

`1 2 3 → 1 3 2`

A minimal change.
A precise evolution.

---

### 🧠 Memory of the Lexicographic Law

-   Find the first decreasing point from the right
-   Swap with the next larger element on the right
-   Reverse the suffix to get smallest order
-   If no pivot exists → reverse entire array
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Next Permutation**,
where the Oracle does not leap recklessly through all possibilities,
but instead nudges destiny forward
by the smallest, most elegant change —
turning order into its next rightful form. 🔁👑✨
