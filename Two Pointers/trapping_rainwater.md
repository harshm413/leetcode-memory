## 🌧️🏹 _The Dance of Two Sentinels: The Trapping Rain Water (Two Pointers) Saga_

> \_"The Oracle had solved the rain water problem
> with prefix and suffix arrays -- O(N) time, O(N) space.
>
> But the King demanded perfection:
>
> **'Solve it with no extra arrays. O(1) space.'**
>
> The Oracle devised the Two Pointer dance.
>
> Two sentinels stood at opposite ends of the wall --
> one at the left edge, one at the right.
> Each carried a torch recording the tallest wall
> they had seen on their side.
>
> At every step, the sentinel on the **shorter** side moved inward.
>
> Why the shorter side?
>
> **If the left wall is shorter than the right wall,
> the water at the left position is bounded by maxLeft --
> because the right side GUARANTEES a wall at least as tall.
> We don't need to know the exact maxRight.**
>
> **If the right wall is shorter,
> the water at the right position is bounded by maxRight --
> because the left side guarantees a taller wall.**
>
> Process the shorter side. Move inward.
> The taller side's guarantee makes the formula exact.
>
> One pass. Two pointers. O(1) space."\_

---

This is the saga of **Trapping Rain Water (Two Pointers Approach)**.

Given an array `height` where `height[i]` is the elevation at position `i`:

-   Compute how much water can be trapped after raining.
-   **O(N) time, O(1) space.**

```
Input:  [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

Input:  [4,2,0,3,2,5]
Output: 9
```

---

## 🧠 The Oracle's Core Insight -- The Shorter Side Is Safe

The water at any position is `min(maxLeft, maxRight) - height[i]`.

We don't need BOTH maxLeft and maxRight precomputed.
We only need the one that is the **bottleneck** --
and the bottleneck is always the shorter side.

```
If height[left] <= height[right]:
  maxRight >= height[right] >= height[left]
  So min(maxLeft, maxRight) = maxLeft  (the bottleneck)
  Water at left = maxLeft - height[left]
  Move left inward.

If height[left] > height[right]:
  maxLeft >= height[left] > height[right]
  So min(maxLeft, maxRight) = maxRight  (the bottleneck)
  Water at right = maxRight - height[right]
  Move right inward.
```

The taller side guarantees a wall exists.
The shorter side's running max is the exact water level.

```
Time:  O(N) -- single pass
Space: O(1) -- four variables
```

---

### 📜 The Scroll of the Two Sentinels

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Two-Pointer Ritual

```cpp
int trap(vector<int>& height) {
    int n = height.size();
    if (n < 3) return 0;

    int left = 0, right = n - 1;
    int maxLeft = 0, maxRight = 0;
    int water = 0;
```

Two pointers at the edges.
Two running maximums -- one per side.
`water` accumulates the total.

---

## 🔁 The Inward March

```cpp
    while (left < right) {
```

The sentinels marched inward until they met.

---

### ⬅️ Left Side Is Shorter -- Process Left

```cpp
        if (height[left] <= height[right]) {
            maxLeft = max(maxLeft, height[left]);
            water += maxLeft - height[left];
            left++;
        }
```

The left pillar was shorter than or equal to the right.

The right side guaranteed a wall at least `height[right]` tall.
So the bottleneck was `maxLeft`.

Update `maxLeft` with the current height.
Water at this position = `maxLeft - height[left]`.
Move left inward.

> _"The right wall stands tall.
> The left sentinel knows its own maximum.
> That maximum IS the water level here."_

---

### ➡️ Right Side Is Shorter -- Process Right

```cpp
        else {
            maxRight = max(maxRight, height[right]);
            water += maxRight - height[right];
            right--;
        }
    }
    return water;
}
```

Mirror image. The left side guaranteed a taller wall.
The bottleneck was `maxRight`.

Update `maxRight`. Compute water. Move right inward.

---

### 🎺 The Trial of the Two Sentinels

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

| Step | left | right | h[l] | h[r] | Side  | maxL | maxR | Water added | Total |
| ---- | ---- | ----- | ---- | ---- | ----- | ---- | ---- | ----------- | ----- |
| 1    | 0    | 11    | 0    | 1    | Left  | 0    | 0    | 0           | 0     |
| 2    | 1    | 11    | 1    | 1    | Left  | 1    | 0    | 0           | 0     |
| 3    | 2    | 11    | 0    | 1    | Left  | 1    | 0    | 1           | 1     |
| 4    | 3    | 11    | 2    | 1    | Right | 1    | 1    | 0           | 1     |
| 5    | 3    | 10    | 2    | 2    | Right | 1    | 2    | 0           | 1     |
| 6    | 3    | 9     | 2    | 1    | Right | 1    | 2    | 1           | 2     |
| 7    | 3    | 8     | 2    | 2    | Right | 1    | 2    | 0           | 2     |
| 8    | 3    | 7     | 2    | 3    | Left  | 2    | 2    | 0           | 2     |
| 9    | 4    | 7     | 1    | 3    | Left  | 2    | 2    | 1           | 3     |
| 10   | 5    | 7     | 0    | 3    | Left  | 2    | 2    | 2           | 5     |
| 11   | 6    | 7     | 1    | 3    | Left  | 2    | 2    | 1           | **6** |

**Answer: 6** ✓

---

**Trace for `[4, 2, 0, 3, 2, 5]`:**

| Step | left | right | h[l] | h[r] | Side  | maxL | maxR | Water | Total |
| ---- | ---- | ----- | ---- | ---- | ----- | ---- | ---- | ----- | ----- |
| 1    | 0    | 5     | 4    | 5    | Left  | 4    | 0    | 0     | 0     |
| 2    | 1    | 5     | 2    | 5    | Left  | 4    | 0    | 2     | 2     |
| 3    | 2    | 5     | 0    | 5    | Left  | 4    | 0    | 4     | 6     |
| 4    | 3    | 5     | 3    | 5    | Left  | 4    | 0    | 1     | 7     |
| 5    | 4    | 5     | 2    | 5    | Left  | 4    | 0    | 2     | **9** |

**Answer: 9** ✓

The right wall (height 5) was always taller.
Every step processed the left side.
`maxLeft = 4` was the bottleneck throughout.

---

**Trace for `[5, 4, 3, 2, 1]` (strictly decreasing):**

| Step | left | right | Side  | Water |
| ---- | ---- | ----- | ----- | ----- |
| 1    | 0    | 4     | Right | 0     |
| 2    | 0    | 3     | Right | 0     |
| 3    | 0    | 2     | Right | 0     |
| 4    | 0    | 1     | Right | 0     |

**Answer: 0** ✓ -- no valleys, water slides off.

---

**Trace for `[3, 0, 3]`:**

| Step | left | right | Side  | maxL | maxR | Water | Total |
| ---- | ---- | ----- | ----- | ---- | ---- | ----- | ----- |
| 1    | 0    | 2     | Left  | 3    | 0    | 0     | 0     |
| 2    | 1    | 2     | Left  | 3    | 0    | 3     | **3** |

**Answer: 3** ✓ -- simple valley between two equal walls.

---

## 🔍 Why Process the Shorter Side?

The formula is `min(maxLeft, maxRight) - height[i]`.

If `height[left] <= height[right]`:
-   `maxRight` is at least `height[right]` (which is >= `height[left]`).
-   So `min(maxLeft, maxRight)` is determined by `maxLeft` alone.
-   We know `maxLeft` exactly -- it's our running max from the left.
-   No need to know the exact `maxRight`.

The taller side acts as a **guarantee** --
"there exists a wall at least this tall on that side."
The shorter side's running max is the precise bottleneck.

This is why the algorithm is correct without precomputing both arrays.

---

## 🔄 Comparison with Prefix/Suffix Approach

| Prefix/Suffix              | Two Pointers               |
| -------------------------- | -------------------------- |
| O(N) time                  | O(N) time                  |
| O(N) space (two arrays)    | **O(1) space**             |
| Three passes               | One pass                   |
| Easier to understand       | Requires the "shorter side" insight |

Both compute the same `min(maxLeft, maxRight) - height[i]` formula.
Two Pointers just computes it on the fly without storing the arrays.

---

### 🧠 Memory of the Two Sentinel Rain Law

-   **Two pointers:** `left = 0`, `right = n - 1`
-   **Two running maxes:** `maxLeft = 0`, `maxRight = 0`
-   **Process the shorter side:**
    -   `height[left] <= height[right]` → update maxLeft, water += maxLeft - height[left], left++
    -   `height[left] > height[right]` → update maxRight, water += maxRight - height[right], right--
-   **Why shorter side?** The taller side guarantees a wall. The shorter side's max is the bottleneck.
-   **Time:** O(N) -- single pass
-   **Space:** O(1) -- four variables
-   **Edge cases:**
    -   Less than 3 bars → 0
    -   All same height → 0
    -   Strictly monotonic → 0

Thus is remembered the saga of **Trapping Rain Water (Two Pointers)**,
where two sentinels danced inward from the edges --
the shorter one always stepping first,
trusting the taller side's guarantee --
measuring the water at each step
with nothing but a running maximum and a subtraction --
until they met in the middle
and every drop had been counted
in O(1) space. 🌧️🏹✨
