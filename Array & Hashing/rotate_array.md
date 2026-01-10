## 🔄👑 _The Turning of the Royal Circle: The Rotate Array Saga_

> \*"In the Kingdom of Cycles,
> numbers stood in a proud line,
> each knowing its position in the realm.
>
> One day, the King commanded a grand ritual:
>
> **‘Rotate the line to the right by _k_ steps.
> Let those at the end lead,
> and those at the front follow.’**
>
> The Oracle was forbidden
> from forging a new array.
>
> The transformation had to happen **in place**,
> with elegance and precision.
>
> Thus she invoked
> the ancient Triple-Reversal Rite —
> where turning the whole,
> then turning the parts,
> reshapes destiny itself."\*

---

This is the saga of **Rotate Array**.

You are given:

-   an array `nums`
-   an integer `k`

Your task:

-   Rotate the array **to the right** by `k` steps
-   Do it **in-place**
-   Use **O(1) extra space**

---

## 🧠 The Oracle’s Core Insight — Rotation Through Reversal

Rotating right by `k` is the same as:

1. Reverse the **entire array**
2. Reverse the **first k elements**
3. Reverse the **remaining elements**

But first, the Oracle normalized destiny:

```
k = k % n
```

So the rotation never exceeded the array’s length.

---

### 📜 The Scroll of Cyclic Transformations

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔮 The Oracle’s Triple-Reversal Ritual

### 🪞 Helper Spell — Reverse a Segment

```cpp
void reverse(vector<int>& nums, int l, int r) {
    while (l < r) {
        swap(nums[l], nums[r]);
        l++;
        r--;
    }
}
```

This spell mirrored a segment in place.

---

### 🔄 The Main Rotation Spell

```cpp
void rotate(vector<int>& nums, int k) {
    int n = nums.size();
    k = k % n;
```

The Oracle adjusted the number of turns
to fit the circle perfectly.

---

### 🔁 Step 1 — Reverse the Entire Realm

```cpp
    reverse(nums, 0, n - 1);
```

The whole line was turned upside down.

---

### 👑 Step 2 — Reverse the First k Elements

```cpp
    reverse(nums, 0, k - 1);
```

The new leaders were placed in proper order.

---

### 🏁 Step 3 — Reverse the Remaining Elements

```cpp
    reverse(nums, k, n - 1);
}
```

The followers reclaimed their internal order.

The rotation was complete.

---

### 🎺 The Trial of the Turning Circle

```cpp
int main() {
    vector<int> nums = {1, 2, 3, 4, 5, 6, 7};
    rotate(nums, 3);

    for (int x : nums) cout << x << " ";
    cout << endl; // expected: 5 6 7 1 2 3 4
    return 0;
}
```

The Oracle rotated the line by 3 steps:

`5 6 7 1 2 3 4`

No extra space.
Perfect symmetry.

---

### 🧠 Memory of the Turning Rite

-   Normalize `k` using modulo
-   Reverse entire array
-   Reverse first `k` elements
-   Reverse remaining `n-k` elements
-   In-place rotation
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Rotate Array**,
where the Oracle turns the royal line through mirrors,
reshaping destiny with three precise reversals —
and proving that even great rotations
can be achieved without moving a single stone
outside the realm. 🔄👑✨
