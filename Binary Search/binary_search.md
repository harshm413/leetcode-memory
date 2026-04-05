## 🎯⚔️ _The Halving of the Infinite Line: The Binary Search Saga_

> \_"In the Realm of Ordered Numbers,
> values stood in perfect ascending discipline --
> each one greater than the last,
> a line stretching from the smallest to the greatest.
>
> The Oracle was commanded:
>
> **'Find the target within this ordered line.
> But you are forbidden from walking step by step.
> The line is too vast for a linear march.'**
>
> The Oracle smiled.
>
> She knew that when the land was sorted,
> a single glance at the middle
> could eliminate half the world.
>
> **If the middle is the target -- the quest ends.
> If the middle is too small -- the target lies to the right.
> If the middle is too large -- the target lies to the left.**
>
> With each glance, half the search space vanished.
> After log2(N) glances, only one element remained --
> either the target, or proof of its absence.
>
> Two sentinels -- `left` and `right` --
> guarded the boundaries of the search space.
> They marched inward with every step,
> squeezing the world tighter and tighter,
> until truth stood alone
> or the sentinels crossed
> and the search space collapsed to nothing.
>
> This was the ritual of halving --
> the most fundamental algorithm
> in all of computer science."\_

---

This is the saga of **Binary Search**.

You are given a sorted array `nums` (ascending order)
and a target value `target`.

Your task:

-   If `target` exists in `nums`, return its **index**.
-   If not, return **-1**.

```
Input:  nums = [-1, 0, 3, 5, 9, 12], target = 9
Output: 4

Input:  nums = [-1, 0, 3, 5, 9, 12], target = 2
Output: -1
```

---

## 🧠 The Oracle's Core Insight -- Halve the Search Space

Binary search works because the array is **sorted**.

At every step, the Oracle looks at the middle element:

```
If nums[mid] == target  -->  found it, return mid
If nums[mid] < target   -->  target is in the RIGHT half, move left boundary
If nums[mid] > target   -->  target is in the LEFT half, move right boundary
```

Each comparison eliminates half the remaining elements.

Starting with N elements:
after 1 step, N/2 remain.
After 2 steps, N/4 remain.
After k steps, N/2^k remain.
When N/2^k = 1, we have k = log2(N) steps.

```
Time:  O(log N) -- halving at every step
Space: O(1) -- only two pointers and one midpoint
```

---

### � The Scroll of the Ordered Line

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Halving Ritual

```cpp
int binarySearch(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
```

Two sentinels were placed at the boundaries:

-   `left` at the very first element (index 0).
-   `right` at the very last element (index n-1).

Together they defined the **search space** --
the range of indices where the target could possibly exist.

---

## 🔁 Continue While the Search Space Exists

```cpp
    while (left <= right) {
```

The loop continued as long as `left <= right` --
meaning at least one element remained to be checked.

When `left > right`, the sentinels had crossed --
the search space was empty, and the target did not exist.

---

### 🎯 Find the Middle

```cpp
        int mid = left + (right - left) / 2;
```

The Oracle computed the midpoint.

Why `left + (right - left) / 2` instead of `(left + right) / 2`?

> _"If `left` and `right` are both large,
> their sum could overflow a 32-bit integer.
> `left + (right - left) / 2` avoids this
> by computing the offset first."_

This is a classic defensive coding practice
that prevents integer overflow in languages like C++ and Java.

---

### ✅ Case 1 -- Target Found

```cpp
        if (nums[mid] == target) {
            return mid;
        }
```

The middle element was the target.
The quest ended. Return its index.

---

### ➡️ Case 2 -- Target Is Larger, Search Right

```cpp
        else if (nums[mid] < target) {
            left = mid + 1;
        }
```

The middle was too small.
The target must lie somewhere to the right.

The left sentinel advanced past `mid` --
everything at `mid` and to its left was eliminated.

---

### ⬅️ Case 3 -- Target Is Smaller, Search Left

```cpp
        else {
            right = mid - 1;
        }
    }
```

The middle was too large.
The target must lie somewhere to the left.

The right sentinel retreated before `mid` --
everything at `mid` and to its right was eliminated.

---

## ❌ Target Not Found

```cpp
    return -1;
}
```

If the loop ended without finding the target,
the sentinels had crossed (`left > right`).
The search space was empty.

The target did not exist in the array. Return `-1`.

---

### 🎺 The Trial of the Ordered Line

```cpp
int main() {
    vector<int> nums = {-1, 0, 3, 5, 9, 12};

    cout << binarySearch(nums, 9) << endl;  // expected: 4
    cout << binarySearch(nums, 2) << endl;  // expected: -1

    return 0;
}
```

---

**Full trace for `[-1, 0, 3, 5, 9, 12]`, target = 9:**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 5     | 2   | 3         | 3 < 9      | left = 3       |
| 2    | 3    | 5     | 4   | 9         | 9 == 9     | Return **4**   |

**Answer: 4** ✓

Two steps. The Oracle halved the 6-element array twice and found `9` at index 4.

---

**Full trace for `[-1, 0, 3, 5, 9, 12]`, target = 2:**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 5     | 2   | 3         | 3 > 2      | right = 1      |
| 2    | 0    | 1     | 0   | -1        | -1 < 2     | left = 1       |
| 3    | 1    | 1     | 1   | 0         | 0 < 2      | left = 2       |
| 4    | 2    | 1     | --  | --        | left>right | Exit loop      |

**Answer: -1** ✓

The sentinels crossed at step 4. The search space collapsed.
`2` does not exist in the array.

---

**Full trace for `[1, 3, 5, 7, 9, 11, 13, 15]`, target = 7:**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 7     | 3   | 7         | 7 == 7     | Return **3**   |

**Answer: 3** ✓

Found on the very first glance -- the target happened to be at the midpoint.

---

## 🔍 Common Pitfalls

**1. Using `(left + right) / 2` instead of `left + (right - left) / 2`:**
Integer overflow when `left + right > INT_MAX`. Always use the safe version.

**2. Using `left < right` instead of `left <= right`:**
Misses the case where `left == right` -- the last remaining element is never checked.

**3. Forgetting `mid + 1` or `mid - 1`:**
Writing `left = mid` or `right = mid` can cause infinite loops
when `left == mid` (which happens when `right - left <= 1`).

---

### 🧠 Memory of the Halving Law

-   **Prerequisite:** array must be **sorted**
-   **Two sentinels:** `left = 0`, `right = n - 1`
-   **Loop condition:** `while (left <= right)` -- search space has at least one element
-   **Midpoint:** `mid = left + (right - left) / 2` -- overflow-safe
-   **Three cases:**
    -   `nums[mid] == target` --> return `mid`
    -   `nums[mid] < target` --> `left = mid + 1` (search right)
    -   `nums[mid] > target` --> `right = mid - 1` (search left)
-   **Not found:** loop exits when `left > right` --> return `-1`
-   **Time:** O(log N) -- halving the search space at every step
-   **Space:** O(1) -- only three variables

Thus is remembered the saga of **Binary Search**,
where the Oracle conquered a vast ordered line
not by walking step by step
but by halving the world with every glance --
two sentinels marching inward,
the middle revealing which half to abandon --
until the target stood alone in the narrowing light,
or the sentinels crossed
and silence confirmed its absence. 🎯⚔️✨
