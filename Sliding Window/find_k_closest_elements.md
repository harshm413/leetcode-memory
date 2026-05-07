## 🔍📏 _The Nearest Neighbors: The Find K Closest Elements Saga_

> \_"The Oracle was given a SORTED array, a target `x`, and a number `k`.
>
> She was commanded:
>
> **'Find the k elements closest to x.
> Return them in sorted order.
> If two elements are equally close, prefer the smaller one.'**
>
> The Oracle saw two approaches:
>
> **Approach 1 — Binary Search + Two Pointers (Expand):**
> Find where x would be inserted. Expand outward from there,
> picking the closer element at each step.
>
> **Approach 2 — Binary Search on the Left Boundary (Shrink):**
> The answer is a contiguous window of size k.
> Binary search for the LEFT boundary of that window.
>
> Both are O(log N + K). The shrink approach is more elegant."\_

---

This is the saga of **Find K Closest Elements (LeetCode 658)**.

Given a sorted array `arr`, integer `k`, and integer `x`:
-   Find the `k` closest elements to `x`.
-   Return them in sorted order.
-   Tie-breaking: prefer the smaller element.

```
Input:  arr = [1, 2, 3, 4, 5], k = 4, x = 3
Output: [1, 2, 3, 4]

Input:  arr = [1, 2, 3, 4, 5], k = 4, x = -1
Output: [1, 2, 3, 4]

Input:  arr = [1, 1, 2, 3, 4, 5], k = 4, x = -1
Output: [1, 1, 2, 3]
```

---

## 🧠 Key Observation — The Answer Is a Contiguous Window

The array is sorted. The k closest elements form a CONTIGUOUS subarray.

Why? If elements `a` and `c` are in the answer but `b` (between them)
is not, then `b` is closer to x than at least one of `a` or `c`
(by the sorted property). Contradiction.

So we just need to find the LEFT BOUNDARY of a window of size k.

---

## 💡 Approach 1 — Binary Search + Expand (Two Pointers)

Find the insertion point of x. Then expand outward with two pointers.

```cpp
vector<int> findClosestElements(vector<int>& arr, int k, int x) {
    int n = arr.size();
    int left = 0, right = n - 1;
```

---

### Start with the full array, shrink from both ends

```cpp
    while (right - left >= k) {
        if (abs(arr[left] - x) > abs(arr[right] - x)) {
            left++;
        } else {
            right--;
        }
    }
```

Start with the entire array. Remove the FARTHER element from either end.
Repeat until exactly k elements remain.

**If left is farther:** remove it (`left++`).
**If right is farther (or equal):** remove it (`right--`).

The `else` handles the tie-breaking: when equally close,
we remove the RIGHT (larger) element, keeping the LEFT (smaller).

> _"The Oracle starts with everyone.
> She removes the farthest person from either end.
> Ties go to the right — the smaller element stays.
> She keeps removing until exactly k remain."_

---

### Return the remaining window

```cpp
    return vector<int>(arr.begin() + left, arr.begin() + right + 1);
}
```

The window `[left, right]` has exactly k elements. Return them.

---

### 🎺 The Trial of the Nearest Neighbors

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 4, 5};
    auto r1 = findClosestElements(a1, 4, 3);
    for (int x : r1) cout << x << " ";
    cout << endl; // expected: 1 2 3 4

    vector<int> a2 = {1, 2, 3, 4, 5};
    auto r2 = findClosestElements(a2, 4, -1);
    for (int x : r2) cout << x << " ";
    cout << endl; // expected: 1 2 3 4

    vector<int> a3 = {1, 1, 2, 3, 4, 5};
    auto r3 = findClosestElements(a3, 4, -1);
    for (int x : r3) cout << x << " ";
    cout << endl; // expected: 1 1 2 3

    return 0;
}
```

---

**Trace for arr = [1, 2, 3, 4, 5], k = 4, x = 3:**

```
Initial: left=0, right=4. Window size = 5. Need 4.

|arr[0]-3| = 2, |arr[4]-3| = 2. Equal → remove right. right=3.
Window size = 4 = k. Stop.

Result: arr[0..3] = [1, 2, 3, 4].
```

**Answer: [1, 2, 3, 4]** ✓ (tie-breaking: prefer smaller, so 5 is removed)

---

**Trace for arr = [1, 2, 3, 4, 5], k = 4, x = -1:**

```
left=0, right=4. |1-(-1)|=2, |5-(-1)|=6. Right farther → right=3.
Window size = 4 = k. Stop.

Result: [1, 2, 3, 4].
```

**Answer: [1, 2, 3, 4]** ✓ (x is to the left of all elements)

---

**Trace for arr = [1, 1, 1, 2, 2, 3, 4], k = 3, x = 2:**

```
left=0, right=6. |1-2|=1, |4-2|=2. Right farther → right=5.
left=0, right=5. |1-2|=1, |3-2|=1. Equal → right=4.
left=0, right=4. |1-2|=1, |2-2|=0. Left farther → left=1.
left=1, right=4. |1-2|=1, |2-2|=0. Left farther → left=2.
Window size = 3 = k. Stop.

Result: arr[2..4] = [1, 2, 2].
```

**Answer: [1, 2, 2]** ✓

---

## 💡 Approach 2 — Binary Search on Left Boundary (O(log(N-K) + K))

The answer is a window of size k. Binary search for its left boundary.

```cpp
vector<int> findClosestElements(vector<int>& arr, int k, int x) {
    int left = 0, right = arr.size() - k;

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (x - arr[mid] > arr[mid + k] - x) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return vector<int>(arr.begin() + left, arr.begin() + left + k);
}
```

**Search space:** `left` can range from 0 to `n - k`.
**Comparison:** is `x` closer to `arr[mid]` or `arr[mid + k]`?
-   If `arr[mid]` is farther → the window should start later. `left = mid + 1`.
-   Otherwise → the window starts here or earlier. `right = mid`.

This is O(log(N-K) + K) — faster than Approach 1's O(N) for large arrays.

**Why `x - arr[mid] > arr[mid+k] - x`?**
We compare the distance from x to the LEFT edge vs the RIGHT edge
of a potential window. If the left edge is farther, shift right.
The `>` (not `>=`) handles the tie-breaking (prefer smaller = prefer left).

---

## 🔍 The Two Approaches Compared

| Approach | Time | Space | Concept |
|----------|------|-------|---------|
| Shrink from both ends | O(N) | O(1) | Remove farther end until k remain |
| **Binary search left boundary** | **O(log(N-K) + K)** | O(1) | Search for optimal start |

For large N and small K, binary search is faster.
For simplicity, the shrink approach is easier to code and explain.

---

## 🔍 Edge Cases

**x smaller than all elements:** answer = first k elements.
**x larger than all elements:** answer = last k elements.
**x in the array:** x is included (it's distance 0 — closest possible).
**Duplicates:** handled naturally by the distance comparison.
**k = n:** return the entire array.

---

## 🔍 Why the Answer Is Always Contiguous

In a sorted array, if you pick the k closest elements,
they must be consecutive. Proof by contradiction:

If `arr[i]` and `arr[j]` (j > i+1) are both in the answer
but `arr[i+1]` is not, then `|arr[i+1] - x| ≤ max(|arr[i] - x|, |arr[j] - x|)`
(by sorted order, arr[i+1] is between arr[i] and arr[j]).
So arr[i+1] should be in the answer too. Contradiction.

---

### 🧠 Memory of the Nearest Neighbors Law

-   **Answer is a contiguous window of size k** (sorted array property)
-   **Approach 1 (shrink):** start with full array, remove farther end. O(N).
-   **Approach 2 (binary search):** search for left boundary. O(log(N-K) + K).
-   **Tie-breaking:** prefer smaller element (remove right on equal distance)
-   **Comparison:** `|arr[left] - x|` vs `|arr[right] - x|`
-   **Time:** O(N) or O(log(N-K) + K). **Space:** O(1).

Thus is remembered the saga of **Find K Closest Elements**,
where the Oracle knew the answer was a contiguous window —
she shrank from both ends, removing the farther element each time,
preferring the smaller on ties —
until exactly k nearest neighbors remained,
sorted and ready. 🔍📏✨
