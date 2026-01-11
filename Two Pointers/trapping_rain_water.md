## 🌧️🏰 _The Valley That Held the Storm: The Trapping Rain Water Saga_

> \*"Between towering walls of stone
> lay valleys forgotten by the sun.
>
> When the storm came,
> rain poured relentlessly —
> yet not all water escaped to the sea.
>
> Some was trapped,
> held prisoner between walls,
> rising only as high
> as the **shortest boundary allowed**.
>
> The Oracle was commanded:
>
> **‘Tell us how much rainwater
> these lands can trap after the storm.’**
>
> She knew this was not about counting drops,
> but about understanding **boundaries**."\*

---

This is the saga of **Trapping Rain Water**.

You are given an array `height`,
where each element represents the height of a bar.

Your task:

-   Compute how much **rainwater** can be trapped
-   After raining
-   Between the bars

---

## 🧠 The Oracle’s Core Insight — Water Depends on Boundaries

For any position `i`:

```
water[i] = min(maxLeft, maxRight) - height[i]
```

Where:

-   `maxLeft` = tallest wall to the left of `i`
-   `maxRight` = tallest wall to the right of `i`

Water can only rise as high as
the **shorter boundary**.

But calculating this for every index separately
would be slow and wasteful.

So the Oracle chose a wiser path.

---

## ⚔️ The Oracle’s Two-Sentinel Storm Ritual

_Track the highest walls from both sides_

Instead of precomputing arrays,
the Oracle placed two sentinels:

-   One from the **left**
-   One from the **right**

Each carried knowledge of the tallest wall seen so far.

---

### 📜 The Scroll of Storm and Stone

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🌧️ The Two-Pointer Rain Ritual

```cpp
int trap(vector<int>& height) {
    int left = 0;
    int right = height.size() - 1;
    int leftMax = 0;
    int rightMax = 0;
    int water = 0;
```

The sentinels stood at the edges.
The storm began.

---

### 🌊 Walk Inward, One Drop at a Time

```cpp
    while (left < right) {
        if (height[left] <= height[right]) {
```

The Oracle compared the boundary walls.

---

### 🧱 Left Side Is the Limiting Wall

```cpp
            if (height[left] >= leftMax) {
                leftMax = height[left];
            } else {
                water += leftMax - height[left];
            }
            left++;
```

If the current wall was lower than the best seen so far,
water filled the gap.

Otherwise, a new boundary was established.

---

### 🧱 Right Side Is the Limiting Wall

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

The same logic applied symmetrically on the right.

Each step accounted for trapped water **locally**,
without ever revisiting past terrain.

---

### 🎺 The Trial of the Flooded Valley

```cpp
int main() {
    vector<int> height = {0,1,0,2,1,0,1,3,2,1,2,1};
    cout << trap(height) << endl; // expected: 6
    return 0;
}
```

The Oracle measured the storm’s aftermath:

-   Total trapped rainwater = **6 units**

The valley had held the storm.

---

### 🧠 Memory of the Storm Law

-   Water trapped depends on shorter boundary
-   Two pointers from both ends
-   Maintain `leftMax` and `rightMax`
-   Process the smaller side first
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Trapping Rain Water**,
where the Oracle reads the land not by counting rain,
but by understanding walls and valleys —
measuring every trapped drop
as the storm fades into legend. 🌧️✨
