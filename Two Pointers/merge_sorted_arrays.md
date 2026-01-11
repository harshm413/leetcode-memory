## ⚔️📜 _The Union of Two Ordered Armies: The Merge Sorted Array Saga_

> \*"In the Plains of Order,
> two armies stood already disciplined —
> each ranked from weakest to strongest.
>
> The first army held extra empty ground at its rear,
> reserved for a future union.
>
> The Oracle was commanded:
>
> **‘Merge the two armies into one,
> keeping perfect order,
> and do it without summoning new land.’**
>
> She knew the secret was not to start at the front,
> where collisions would cause chaos,
> but at the **end**,
> where empty ground awaited the strongest."\*

---

This is the saga of **Merge Sorted Array**.

You are given:

-   `nums1` of size `m + n`

    -   first `m` elements are valid and sorted
    -   last `n` elements are empty space

-   `nums2` of size `n`, sorted

Your task:

-   Merge `nums2` into `nums1`
-   Result must be sorted
-   Do it **in-place**

---

## 🧠 The Oracle’s Core Insight — Fill from the End

The Oracle realized:

-   The **largest elements** belong at the end
-   Empty space already exists there
-   So compare from the **back**, not the front

Three sentinels were placed:

-   `i` → last valid soldier in `nums1`
-   `j` → last soldier in `nums2`
-   `k` → last position in `nums1`

---

### 📜 The Scroll of Final Positions

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Backward Merge Ritual

_Strongest placed first_

```cpp
void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
    int i = m - 1;
    int j = n - 1;
    int k = m + n - 1;
```

The Oracle stood at the far end of the field.

---

### 🏋️ Compare the Strongest Remaining Warriors

```cpp
    while (i >= 0 && j >= 0) {
        if (nums1[i] > nums2[j]) {
            nums1[k] = nums1[i];
            i--;
        } else {
            nums1[k] = nums2[j];
            j--;
        }
        k--;
    }
```

Whichever army had the stronger warrior
claimed the last open position.

That warrior was placed,
and the battlefield shrank inward.

---

### 🧩 March in Any Remaining Warriors

```cpp
    while (j >= 0) {
        nums1[k] = nums2[j];
        j--;
        k--;
    }
}
```

If the first army finished early,
the remaining warriors of the second
marched in unopposed.

(If `nums1` remains, they are already in place.)

---

### 🎺 The Trial of the United Armies

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3, 0, 0, 0};
    vector<int> nums2 = {2, 5, 6};

    merge(nums1, 3, nums2, 3);

    for (int x : nums1) cout << x << " ";
    cout << endl; // expected: 1 2 2 3 5 6
    return 0;
}
```

The Oracle united the armies into perfect order:

`1 2 2 3 5 6`

No extra land used.
No rank disturbed.

---

### 🧠 Memory of the Backward Union Law

-   Start merging from the **end**
-   Compare largest remaining elements
-   Use three pointers: `i`, `j`, `k`
-   Extra space already exists in `nums1`
-   **Time:** O(m + n)
-   **Space:** O(1)

Thus is remembered the saga of **Merge Sorted Array**,
where the Oracle unites two disciplined armies,
placing the strongest first upon empty ground —
and proving that true order
is achieved not by rushing forward,
but by **building carefully from the end**. ⚔️✨
