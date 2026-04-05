## 🎭🎯 _The Impostor Knights of the Twisted Kingdom: The Search in Rotated Sorted Array II Saga_

> \_"The Twisted Kingdom had been searched before.
> The Seeker had learned its ways --
> always one half obeys the old order,
> and the other curves strangely around the rotation point.
>
> But the King, cunning and devious,
> had now filled the kingdom with **Impostor Knights** --
> soldiers bearing the same banners,
> duplicates lurking at every corner.
>
> When the Seeker looked at the edges
> and peered at the middle,
> he could no longer trust what he saw.
>
> **Were the borders and the middle
> wearing the same banner
> because the sorted half was there --
> or merely because an impostor stood in disguise?**
>
> The Seeker could not know.
>
> So he devised a new law:
>
> **'When the left border, the middle, and the right border
> all wear the same face --
> strip one impostor from each side
> and look again.'**
>
> The kingdom would shrink,
> the disguise would fall,
> and truth would stand revealed.
>
> Everything else remained the same as the original quest --
> identify which half is sorted,
> check if the target lies within that sorted range,
> and eliminate the other half."\_

---

This is the saga of **Search in Rotated Sorted Array II**.

You are given a rotated sorted array `nums` that **may contain duplicates**
and a target value `target`.

Return `true` if `target` exists, `false` otherwise.

```
Input:  nums = [2, 5, 6, 0, 0, 1, 2], target = 0
Output: true

Input:  nums = [2, 5, 6, 0, 0, 1, 2], target = 3
Output: false

Input:  nums = [1, 0, 1, 1, 1], target = 0
Output: true
```

---

## 🧠 The Oracle's Core Problem -- Duplicates Break the Old Law

In the original rotated search (no duplicates),
the Seeker could always determine which half was sorted:

```
If nums[left] <= nums[mid]  -->  left half is sorted
Else                         -->  right half is sorted
```

But with duplicates, consider:

```
nums = [1, 0, 1, 1, 1]
left = 0, right = 4, mid = 2
nums[left] = 1, nums[mid] = 1, nums[right] = 1
```

All three are identical. Is the left half sorted? Is the right?
The Seeker cannot tell. The impostors have created perfect confusion.

The solution: **strip the ambiguity**.

> When `nums[left] == nums[mid] == nums[right]`,
> shrink both ends: `left++`, `right--`.
> This removes one impostor from each flank
> without losing the target.

After stripping, the standard rotated binary search logic resumes.

---

### 📜 The Scroll of the Impostor Kingdom

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🗡️ The Search Ritual -- Rotated Binary Search with Impostor Detection

```cpp
bool search(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
```

The Seeker planted sentinels at both ends of the realm.

---

## 🔁 Halve Until Found or Exhausted

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Seeker split the kingdom at the middle.

---

### 🎯 Step 1 -- Target Found at Mid

```cpp
        if (nums[mid] == target) return true;
```

If the middle element matched the target --
the quest ended immediately.

---

### 🎭 Step 2 -- Strip the Impostors

```cpp
        if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
            left++;
            right--;
        }
```

This is the **crucial new step** that did not exist in the original.

When all three -- left border, middle, right border --
wore the same banner, the Seeker could not determine
which half was sorted.

He stripped one soldier from each flank and tried again.

No information was lost -- the target could not hide
in a border soldier that was merely a copy of the middle.

> _"When the disguise is perfect,
> do not guess. Simply peel away the mask
> one layer at a time."_

---

### ⚔️ Step 3 -- Left Half Is Sorted

```cpp
        else if (nums[left] <= nums[mid]) {
```

With impostors stripped, if `nums[left] <= nums[mid]`,
the left half `[left..mid]` was in sorted order.

---

```cpp
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
```

If the target fell within the sorted left range
(`nums[left] <= target < nums[mid]`) --
search left. Eliminate the right half.

Otherwise -- the target was in the unsorted right half.
Search right.

---

### 🔄 Step 4 -- Right Half Is Sorted

```cpp
        } else {
```

If the left half was not sorted,
the right half `[mid..right]` must be sorted
(by the nature of rotated arrays).

---

```cpp
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
```

If the target fell within the sorted right range
(`nums[mid] < target <= nums[right]`) --
search right.

Otherwise -- search left.

---

## 🏁 Target Not Found

```cpp
    return false;
}
```

The sentinels crossed. The kingdom was exhausted.
The target did not exist.

---

### 🎺 The Trial of the Impostor Kingdom

```cpp
int main() {
    vector<int> nums1 = {2, 5, 6, 0, 0, 1, 2};
    cout << (search(nums1, 0) ? "true" : "false") << endl;
    // expected: true

    vector<int> nums2 = {2, 5, 6, 0, 0, 1, 2};
    cout << (search(nums2, 3) ? "true" : "false") << endl;
    // expected: false

    vector<int> nums3 = {1, 0, 1, 1, 1};
    cout << (search(nums3, 0) ? "true" : "false") << endl;
    // expected: true

    return 0;
}
```

---

**Full trace for `[2, 5, 6, 0, 0, 1, 2]`, target = 0:**

| Step | left | right | mid | nums[mid] | Action                                    |
| ---- | ---- | ----- | --- | --------- | ----------------------------------------- |
| 1    | 0    | 6     | 3   | 0         | 0 == target --> Return **true**           |

**Answer: true** ✓

Found on the first glance -- mid happened to be the target.

---

**Full trace for `[2, 5, 6, 0, 0, 1, 2]`, target = 3:**

| Step | left | right | mid | nums[mid] | nums[l] | nums[r] | Action                          |
| ---- | ---- | ----- | --- | --------- | ------- | ------- | ------------------------------- |
| 1    | 0    | 6     | 3   | 0         | 2       | 2       | Left sorted? 2<=0? No. Right sorted. 0<3<=2? No. right=2 |
| 2    | 0    | 2     | 1   | 5         | 2       | 6       | Left sorted? 2<=5? Yes. 2<=3<5? Yes. right=0 |
| 3    | 0    | 0     | 0   | 2         | 2       | 2       | 2 != 3. All equal? Yes. left=1, right=-1 |
| 4    | 1    | -1    | --  | --        | --      | --      | left > right. Exit loop.        |

**Answer: false** ✓

---

**Full trace for `[1, 0, 1, 1, 1]`, target = 0:**

| Step | left | right | mid | nums[mid] | nums[l] | nums[r] | Action                          |
| ---- | ---- | ----- | --- | --------- | ------- | ------- | ------------------------------- |
| 1    | 0    | 4     | 2   | 1         | 1       | 1       | All three equal (1==1==1). Strip: left=1, right=3 |
| 2    | 1    | 3     | 2   | 1         | 0       | 1       | 1 != 0. Left sorted? 0<=1? Yes. 0<=0<1? Yes. right=1 |
| 3    | 1    | 1     | 1   | 0         | 0       | 0       | 0 == target --> Return **true** |

**Answer: true** ✓

Step 1 stripped the impostors (all three were `1`).
Step 2 identified the left half as sorted and narrowed the range.
Step 3 found the target.

---

## ⚠️ Worst Case -- O(N)

When all elements are identical except one:

```
nums = [1, 1, 1, 1, 1, 1, 0, 1, 1, 1]
```

The impostor stripping step (`left++, right--`) removes only 2 elements per iteration.
In the worst case, this degrades to O(N).

This is unavoidable with duplicates --
no algorithm can do better than O(N) in the worst case
because duplicates hide information that binary search needs.

**Average case** is still O(log N) when duplicates are sparse.

---

### 🧠 Memory of the Impostor Kingdom Law

-   Same as Rotated Binary Search I -- but with **one new step**
-   **The impostor step:** when `nums[left] == nums[mid] == nums[right]`
    -   Cannot determine which half is sorted
    -   Strip both ends: `left++`, `right--`
-   After stripping, the standard logic resumes:
    -   If `nums[left] <= nums[mid]` --> left half is sorted
    -   Else --> right half is sorted
    -   Check if target is in the sorted range, eliminate the other half
-   **Best/Average Time:** O(log N)
-   **Worst Time:** O(N) -- when all elements are duplicates
-   **Space:** O(1)

Thus is remembered the saga of **Search in Rotated Sorted Array II**,
where the Twisted Kingdom returned disguised in duplicate banners.
The Seeker did not panic.
He simply learned to strip one impostor from each flank
whenever the borders and the middle wore the same face --
shrinking the deception step by step
until the truth of the sorted half was clear again,
and the lost knight's banner
was either found or declared never to have existed. 🎭🎯✨
