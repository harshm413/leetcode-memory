## 🔀📥 _The Backward Merge: The Merge Sorted Array Saga_

> \_"The Oracle was given two sorted arrays: `nums1` and `nums2`.
>
> `nums1` had extra space at the end — enough room
> to hold all elements of `nums2`.
>
> She was commanded:
>
> **'Merge nums2 into nums1 in-place.
> The result must be sorted.
> Do not return a new array — modify nums1 directly.'**
>
> The naive approach: merge from the front.
> But that would overwrite elements in nums1
> before they've been placed.
>
> The Oracle's insight:
>
> **Merge from the BACK.**
>
> Start three pointers at the ends:
> `i` at the last real element of nums1,
> `j` at the last element of nums2,
> `k` at the very last position of nums1 (the empty space).
>
> Compare `nums1[i]` and `nums2[j]`.
> Place the LARGER one at position `k`.
> Move backward.
>
> By filling from the back, we never overwrite
> an element that hasn't been processed yet."\_

---

This is the saga of **Merge Sorted Array (LeetCode 88)**.

Given:
-   `nums1` of size `m + n` (first `m` elements are sorted, last `n` are zeros/placeholder).
-   `nums2` of size `n` (sorted).
-   Merge `nums2` into `nums1` so the result is sorted.

```
Input:  nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: nums1 = [1,2,2,3,5,6]

Input:  nums1 = [1], m = 1, nums2 = [], n = 0
Output: nums1 = [1]

Input:  nums1 = [0], m = 0, nums2 = [1], n = 1
Output: nums1 = [1]
```

---

## 💡 Approach 1 — Extra Space (Quick Mention)

The straightforward way: create a temp array, merge forward, copy back.

```cpp
void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
    vector<int> res(m + n);
    int i = 0, j = 0, k = 0;
    while (i < m && j < n) {
        if (nums1[i] <= nums2[j]) res[k++] = nums1[i++];
        else res[k++] = nums2[j++];
    }
    while (i < m) res[k++] = nums1[i++];
    while (j < n) res[k++] = nums2[j++];
    nums1 = res;
}
```

Classic merge from merge sort. Compare fronts, take the smaller,
drain the rest. **O(M+N) time, O(M+N) space.**

This works perfectly — but the problem asks us to do it in O(1) space.
The extra space exists INSIDE nums1 already (the trailing zeros).
Can we use that instead of a temp array?

---

## 🧠 Why Forward Merge In-Place Fails

Imagine merging forward directly into nums1 without temp space:

```
nums1 = [1, 2, 3, 0, 0, 0], nums2 = [2, 5, 6]
         i                            j
         k (write position)

Step 1: nums1[0]=1 < nums2[0]=2. Place 1 at k=0. Fine (1 was already there).
Step 2: nums1[1]=2 <= nums2[0]=2. Place 2 at k=1. Fine (2 was already there).
Step 3: nums1[2]=3 > nums2[0]=2. Place 2 at k=2. 
        BUT nums1[2] WAS 3! We just OVERWROTE 3 with 2!
        3 is lost forever. We can never recover it.
```

The problem: the write pointer `k` catches up to the read pointer `i`.
When `k` reaches an unread position in nums1, it destroys data.

**Forward merging into the SAME array is unsafe**
because reads and writes collide.

---

## 🧠 Why Backward Merge Is 100% Safe

Now consider merging from the BACK:

```
nums1 = [1, 2, 3, 0, 0, 0], nums2 = [2, 5, 6]
                  i                         j
                              k (write position = 5)
```

The write pointer `k` starts at position 5 (the empty space).
The read pointer `i` starts at position 2 (the last real element).

**Key insight: `k` is ALWAYS ahead of (or equal to) `i`.**

Why? At any point:
-   `k = i + j + 1` (remaining write positions = remaining reads from both).
-   Since `j >= 0`, we have `k >= i + 1`, meaning `k > i`. Always.

So `k` NEVER catches up to `i`. The write position is always
in the empty zone or in a position already read. No data is destroyed.

> _"The write pointer lives in the empty space at the back.
> The read pointer lives in the real data at the front.
> They move toward each other but the write pointer
> can never overwrite something the read pointer hasn't seen yet.
> The gap between them is exactly the remaining nums2 elements.
> That gap is the safety buffer."_

**The mathematical guarantee:**

At any moment, positions `0..i` contain unread nums1 elements.
Position `k` is where we write next.
`k - i = j + 1` (the number of unread nums2 elements + 1).
Since `j >= 0`, `k > i`. Always safe.

---

## 🧠 The Three Pointers

```
i = m - 1     → last real element in nums1
j = n - 1     → last element in nums2
k = m + n - 1 → last position in nums1 (the write position)
```

At each step: compare `nums1[i]` and `nums2[j]`.
Place the LARGER at `nums1[k]`. Move the winner and `k` backward.

---

### 📜 The Scroll of the Backward Merge

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📥 The Backward Merge Solution

### Initialize three pointers

```cpp
void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
    int i = m - 1;
    int j = n - 1;
    int k = m + n - 1;
```

`i` reads from the end of nums1's real elements.
`j` reads from the end of nums2.
`k` writes from the end of nums1's total space.

---

### Main merge loop

```cpp
    while (i >= 0 && j >= 0) {
```

Continue while both arrays have unprocessed elements.

---

### Place the larger element at the back

```cpp
        if (nums1[i] > nums2[j]) {
            nums1[k] = nums1[i];
            i--;
        } else {
            nums1[k] = nums2[j];
            j--;
        }
        k--;
    }
```

**If `nums1[i]` is larger:** place it at `k`, move `i` back.
**If `nums2[j]` is larger (or equal):** place it at `k`, move `j` back.

We always place the LARGER element at the back.
This ensures the back fills with the biggest values first,
working our way down to the smallest.

Why `else` covers the equal case too? When equal, it doesn't matter
which one goes first. Placing `nums2[j]` is fine.

> _"The largest warrior takes the last position.
> The second largest takes the second-to-last.
> The army assembles from the back, strongest first."_

---

### Drain remaining nums2 elements

```cpp
    while (j >= 0) {
        nums1[k] = nums2[j];
        j--;
        k--;
    }
}
```

If `j >= 0`, nums2 still has elements. Copy them into nums1.

**Why no drain loop for nums1?**
If `i >= 0` but `j < 0`, the remaining nums1 elements
are already in their correct positions in nums1.
They don't need to be moved — they're already there.

This is the beauty of merging INTO nums1.
nums1's leftover elements are already home.

> _"If nums2 is exhausted first — nums1's remaining elements
> are already sitting in the right place. Nothing to do.
> If nums1 is exhausted first — nums2's remaining elements
> must be copied over. They have no home yet."_

---

### 🎺 The Trial of the Backward Merge

```cpp
int main() {
    vector<int> n1 = {1, 2, 3, 0, 0, 0};
    vector<int> n2 = {2, 5, 6};
    merge(n1, 3, n2, 3);
    for (int x : n1) cout << x << " ";
    cout << endl; // expected: 1 2 2 3 5 6

    vector<int> n3 = {1};
    vector<int> n4 = {};
    merge(n3, 1, n4, 0);
    for (int x : n3) cout << x << " ";
    cout << endl; // expected: 1

    vector<int> n5 = {0};
    vector<int> n6 = {1};
    merge(n5, 0, n6, 1);
    for (int x : n5) cout << x << " ";
    cout << endl; // expected: 1

    return 0;
}
```

---

**Full trace for nums1 = [1,2,3,0,0,0], m=3, nums2 = [2,5,6], n=3:**

```
Initial: i=2, j=2, k=5
```

| Step | i | j | k | nums1[i] | nums2[j] | Larger | Action | nums1 |
|------|---|---|---|----------|----------|--------|--------|-------|
| 1 | 2 | 2 | 5 | 3 | 6 | 6 | nums1[5]=6, j-- | [1,2,3,0,0,**6**] |
| 2 | 2 | 1 | 4 | 3 | 5 | 5 | nums1[4]=5, j-- | [1,2,3,0,**5**,6] |
| 3 | 2 | 0 | 3 | 3 | 2 | 3 | nums1[3]=3, i-- | [1,2,3,**3**,5,6] |
| 4 | 1 | 0 | 2 | 2 | 2 | 2(nums2) | nums1[2]=2, j-- | [1,2,**2**,3,5,6] |
| 5 | j<0 | — | — | — | — | — | Loop ends | [1,2,2,3,5,6] |

No drain needed — nums1's remaining [1,2] are already in place.

**Result: [1, 2, 2, 3, 5, 6]** ✓

---

**Trace for nums1 = [4,5,6,0,0,0], m=3, nums2 = [1,2,3], n=3:**

| Step | i | j | k | nums1[i] | nums2[j] | Action | nums1 |
|------|---|---|---|----------|----------|--------|-------|
| 1 | 2 | 2 | 5 | 6 | 3 | place 6, i-- | [4,5,6,0,0,6] |
| 2 | 1 | 2 | 4 | 5 | 3 | place 5, i-- | [4,5,6,0,5,6] |
| 3 | 0 | 2 | 3 | 4 | 3 | place 4, i-- | [4,5,6,4,5,6] |
| 4 | i<0 | 2 | 2 | — | 3 | drain: place 3 | [4,5,3,4,5,6] |
| 5 | — | 1 | 1 | — | 2 | drain: place 2 | [4,2,3,4,5,6] |
| 6 | — | 0 | 0 | — | 1 | drain: place 1 | [1,2,3,4,5,6] |

Here nums2 had all smaller elements — the drain loop was needed.

**Result: [1, 2, 3, 4, 5, 6]** ✓

---

**Trace for nums1 = [0], m=0, nums2 = [1], n=1:**

```
i = -1 (no real elements in nums1). Main loop skipped.
Drain: nums1[0] = nums2[0] = 1.
```

**Result: [1]** ✓

---

## 🔍 The Two Approaches Compared

| Aspect | Extra Space | Backward Merge |
|--------|------------|----------------|
| Time | O(M + N) | O(M + N) |
| Space | O(M + N) | **O(1)** |
| Direction | Forward (natural) | Backward (trick) |
| Safety | Always safe (separate array) | Safe because k > i always |
| Code | Simpler | Slightly trickier |

Know both. Start with extra space if asked, then optimize to backward.

---

## 🔍 Connection to Merge Sort

This is exactly the merge step of merge sort —
but done in-place by exploiting the pre-allocated space.

In merge sort, you always need O(N) extra space for merging.
Here, the extra space is already provided inside nums1.

---

### 🧠 Memory of the Backward Merge Law

-   **Three pointers:** `i = m-1`, `j = n-1`, `k = m+n-1`
-   **Compare from the back:** place the LARGER at `k`, move backward
-   **Drain nums2** if it has remaining elements
-   **No drain for nums1** — its leftovers are already in place
-   **Why backward:** empty space at the back prevents overwrites
-   **Time:** O(M + N). **Space:** O(1).

Thus is remembered the saga of **Merge Sorted Array**,
where the Oracle merged from the back —
filling the empty space with the largest elements first,
working her way forward,
never overwriting an element before its time —
until two sorted armies became one,
assembled in perfect order
within the walls of the first array. 🔀📥✨
