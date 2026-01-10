## 🧭⚔️ _The Lost Number of the Realm: The First Missing Positive Saga_

> \*"In the Kingdom of Integers,
> numbers wandered freely —
> positives mingled with negatives,
> zeros hid in shadows,
> and duplicates caused confusion.
>
> The Oracle was summoned with a sharp command:
>
> **‘Find the smallest positive number
> that does NOT exist in this realm.’**
>
> No extra ledgers were allowed.
> No auxiliary scrolls could be used.
>
> Only the land itself could be reshaped.
>
> Thus the Oracle devised a bold strategy:
> **place every rightful number
> onto its destined throne —
> and then see which throne remains empty.’**"\*

---

This is the saga of **First Missing Positive**.

You are given an unsorted integer array `nums`.
Your task is to find the **smallest missing positive integer**
in **O(n) time** and **O(1) extra space**.

The Oracle knew an ancient truth:

> If the array has length `n`,
> the answer must lie between **1 and n+1**.

So she turned the array itself
into a battlefield of placement.

---

## 🧠 The Oracle’s Core Insight

_Every number belongs to one throne_

-   Number `1` belongs at index `0`
-   Number `2` belongs at index `1`
-   …
-   Number `x` belongs at index `x - 1`

If every number sat correctly,
the realm would contain `1…n`,
and the answer would be `n + 1`.

Thus began the **Cyclic Placement Ritual**.

---

### 📜 The Scroll of Mutable Destiny

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Throne-Placement Ritual

_Place numbers where they belong_

```cpp
int firstMissingPositive(vector<int>& nums) {
    int n = nums.size();
```

The Oracle surveyed the realm.

---

### 🔄 Phase 1 — Seat the Numbers on Their Thrones

```cpp
    for (int i = 0; i < n; i++) {
        while (nums[i] > 0 && nums[i] <= n
               && nums[nums[i] - 1] != nums[i]) {
            swap(nums[i], nums[nums[i] - 1]);
        }
    }
```

At each position:

-   If the number was **positive**
-   And within the valid range `[1, n]`
-   And not already seated correctly

The Oracle swapped it into its rightful throne.

This continued until:

-   the number was invalid, or
-   already correctly placed, or
-   a duplicate blocked the way

Chaos slowly turned into structure.

---

### 🔍 Phase 2 — Find the Empty Throne

```cpp
    for (int i = 0; i < n; i++) {
        if (nums[i] != i + 1)
            return i + 1;
    }
```

The Oracle walked the thrones in order:

-   The first throne where the rightful number was missing
    revealed the smallest missing positive.

---

### 🌟 If All Thrones Are Filled

```cpp
    return n + 1;
}
```

If every throne from `1` to `n` was occupied,
the missing number lay just beyond the realm.

---

### 🎺 The Trial of the Disordered Realm

```cpp
int main() {
    vector<int> nums = {3, 4, -1, 1};
    cout << firstMissingPositive(nums) << endl; // expected: 2
    return 0;
}
```

The Oracle seated:

-   `1` at index `0`
-   `3` at index `2`
-   `4` beyond concern

Throne `2` stood empty.
Thus the answer was **2**.

---

### 🧠 Memory of the Throne Law

-   Only numbers `1…n` matter
-   Place each number at index `num - 1`
-   Ignore negatives, zeros, and large numbers
-   First mismatch reveals the answer
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **First Missing Positive**,
where the Oracle reshapes the realm itself,
seating every number upon its rightful throne —
and discovering the missing ruler
by the silence of an empty seat. 🧭✨
