## 🏆⚔️ _The Kth Throne of the Ranked Kingdom: The Kth Largest Element in an Array Saga_

> \_"The Kingdom held a grand ranking ceremony —
> every warrior assigned to a throne
> in descending order of strength.
>
> The King pointed at the Kth throne and declared:
>
> **'I want the warrior who sits at rank K.
> Not the strongest. Not the weakest.
> The one who is exactly Kth from the top.'**
>
> The naive approach was clear —
> sort all warriors from strongest to weakest
> and return the one at position K.
> Correct. But costly — O(N log N) for a full sort.
>
> Two wiser paths existed:
>
> **Path One — The Min-Heap of K Champions:**
> Maintain a leaderboard of exactly K strongest warriors.
> The weakest among them is the Kth largest overall.
> O(N log K) — better when K is small.
>
> **Path Two — QuickSelect:**
> The genius of partial sorting.
> Do not sort the entire array.
> Pick a pivot. Partition.
> If the pivot lands at rank K — done.
> If not — recurse only into the half that contains rank K.
> O(N) average — the fastest possible.
>
> The Kingdom learned both paths —
> for different battles call for different weapons.
> The heap for streaming data.
> QuickSelect for a fixed array.
>
> Both crowned the same warrior on the Kth throne."\_

---

This is the saga of **Kth Largest Element in an Array**.

You are given an integer array `nums` and an integer `k`.

Return the **Kth largest element** in the array.

Note: it is the Kth largest in **sorted order**, not the Kth distinct element.

```
Input:  nums = [3,2,1,5,6,4],  k = 2
Output: 5

Input:  nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

---

## 🧠 The Oracle's Core Insight — Two Weapons for One Crown

Both approaches find the same answer via different philosophies:

```
Min-Heap of size K:
  → Build a window of the K largest seen so far
  → Heap top is always the Kth largest
  → O(N log K) time,  O(K) space

QuickSelect:
  → Partition around a pivot
  → Pivot lands at its final sorted position
  → If position == (N-K) from left → it IS the Kth largest
  → Recurse only into the relevant half
  → O(N) average time,  O(1) extra space
```

We will implement and explain **both** —
because understanding both is the mark of mastery.

---

### 📜 The Scroll of the Ranked Kingdom

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;
```

---

### 🧱 The Structure of the Problem

```cpp
// The Kth largest in descending order
// = the (N-K)th smallest in ascending order (0-indexed)
// QuickSelect finds the element that would sit at index (N-K)
// after full sorting.
```

This equivalence is the key to QuickSelect:
finding the Kth **largest** is the same as finding
the element at index `n - k` in the **ascending** sorted order.

---

## ⚗️ Path One — The Min-Heap of K Champions

_The same leaderboard pattern from Kth Largest in a Stream_

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

The Min-Heap maintained a rolling leaderboard of K strongest warriors.

Every warrior was pushed in.
When the heap exceeded K — the weakest was expelled.
After all warriors were evaluated —
the heap held exactly the K strongest,
with the weakest of them — the Kth largest — sitting at the top.

Return `minHeap.top()`.

Simple. Clean. Already mastered in the Stream saga.

---

## ⚔️ Path Two — QuickSelect: The Partial Sort Champion

_Find rank without sorting everything_

### The Core Idea: The Partition

```cpp
int partition(vector<int>& nums, int left, int right) {
    int pivot = nums[right];
    int storeIdx = left;
```

A **pivot** was chosen — the rightmost element.

`storeIdx` tracked where the pivot would eventually land —
its correct final position in the sorted array.

---

```cpp
    for (int i = left; i < right; i++) {
        if (nums[i] <= pivot) {
            swap(nums[i], nums[storeIdx]);
            storeIdx++;
        }
    }
```

Every element smaller than or equal to the pivot
was swapped to the left portion.

`storeIdx` advanced each time.

After the loop, everything to the left of `storeIdx`
was ≤ pivot. Everything to the right was > pivot.

---

```cpp
    swap(nums[storeIdx], nums[right]);
    return storeIdx;
}
```

The pivot was placed at its final position — `storeIdx`.

This was the pivot's **true rank** in the sorted array —
exactly how many elements were smaller or equal to it on its left.

> _"The pivot asks nothing more.
> It has found its rightful throne.
> Everything to its left belongs below it.
> Everything to its right belongs above it."_

---

### The QuickSelect Recursion

```cpp
int quickSelect(vector<int>& nums, int left, int right, int targetIdx) {
    if (left == right) return nums[left];

    int pivotIdx = partition(nums, left, right);
```

`targetIdx` was the index we sought — `n - k` (the Kth largest in ascending order).

After partitioning, the pivot landed at `pivotIdx`.

---

```cpp
    if (pivotIdx == targetIdx) {
        return nums[pivotIdx];
    }
```

**If the pivot landed exactly at `targetIdx`** —
the oracle had found the Kth largest. Return it immediately.

---

```cpp
    else if (pivotIdx < targetIdx) {
        return quickSelect(nums, pivotIdx + 1, right, targetIdx);
    }
```

**If the pivot landed too far left** —
the Kth largest is in the right half.
Recurse right.

---

```cpp
    else {
        return quickSelect(nums, left, pivotIdx - 1, targetIdx);
    }
}
```

**If the pivot landed too far right** —
the Kth largest is in the left half.
Recurse left.

> _"At every step, half the battlefield is abandoned.
> Only the half that contains the target rank
> is explored.
> The rest of the warriors do not matter."_

---

### The Main Entry Point for QuickSelect

```cpp
int findKthLargest_quickselect(vector<int>& nums, int k) {
    int n = nums.size();
    int targetIdx = n - k;
    return quickSelect(nums, 0, n - 1, targetIdx);
}
```

The Kth largest in descending order
is at index `n - k` in ascending sorted order.

QuickSelect was launched with the full range
and the target index.

---

### 🎺 The Trial of the Ranked Kingdom

```cpp
int main() {
    vector<int> nums1 = {3, 2, 1, 5, 6, 4};
    cout << findKthLargest_heap(nums1, 2) << endl;        // expected: 5

    vector<int> nums2 = {3, 2, 1, 5, 6, 4};
    cout << findKthLargest_quickselect(nums2, 2) << endl;  // expected: 5

    vector<int> nums3 = {3,2,3,1,2,4,5,5,6};
    cout << findKthLargest_quickselect(nums3, 4) << endl;  // expected: 4
    return 0;
}
```

**Heap trace for `[3,2,1,5,6,4]`, k=2:**

| Element | Heap After Push | Pop?    | Heap After |
| ------- | --------------- | ------- | ---------- |
| 3       | {3}             | No      | {3}        |
| 2       | {2,3}           | No      | {2,3}      |
| 1       | {1,2,3}         | Yes → 1 | {2,3}      |
| 5       | {2,3,5}         | Yes → 2 | {3,5}      |
| 6       | {3,5,6}         | Yes → 3 | {5,6}      |
| 4       | {4,5,6}         | Yes → 4 | {5,6}      |

Heap top = `5` ✓ — the 2nd largest.

**QuickSelect trace for `[3,2,1,5,6,4]`, k=2, targetIdx=4:**

Sorted ascending: `[1,2,3,4,5,6]` — index 4 = `5`.

Partition around pivot `4` (rightmost):

-   Elements ≤ 4: `3,2,1,4` → left. Elements > 4: `5,6` → right.
-   Array becomes: `[3,2,1,4,6,5]` or similar, pivot `4` at index 3.
-   pivotIdx=3 < targetIdx=4 → recurse right: `[6,5]`
-   Partition `[6,5]` around pivot `5`: `5` at index 4, `6` at 5.
-   pivotIdx=4 == targetIdx=4 → **return `5`** ✓

---

### 🧠 Memory of the Kth Throne Law

**Min-Heap approach:**

-   Min-heap of size K — top = Kth largest
-   Push every element, pop when size > K
-   O(N log K) time, O(K) space
-   Best for streaming data or small K

**QuickSelect approach:**

-   Target index = `n - k` (Kth largest = index n-k in ascending order)
-   Partition around pivot — pivot lands at its final sorted position
-   If `pivotIdx == targetIdx` → found it
-   If `pivotIdx < targetIdx` → recurse right
-   If `pivotIdx > targetIdx` → recurse left
-   O(N) average time, O(1) extra space
-   Best for fixed arrays where in-place modification is allowed

**The shared insight:**

-   Both reduce the problem to maintaining or finding a K-ranked boundary
-   The heap expels the too-weak. QuickSelect abandons the irrelevant half.

Thus is remembered the saga of **Kth Largest Element in an Array**,
where two weapons served one crown —
the Min-Heap maintaining a rolling leaderboard of K champions,
and QuickSelect hunting the Kth throne by partitioning
and abandoning irrelevant halves —
until the warrior at rank K was revealed
without ever needing to rank everyone else. 🏆⚔️✨
