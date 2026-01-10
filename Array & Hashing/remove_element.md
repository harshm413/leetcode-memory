## 🗡️📜 _The Purge of the Unwanted: The Remove Element Saga_

> \*"In the Hall of Numbers stood a long line of values,
> each sworn to serve the realm.
>
> But one value had been declared **forbidden**.
>
> The Oracle was commanded:
>
> **‘Remove all traces of this value —
> in place, without forging a new list —
> and return how many loyal elements remain.’**
>
> Order mattered not,
> only that the unwanted be purged
> and the survivors gathered at the front."\*

---

The Oracle was given:

-   an array `nums`
-   a forbidden value `val`

Her task:

-   **Remove all occurrences of `val` in-place**
-   **Return the count of remaining elements**
-   The first `k` positions of the array must hold the survivors

She chose the ancient **Two-Pointer Rite**,
where one pointer scans the land
and the other builds the purified front.

Thus began the saga of **Remove Element**.

---

### 📜 The Scroll of Mutable Arrays

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The array lay ready for judgment.

---

## 🔮 The Oracle’s Two-Pointer Purification Ritual

_One scans, one writes_

```cpp
int removeElement(vector<int>& nums, int val) {
    int k = 0;
```

The pointer `k` marked the boundary
between purified elements
and the yet-unjudged.

---

### 🧭 Walk Through Every Element

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

Pointer `i` walked the entire array,
inspecting each value.

---

### 🗑️ If the Value Is Forbidden — Skip It

```cpp
        if (nums[i] == val) continue;
```

The unwanted element was ignored,
left behind to fade into irrelevance.

---

### 🏗️ If the Value Is Loyal — Move It Forward

```cpp
        nums[k] = nums[i];
        k++;
    }
```

Every loyal value was moved to the front,
stacked tightly without gaps.

`k` advanced,
marking one more survivor.

---

### 🏁 Return the Count of the Survivors

```cpp
    return k;
}
```

The first `k` elements now formed the purified array.

---

### 🎺 The Trial of the Purge

```cpp
int main() {
    vector<int> nums = {3, 2, 2, 3};
    int val = 3;

    int k = removeElement(nums, val);
    cout << k << endl;        // expected: 2
    for (int i = 0; i < k; i++)
        cout << nums[i] << " ";
    cout << endl;

    return 0;
}
```

The Oracle removed all `3`s.
The survivors stood as `{2, 2}`.
The count returned was **2**.

Order beyond the first `k` mattered not.

---

### 🧠 Memory of the Purge Rite

-   Use two pointers:

    -   `i` scans all elements
    -   `k` builds the valid prefix

-   Copy only elements not equal to `val`
-   In-place, no extra memory
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Remove Element**,
where the Oracle walks the line of values,
silently discarding the forbidden,
and leaving behind only those
worthy to stand at the front of the realm. 🗡️✨
