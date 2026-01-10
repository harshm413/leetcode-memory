## 🏃‍♂️⚔️ _The March of the Silent Stones: The Move Zeroes Saga_

> \*"In the Valley of Values,
> numbers marched in a long line —
> some strong and non-zero,
> others silent as stone,
> carrying the weight of nothingness.
>
> The Oracle was commanded:
>
> **‘Move all silent stones (zeroes)
> to the end of the line,
> while preserving the order
> of all living numbers.’**
>
> No extra land could be used.
> The march had to happen **in place**,
> disciplined,
> and swift."\*

---

This is the saga of **Move Zeroes**.

You are given an array `nums`.
Your task:

-   Move all `0`s to the **end** of the array
-   Keep the **relative order** of non-zero elements
-   Do it **in-place** with **O(1) extra space**

The Oracle chose the **Two-Pointer March**,
where one pointer scouts,
and the other builds the new front line.

---

### 📜 The Scroll of Ordered Marches

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Two-Pointer March

_Scout and builder move as one_

```cpp
void moveZeroes(vector<int>& nums) {
    int k = 0;
```

`k` marked the next position
where a non-zero soldier should stand.

---

### 🧭 Scout the Entire Line

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

Pointer `i` inspected every position in the march.

---

### 🏃‍♂️ Call Forward the Living Numbers

```cpp
        if (nums[i] != 0) {
            nums[k] = nums[i];
            k++;
        }
    }
```

Every non-zero number was moved forward,
preserving order and tightening the formation.

The silent stones were ignored —
for now.

---

### 🪨 Send the Silent Stones to the Rear

```cpp
    while (k < nums.size()) {
        nums[k] = 0;
        k++;
    }
}
```

After all living numbers stood in front,
the remaining positions were filled with zeroes.

The silent stones marched quietly to the back.

---

### 🎺 The Trial of the Silent Stones

```cpp
int main() {
    vector<int> nums = {0, 1, 0, 3, 12};
    moveZeroes(nums);

    for (int x : nums) cout << x << " ";
    cout << endl; // expected: 1 3 12 0 0
    return 0;
}
```

The Oracle reordered the march into:

`1 3 12 0 0`

Order preserved.
Silence contained.

---

### 🧠 Memory of the Silent March

-   Use two pointers:

    -   `i` scans all elements
    -   `k` tracks next non-zero position

-   First pass compacts non-zero values
-   Second pass fills remaining spots with zeroes
-   Order of non-zero elements is preserved
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Move Zeroes**,
where the Oracle leads a disciplined march,
ushering the silent stones to the rear,
and allowing the living numbers
to stride forward without disturbance. 🏃‍♂️✨
