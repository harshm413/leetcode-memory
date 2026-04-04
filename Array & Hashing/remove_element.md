## 🗡️📜 _The Purge of the Unwanted: The Remove Element Saga_

> \_"In the Hall of Numbers stood a long line of values,
> each sworn to serve the realm.
>
> But one value had been declared **forbidden**.
>
> The Oracle was commanded:
>
> **'Remove all traces of this forbidden value --
> in place, without forging a new array --
> and return how many loyal elements remain.
> The first k positions must hold the survivors.
> What lies beyond k does not matter.'**
>
> The Oracle studied the line.
>
> She could not create a new array --
> the decree demanded in-place transformation.
>
> She could not simply mark elements as deleted --
> the survivors had to be compacted at the front.
>
> So she devised the Two-Pointer Purification:
>
> **A scout `i` walks every position in the array.
> A builder `k` stands at the front,
> marking the next slot for a survivor.
>
> When the scout finds a forbidden value -- skip it.
> When the scout finds a loyal value --
> place it at position `k` and advance `k`.
>
> After the march, `k` is the count of survivors,
> and positions 0 to k-1 hold them all.**
>
> One pass. Two pointers. The unwanted erased."\_

---

This is the saga of **Remove Element**.

You are given an integer array `nums` and a value `val`.

Your task:

-   Remove all occurrences of `val` **in-place**.
-   Return `k` -- the number of elements not equal to `val`.
-   The first `k` elements of `nums` must contain the survivors.
-   The order of survivors does not need to match the original order
    (though our approach preserves it naturally).
-   Elements beyond index `k-1` do not matter.

```
Input:  nums = [3, 2, 2, 3], val = 3
Output: k = 2, nums = [2, 2, _, _]

Input:  nums = [0, 1, 2, 2, 3, 0, 4, 2], val = 2
Output: k = 5, nums = [0, 1, 3, 0, 4, _, _, _]
```

---

## 🧠 The Oracle's Core Insight -- Compact the Survivors

This problem is nearly identical to Move Zeroes --
but instead of moving zeroes to the end,
we remove all occurrences of a specific value.

The pattern is the same:

> **Use a builder pointer `k` to track the next available slot.
> Walk the array with a scout pointer `i`.
> Copy every non-forbidden element to position `k`.
> Skip every forbidden element.**

After the walk, `k` is the count of survivors,
and positions `0` to `k-1` hold them compactly.

Since `k <= i` at all times,
writing to `nums[k]` never overwrites an unprocessed element.

```
Time:  O(N) -- single pass
Space: O(1) -- only one extra variable k
```

---

### 📜 The Scroll of the Purification

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Two-Pointer Purification Ritual

```cpp
int removeElement(vector<int>& nums, int val) {
    int k = 0;
```

The builder `k` was placed at position 0 --
the very front of the line.

`k` marked the next open slot
where a loyal survivor deserved to stand.

---

## 🧭 Walk the Entire Line

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

The scout `i` began walking every position,
inspecting each value from front to back.

---

### 🗡️ Judge Each Element

```cpp
        if (nums[i] != val) {
            nums[k] = nums[i];
            k++;
        }
    }
```

At each position, the Oracle asked one question:

> **"Is this element the forbidden value?"**

**If NO** -- the element was loyal.
It was placed at position `k`, the next open slot.
`k` advanced, ready for the next survivor.

**If YES** -- the element was forbidden.
It was simply skipped. No placement. No advancement of `k`.
The forbidden value was left behind to be overwritten.

> _"The loyal march forward.
> The forbidden are ignored.
> What remains beyond k is irrelevant --
> the survivors stand compactly at the front."_

---

## 🏁 Return the Count of Survivors

```cpp
    return k;
}
```

When the scout finished walking,
`k` held the total number of survivors.

The first `k` positions of `nums` contained
every element that was not equal to `val`,
in their original relative order.

---

### 🎺 The Trial of the Purge

```cpp
int main() {
    vector<int> nums1 = {3, 2, 2, 3};
    int k1 = removeElement(nums1, 3);
    cout << "k = " << k1 << ", nums = ";
    for (int i = 0; i < k1; i++) cout << nums1[i] << " ";
    cout << endl; // expected: k = 2, nums = 2 2

    vector<int> nums2 = {0, 1, 2, 2, 3, 0, 4, 2};
    int k2 = removeElement(nums2, 2);
    cout << "k = " << k2 << ", nums = ";
    for (int i = 0; i < k2; i++) cout << nums2[i] << " ";
    cout << endl; // expected: k = 5, nums = 0 1 3 0 4

    return 0;
}
```

---

**Full trace for `[3, 2, 2, 3]`, val = 3:**

| i | nums[i] | Forbidden? | Action           | k after | Array state     |
| - | ------- | ---------- | ---------------- | ------- | --------------- |
| 0 | 3       | Yes        | Skip             | 0       | [3, 2, 2, 3]   |
| 1 | 2       | No         | nums[0] = 2, k++ | 1       | [2, 2, 2, 3]   |
| 2 | 2       | No         | nums[1] = 2, k++ | 2       | [2, 2, 2, 3]   |
| 3 | 3       | Yes        | Skip             | 2       | [2, 2, 2, 3]   |

**Answer: k = 2, first k elements = [2, 2]** ✓

Both `3`s were skipped. Both `2`s were compacted to the front.

---

**Full trace for `[0, 1, 2, 2, 3, 0, 4, 2]`, val = 2:**

| i | nums[i] | Forbidden? | Action           | k after | First k elements so far |
| - | ------- | ---------- | ---------------- | ------- | ----------------------- |
| 0 | 0       | No         | nums[0] = 0, k++ | 1       | [0]                     |
| 1 | 1       | No         | nums[1] = 1, k++ | 2       | [0, 1]                  |
| 2 | 2       | Yes        | Skip             | 2       | [0, 1]                  |
| 3 | 2       | Yes        | Skip             | 2       | [0, 1]                  |
| 4 | 3       | No         | nums[2] = 3, k++ | 3       | [0, 1, 3]               |
| 5 | 0       | No         | nums[3] = 0, k++ | 4       | [0, 1, 3, 0]            |
| 6 | 4       | No         | nums[4] = 4, k++ | 5       | [0, 1, 3, 0, 4]         |
| 7 | 2       | Yes        | Skip             | 5       | [0, 1, 3, 0, 4]         |

**Answer: k = 5, first k elements = [0, 1, 3, 0, 4]** ✓

Three `2`s were purged. Five survivors compacted at the front.

---

## 🔄 Alternative -- Swap with End (Order Not Preserved)

If preserving relative order is not required,
a slightly different approach avoids unnecessary copies:

```cpp
int removeElement(vector<int>& nums, int val) {
    int i = 0;
    int n = nums.size();
    while (i < n) {
        if (nums[i] == val) {
            nums[i] = nums[n - 1];
            n--;
        } else {
            i++;
        }
    }
    return n;
}
```

When a forbidden element is found, swap it with the last element
and shrink the array. This avoids shifting but changes order.
Useful when the problem explicitly says order doesn't matter.

---

### 🧠 Memory of the Purge Law

-   **Two-pointer compaction:** scout `i` walks all elements, builder `k` collects survivors
    -   `nums[i] != val` -- place at `nums[k]`, advance `k`
    -   `nums[i] == val` -- skip entirely
-   `k <= i` always holds -- writing to `nums[k]` never corrupts unprocessed data
-   Relative order of survivors is preserved (scout encounters them left-to-right)
-   Return `k` -- the count of non-val elements
-   First `k` positions hold all survivors; beyond `k` is irrelevant
-   **Swap-with-end alternative:** does not preserve order but avoids unnecessary copies
-   **Time:** O(N) -- single pass
-   **Space:** O(1) -- only one extra variable

Thus is remembered the saga of **Remove Element**,
where the Oracle walked the line of values
with a scout and a builder --
silently skipping every forbidden number,
compacting every loyal survivor to the front --
until the purge was complete
and only the worthy remained,
standing shoulder to shoulder
in the first k positions of the realm. 🗡️📜✨
