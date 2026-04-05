## 🧭⚔️ _The Rightful Seat in the Ordered Hall: The Search Insert Position Saga_

> \_"In the Hall of Sorted Seats,
> numbers sat in perfect ascending order.
>
> A traveler arrived at the gates,
> carrying a banner marked with a number.
>
> The Oracle was summoned:
>
> **'Find this traveler's rightful seat.
> If the number already sits within the hall --
> reveal its position.
> If it does not --
> reveal where it MUST be inserted
> so that the sacred order is never broken.'**
>
> The Oracle knew this was binary search
> with one elegant twist:
>
> **When the search ends without finding the target,
> the left sentinel stands exactly
> at the position where the target belongs.**
>
> Every number smaller than the target
> has been pushed behind `left`.
> Every number greater stands ahead.
>
> One search. Two answers.
> The seat was always revealed."\_

---

This is the saga of **Search Insert Position**.

You are given a sorted array `nums` of **distinct** integers
and a target value `target`.

-   If `target` exists in `nums` -- return its **index**.
-   If not -- return the **index where it would be inserted**.

Must run in **O(log N)** time.

```
Input:  nums = [1, 3, 5, 6], target = 5  →  Output: 2
Input:  nums = [1, 3, 5, 6], target = 2  →  Output: 1
Input:  nums = [1, 3, 5, 6], target = 7  →  Output: 4
Input:  nums = [1, 3, 5, 6], target = 0  →  Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Left IS the Insertion Point

Standard binary search with one key observation:

> **When the loop ends without finding the target,
> `left` points to the exact insertion index.**

Why?

-   `left = mid + 1` fires when `nums[mid] < target` --
    everything at `mid` and before is too small.
-   `right = mid - 1` fires when `nums[mid] > target` --
    everything at `mid` and after is too large.

When the loop ends (`left > right`):

```
All elements at indices < left  are  < target
All elements at indices >= left are  > target (or don't exist)
```

So `left` is the first position where the target could sit
without violating sorted order. This handles every edge case:

-   Target smaller than all elements → `left = 0`
-   Target larger than all elements → `left = n`
-   Target between two elements → `left` = the gap position

```
Time:  O(log N) -- halving at every step
Space: O(1) -- only three variables
```

---

### 📜 The Scroll of the Ordered Hall

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Seat-Finding Ritual

```cpp
int searchInsert(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
```

Two sentinels at the boundaries:
`left` at index 0, `right` at index n-1.

---

## 🔁 Halve Until Found or Exhausted

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle inspected the middle seat.
`left + (right - left) / 2` prevented integer overflow.

---

### ✅ Case 1 -- Target Found

```cpp
        if (nums[mid] == target) {
            return mid;
        }
```

The middle seat held the target. Return its index.

---

### ➡️ Case 2 -- Target Is Larger, Search Right

```cpp
        else if (nums[mid] < target) {
            left = mid + 1;
        }
```

The middle was too small. Target belongs to the right.
Left sentinel advanced past `mid`.

> _"Every time left advances,
> it steps past a value that is too small.
> When the search ends, left has stepped past ALL too-small values --
> landing exactly at the insertion point."_

---

### ⬅️ Case 3 -- Target Is Smaller, Search Left

```cpp
        else {
            right = mid - 1;
        }
    }
```

The middle was too large. Target belongs to the left.
Right sentinel retreated before `mid`.

---

## 🪑 The Seat Is Revealed

```cpp
    return left;
}
```

If found during the loop -- returned immediately.
If not found -- sentinels crossed (`left > right`).

`left` now points to the exact insertion position.

> _"The left sentinel never lies.
> It has marched past every value too small
> and stopped at the first value too large --
> or at the end of the hall if nothing was large enough."_

---

### 🎺 The Trial of the Ordered Hall

```cpp
int main() {
    vector<int> nums = {1, 3, 5, 6};

    cout << searchInsert(nums, 5) << endl; // expected: 2
    cout << searchInsert(nums, 2) << endl; // expected: 1
    cout << searchInsert(nums, 7) << endl; // expected: 4
    cout << searchInsert(nums, 0) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for `[1, 3, 5, 6]`, target = 5 (exists):**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 3     | 1   | 3         | 3 < 5      | left = 2       |
| 2    | 2    | 3     | 2   | 5         | 5 == 5     | Return **2**   |

**Answer: 2** ✓

---

**Full trace for `[1, 3, 5, 6]`, target = 2 (insert between 1 and 3):**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 3     | 1   | 3         | 3 > 2      | right = 0      |
| 2    | 0    | 0     | 0   | 1         | 1 < 2      | left = 1       |
| 3    | 1    | 0     | --  | --        | left>right | Exit loop      |

**Answer: left = 1** ✓

`left` advanced past index 0 (value `1`, too small).
Inserting `2` at index 1 gives `[1, 2, 3, 5, 6]`.

---

**Full trace for `[1, 3, 5, 6]`, target = 7 (insert at end):**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 3     | 1   | 3         | 3 < 7      | left = 2       |
| 2    | 2    | 3     | 2   | 5         | 5 < 7      | left = 3       |
| 3    | 3    | 3     | 3   | 6         | 6 < 7      | left = 4       |
| 4    | 4    | 3     | --  | --        | left>right | Exit loop      |

**Answer: left = 4** ✓

Every element was too small. `left` marched to index 4.
Inserting `7` at index 4 gives `[1, 3, 5, 6, 7]`.

---

**Full trace for `[1, 3, 5, 6]`, target = 0 (insert at beginning):**

| Step | left | right | mid | nums[mid] | Compare    | Action         |
| ---- | ---- | ----- | --- | --------- | ---------- | -------------- |
| 1    | 0    | 3     | 1   | 3         | 3 > 0      | right = 0      |
| 2    | 0    | 0     | 0   | 1         | 1 > 0      | right = -1     |
| 3    | 0    | -1    | --  | --        | left>right | Exit loop      |

**Answer: left = 0** ✓

Every element was too large. `left` never moved.
Inserting `0` at index 0 gives `[0, 1, 3, 5, 6]`.

---

## 🔍 Why `left` and Not `right`?

When the loop ends, `left = right + 1`.

-   `right` = last index where `nums[right] < target`
-   `left` = first index where `nums[left] >= target` (or `n`)

The insertion point is **after** all smaller elements
and **before** all larger elements. That is `left`.

This is exactly what C++'s `lower_bound` computes.

---

### 🧠 Memory of the Ordered Seat Law

-   **Standard binary search** -- return `left` when target is not found
-   **Two sentinels:** `left = 0`, `right = n - 1`
-   **Loop:** `while (left <= right)` -- at least one element remains
-   **Midpoint:** `mid = left + (right - left) / 2` -- overflow-safe
-   **Three cases:**
    -   `nums[mid] == target` --> return `mid`
    -   `nums[mid] < target` --> `left = mid + 1`
    -   `nums[mid] > target` --> `right = mid - 1`
-   **Not found:** return `left` -- the insertion point
-   `left` always lands at the first position where `nums[left] >= target`
-   Equivalent to C++ `lower_bound`
-   **Time:** O(log N) -- halving at every step
-   **Space:** O(1) -- only three variables

Thus is remembered the saga of **Search Insert Position**,
where the Oracle walked the Hall of Sorted Seats
with the ancient ritual of halving --
two sentinels marching inward,
the middle revealing which half to abandon --
and when the target was found, its seat was declared,
and when it was not, the left sentinel stood firm
at the exact position where the traveler belonged --
preserving the sacred order of the hall
without a single seat displaced. 🧭⚔️✨
