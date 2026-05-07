## 🔄🔢 _The Out-of-Order Pairs: The Count Inversions Saga_

> \_"The Oracle was given an array.
>
> She was commanded:
>
> **'Count the number of inversions —
> pairs (i, j) where i < j but arr[i] > arr[j].'**
>
> An inversion is a pair that's "out of order."
> In a sorted array, there are zero inversions.
> In a reverse-sorted array, every pair is an inversion.
>
> Brute force: check every pair. O(N²). Too slow.
>
> The Oracle realized:
>
> **Merge Sort already compares elements across halves.
> During the MERGE step, when a right element is placed
> before remaining left elements — that's inversions.
> Count them during the merge. Free.**
>
> Modified Merge Sort. O(N log N). Inversions counted
> as a byproduct of sorting."\_

---

This is the saga of **Count Inversions**.

Given an array `arr`:
-   Count pairs `(i, j)` where `i < j` and `arr[i] > arr[j]`.

```
Input:  arr = [2, 4, 1, 3, 5]
Output: 3   (inversions: (2,1), (4,1), (4,3))

Input:  arr = [5, 4, 3, 2, 1]
Output: 10  (every pair — fully reversed)

Input:  arr = [1, 2, 3, 4, 5]
Output: 0   (already sorted — no inversions)
```

---

## 🧠 The Key Insight — Inversions During Merge

In merge sort, we split the array into two halves, sort each,
then merge them back together.

During the merge step, we compare elements from the left half
and the right half. When a right element is SMALLER than
a left element — that right element is smaller than ALL remaining
left elements (because the left half is sorted).

```
Left:  [2, 4]    Right: [1, 3]
        ↑                 ↑
        i                 j

arr[j]=1 < arr[i]=2.
Since left is sorted, 1 < 2 AND 1 < 4.
That's 2 inversions (mid - i + 1 remaining left elements).
```

Every time we pick from the right half during merge,
the number of inversions = number of remaining elements in the left half.

---

## 🧠 Why This Counts ALL Inversions

Inversions fall into three categories:
1. **Both in left half** — counted when recursively sorting the left.
2. **Both in right half** — counted when recursively sorting the right.
3. **One in left, one in right** — counted during the MERGE step.

Merge sort's recursion naturally handles all three.
The merge step handles cross-half inversions.
The recursive calls handle within-half inversions.

---

### 📜 The Scroll of the Out-of-Order Pairs

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔄 The Modified Merge Sort

### Entry point

```cpp
long long countInversions(vector<int>& arr) {
    return mergeSort(arr, 0, arr.size() - 1);
}
```

Returns the total inversion count. The array gets sorted as a side effect.

---

### Recursive split

```cpp
long long mergeSort(vector<int>& arr, int low, int high) {
    if (low >= high) return 0;
```

Base case: single element or empty — no inversions.

---

```cpp
    int mid = low + (high - low) / 2;
    long long count = 0;
```

---

### Count inversions in each half

```cpp
    count += mergeSort(arr, low, mid);
    count += mergeSort(arr, mid + 1, high);
```

Recursively sort and count inversions within each half.

---

### Count cross-half inversions during merge

```cpp
    count += mergeAndCount(arr, low, mid, high);
    return count;
}
```

The merge step counts inversions between the two halves.

---

## 🔄 The Merge Step — Where Inversions Are Counted

```cpp
long long mergeAndCount(vector<int>& arr, int low, int mid, int high) {
    vector<int> temp;
    int i = low, j = mid + 1;
    long long count = 0;
```

`i` walks the left half `[low..mid]`.
`j` walks the right half `[mid+1..high]`.
`temp` holds the merged result.
`count` accumulates cross-half inversions.

---

### Compare and merge

```cpp
    while (i <= mid && j <= high) {
```

---

### Left element ≤ right — no inversion

```cpp
        if (arr[i] <= arr[j]) {
            temp.push_back(arr[i]);
            i++;
        }
```

Left element is smaller or equal. It's in the correct order
relative to `arr[j]`. No inversion. Take from left.

---

### Right element < left — INVERSIONS FOUND

```cpp
        else {
            temp.push_back(arr[j]);
            count += (mid - i + 1);
            j++;
        }
    }
```

`arr[j] < arr[i]`. The right element is smaller.

Since the left half is sorted, `arr[j]` is also smaller than
`arr[i+1], arr[i+2], ..., arr[mid]`.
That's `mid - i + 1` elements — ALL of them form inversions with `arr[j]`.

> _"When the right warrior steps ahead of the left,
> it's not just one inversion — it's an inversion with
> EVERY remaining left warrior. They're all bigger.
> Count them all at once: mid - i + 1."_

This is the magic line. One comparison counts MULTIPLE inversions.
That's why the algorithm is O(N log N) instead of O(N²).

---

### Drain remaining elements

```cpp
    while (i <= mid) temp.push_back(arr[i++]);
    while (j <= high) temp.push_back(arr[j++]);
```

No inversions during draining — one half is already exhausted.

---

### Copy back to original array

```cpp
    for (int k = low; k <= high; k++) {
        arr[k] = temp[k - low];
    }
    return count;
}
```

The merged (sorted) result is written back to `arr`.
This is necessary for future merge steps to work correctly.

---

### 🎺 The Trial of the Out-of-Order Pairs

```cpp
int main() {
    vector<int> a1 = {2, 4, 1, 3, 5};
    cout << countInversions(a1) << endl; // expected: 3

    vector<int> a2 = {5, 4, 3, 2, 1};
    cout << countInversions(a2) << endl; // expected: 10

    vector<int> a3 = {1, 2, 3, 4, 5};
    cout << countInversions(a3) << endl; // expected: 0

    return 0;
}
```

---

**Trace for arr = [2, 4, 1, 3, 5]:**

```
mergeSort([2, 4, 1, 3, 5], 0, 4)
  mergeSort([2, 4], 0, 1)
    mergeSort([2], 0, 0) → 0
    mergeSort([4], 1, 1) → 0
    merge([2, 4]): 2 ≤ 4, no inversions. count = 0.
  
  mergeSort([1, 3, 5], 2, 4)
    mergeSort([1], 2, 2) → 0
    mergeSort([3, 5], 3, 4)
      merge([3, 5]): 3 ≤ 5, no inversions. count = 0.
    merge([1, 3, 5]): 1 ≤ 3, no inversions. count = 0.
  
  merge([2, 4] with [1, 3, 5]):
    i=0(2), j=0(1): 1 < 2 → count += (1-0+1) = 2. Take 1.
    i=0(2), j=1(3): 2 ≤ 3 → take 2.
    i=1(4), j=1(3): 3 < 4 → count += (1-1+1) = 1. Take 3.
    i=1(4), j=2(5): 4 ≤ 5 → take 4.
    Drain: take 5.
    count = 2 + 1 = 3.

Total: 0 + 0 + 3 = 3.
```

**Answer: 3** ✓ (inversions: (2,1), (4,1), (4,3))

---

**Trace for arr = [5, 4, 3, 2, 1]:**

Every pair is an inversion. N=5, total = N×(N-1)/2 = 10.

The merge sort counts them level by level:
-   Leaf merges: (5,4)→1, (3,2)→1.
-   Mid merges: [4,5] with [2,3]→4. [1] stays.
-   Final merge: [2,3,4,5] with [1]→4.
-   Total: 1+1+4+4 = 10. ✓

---

## 🔍 Why `mid - i + 1` and Not Just 1?

Because the left half is SORTED. If `arr[j] < arr[i]`,
then `arr[j]` is also less than `arr[i+1], arr[i+2], ..., arr[mid]`.

All those elements were originally to the LEFT of `arr[j]`
in the array (before sorting). Each forms an inversion.

Counting them one by one would be O(N²).
Counting them all at once (`mid - i + 1`) keeps it O(N log N).

---

## 🔍 Why Use `long long`?

Maximum inversions = N×(N-1)/2.
For N = 10⁵: inversions ≈ 5×10⁹. Exceeds `int` range.
Use `long long` for the count.

---

## 🔍 Connection to Sorting Distance

The inversion count measures "how far from sorted" an array is.
-   0 inversions = sorted.
-   N(N-1)/2 inversions = reverse sorted (maximum disorder).

Bubble sort performs exactly one swap per inversion.
So inversion count = number of bubble sort swaps.

---

### 🧠 Memory of the Out-of-Order Pairs Law

-   **Modified Merge Sort** — count inversions during the merge step
-   **When right < left:** `count += mid - i + 1` (all remaining left elements)
-   **Three categories:** left-left (recursive), right-right (recursive), cross (merge)
-   **Use `long long`** — max inversions = N(N-1)/2 can overflow int
-   **Inversion count = bubble sort swaps** = distance from sorted
-   **Time:** O(N log N). **Space:** O(N) for temp array.

Thus is remembered the saga of **Count Inversions**,
where the Oracle modified merge sort to count disorder —
every time a right element stepped ahead of the left,
it wasn't just one inversion but many,
counted all at once with `mid - i + 1` —
the total disorder of the array revealed
as a byproduct of sorting it. 🔄🔢✨
