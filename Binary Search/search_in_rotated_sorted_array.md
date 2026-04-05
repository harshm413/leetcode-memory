## 🎯⚔️ _The Lost Knight in the Twisted Kingdom: The Search in Rotated Sorted Array Saga_

> \_"A kingdom once straight as an arrow
> was bent upon its axis.
> The numbers had once stood in perfect ascending order,
> but the Wheel of Rotation spun the land --
> moving some elements from the front to the back.
>
> The knight the Seeker sought still stood somewhere within --
> neither vanished nor changed --
> but the roads curved strangely now.
>
> The Seeker was commanded:
>
> **'Find the target in this rotated sorted array.
> Return its index, or -1 if it does not exist.
> All elements are unique.
> You must do it in O(log N) time.'**
>
> The Seeker could not walk the entire kingdom --
> that would be O(N), unworthy of the decree.
>
> But he knew a powerful truth about rotated sorted arrays:
>
> **At any midpoint, one half is always perfectly sorted.**
>
> The rotation creates exactly one break point.
> On one side of any mid, the numbers ascend cleanly.
> On the other side, the rotation break exists.
>
> The Seeker's strategy:
>
> **Split the kingdom at mid.
> Determine which half is sorted.
> Check if the target lies within that sorted range.
> If yes -- search that half.
> If no -- search the other half.**
>
> One sorted half always guided the way.
> The twisted half was eliminated by exclusion."\_

---

This is the saga of **Search in Rotated Sorted Array**.

You are given a sorted array of **unique** elements
that has been rotated at some pivot, and a target value.

Return the **index** of the target, or **-1** if not found.
Must run in **O(log N)** time.

```
Input:  nums = [4, 5, 6, 7, 0, 1, 2], target = 0
Output: 4

Input:  nums = [4, 5, 6, 7, 0, 1, 2], target = 3
Output: -1

Input:  nums = [1], target = 0
Output: -1
```

---

## 🧠 The Oracle's Core Insight -- One Half Is Always Sorted

A rotated sorted array looks like this:

```
[4, 5, 6, 7, 0, 1, 2]
 ^-----------^  ^----^
 sorted (left)  sorted (right)
         ^ rotation break
```

At any midpoint, one of the two halves is guaranteed to be sorted.

**How to determine which half is sorted:**

```
If nums[left] <= nums[mid]  -->  left half [left..mid] is sorted
Else                         -->  right half [mid..right] is sorted
```

**Once we know which half is sorted, we can check if the target is in it:**

```
If target is within the sorted half's range  -->  search that half
Else                                          -->  search the other half
```

The sorted half gives us certainty.
The unsorted half is eliminated by exclusion.

```
Time:  O(log N) -- halving at every step
Space: O(1)
```

---

### 📜 The Scroll of the Twisted Kingdom

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🗡️ The Seeker's Rotated Binary Search Ritual

```cpp
int search(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
```

Two sentinels were placed at the boundaries of the twisted kingdom.

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
        if (nums[mid] == target) return mid;
```

If the middle element was the target --
the quest ended. Return its index.

---

### ⚔️ Step 2 -- Left Half Is Sorted

```cpp
        if (nums[left] <= nums[mid]) {
```

If `nums[left] <= nums[mid]`, the left half `[left..mid]`
was in ascending order -- no rotation break within it.

---

```cpp
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
```

**Is the target within the sorted left range?**

If `nums[left] <= target < nums[mid]` -- yes.
The target is somewhere in the clean, sorted left half.
Eliminate the right: `right = mid - 1`.

If not -- the target must be in the unsorted right half.
Eliminate the left: `left = mid + 1`.

> _"The sorted half gives certainty.
> If the target is not within its clean range,
> it must be hiding in the twisted other half."_

---

### 🔄 Step 3 -- Right Half Is Sorted

```cpp
        } else {
```

If the left half was not sorted,
the right half `[mid..right]` must be sorted
(the rotation break is in the left half).

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

**Is the target within the sorted right range?**

If `nums[mid] < target <= nums[right]` -- yes.
Search right: `left = mid + 1`.

If not -- search left: `right = mid - 1`.

---

## 🏁 Target Not Found

```cpp
    return -1;
}
```

The sentinels crossed. The kingdom was exhausted.
The knight did not exist in this realm.

---

### 🎺 The Trial of the Twisted Kingdom

```cpp
int main() {
    vector<int> nums1 = {4, 5, 6, 7, 0, 1, 2};
    cout << search(nums1, 0) << endl;  // expected: 4
    cout << search(nums1, 3) << endl;  // expected: -1

    vector<int> nums2 = {1};
    cout << search(nums2, 0) << endl;  // expected: -1

    return 0;
}
```

---

**Full trace for `[4, 5, 6, 7, 0, 1, 2]`, target = 0:**

| Step | left | right | mid | nums[mid] | Which half sorted? | Target in sorted range? | Action      |
| ---- | ---- | ----- | --- | --------- | ------------------ | ----------------------- | ----------- |
| 1    | 0    | 6     | 3   | 7         | Left (4<=7)        | 4<=0<7? No              | left = 4    |
| 2    | 4    | 6     | 5   | 1         | Left? (0<=1) Yes   | 0<=0<1? Yes             | right = 4   |
| 3    | 4    | 4     | 4   | 0         | 0 == target        | --                      | Return **4**|

**Answer: 4** ✓

Step 1: Left half `[4,5,6,7]` is sorted. Target `0` is not in range `[4,7)`. Search right.
Step 2: Left half `[0,1]` is sorted. Target `0` is in range `[0,1)`. Search left.
Step 3: Found `0` at index 4.

---

**Full trace for `[4, 5, 6, 7, 0, 1, 2]`, target = 3:**

| Step | left | right | mid | nums[mid] | Which half sorted? | Target in sorted range? | Action      |
| ---- | ---- | ----- | --- | --------- | ------------------ | ----------------------- | ----------- |
| 1    | 0    | 6     | 3   | 7         | Left (4<=7)        | 4<=3<7? No              | left = 4    |
| 2    | 4    | 6     | 5   | 1         | Left? (0<=1) Yes   | 0<=3<1? No              | left = 6    |
| 3    | 6    | 6     | 6   | 2         | 2 != 3             | Left (2<=2) Yes. 2<=3<2? No | left = 7 |
| 4    | 7    | 6     | --  | --        | left > right       | --                      | Exit loop   |

**Answer: -1** ✓

`3` does not exist in the array. It would fall between `2` and `4` -- a gap created by the rotation.

---

**Full trace for `[6, 7, 0, 1, 2, 4, 5]`, target = 4:**

| Step | left | right | mid | nums[mid] | Which half sorted? | Target in sorted range? | Action      |
| ---- | ---- | ----- | --- | --------- | ------------------ | ----------------------- | ----------- |
| 1    | 0    | 6     | 3   | 1         | Right (1<..<=5)    | 1<4<=5? Yes             | left = 4    |
| 2    | 4    | 6     | 5   | 4         | 4 == target        | --                      | Return **5**|

**Answer: 5** ✓

Step 1: Left half `[6,7,0,1]` -- `nums[left]=6 > nums[mid]=1`, so right half is sorted.
Target `4` is in range `(1, 5]`. Search right.
Step 2: Found `4` at index 5.

---

## 🔍 Why `nums[left] <= nums[mid]` and Not `<`?

The `<=` handles the case where `left == mid` (when only 2 elements remain).

Example: `left = 4, right = 5, mid = 4`.
`nums[left] == nums[mid]` because they're the same index.
The "left half" is just one element -- trivially sorted.
Using `<` would incorrectly fall into the "right half sorted" branch.

---

### 🧠 Memory of the Twisted Kingdom Law

-   **Key insight:** in a rotated sorted array, one half is always sorted
-   **Determine sorted half:** `nums[left] <= nums[mid]` --> left is sorted; else right is sorted
-   **Check if target is in the sorted range:**
    -   Left sorted: `nums[left] <= target < nums[mid]` --> search left
    -   Right sorted: `nums[mid] < target <= nums[right]` --> search right
-   **If target is NOT in the sorted range** --> search the other (unsorted) half
-   **All elements are unique** -- no impostor/duplicate ambiguity
-   **Time:** O(log N) -- halving at every step
-   **Space:** O(1)

Thus is remembered the saga of **Search in Rotated Sorted Array**,
where the Seeker split the twisted realm again and again --
always identifying which half still honored the old sorted law,
checking if the target hid within that clean range,
and eliminating the other half by exclusion --
until the lost knight's banner was found
or the kingdom was exhausted
and silence confirmed his absence. 🎯⚔️✨
