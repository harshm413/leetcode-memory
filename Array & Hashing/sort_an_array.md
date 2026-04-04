## ⚔️📜 _The Great Ordering of Chaos: The Sort an Array Saga_

> \_"In the Realm of Numbers,
> an army stood in perfect disorder --
> strong and weak, large and small,
> all jumbled together with no hierarchy.
>
> The Oracle was commanded:
>
> **'Bring order to this chaos.
> Sort the army from weakest to strongest.
> Your algorithm must run in O(N log N) time.'**
>
> She considered her options.
>
> Bubble Sort and Insertion Sort were O(N^2) --
> too slow for a vast army.
>
> QuickSort was O(N log N) on average --
> but O(N^2) in the worst case,
> and LeetCode's test cases were designed to punish it.
>
> So the Oracle chose **Merge Sort** --
> the one algorithm that guaranteed O(N log N)
> in ALL cases, without exception.
>
> The strategy was ancient and elegant:
>
> **Divide the army in half, recursively,
> until every soldier stands alone.
> A single soldier is already sorted.
> Then merge the halves back together --
> always picking the smaller soldier first --
> rebuilding order from the ground up.**
>
> This was not about cleverness.
> This was about discipline and structure.
> Divide. Conquer. Merge. Repeat."\_

---

This is the saga of **Sort an Array**.

You are given an integer array `nums`.

Your task:

-   Sort the array in **ascending order**.
-   Return the sorted array.
-   Must run in **O(N log N)** time.

```
Input:  [5, 2, 3, 1]
Output: [1, 2, 3, 5]

Input:  [5, 1, 1, 2, 0, 0]
Output: [0, 0, 1, 1, 2, 5]
```

---

## 🧠 The Oracle's Core Insight -- Divide Until Trivial, Merge with Discipline

Merge Sort works on one principle:

> **Merging two sorted arrays into one sorted array is easy and linear.**

If we can break the problem down until every piece is trivially sorted
(a single element), then merging them back together
produces a fully sorted array.

```
Divide phase:  Split the array in half, recursively, until size = 1.
               This takes O(log N) levels of recursion.

Merge phase:   At each level, merge adjacent sorted halves.
               Each level processes all N elements once.

Total:         O(log N) levels x O(N) work per level = O(N log N).
```

**Why not QuickSort?**

QuickSort is O(N log N) average but O(N^2) worst case.
LeetCode specifically crafts inputs (like already-sorted arrays
or arrays with many duplicates) that trigger QuickSort's worst case.
Merge Sort has no worst case -- it's always O(N log N).

---

### 📜 The Scroll of Divided Armies

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🧩 The Merge Ritual -- Unite Two Sorted Halves

_The heart of Merge Sort_

```cpp
void merge(vector<int>& nums, int left, int mid, int right) {
    vector<int> temp;
    int i = left, j = mid + 1;
```

Two sorted squads stood ready:
-   Left squad: indices `left` to `mid`.
-   Right squad: indices `mid + 1` to `right`.

`i` pointed to the front of the left squad.
`j` pointed to the front of the right squad.
`temp` would hold the merged result.

---

### ⚔️ Compare Front Soldiers -- Pick the Smaller

```cpp
    while (i <= mid && j <= right) {
        if (nums[i] <= nums[j]) {
            temp.push_back(nums[i++]);
        } else {
            temp.push_back(nums[j++]);
        }
    }
```

While both squads still had soldiers,
the Oracle compared their front-line warriors.

The smaller one stepped into `temp` first.
If equal, the left squad's soldier went first --
preserving **stability** (equal elements keep their original order).

The chosen soldier's pointer advanced.
The comparison continued until one squad was exhausted.

---

### 🏹 March the Remaining Soldiers

```cpp
    while (i <= mid) temp.push_back(nums[i++]);
    while (j <= right) temp.push_back(nums[j++]);
```

When one squad ran out,
the other's remaining soldiers marched in unopposed.

They were already sorted --
no further comparison needed.

---

### 🍟 Write the Merged Result Back

```cpp
    for (int k = 0; k < (int)temp.size(); k++) {
        nums[left + k] = temp[k];
    }
}
```

The merged formation replaced the original positions
in `nums` from `left` to `right`.

The battlefield was now sorted within this range.

---

## 🔮 The Divide-and-Conquer Spell

_Break until trivial, then rebuild_

```cpp
void mergeSort(vector<int>& nums, int left, int right) {
    if (left >= right) return;
```

**Base case:** a single element (or empty range) is already sorted.
Nothing to do. Return immediately.

---

### ✂️ Divide the Army in Half

```cpp
    int mid = left + (right - left) / 2;
    mergeSort(nums, left, mid);
    mergeSort(nums, mid + 1, right);
```

The Oracle split the range at the midpoint.

`left + (right - left) / 2` avoids integer overflow
compared to `(left + right) / 2`.

Both halves were recursively sorted --
the left half first, then the right half.

Each recursive call split further and further
until every sub-array was a single element.

---

### 🔗 Merge the Sorted Halves

```cpp
    merge(nums, left, mid, right);
}
```

Once both halves were sorted,
the merge ritual combined them into one sorted range.

Order was rebuilt from the smallest fragments upward.

---

## 🏁 The Entry Point

```cpp
vector<int> sortArray(vector<int>& nums) {
    mergeSort(nums, 0, nums.size() - 1);
    return nums;
}
```

The Oracle launched the recursion across the entire array
and returned the sorted result.

---

### 🎺 The Trial of the Chaotic Army

```cpp
int main() {
    vector<int> nums1 = {5, 2, 3, 1};
    nums1 = sortArray(nums1);
    for (int x : nums1) cout << x << " ";
    cout << endl; // expected: 1 2 3 5

    vector<int> nums2 = {5, 1, 1, 2, 0, 0};
    nums2 = sortArray(nums2);
    for (int x : nums2) cout << x << " ";
    cout << endl; // expected: 0 0 1 1 2 5

    return 0;
}
```

---

**Full recursion trace for `[5, 2, 3, 1]`:**

```
mergeSort([5, 2, 3, 1], 0, 3)
  mid = 1
  mergeSort([5, 2, 3, 1], 0, 1)
    mid = 0
    mergeSort([5, 2, 3, 1], 0, 0)  -> base case, return
    mergeSort([5, 2, 3, 1], 1, 1)  -> base case, return
    merge(0, 0, 1): [5, 2] -> [2, 5]
  mergeSort([2, 5, 3, 1], 2, 3)
    mid = 2
    mergeSort([2, 5, 3, 1], 2, 2)  -> base case, return
    mergeSort([2, 5, 3, 1], 3, 3)  -> base case, return
    merge(2, 2, 3): [3, 1] -> [1, 3]
  merge(0, 1, 3): [2, 5] + [1, 3] -> [1, 2, 3, 5]
```

**Merge walkthrough for `[2, 5]` + `[1, 3]`:**

| i (left) | j (right) | Compare     | Pick | temp          |
| -------- | --------- | ----------- | ---- | ------------- |
| 2        | 1         | 2 vs 1      | 1    | [1]           |
| 2        | 3         | 2 vs 3      | 2    | [1, 2]        |
| 5        | 3         | 5 vs 3      | 3    | [1, 2, 3]     |
| 5        | exhausted | --          | 5    | [1, 2, 3, 5]  |

**Answer: [1, 2, 3, 5]** ✓

---

## 🔍 Why O(N log N)? -- The Complexity Breakdown

```
Level 0:  1 array of size N         -> N work to merge
Level 1:  2 arrays of size N/2      -> N work to merge
Level 2:  4 arrays of size N/4      -> N work to merge
...
Level k:  2^k arrays of size N/2^k  -> N work to merge

Total levels = log2(N)
Total work = N * log2(N) = O(N log N)
```

Every level does exactly O(N) total merge work.
There are O(log N) levels.
The product is O(N log N) -- guaranteed, always.

---

### 🧠 Memory of the Great Ordering Law

-   **Merge Sort** -- divide and conquer with guaranteed O(N log N)
-   **Divide:** split array at midpoint recursively until size = 1
-   **Merge:** compare front elements of two sorted halves, pick smaller, repeat
    -   When one half is exhausted, append the rest of the other
    -   Write merged result back to original array
-   **Stable:** equal elements preserve their original relative order
-   **No worst case:** unlike QuickSort, Merge Sort is O(N log N) always
-   `mid = left + (right - left) / 2` prevents integer overflow
-   **Time:** O(N log N) -- log N levels, N work per level
-   **Space:** O(N) -- temporary array for merging

Thus is remembered the saga of **Sort an Array**,
where the Oracle brought order not by force
but by patient division and disciplined reunion --
splitting the chaotic army into lone soldiers,
then merging them back together
with the smaller always stepping forward first --
until the entire realm stood in perfect ascending harmony,
guaranteed in O(N log N),
without exception,
without mercy for disorder. ⚔️📜✨
