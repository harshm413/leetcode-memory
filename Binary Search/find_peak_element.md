## 🏔️⚡ _The Summit in the Unsorted Mountains: The Find Peak Element Saga_

> \_"Across the Mountain Range of Numbers,
> peaks and valleys rose and fell without pattern --
> no sorted order, no predictable terrain.
>
> The Oracle was summoned:
>
> **'Find ANY peak --
> an element that is strictly greater
> than both its neighbors.
> You need not find the highest peak.
> Any peak will do.
> But you must find it in O(log N).'**
>
> The Oracle was puzzled at first.
>
> Binary search demands order --
> how could it work on an unsorted array?
>
> Then she saw the hidden truth:
>
> **The array's boundaries are negative infinity.
> nums[-1] = -∞ and nums[n] = -∞.
> This means the array always rises from -∞ on the left
> and falls back to -∞ on the right.
> A peak MUST exist somewhere in between.**
>
> And at any midpoint, the slope tells you
> which direction leads uphill toward a peak:
>
> **If nums[mid] < nums[mid+1] --
> the terrain is rising to the right.
> A peak must exist on the right side.**
>
> **If nums[mid] >= nums[mid+1] --
> the terrain is falling or flat to the right.
> A peak must exist at mid or to the left.**
>
> Follow the uphill slope.
> A peak is guaranteed.
> Binary search on the gradient."\_

---

This is the saga of **Find Peak Element**.

A peak element is strictly greater than its neighbors.

Given an integer array `nums` where `nums[-1] = nums[n] = -∞`:

-   Find **any** peak element and return its **index**.
-   Adjacent elements are never equal (`nums[i] != nums[i+1]`).
-   Must run in **O(log N)** time.

```
Input:  [1,2,3,1]
Output: 2   (nums[2] = 3 is a peak)

Input:  [1,2,1,3,5,6,4]
Output: 5   (nums[5] = 6 is a peak; index 1 also valid)

Input:  [1]
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Follow the Uphill Slope

The key realization:

> **Because both boundaries are -∞,
> if you walk uphill from any point,
> you MUST eventually reach a peak before falling off the edge.**

At any `mid`, compare `nums[mid]` with `nums[mid+1]`:

```
If nums[mid] < nums[mid+1]:
  The slope rises to the right.
  A peak exists somewhere in [mid+1, right].
  Move left = mid + 1.

If nums[mid] > nums[mid+1]:
  The slope falls to the right.
  A peak exists at mid or somewhere in [left, mid].
  Move right = mid.
```

We never need to check both neighbors at once.
The comparison with `mid+1` alone tells us
which direction is guaranteed to contain a peak.

This works because:
-   If we keep going right and the slope keeps rising → the last element
    is a peak (since `nums[n] = -∞`).
-   If we keep going left and the slope keeps rising → the first element
    is a peak (since `nums[-1] = -∞`).

A peak is **always** reachable by following the uphill direction.

```
Time:  O(log N) -- halving at every step
Space: O(1)
```

---

### 📜 The Scroll of the Mountain Range

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Peak-Hunting Ritual

```cpp
int findPeakElement(vector<int>& nums) {
    int left = 0, right = nums.size() - 1;
```

Two sentinels at the edges of the mountain range.

---

## 🔁 Halve Until One Summit Remains

```cpp
    while (left < right) {
        int mid = left + (right - left) / 2;
```

The Oracle stood at the middle of the range.

`left < right` (not `<=`) because we converge
both sentinels onto a single peak index.

---

### 📈 Slope Rises to the Right -- Peak Is Right

```cpp
        if (nums[mid] < nums[mid + 1]) {
            left = mid + 1;
        }
```

The terrain climbed from `mid` to `mid+1`.

A peak must exist somewhere to the right --
either `mid+1` itself is a peak,
or the slope continues rising until it must fall
(because `nums[n] = -∞`).

`left = mid + 1` -- `mid` cannot be a peak
(its right neighbor is larger).

---

### 📉 Slope Falls to the Right -- Peak Is Here or Left

```cpp
        else {
            right = mid;
        }
    }
```

The terrain fell from `mid` to `mid+1`.

`mid` might be a peak (if its left neighbor is also smaller),
or a peak exists somewhere to the left.

`right = mid` -- keep `mid` in the search space.

> _"When the slope falls to the right,
> the Oracle does not step past mid --
> for mid itself might be the summit."_

---

## 🏔️ The Peak Is Revealed

```cpp
    return left;
}
```

When `left == right`, the sentinels converged on a peak.

---

### 🎺 The Trial of the Mountain Range

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3, 1};
    cout << findPeakElement(nums1) << endl; // expected: 2

    vector<int> nums2 = {1, 2, 1, 3, 5, 6, 4};
    cout << findPeakElement(nums2) << endl; // expected: 5 (or 1)

    vector<int> nums3 = {1};
    cout << findPeakElement(nums3) << endl; // expected: 0

    vector<int> nums4 = {2, 1};
    cout << findPeakElement(nums4) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for `[1, 2, 3, 1]`:**

| Step | left | right | mid | nums[mid] | nums[mid+1] | Slope    | Action      |
| ---- | ---- | ----- | --- | --------- | ----------- | -------- | ----------- |
| 1    | 0    | 3     | 1   | 2         | 3           | Rising → | left = 2    |
| 2    | 2    | 3     | 2   | 3         | 1           | Falling →| right = 2   |
| 3    | 2    | 2     | --  | --        | --          | Converged| Exit loop   |

**Answer: 2** ✓ (nums[2] = 3, neighbors are 2 and 1)

---

**Full trace for `[1, 2, 1, 3, 5, 6, 4]`:**

| Step | left | right | mid | nums[mid] | nums[mid+1] | Slope    | Action      |
| ---- | ---- | ----- | --- | --------- | ----------- | -------- | ----------- |
| 1    | 0    | 6     | 3   | 3         | 5           | Rising → | left = 4    |
| 2    | 4    | 6     | 5   | 6         | 4           | Falling →| right = 5   |
| 3    | 4    | 5     | 4   | 5         | 6           | Rising → | left = 5    |
| 4    | 5    | 5     | --  | --        | --          | Converged| Exit loop   |

**Answer: 5** ✓ (nums[5] = 6, neighbors are 5 and 4)

The Oracle followed the rising slope rightward at steps 1 and 3,
then the falling slope locked the right boundary at step 2.
Converged on index 5 -- the peak.

---

**Full trace for `[2, 1]`:**

| Step | left | right | mid | nums[mid] | nums[mid+1] | Slope    | Action      |
| ---- | ---- | ----- | --- | --------- | ----------- | -------- | ----------- |
| 1    | 0    | 1     | 0   | 2         | 1           | Falling →| right = 0   |
| 2    | 0    | 0     | --  | --        | --          | Converged| Exit loop   |

**Answer: 0** ✓ (nums[0] = 2, left boundary is -∞, right neighbor is 1)

---

**Full trace for `[1, 2, 3, 4, 5]` (strictly increasing):**

| Step | left | right | mid | nums[mid] | nums[mid+1] | Slope    | Action      |
| ---- | ---- | ----- | --- | --------- | ----------- | -------- | ----------- |
| 1    | 0    | 4     | 2   | 3         | 4           | Rising → | left = 3    |
| 2    | 3    | 4     | 3   | 4         | 5           | Rising → | left = 4    |
| 3    | 4    | 4     | --  | --        | --          | Converged| Exit loop   |

**Answer: 4** ✓ (nums[4] = 5, right boundary is -∞)

The slope kept rising. The Oracle followed it all the way
to the last element -- which is a peak because `nums[n] = -∞`.

---

## 🔍 Why Only Compare with `mid+1`?

A common question: why not check both neighbors?

Because one comparison is enough to determine
which half **guarantees** a peak:

-   Rising right → peak guaranteed right (boundary is -∞).
-   Falling right → peak guaranteed at mid or left.

Checking both neighbors would find a peak at `mid` directly,
but it complicates the boundary logic and isn't needed.
The single comparison cleanly halves the search space every time.

---

### 🧠 Memory of the Mountain Summit Law

-   **Boundaries are -∞:** `nums[-1] = nums[n] = -∞` → a peak always exists
-   **Binary search on gradient:** compare `nums[mid]` with `nums[mid+1]`
    -   Rising (`nums[mid] < nums[mid+1]`) → `left = mid + 1`
    -   Falling (`nums[mid] > nums[mid+1]`) → `right = mid`
-   **Loop:** `while (left < right)` -- converge onto the peak
-   **Return `left`** when `left == right`
-   Works on **unsorted** arrays -- only the slope matters, not global order
-   Finds **any** peak, not necessarily the global maximum
-   Adjacent elements are guaranteed different (`nums[i] != nums[i+1]`)
-   **Time:** O(log N) -- halving at every step
-   **Space:** O(1)
-   **Edge cases:**
    -   Single element → always a peak (both neighbors are -∞)
    -   Strictly increasing → last element is the peak
    -   Strictly decreasing → first element is the peak

Thus is remembered the saga of **Find Peak Element**,
where the Oracle conquered an unsorted mountain range
not by scanning every ridge
but by reading the slope at the midpoint --
always following the uphill direction,
trusting that the -∞ boundaries guaranteed
a summit would be reached --
halving the range with every step
until the lone peak stood revealed
in logarithmic time. 🏔️⚡✨
