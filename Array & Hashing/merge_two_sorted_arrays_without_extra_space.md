## 🔀🚫 _The Spaceless Merge: The Merge Two Sorted Arrays Without Extra Space Saga_

> \_"The Oracle was given two sorted arrays: `arr1` and `arr2`.
>
> She was commanded:
>
> **'After merging, arr1 should contain the N smallest elements
> and arr2 should contain the M largest elements.
> Both must remain sorted.
> Use NO extra space.'**
>
> This was NOT LeetCode 88 (where arr1 has trailing zeros).
> Here both arrays are full. No room to merge into.
>
> The Oracle knew two approaches:
>
> **Approach 1 — The Swap and Re-sort:**
> Compare arr1's largest with arr2's smallest.
> If arr1's largest > arr2's smallest → swap them.
> After all swaps, sort both arrays individually.
>
> **Approach 2 — Gap Method (Shell Sort inspired):**
> Start with a large gap. Compare elements that are `gap` apart.
> Swap if out of order. Shrink the gap. Repeat until gap = 0.
>
> Both achieve O(1) extra space."\_

---

This is the saga of **Merge Two Sorted Arrays Without Extra Space**.

Given two sorted arrays `arr1` (size n) and `arr2` (size m):
-   Rearrange so `arr1` has the n smallest elements (sorted)
    and `arr2` has the m largest elements (sorted).
-   **No extra space** (O(1) auxiliary).

```
Input:  arr1 = [1, 3, 5, 7], arr2 = [0, 2, 6, 8, 9]
Output: arr1 = [0, 1, 2, 3], arr2 = [5, 6, 7, 8, 9]

Input:  arr1 = [10, 12], arr2 = [5, 18, 20]
Output: arr1 = [5, 10], arr2 = [12, 18, 20]
```

---

## 💡 Approach 1 — Swap and Re-sort

### 🧠 The Insight

After merging, the n smallest elements belong in arr1
and the m largest belong in arr2.

If arr1's largest element > arr2's smallest element,
they're in the wrong arrays. Swap them.

Repeat from arr1's end and arr2's start, moving inward,
until no more swaps are needed.

After swapping, both arrays have the right ELEMENTS
but may not be internally sorted. Sort each individually.

---

### The Code

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

```cpp
void mergeWithoutSpace(vector<int>& arr1, vector<int>& arr2) {
    int n = arr1.size(), m = arr2.size();
```

---

### Compare arr1's end with arr2's start

```cpp
    int i = n - 1, j = 0;
```

`i` starts at arr1's last element (arr1's largest).
`j` starts at arr2's first element (arr2's smallest).

---

### Swap misplaced elements

```cpp
    while (i >= 0 && j < m && arr1[i] > arr2[j]) {
        swap(arr1[i], arr2[j]);
        i--;
        j++;
    }
```

If arr1's largest > arr2's smallest → they're in the wrong arrays.
Swap them. Move inward.

Stop when:
-   `arr1[i] <= arr2[j]` — everything is in the right array now.
-   `i < 0` or `j >= m` — one array is exhausted.

> _"The Oracle compares the biggest of the small pile
> with the smallest of the big pile.
> If they're swapped — exchange them.
> Keep going until the piles are correctly divided."_

---

### Sort both arrays individually

```cpp
    sort(arr1.begin(), arr1.end());
    sort(arr2.begin(), arr2.end());
}
```

After swapping, the right elements are in the right arrays
but not necessarily in sorted order within each array.
Sorting fixes the internal order.

**Time:** O(min(N,M)) for swaps + O(N log N + M log M) for sorts.
**Space:** O(1) extra (in-place sorts use O(log N) stack).

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> a1 = {1, 3, 5, 7}, b1 = {0, 2, 6, 8, 9};
    mergeWithoutSpace(a1, b1);
    for (int x : a1) cout << x << " "; cout << " | ";
    for (int x : b1) cout << x << " "; cout << endl;
    // expected: 0 1 2 3  | 5 6 7 8 9

    vector<int> a2 = {10, 12}, b2 = {5, 18, 20};
    mergeWithoutSpace(a2, b2);
    for (int x : a2) cout << x << " "; cout << " | ";
    for (int x : b2) cout << x << " "; cout << endl;
    // expected: 5 10  | 12 18 20

    return 0;
}
```

---

**Trace for arr1 = [1,3,5,7], arr2 = [0,2,6,8,9]:**

```
i=3 (7), j=0 (0): 7 > 0 → swap. arr1=[1,3,5,0], arr2=[7,2,6,8,9]. i=2, j=1.
i=2 (5), j=1 (2): 5 > 2 → swap. arr1=[1,3,2,0], arr2=[7,5,6,8,9]. i=1, j=2.
i=1 (3), j=2 (6): 3 < 6 → STOP. Elements are in the right arrays.

Sort arr1: [0, 1, 2, 3]
Sort arr2: [5, 6, 7, 8, 9]
```

**Result: arr1 = [0,1,2,3], arr2 = [5,6,7,8,9]** ✓

---

**Trace for arr1 = [10,12], arr2 = [5,18,20]:**

```
i=1 (12), j=0 (5): 12 > 5 → swap. arr1=[10,5], arr2=[12,18,20]. i=0, j=1.
i=0 (10), j=1 (18): 10 < 18 → STOP.

Sort arr1: [5, 10]
Sort arr2: [12, 18, 20]
```

**Result: arr1 = [5,10], arr2 = [12,18,20]** ✓

---

## 💡 Approach 2 — Gap Method (Shell Sort Inspired)

### 🧠 The Insight

Treat both arrays as ONE virtual array of size `n + m`.
Use a gap-based comparison (like Shell Sort):

Start with `gap = ceil((n + m) / 2)`.
Compare elements that are `gap` positions apart.
If the left element > right element → swap.
After one pass, halve the gap: `gap = ceil(gap / 2)`.
Repeat until `gap = 0`.

The trick: when comparing across the boundary of arr1 and arr2,
map the virtual index to the correct array.

---

### The Code

```cpp
void mergeGap(vector<int>& arr1, vector<int>& arr2) {
    int n = arr1.size(), m = arr2.size();
    int total = n + m;
    int gap = (total + 1) / 2;
```

Initial gap = ceiling of total/2.

---

### Shrink gap until done

```cpp
    while (gap > 0) {
```

---

### Compare elements gap apart

```cpp
        int i = 0, j = gap;
        while (j < total) {
```

`i` and `j` are virtual indices into the combined array.
`j = i + gap` always.

---

### Map virtual indices to real arrays

```cpp
            int valI = (i < n) ? arr1[i] : arr2[i - n];
            int valJ = (j < n) ? arr1[j] : arr2[j - n];
```

If virtual index < n → it's in arr1. Otherwise → arr2 at `index - n`.

---

### Swap if out of order

```cpp
            if (valI > valJ) {
                if (i < n && j < n) swap(arr1[i], arr1[j]);
                else if (i < n && j >= n) swap(arr1[i], arr2[j - n]);
                else swap(arr2[i - n], arr2[j - n]);
            }
            i++;
            j++;
        }
```

Three cases for where `i` and `j` land:
-   Both in arr1 → swap within arr1.
-   `i` in arr1, `j` in arr2 → swap across arrays.
-   Both in arr2 → swap within arr2.

---

### Halve the gap

```cpp
        if (gap == 1) break;
        gap = (gap + 1) / 2;
    }
}
```

Ceiling division: `(gap + 1) / 2`.
When gap reaches 1, one final pass, then done.

**Time:** O((N+M) × log(N+M)). **Space:** O(1).

---

## 🔍 The Two Approaches Compared

| Aspect | Swap + Re-sort | Gap Method |
|--------|---------------|------------|
| Time | O(min(N,M) + N log N + M log M) | O((N+M) log(N+M)) |
| Space | O(1) | O(1) |
| Concept | Simple swap + sort | Shell sort on virtual array |
| Code | Simpler | More complex |
| Preferred | Easier to explain | More elegant |

Both are valid. The swap + re-sort approach is easier to code
and explain. The gap method is more "pure" (no re-sorting step).

---

## 🔍 How This Differs from LeetCode 88

| LeetCode 88 | This problem |
|-------------|-------------|
| arr1 has trailing zeros (extra space) | Both arrays are full |
| Merge INTO arr1 | Rearrange BETWEEN arr1 and arr2 |
| Backward merge (fill from end) | Swap misplaced elements + sort |
| Result: one merged array | Result: two sorted arrays |

---

### 🧠 Memory of the Spaceless Merge Law

-   **Approach 1:** compare arr1's end with arr2's start, swap if wrong, sort both
-   **Approach 2:** gap method — Shell Sort on virtual combined array
-   **Both are O(1) extra space**
-   **After swapping:** right elements in right arrays, but unsorted internally → sort
-   **Stop swapping when:** `arr1[i] <= arr2[j]` — partition is correct
-   **Different from LeetCode 88:** no trailing zeros, two separate output arrays

Thus is remembered the saga of **Merge Two Sorted Arrays Without Extra Space**,
where the Oracle compared the largest of the small pile
with the smallest of the big pile —
swapping misplaced elements across the boundary —
then sorting each pile to restore internal order —
all without a single byte of extra space. 🔀🚫✨
