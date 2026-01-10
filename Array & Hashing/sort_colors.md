## 🎨⚔️ _The Three Banners of Order: The Sort Colors Saga_

> \*"In the Kingdom of Tricolor,
> warriors marched under three banners only —
> **Red (0)**, **White (1)**, and **Blue (2)**.
>
> They stood in chaos,
> banners interwoven without rank or formation.
>
> The Oracle was commanded:
> **‘Arrange them so that Reds lead,
> Whites follow,
> and Blues stand at the end —
> all in a single march,
> without counting scrolls or extra space.’**
>
> To do this, she summoned the ancient
> **Dutch National Flag Ritual**,
> where three sentinels divide the land
> and every warrior is placed instantly in destiny’s lane."\*

---

The task:
Given an array `nums` containing only `0`, `1`, and `2`,
sort it **in-place** so that:

```
0s come first,
then 1s,
then 2s.
```

No extra array.
One single pass.
Pure pointer discipline.

Thus began the saga of **Sort Colors**.

---

### 📜 The Scroll of Three Banners

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The battlefield was prepared.

---

## ⚔️ The Oracle’s Three-Sentinel Ritual

_Low, Mid, High — guardians of color_

```cpp
void sortColors(vector<int>& nums) {
    int low = 0;
    int mid = 0;
    int high = nums.size() - 1;
```

Three sentinels were placed:

-   **low** → boundary of Reds (0)
-   **mid** → current warrior under inspection
-   **high** → boundary of Blues (2)

The land was divided into four zones:

```
[0 ... low-1]     -> Reds (0)
[low ... mid-1]   -> Whites (1)
[mid ... high]    -> Unknown
[high+1 ... end]  -> Blues (2)
```

---

### 🟥 Case 1 — Red Warrior (0)

```cpp
    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums[low], nums[mid]);
            low++;
            mid++;
        }
```

When a Red appeared:

-   Swap it to the front (`low`)
-   Advance both `low` and `mid`
-   Red is now exactly where it belongs

---

### ⚪ Case 2 — White Warrior (1)

```cpp
        else if (nums[mid] == 1) {
            mid++;
        }
```

White needs no movement.
It already belongs in the middle.
The Oracle simply moved forward.

---

### 🟦 Case 3 — Blue Warrior (2)

```cpp
        else {
            swap(nums[mid], nums[high]);
            high--;
        }
    }
}
```

When Blue appeared:

-   Swap it to the end (`high`)
-   Decrease `high`
-   **Do not move `mid`**,
    because the swapped warrior must still be judged

Thus the battlefield shrank inward,
order emerging with each step.

---

### 🎺 The Trial of the Tricolor Army

```cpp
int main() {
    vector<int> nums = {2, 0, 2, 1, 1, 0};
    sortColors(nums);

    for (int x : nums) cout << x << " ";
    cout << endl; // expected: 0 0 1 1 2 2
    return 0;
}
```

The Oracle marched once across the land,
and the warriors fell into perfect order:

`0 0 1 1 2 2`

No second pass.
No extra space.
Pure discipline.

---

### 🧠 Memory of the Three-Banner Law

-   Use **three pointers**: low, mid, high
-   Process array in **one pass**
-   Swap based on current color
-   Do **not** increment mid when swapping with high
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Sort Colors**,
where the Oracle commands three sentinels,
and in a single flawless march,
brings order to the Tricolor Kingdom —
Red first, White centered, Blue at the end. 🎨⚔️✨
