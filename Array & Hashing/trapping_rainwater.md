## 🌧️🏛️ _The Pools Between the Pillars: The Trapping Rain Water (Prefix/Suffix) Saga_

> \_"Along the Kingdom's great wall,
> pillars of stone rose to different heights.
> When the rains came,
> water pooled in the valleys between them.
>
> The King commanded:
>
> **'How much water is trapped after the rain?'**
>
> The Oracle studied the wall.
>
> She realized that the water above any position
> depended on two things:
>
> **The tallest pillar to its LEFT.
> The tallest pillar to its RIGHT.
> Water rises to the SHORTER of these two walls --
> then subtract the pillar's own height.**
>
> `water[i] = min(maxLeft[i], maxRight[i]) - height[i]`
>
> If this was negative -- no water, the pillar was too tall.
>
> She precomputed both arrays in two passes,
> then summed the water in a third.
>
> Three passes. Two arrays.
> Every drop counted."\_

---

This is the saga of **Trapping Rain Water (Prefix/Suffix Approach)**.

Given an array `height` where `height[i]` is the elevation at position `i`:

-   Compute how much water can be trapped after raining.

```
Input:  [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

Input:  [4,2,0,3,2,5]
Output: 9
```

---

## 🧠 The Oracle's Core Insight -- Water at Each Column

For any position `i`:

```
water[i] = min(maxLeft[i], maxRight[i]) - height[i]
```

-   `maxLeft[i]` = tallest pillar from index 0 to i (inclusive).
-   `maxRight[i]` = tallest pillar from index i to n-1 (inclusive).

Water cannot rise above the shorter bounding wall.
The pillar's own height displaces water.

If `min(maxLeft, maxRight) <= height[i]` → no water at this position.

```
Time:  O(N) -- three linear passes
Space: O(N) -- two prefix arrays
```

---

### 📜 The Scroll of the Rainy Kingdom

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Rain-Counting Ritual

```cpp
int trap(vector<int>& height) {
    int n = height.size();
    if (n < 3) return 0;
```

Fewer than 3 pillars can never trap water --
you need at least a left wall, a valley, and a right wall.

---

## ⬅️ Pass One -- Build maxLeft

```cpp
    vector<int> maxLeft(n);
    maxLeft[0] = height[0];
    for (int i = 1; i < n; i++) {
        maxLeft[i] = max(maxLeft[i - 1], height[i]);
    }
```

Walk left to right. At each position,
the tallest wall to the left is the max of
the previous tallest and the current height.

`maxLeft[i]` = the highest pillar from index 0 through i.

---

## ➡️ Pass Two -- Build maxRight

```cpp
    vector<int> maxRight(n);
    maxRight[n - 1] = height[n - 1];
    for (int i = n - 2; i >= 0; i--) {
        maxRight[i] = max(maxRight[i + 1], height[i]);
    }
```

Walk right to left. Mirror image.

`maxRight[i]` = the highest pillar from index i through n-1.

---

## 💧 Pass Three -- Sum the Water

```cpp
    int water = 0;
    for (int i = 0; i < n; i++) {
        water += min(maxLeft[i], maxRight[i]) - height[i];
    }
    return water;
}
```

At each position, the water level is `min(maxLeft, maxRight)`.
Subtract the pillar height to get the water depth.
Sum everything.

> _"The water above each pillar
> is the gap between the water level
> and the stone beneath.
> The water level is set by the shorter of the two tallest walls."_

---

### 🎺 The Trial of the Rainy Wall

```cpp
int main() {
    vector<int> h1 = {0,1,0,2,1,0,1,3,2,1,2,1};
    cout << trap(h1) << endl; // expected: 6

    vector<int> h2 = {4,2,0,3,2,5};
    cout << trap(h2) << endl; // expected: 9

    return 0;
}
```

---

**Full trace for `[0,1,0,2,1,0,1,3,2,1,2,1]`:**

**maxLeft:**

| i  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11|
|----|---|---|---|---|---|---|---|---|---|---|---|---|
| h  | 0 | 1 | 0 | 2 | 1 | 0 | 1 | 3 | 2 | 1 | 2 | 1 |
| mL | 0 | 1 | 1 | 2 | 2 | 2 | 2 | 3 | 3 | 3 | 3 | 3 |

**maxRight:**

| i  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11|
|----|---|---|---|---|---|---|---|---|---|---|---|---|
| mR | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 2 | 2 | 2 | 1 |

**Water at each position:**

| i  | h | mL | mR | min(mL,mR) | water = min-h |
|----|---|----|----|------------|---------------|
| 0  | 0 | 0  | 3  | 0          | 0             |
| 1  | 1 | 1  | 3  | 1          | 0             |
| 2  | 0 | 1  | 3  | 1          | 1             |
| 3  | 2 | 2  | 3  | 2          | 0             |
| 4  | 1 | 2  | 3  | 2          | 1             |
| 5  | 0 | 2  | 3  | 2          | 2             |
| 6  | 1 | 2  | 3  | 2          | 1             |
| 7  | 3 | 3  | 3  | 3          | 0             |
| 8  | 2 | 3  | 2  | 2          | 0             |
| 9  | 1 | 3  | 2  | 2          | 1             |
| 10 | 2 | 3  | 2  | 2          | 0             |
| 11 | 1 | 3  | 1  | 1          | 0             |

**Total water: 0+0+1+0+1+2+1+0+0+1+0+0 = 6** ✓

---

**Trace for `[4, 2, 0, 3, 2, 5]`:**

| i | h | maxLeft | maxRight | min  | water |
|---|---|---------|----------|------|-------|
| 0 | 4 | 4       | 5        | 4    | 0     |
| 1 | 2 | 4       | 5        | 4    | 2     |
| 2 | 0 | 4       | 5        | 4    | 4     |
| 3 | 3 | 4       | 5        | 4    | 1     |
| 4 | 2 | 4       | 5        | 4    | 2     |
| 5 | 5 | 5       | 5        | 5    | 0     |

**Total: 0+2+4+1+2+0 = 9** ✓

---

**Trace for `[3, 0, 3]`:**

maxLeft = [3, 3, 3], maxRight = [3, 3, 3].
Water at index 1: min(3,3) - 0 = 3.

**Answer: 3** ✓ -- a simple valley between two equal walls.

---

**Trace for `[1, 2, 3, 4, 5]` (strictly increasing):**

maxLeft = [1,2,3,4,5], maxRight = [5,5,5,5,5].
At every position: min(mL, mR) = mL = height[i]. Water = 0 everywhere.

**Answer: 0** ✓ -- no valleys, water slides off the slope.

---

## 🔍 Why Include height[i] in maxLeft and maxRight?

Including the current height ensures that `min(maxLeft, maxRight) >= height[i]`.
This means `water[i]` is always >= 0 -- no need for a `max(0, ...)` guard.

If we excluded height[i] from the prefix arrays,
we'd need to clamp negative values to zero.
Including it is cleaner.

---

## 🔄 Limitations of This Approach

This approach uses O(N) extra space for the two arrays.
The **Two Pointers** approach achieves the same result in O(1) space
by computing maxLeft and maxRight on the fly.

But the prefix/suffix approach is the most intuitive --
it directly implements the formula `min(maxLeft, maxRight) - height[i]`
with no tricks, no cleverness, just precomputation.

---

### 🧠 Memory of the Prefix Rain Law

-   **Water at position i:** `min(maxLeft[i], maxRight[i]) - height[i]`
-   **maxLeft[i]:** `max(maxLeft[i-1], height[i])` -- left to right pass
-   **maxRight[i]:** `max(maxRight[i+1], height[i])` -- right to left pass
-   **Sum** all water values for the answer
-   Including `height[i]` in both arrays guarantees water >= 0
-   First and last positions always have water = 0
-   **Time:** O(N) -- three linear passes
-   **Space:** O(N) -- two prefix arrays
-   **Edge cases:**
    -   Less than 3 bars → 0
    -   All same height → 0
    -   Strictly increasing or decreasing → 0
    -   Single valley → water = min(walls) - valley depth

Thus is remembered the saga of **Trapping Rain Water (Prefix/Suffix)**,
where the Oracle precomputed the tallest wall
to the left and right of every pillar --
then walked the wall one final time,
measuring the gap between the water level
and the stone beneath --
summing every drop into the total
that the rains had left behind. 🌧️🏛️✨
