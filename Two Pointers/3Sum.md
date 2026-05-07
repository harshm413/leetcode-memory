## 🎯🎯🎯 _The Three Warriors: The 3Sum Saga_

> \_"The Oracle was given an array of integers.
>
> She was commanded:
>
> **'Find ALL unique triplets that sum to zero.
> No duplicate triplets in the result.'**
>
> The Oracle saw this as an extension of Two Sum:
>
> **Fix one element. Then find a two-sum pair
> in the remaining array that completes the target.**
>
> But with a twist: the result must have NO duplicates.
> `[-1, 0, 1]` and `[-1, 0, 1]` appearing twice is forbidden.
>
> The Oracle's strategy:
>
> **Sort the array.**
> **Fix element `i`. Use two pointers on the rest.**
> **Skip duplicates at every level.**
>
> Sorting enabled two pointers AND duplicate skipping."\_

---

This is the saga of **3Sum (LeetCode 15)**.

Given an integer array `nums`:
-   Find all unique triplets `[a, b, c]` where `a + b + c = 0`.
-   No duplicate triplets.

```
Input:  nums = [-1, 0, 1, 2, -1, -4]
Output: [[-1,-1,2], [-1,0,1]]

Input:  nums = [0, 1, 1]
Output: []

Input:  nums = [0, 0, 0]
Output: [[0, 0, 0]]
```

---

## 🧠 The Strategy — Fix One, Two-Pointer the Rest

**Step 1: Sort the array.**
This enables two pointers and makes duplicate skipping easy.

**Step 2: For each element `nums[i]` (the "fixed" element):**
Find two elements in `nums[i+1..n-1]` that sum to `-nums[i]`.
That's a Two Sum II problem on a sorted subarray.

**Step 3: Skip duplicates at both levels.**
-   Skip duplicate `i` values (outer loop).
-   Skip duplicate `left`/`right` values (inner loop).

---

## 🧠 Why Sort First?

1. **Two pointers work on sorted arrays.** Left pointer moves right
   to increase the sum, right pointer moves left to decrease it.

2. **Duplicate skipping is trivial.** In a sorted array, duplicates
   are adjacent. Just check `nums[i] == nums[i-1]` to skip.

3. **Early termination.** If `nums[i] > 0`, no triplet starting here
   can sum to 0 (all remaining elements are also positive).

---

### 📜 The Scroll of the Three Warriors

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🎯 Sort the Array

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> result;
    sort(nums.begin(), nums.end());
    int n = nums.size();
```

Sorting: O(N log N). Enables everything that follows.

---

## 🎯 Fix Element i — The Outer Loop

```cpp
    for (int i = 0; i < n - 2; i++) {
```

`i` goes up to `n - 3` (need at least 2 elements after it for a triplet).

---

### Early termination

```cpp
        if (nums[i] > 0) break;
```

If `nums[i]` is positive, and the array is sorted,
then `nums[i] + nums[left] + nums[right] > 0` always.
No valid triplet exists from here onward. Stop.

---

### Skip duplicate i values

```cpp
        if (i > 0 && nums[i] == nums[i - 1]) continue;
```

If this `nums[i]` is the same as the previous one,
we've already found all triplets starting with this value.
Skip to avoid duplicate triplets.

Why `i > 0`? The first element has no previous to compare with.

> _"The Oracle asks: 'Have I seen this warrior before?'
> If the same value was already fixed — skip.
> All its triplets were already found."_

---

### Two pointers for the remaining pair

```cpp
        int left = i + 1, right = n - 1;
```

`left` starts right after `i`. `right` starts at the end.
They'll walk toward each other looking for pairs that sum to `-nums[i]`.

---

### The two-pointer search

```cpp
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
```

Compute the triplet sum.

---

### Sum too small — move left right

```cpp
            if (sum < 0) {
                left++;
            }
```

Need a bigger sum. Move `left` right to increase it.

---

### Sum too big — move right left

```cpp
            else if (sum > 0) {
                right--;
            }
```

Need a smaller sum. Move `right` left to decrease it.

---

### Found a triplet — record and skip duplicates

```cpp
            else {
                result.push_back({nums[i], nums[left], nums[right]});
```

Sum is zero. Record the triplet.

---

### Skip duplicate left values

```cpp
                while (left < right && nums[left] == nums[left + 1]) left++;
```

Move `left` past all duplicates of the current value.
Without this, the same pair would be found again.

---

### Skip duplicate right values

```cpp
                while (left < right && nums[right] == nums[right - 1]) right--;
```

Move `right` past all duplicates of the current value.

---

### Move both pointers inward

```cpp
                left++;
                right--;
            }
        }
    }
    return result;
}
```

After skipping duplicates, move both pointers one more step
to the next distinct values. Continue searching for more pairs.

> _"A triplet is found. The Oracle records it.
> Then she skips past all clones of left and right —
> no duplicate triplets shall pass —
> and continues the search inward."_

---

### 🎺 The Trial of the Three Warriors

```cpp
int main() {
    vector<int> n1 = {-1, 0, 1, 2, -1, -4};
    auto r1 = threeSum(n1);
    for (auto& t : r1) {
        cout << "[" << t[0] << "," << t[1] << "," << t[2] << "] ";
    }
    cout << endl; // expected: [-1,-1,2] [-1,0,1]

    vector<int> n2 = {0, 0, 0};
    auto r2 = threeSum(n2);
    for (auto& t : r2) {
        cout << "[" << t[0] << "," << t[1] << "," << t[2] << "] ";
    }
    cout << endl; // expected: [0,0,0]

    return 0;
}
```

---

**Trace for nums = [-1, 0, 1, 2, -1, -4]:**

After sorting: `[-4, -1, -1, 0, 1, 2]`

**i=0, nums[i]=-4:** target = 4. left=1, right=5.
-   sum = -4 + (-1) + 2 = -3 < 0 → left++.
-   sum = -4 + (-1) + 2 = -3 < 0 → left++.
-   sum = -4 + 0 + 2 = -2 < 0 → left++.
-   sum = -4 + 1 + 2 = -1 < 0 → left++.
-   left=5 == right=5 → done. No triplet with -4.

**i=1, nums[i]=-1:** target = 1. left=2, right=5.
-   sum = -1 + (-1) + 2 = 0 ✓ → record **[-1, -1, 2]**.
    Skip dup left (no dup). Skip dup right (no dup). left=3, right=4.
-   sum = -1 + 0 + 1 = 0 ✓ → record **[-1, 0, 1]**.
    left=4, right=3 → done.

**i=2, nums[i]=-1:** same as i=1 → **skip** (duplicate).

**i=3, nums[i]=0:** target = 0. left=4, right=5.
-   sum = 0 + 1 + 2 = 3 > 0 → right--.
-   left=4 == right=4 → done.

**Result: [[-1,-1,2], [-1,0,1]]** ✓

---

**Trace for nums = [0, 0, 0]:**

After sorting: `[0, 0, 0]`

**i=0, nums[i]=0:** left=1, right=2.
-   sum = 0 + 0 + 0 = 0 ✓ → record **[0, 0, 0]**.
-   left=2, right=1 → done.

**Result: [[0,0,0]]** ✓

---

## 🔍 The Duplicate Skipping — Three Levels

| Level | What's skipped | Code |
|-------|---------------|------|
| Outer (i) | Same fixed element | `if (nums[i] == nums[i-1]) continue` |
| Inner (left) | Same left value after a match | `while (nums[left] == nums[left+1]) left++` |
| Inner (right) | Same right value after a match | `while (nums[right] == nums[right-1]) right--` |

All three are needed. Missing any one produces duplicate triplets.

---

## 🔍 Extending to 4Sum

Same pattern: fix TWO elements, two-pointer the rest.
Add one more outer loop. Skip duplicates at all levels.
O(N³) instead of O(N²).

---

### 🧠 Memory of the Three Warriors Law

-   **Sort first** — enables two pointers + duplicate skipping
-   **Fix `i`, two-pointer `left` and `right`** on the rest
-   **Three duplicate skips:** outer `i`, inner `left`, inner `right`
-   **Early termination:** `nums[i] > 0` → break
-   **Sum < 0:** left++. **Sum > 0:** right--. **Sum == 0:** record + skip dups.
-   **Time:** O(N²) — outer loop × two-pointer. **Space:** O(1) extra.

Thus is remembered the saga of **3Sum**,
where the Oracle sorted the warriors,
fixed one at a time,
and sent two pointers marching inward
to find the pair that balanced the sum to zero —
skipping every duplicate at every level —
until all unique triplets were found. 🎯🎯🎯✨
