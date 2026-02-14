## 🌧️🏰 _The Valley That Captured the Storm: The Trapping Rain Water Saga_

> \*"Across the rugged land stood uneven walls of stone —
> some tall, some broken, some barely rising above the earth.
>
> Then came the storm.
> Rain fell without mercy.
>
> Yet not all water escaped.
> Some was trapped —
> held prisoner between greater heights.
>
> The Oracle was commanded:
>
> **‘Tell us how much water remains
> after the storm has passed.’**
>
> She knew the secret:
> water cannot rise higher than
> the shorter boundary that holds it."\*

---

This is the epic saga of **Trapping Rain Water**.

You are given an array `height`
where each element represents the elevation of a bar.

Your task:

-   Compute how much **rainwater** can be trapped
-   After raining

---

## 🧠 The Oracle’s Core Insight — Water is Limited by Boundaries

For any position `i`:

```
water[i] = min(maxLeft, maxRight) - height[i]
```

Where:

-   `maxLeft` = tallest wall to the left
-   `maxRight` = tallest wall to the right

But computing these separately for every index
would be slow.

Instead, the Oracle chose a wiser ritual.

---

## ⚔️ The Two-Watchtower Strategy

_Let the shorter wall decide_

If:

-   `leftMax < rightMax` →
    water trapped depends on `leftMax`
-   Otherwise →
    depends on `rightMax`

Thus two pointers march inward.

---

### 📜 The Scroll of Elevations

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🌊 The Two-Pointer Rain Ritual

```cpp
int trap(vector<int>& height) {
    int left = 0;
    int right = height.size() - 1;
    int leftMax = 0;
    int rightMax = 0;
    int water = 0;
```

Two sentinels stand at both ends.

---

### 🧭 March Toward the Center

```cpp
    while (left < right) {
        if (height[left] < height[right]) {
```

The smaller boundary determines the limit.

---

### 🧱 Process Left Side

```cpp
            if (height[left] >= leftMax) {
                leftMax = height[left];
            } else {
                water += leftMax - height[left];
            }
            left++;
```

If current wall is lower than the tallest seen so far,
water fills the gap.

---

### 🧱 Process Right Side

```cpp
        } else {
            if (height[right] >= rightMax) {
                rightMax = height[right];
            } else {
                water += rightMax - height[right];
            }
            right--;
        }
    }
    return water;
}
```

Same logic mirrored on the right.

---

### 🎺 The Trial of the Storm

```cpp
int main() {
    vector<int> height = {0,1,0,2,1,0,1,3,2,1,2,1};
    cout << trap(height) << endl;
    // expected: 6
    return 0;
}
```

The Oracle measured the valley.

Total trapped water: **6 units**

---

### 🧠 Memory of the Storm Law

-   Use two pointers
-   Track `leftMax` and `rightMax`
-   Move the smaller boundary
-   Add trapped water locally
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Trapping Rain Water**,
where the Oracle reads the terrain not by counting rain,
but by understanding walls —
knowing that water rests only
where greater heights
stand guard on both sides. 🌧️✨
