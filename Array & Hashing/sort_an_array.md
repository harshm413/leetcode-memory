## ⚔️📜 _The Great Ordering of Chaos: The Sort an Array Saga_

> \*"In the Realm of Numbers,
> an army stood in perfect disorder —
> strong and weak, large and small,
> all jumbled together with no hierarchy.
>
> The Oracle was commanded:
> **‘Bring order to this chaos.
> Sort the army from weakest to strongest.’**
>
> She knew brute force would exhaust the land,
> and clever tricks would fail at scale.
>
> So she invoked an ancient strategy:
> **Divide the army until each warrior stands alone,
> then merge them back together in perfect order.**
>
> Thus began the saga of **Merge Sort** —
> the art of sorting by division and reunion."\*

---

The task:
Given an array `nums`,
return it **sorted in ascending order**.

The Oracle chose **Merge Sort**, because:

-   Guaranteed **O(n log n)** time
-   Stable and reliable
-   Perfect for large armies of numbers

This was not about shortcuts —
it was about **discipline and structure**.

---

### 📜 The Scroll of Divided Armies

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The chaotic army awaited command.

---

## 🧩 The Oracle’s Merge Ritual

_Unite two ordered halves into one_

```cpp
void merge(vector<int>& nums, int left, int mid, int right) {
    vector<int> temp;
    int i = left, j = mid + 1;
```

Two disciplined squads stood ready:

-   Left squad → `left` to `mid`
-   Right squad → `mid+1` to `right`

---

### ⚔️ Compare and Choose the Weaker First

```cpp
    while (i <= mid && j <= right) {
        if (nums[i] <= nums[j]) {
            temp.push_back(nums[i++]);
        } else {
            temp.push_back(nums[j++]);
        }
    }
```

The Oracle compared front-line warriors
and always chose the weaker first,
ensuring order was preserved.

---

### 🏹 March the Remaining Warriors

```cpp
    while (i <= mid) temp.push_back(nums[i++]);
    while (j <= right) temp.push_back(nums[j++]);
```

If one squad exhausted early,
the rest of the other marched in unopposed.

---

### 🏛️ Replace the Battlefield

```cpp
    for (int k = 0; k < temp.size(); k++) {
        nums[left + k] = temp[k];
    }
}
```

The merged formation replaced the old chaos.

---

## 🔮 The Oracle’s Divide-and-Conquer Spell

_Break until one, then rebuild_

```cpp
void mergeSort(vector<int>& nums, int left, int right) {
    if (left >= right) return;
```

A single warrior needed no sorting.

---

### ✂️ Divide the Army

```cpp
    int mid = left + (right - left) / 2;
    mergeSort(nums, left, mid);
    mergeSort(nums, mid + 1, right);
```

The Oracle split the army again and again,
until every soldier stood alone.

---

### 🔗 Reunite with Order

```cpp
    merge(nums, left, mid, right);
}
```

From smallest fragments,
a perfectly ordered army was rebuilt.

---

## 🏁 The Final Invocation

```cpp
vector<int> sortArray(vector<int>& nums) {
    mergeSort(nums, 0, nums.size() - 1);
    return nums;
}
```

The Oracle commanded the ritual,
and order spread across the land.

---

### 🎺 The Trial of the Chaotic Army

```cpp
int main() {
    vector<int> nums = {5, 2, 3, 1};
    nums = sortArray(nums);

    for (int x : nums) cout << x << " ";
    cout << endl; // expected: 1 2 3 5
    return 0;
}
```

From chaos arose harmony:
`1 2 3 5`

The army stood ranked,
each warrior knowing their place.

---

### 🧠 Memory of the Great Ordering

-   Divide the array until size = 1
-   Merge sorted halves carefully
-   Always pick the smaller element first
-   **Time:** O(n log n)
-   **Space:** O(n) extra memory
-   Stable, predictable, and powerful

Thus is remembered the saga of **Sort an Array**,
where the Oracle brings order not by force,
but by patience, division,
and the disciplined reunion of numbers
into perfect ascending harmony. ⚔️✨
