## 🌅🧭 _The Hidden Dawn: The Find Minimum in Rotated Sorted Array Saga_

> \_"Once, a kingdom arranged its numbers
> in perfect ascending order --
> a serene sunrise across the horizon.
>
> But one night, the Wheel of Rotation spun.
> Some numbers from the front were moved to the back.
> The dawn -- the smallest number --
> was buried somewhere in the middle.
>
> The Oracle was commanded:
>
> **'Find the minimum element
> in this rotated sorted array.
> All elements are unique.
> You must do it in O(log N) time.'**
>
> The Oracle could not walk the entire array --
> that would be O(N), unworthy of the decree.
>
> But she knew a powerful truth:
>
> **In a rotated sorted array,
> one half is always perfectly sorted,
> and the other half contains the rotation point --
> where the minimum lives.**
>
> By comparing `nums[mid]` with `nums[right]`,
> the Oracle could determine which half
> contained the minimum:
>
> **If `nums[mid] > nums[right]` --
> the mid is in the larger, pre-rotation portion.
> The minimum must be to the right of mid.**
>
> **If `nums[mid] <= nums[right]` --
> the mid is in the smaller, post-rotation portion.
> The minimum is at mid or to its left.**
>
> Halve the search space with every comparison.
> The minimum is found in O(log N)."\_

---

This is the saga of **Find Minimum in Rotated Sorted Array**.

You are given a sorted array of **unique** elements
that has been rotated between 1 and n times.

Your task:

-   Find the **minimum element** in O(log N) time.

```
Input:  [3, 4, 5, 1, 2]
Output: 1   (originally [1,2,3,4,5], rotated 3 times)

Input:  [4, 5, 6, 7, 0, 1, 2]
Output: 0

Input:  [11, 13, 15, 17]
Output: 11  (not rotated, or rotated n times)
```

---

## 🧠 The Oracle's Core Insight -- Compare Mid with Right

A rotated sorted array looks like this:

```
[4, 5, 6, 7, 0, 1, 2]
 ^-----------^  ^----^
 larger portion  smaller portion
                 ^ minimum is here
```

The rotation creates exactly one "drop" --
where a large number is followed by a small number.
The minimum is the element right after the drop.

The key comparison is `nums[mid]` vs `nums[right]`:

```
If nums[mid] > nums[right]:
  mid is in the LARGER portion (before the drop).
  The minimum is somewhere to the RIGHT of mid.
  Move left = mid + 1.

If nums[mid] <= nums[right]:
  mid is in the SMALLER portion (after the drop), or the array is not rotated.
  The minimum is at mid or to its LEFT.
  Move right = mid.  (not mid-1, because mid itself could be the answer)
```

**Why compare with `right` and not `left`?**

Comparing with `left` is ambiguous. Consider `[3, 4, 5, 1, 2]`:
`nums[mid] = 5 > nums[left] = 3` -- but the minimum is to the right.
Now consider `[1, 2, 3, 4, 5]`:
`nums[mid] = 3 > nums[left] = 1` -- but the minimum is to the left.
Same comparison, opposite conclusions. Ambiguous.

Comparing with `right` always gives a clear answer.

---

### 📜 The Scroll of the Rotated Kingdom

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Dawn-Finding Ritual

```cpp
int findMin(vector<int>& nums) {
    int left = 0, right = nums.size() - 1;
```

Two sentinels were placed at the boundaries.
The minimum lay somewhere between them.

---

## 🔁 Halve Until One Element Remains

```cpp
    while (left < right) {
```

The loop continued while the search space had more than one element.

When `left == right`, only one candidate remained -- the minimum.

Note: this uses `left < right` (not `left <= right`)
because we converge `left` and `right` onto the same index.

---

### 🎯 Find the Middle

```cpp
        int mid = left + (right - left) / 2;
```

The Oracle split the search space at the midpoint.

---

### ➡️ Mid Is in the Larger Portion -- Minimum Is Right

```cpp
        if (nums[mid] > nums[right]) {
            left = mid + 1;
        }
```

`nums[mid] > nums[right]` means mid is in the pre-rotation portion --
the larger numbers that were pushed to the front.

The drop (and the minimum) must be somewhere to the right of mid.

`left = mid + 1` -- mid itself is too large to be the minimum.

---

### ⬅️ Mid Is in the Smaller Portion -- Minimum Is Here or Left

```cpp
        else {
            right = mid;
        }
    }
```

`nums[mid] <= nums[right]` means mid is in the post-rotation portion --
the smaller numbers, or the array isn't rotated at all.

The minimum could be at mid itself, or somewhere to its left.

`right = mid` -- not `mid - 1`, because mid might be the answer.

> _"When the Oracle cannot rule out mid,
> she keeps it in the search space.
> This is why `right = mid` and not `right = mid - 1`."_

---

## 🌅 The Dawn Is Revealed

```cpp
    return nums[left];
}
```

When `left == right`, the sentinels converged on a single index.
That index held the minimum element.

---

### 🎺 The Trial of the Rotated Kingdom

```cpp
int main() {
    vector<int> nums1 = {4, 5, 6, 7, 0, 1, 2};
    cout << findMin(nums1) << endl; // expected: 0

    vector<int> nums2 = {3, 4, 5, 1, 2};
    cout << findMin(nums2) << endl; // expected: 1

    vector<int> nums3 = {11, 13, 15, 17};
    cout << findMin(nums3) << endl; // expected: 11

    return 0;
}
```

---

**Full trace for `[4, 5, 6, 7, 0, 1, 2]`:**

| Step | left | right | mid | nums[mid] | nums[right] | Compare   | Action       |
| ---- | ---- | ----- | --- | --------- | ----------- | --------- | ------------ |
| 1    | 0    | 6     | 3   | 7         | 2           | 7 > 2     | left = 4     |
| 2    | 4    | 6     | 5   | 1         | 2           | 1 <= 2    | right = 5    |
| 3    | 4    | 5     | 4   | 0         | 1           | 0 <= 1    | right = 4    |
| 4    | 4    | 4     | --  | --        | --          | left==right| Exit loop   |

**Answer: nums[4] = 0** ✓

Step 1: mid=7 > right=2, so the minimum is to the right. Eliminate left half.
Step 2: mid=1 <= right=2, so the minimum is at mid or left. Keep mid.
Step 3: mid=0 <= right=1, so the minimum is at mid or left. Keep mid.
Converged at index 4. The dawn was `0`.

---

**Full trace for `[3, 4, 5, 1, 2]`:**

| Step | left | right | mid | nums[mid] | nums[right] | Compare   | Action       |
| ---- | ---- | ----- | --- | --------- | ----------- | --------- | ------------ |
| 1    | 0    | 4     | 2   | 5         | 2           | 5 > 2     | left = 3     |
| 2    | 3    | 4     | 3   | 1         | 2           | 1 <= 2    | right = 3    |
| 3    | 3    | 3     | --  | --        | --          | left==right| Exit loop   |

**Answer: nums[3] = 1** ✓

---

**Full trace for `[11, 13, 15, 17]` (not rotated):**

| Step | left | right | mid | nums[mid] | nums[right] | Compare    | Action       |
| ---- | ---- | ----- | --- | --------- | ----------- | ---------- | ------------ |
| 1    | 0    | 3     | 1   | 13        | 17          | 13 <= 17   | right = 1    |
| 2    | 0    | 1     | 0   | 11        | 13          | 11 <= 13   | right = 0    |
| 3    | 0    | 0     | --  | --        | --          | left==right| Exit loop   |

**Answer: nums[0] = 11** ✓

The array was already sorted. `nums[mid] <= nums[right]` at every step,
so `right` kept moving left until it converged at index 0 -- the true minimum.

---

### 🧠 Memory of the Hidden Dawn Law

-   **Compare `nums[mid]` with `nums[right]`** -- never with `nums[left]`
-   **If `nums[mid] > nums[right]`:** mid is in the larger portion, minimum is to the right
    -   `left = mid + 1` (mid is too large to be the answer)
-   **If `nums[mid] <= nums[right]`:** mid is in the smaller portion or array is sorted
    -   `right = mid` (mid could be the answer, keep it)
-   **Loop condition:** `while (left < right)` -- converge to a single index
-   **Return `nums[left]`** when `left == right` -- the minimum
-   Handles non-rotated arrays naturally (always takes the `right = mid` branch)
-   **Time:** O(log N) -- halving at every step
-   **Space:** O(1) -- only three variables

Thus is remembered the saga of **Find Minimum in Rotated Sorted Array**,
where the Oracle found the hidden dawn
in a kingdom twisted by rotation --
not by walking the entire horizon
but by comparing the middle with the right edge,
halving the search space with every glance,
until the sentinels converged
on the single smallest flame
buried in the rotated night. 🌅🧭✨
