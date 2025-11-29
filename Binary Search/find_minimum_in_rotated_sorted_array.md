## 🧭 _The Hidden Dawn: The Find Minimum in Rotated Sorted Array Saga_

> _"A line once ordered was turned upon itself,
> its dawn pushed somewhere into the night.
> To find the first light, do not wander blindly —
> divide the darkness, follow the tilt,
> and the sun will reveal its smallest flame."_

---

Once, a kingdom arranged its numbers in perfect ascending order — a serene sunrise across the horizon.
But one night, the Wheel of Rotation spun the kingdom. The front was moved to the back, the dawn buried somewhere mid-sky.

Travelers stood confused:
Where is the **smallest** number now — the first light of the new dawn?

The Seeker of Horizons used not brute force but **binary search**, knowing that in a rotated land, one half always remained sorted, and the other half held the lost sunrise.

Thus began the saga of **Find Minimum in Rotated Sorted Array**.

---

### 📜 The Seeker’s Compass

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The Seeker carried a compass (`l` and `r`) to divide the land and a map (`nums`) representing the quietly rotated kingdom.

---

### 🌅 The Dawn-Finding Ritual — Binary Search

```cpp
int findMin(vector<int>& nums) {
    int l = 0, r = nums.size() - 1;
```

Two markers stood at the edges of the realm: `l` at the leftmost peak, `r` at the rightmost cliff. The dawn lay somewhere between them.

---

```cpp
    while (l < r) {
        int mid = l + (r - l) / 2;
```

The Seeker thrust a staff into the middle — `mid` — splitting the land into two halves.

---

```cpp
        if (nums[mid] > nums[r]) {
            // the dawn is to the right
            l = mid + 1;
        } else {
            // mid is in the rotated (smaller) half
            r = mid;
        }
    }
    return nums[l];
}
```

The Seeker compared `nums[mid]` with `nums[r]`.
From this simple insight, truth emerged:

-   If `nums[mid] > nums[r]`, the middle lies **after** the dawn — in the darker half.
    The sunrise must be farther right, so the Seeker marched `l = mid + 1`.

-   Otherwise, the middle is in the half **containing** the dawn or near it.
    The Seeker pulled `r = mid`, narrowing the search.

When `l == r`, the dawn stood revealed — the smallest element returned.

---

### 🎺 The Trial of the Rotated Kingdom

```cpp
int main() {
    vector<int> nums = {4,5,6,7,0,1,2};
    cout << findMin(nums) << endl; // expected: 0
    return 0;
}
```

In a kingdom rotated so that the dawn (0) was buried after 7, the Seeker’s ritual found it swiftly — dividing and narrowing until the first light glowed alone.

---

### 🧠 Memory of the Hidden Dawn

-   **Binary search** — because the land is “almost sorted” with only one rotated pivot.
-   **Key comparison:**

    -   If `nums[mid] > nums[r]` → dawn lies to the **right**.
    -   Else → dawn lies to the **left** (including `mid`).

-   **Loop ends** when `l == r`: the smallest element.
-   **Time:** O(log n) — divide at each step.
-   **Space:** O(1).

Thus is remembered the saga of **Find Minimum in Rotated Sorted Array**,
where a kingdom twisted by rotation yields its hidden sunrise only to those who split the land with wisdom and follow the tilt of the numbers. 🌅✨
