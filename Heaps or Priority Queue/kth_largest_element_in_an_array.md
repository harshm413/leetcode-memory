## 🏆⚔️ _The Kth Throne of the Ranked Kingdom: The Kth Largest Element in an Array Saga_

> \_"The Kingdom held a grand ranking ceremony --
> every warrior assigned to a throne
> in descending order of strength.
>
> The King pointed at the Kth throne and declared:
>
> **'I want the warrior who sits at rank K.
> Not the strongest. Not the weakest.
> The one who is exactly Kth from the top.'**
>
> Two paths existed:
>
> **Path One -- The Min-Heap of K Champions:**
> Maintain a leaderboard of exactly K strongest warriors.
> The weakest among them is the Kth largest overall.
> O(N log K).
>
> **Path Two -- QuickSelect:**
> The genius of partial sorting.
> Pick a pivot. Partition.
> If the pivot lands at rank K -- done.
> If not -- recurse only into the half that contains rank K.
> O(N) average.
>
> The Kingdom learned both paths --
> for different battles call for different weapons."\_

---

This is the saga of **Kth Largest Element in an Array**.

Given an integer array `nums` and an integer `k`:

-   Return the **Kth largest element** in the array.
-   It is the Kth largest in **sorted order**, not the Kth distinct element.

```
Input:  nums = [3,2,1,5,6,4], k = 2
Output: 5

Input:  nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

---

## 🧠 Path One -- Min-Heap of Size K

The same pattern as Kth Largest in a Stream:

> **The Kth largest overall
> is the SMALLEST element among the top K largest.**

A min-heap of size K keeps the weakest champion on top.
That weakest champion IS the Kth largest.

```
For each element:
  Push into heap.
  If heap.size() > k → pop the minimum.

After all elements: heap.top() = Kth largest.
```

```
Time:  O(N log K) -- N pushes, each O(log K)
Space: O(K)
```

---

### 📜 The Scroll of the Min-Heap Path

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;
```

---

## ⚗️ The Min-Heap Ritual

```cpp
int findKthLargest_heap(vector<int>& nums, int k) {
    priority_queue<int, vector<int>, greater<int>> minHeap;

    for (int num : nums) {
        minHeap.push(num);
        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
    }
    return minHeap.top();
}
```

Every element was pushed. When the heap exceeded K,
the weakest was expelled.

After all elements, the heap held exactly the K largest.
The top -- the smallest of those K -- was the Kth largest.

---

## 🧠 Path Two -- QuickSelect: The Partial Sort Champion

QuickSelect finds the element at a specific rank
**without sorting the entire array**.

The Kth largest in descending order
= the element at index `n - k` in ascending sorted order.

```
Target index = n - k

Partition around a pivot.
If pivot lands at targetIdx → found it.
If pivot < targetIdx → recurse right.
If pivot > targetIdx → recurse left.
```

Each recursion discards half the remaining elements (on average).

```
Time:  O(N) average, O(N²) worst case
Space: O(1) extra (in-place partitioning)
```

---

## ⚔️ The Partition -- Heart of QuickSelect

```cpp
int partition(vector<int>& nums, int left, int right) {
    int pivot = nums[right];
    int storeIdx = left;

    for (int i = left; i < right; i++) {
        if (nums[i] <= pivot) {
            swap(nums[i], nums[storeIdx]);
            storeIdx++;
        }
    }
    swap(nums[storeIdx], nums[right]);
    return storeIdx;
}
```

The pivot (rightmost element) was placed at its **final sorted position**.

Everything to the left of `storeIdx` was <= pivot.
Everything to the right was > pivot.

`storeIdx` was the pivot's true rank in the sorted array.

> _"The pivot asks nothing more.
> It has found its rightful throne.
> Everything to its left belongs below it.
> Everything to its right belongs above it."_

---

## 🔮 The QuickSelect Recursion

```cpp
int quickSelect(vector<int>& nums, int left, int right, int targetIdx) {
    if (left == right) return nums[left];

    int pivotIdx = partition(nums, left, right);

    if (pivotIdx == targetIdx) {
        return nums[pivotIdx];
    } else if (pivotIdx < targetIdx) {
        return quickSelect(nums, pivotIdx + 1, right, targetIdx);
    } else {
        return quickSelect(nums, left, pivotIdx - 1, targetIdx);
    }
}
```

**Pivot at target** → found the Kth largest. Return it.
**Pivot too far left** → target is in the right half. Recurse right.
**Pivot too far right** → target is in the left half. Recurse left.

> _"At every step, half the battlefield is abandoned.
> Only the half containing the target rank is explored."_

---

## 🏁 The Entry Point

```cpp
int findKthLargest_quickselect(vector<int>& nums, int k) {
    int n = nums.size();
    return quickSelect(nums, 0, n - 1, n - k);
}
```

Kth largest in descending = index `n - k` in ascending.

---

### 🎺 The Trial of the Ranked Kingdom

```cpp
int main() {
    vector<int> nums1 = {3, 2, 1, 5, 6, 4};
    cout << findKthLargest_heap(nums1, 2) << endl;        // 5

    vector<int> nums2 = {3, 2, 1, 5, 6, 4};
    cout << findKthLargest_quickselect(nums2, 2) << endl;  // 5

    vector<int> nums3 = {3,2,3,1,2,4,5,5,6};
    cout << findKthLargest_heap(nums3, 4) << endl;         // 4

    return 0;
}
```

---

**Heap trace for `[3,2,1,5,6,4]`, k=2:**

| Element | Heap after push | Size>2? | Pop | Heap after |
| ------- | --------------- | ------- | --- | ---------- |
| 3       | {3}             | No      | --  | {3}        |
| 2       | {2,3}           | No      | --  | {2,3}      |
| 1       | {1,2,3}         | Yes     | 1   | {2,3}      |
| 5       | {2,3,5}         | Yes     | 2   | {3,5}      |
| 6       | {3,5,6}         | Yes     | 3   | {5,6}      |
| 4       | {4,5,6}         | Yes     | 4   | {5,6}      |

**Answer: heap.top() = 5** ✓

---

**QuickSelect trace for `[3,2,1,5,6,4]`, k=2, targetIdx=4:**

Sorted ascending: `[1,2,3,4,5,6]` -- index 4 = `5`.

Partition around pivot `4` (rightmost):

Elements <= 4: `3,2,1,4` → left side. Elements > 4: `5,6` → right side.
Pivot `4` lands at index 3.

`pivotIdx=3 < targetIdx=4` → recurse right: `[5,6]`.

Partition `[5,6]` around pivot `6`:
`5` goes left. Pivot `6` at index 5.
`pivotIdx=5 > targetIdx=4` → recurse left: `[5]`.

Single element → return `5`.

**Answer: 5** ✓

---

**Heap trace for `[3,2,3,1,2,4,5,5,6]`, k=4:**

| Element | Heap after push   | Size>4? | Pop | Heap after    |
| ------- | ----------------- | ------- | --- | ------------- |
| 3       | {3}               | No      | --  | {3}           |
| 2       | {2,3}             | No      | --  | {2,3}         |
| 3       | {2,3,3}           | No      | --  | {2,3,3}       |
| 1       | {1,2,3,3}         | No      | --  | {1,2,3,3}     |
| 2       | {1,2,2,3,3}       | Yes     | 1   | {2,2,3,3}     |
| 4       | {2,2,3,3,4}       | Yes     | 2   | {2,3,3,4}     |
| 5       | {2,3,3,4,5}       | Yes     | 2   | {3,3,4,5}     |
| 5       | {3,3,4,5,5}       | Yes     | 3   | {3,4,5,5}     |
| 6       | {3,4,5,5,6}       | Yes     | 3   | {4,5,5,6}     |

**Answer: heap.top() = 4** ✓

---

## 🔄 Heap vs QuickSelect -- When to Choose Which

| Min-Heap (O(N log K))             | QuickSelect (O(N) avg)            |
| --------------------------------- | --------------------------------- |
| Always O(N log K)                 | O(N) average, O(N²) worst case   |
| Does not modify the input array   | Modifies the array (in-place)     |
| Works for streaming data          | Requires the full array upfront   |
| Simple to implement               | Partition logic is trickier       |
| Best when K is small              | Best when K is moderate/large     |

**For interviews:** the min-heap is safer (no worst case).
**For optimal average:** QuickSelect is faster.

---

## ⚠️ QuickSelect Worst Case and Mitigation

If the pivot is always the smallest or largest element,
QuickSelect degrades to O(N²) -- similar to QuickSort's worst case.

Mitigation strategies:
-   **Random pivot:** `swap(nums[rand() % (right-left+1) + left], nums[right])`
    before partitioning. Expected O(N).
-   **Median of three:** pick median of first, middle, last elements as pivot.

LeetCode's test cases are designed to punish naive pivot selection.
Always randomize.

---

### 🧠 Memory of the Kth Throne Law

**Min-Heap approach:**
-   Min-heap of size K -- top = Kth largest
-   Push every element, pop when size > K
-   O(N log K) time, O(K) space

**QuickSelect approach:**
-   Target index = `n - k` (Kth largest = index n-k in ascending)
-   Partition around pivot -- pivot lands at its final sorted position
-   If `pivotIdx == targetIdx` → found it
-   If `pivotIdx < targetIdx` → recurse right
-   If `pivotIdx > targetIdx` → recurse left
-   O(N) average, O(N²) worst case, O(1) extra space
-   **Randomize pivot** to avoid worst case

-   **Edge cases:**
    -   k = 1 → find the maximum
    -   k = n → find the minimum
    -   All elements equal → any element is the answer
    -   Duplicates → both approaches handle them naturally

Thus is remembered the saga of **Kth Largest Element in an Array**,
where two weapons served one crown --
the Min-Heap maintaining a rolling leaderboard of K champions,
and QuickSelect hunting the Kth throne by partitioning
and abandoning irrelevant halves --
until the warrior at rank K was revealed
without ever needing to rank everyone else. 🏆⚔️✨
