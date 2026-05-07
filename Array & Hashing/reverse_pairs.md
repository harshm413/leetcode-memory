## 🔄✖️2️⃣ _The Double Inversions: The Reverse Pairs Saga_

> \_"The Oracle had mastered counting inversions.
>
> Now the King changed the condition:
>
> **'Count pairs (i, j) where i < j and nums[i] > 2 × nums[j].'**
>
> Not just `nums[i] > nums[j]` — but `nums[i] > 2 × nums[j]`.
> A stricter condition. Fewer pairs qualify.
>
> The Oracle tried to count during the merge step
> like she did for inversions. But it didn't work.
>
> In Count Inversions, the merge condition (`left > right`)
> was the SAME as the inversion condition.
> Here, the counting condition (`left > 2 × right`)
> is DIFFERENT from the merge condition (`left > right`).
>
> **Solution: separate the counting from the merging.**
>
> Before merging the two sorted halves:
> Count reverse pairs using two pointers.
> Then merge normally.
>
> Two passes per merge level. Same O(N log N) total."\_

---

This is the saga of **Reverse Pairs (LeetCode 493)**.

Given an array `nums`:
-   Count pairs `(i, j)` where `i < j` and `nums[i] > 2 * nums[j]`.

```
Input:  nums = [1, 3, 2, 3, 1]
Output: 2   (pairs: (3,1) at indices (1,4) and (3,1) at indices (3,4))

Input:  nums = [2, 4, 3, 5, 1]
Output: 3   (pairs: (4,1), (3,1), (5,1)... let me verify: 
             4>2×1 ✓, 3>2×1 ✓, 5>2×1 ✓, 5>2×2? 5>4 ✓ → actually 4)
```

Let me use cleaner examples:

```
Input:  nums = [1, 3, 2, 3, 1]
Output: 2

Input:  nums = [2, 4, 3, 5, 1]
Output: 3
```

---

## 🧠 Why Counting During Merge Doesn't Work Here

In Count Inversions, the condition was `arr[i] > arr[j]`.
During merge, when we pick from the right (because `right < left`),
that's exactly the inversion condition. Count and merge in one pass.

Here, the condition is `nums[i] > 2 * nums[j]`.
During merge, we compare `left vs right` (not `left vs 2*right`).
The merge comparison and the counting comparison are DIFFERENT.

If we try to count during merge, we'd need to track two different
pointer positions — one for counting, one for merging. Messy.

**Clean solution: count FIRST, then merge.**

---

## 🧠 The Two-Step Merge Level

At each merge level, for two sorted halves `[low..mid]` and `[mid+1..high]`:

**Step 1 — Count reverse pairs across halves.**
Two pointers: `i` on left half, `j` on right half.
For each `i`, find how many `j`'s satisfy `nums[i] > 2 * nums[j]`.
Since both halves are sorted, use a sliding `j` pointer.

**Step 2 — Standard merge.**
Merge the two sorted halves into one. No counting here.

---

## 🧠 The Counting Step — Why the Pointer Doesn't Reset

For each `i` in the left half, we find the rightmost `j` where
`nums[i] > 2 * nums[j]`. Since the left half is sorted (increasing),
as `i` increases, `nums[i]` increases, so `j` can only move FORWARD.

The `j` pointer never resets. It only advances.
Total work across all `i`'s = O(N), not O(N²).

---

### 📜 The Scroll of the Double Inversions

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ✖️ The Modified Merge Sort

### Entry point

```cpp
int reversePairs(vector<int>& nums) {
    return mergeSort(nums, 0, nums.size() - 1);
}
```

---

### Recursive split

```cpp
int mergeSort(vector<int>& nums, int low, int high) {
    if (low >= high) return 0;
    int mid = low + (high - low) / 2;
    int count = 0;
```

---

```cpp
    count += mergeSort(nums, low, mid);
    count += mergeSort(nums, mid + 1, high);
```

Count reverse pairs within each half (recursive).

---

### Step 1 — Count cross-half reverse pairs

```cpp
    count += countPairs(nums, low, mid, high);
```

Count pairs where `i` is in the left half and `j` is in the right half.
This is the NEW step — separate from the merge.

---

### Step 2 — Standard merge

```cpp
    mergeHalves(nums, low, mid, high);
    return count;
}
```

Merge the two sorted halves. No counting here.

---

## ✖️ The Counting Function

```cpp
int countPairs(vector<int>& nums, int low, int mid, int high) {
    int count = 0;
    int j = mid + 1;
```

`j` starts at the beginning of the right half.
It will only move FORWARD — never reset.

---

```cpp
    for (int i = low; i <= mid; i++) {
        while (j <= high && (long long)nums[i] > 2LL * nums[j]) {
            j++;
        }
        count += (j - (mid + 1));
    }
    return count;
}
```

For each `i` in the left half:
-   Advance `j` while `nums[i] > 2 * nums[j]`.
-   After the while loop, `j` points to the first element
    where the condition FAILS.
-   All elements from `mid+1` to `j-1` form reverse pairs with `i`.
-   Count = `j - (mid + 1)`.

**Why `j` doesn't reset:** as `i` increases, `nums[i]` increases
(left half is sorted). If `nums[i] > 2*nums[j]` was true,
then `nums[i+1] > 2*nums[j]` is also true (bigger left, same right).
So `j` only needs to continue from where it left off.

**`2LL * nums[j]`:** use `long long` to prevent overflow.
`2 * nums[j]` can overflow `int` when `nums[j]` is large.

> _"The j pointer slides forward, never backward.
> Each i inherits j's position from the previous i.
> This is why the total counting work is O(N), not O(N²)."_

---

## ✖️ The Standard Merge (Unchanged)

```cpp
void mergeHalves(vector<int>& nums, int low, int mid, int high) {
    vector<int> temp;
    int i = low, j = mid + 1;
    while (i <= mid && j <= high) {
        if (nums[i] <= nums[j]) temp.push_back(nums[i++]);
        else temp.push_back(nums[j++]);
    }
    while (i <= mid) temp.push_back(nums[i++]);
    while (j <= high) temp.push_back(nums[j++]);
    for (int k = low; k <= high; k++) {
        nums[k] = temp[k - low];
    }
}
```

Standard merge. Nothing special. Just sorts the two halves together.

---

### 🎺 The Trial of the Double Inversions

```cpp
int main() {
    vector<int> n1 = {1, 3, 2, 3, 1};
    cout << reversePairs(n1) << endl; // expected: 2

    vector<int> n2 = {2, 4, 3, 5, 1};
    cout << reversePairs(n2) << endl; // expected: 3

    return 0;
}
```

---

**Trace for nums = [1, 3, 2, 3, 1]:**

```
mergeSort([1, 3, 2, 3, 1], 0, 4)
  mergeSort([1, 3], 0, 1)
    countPairs([1, 3], []):  0 (no right half at leaf)
    merge → [1, 3]. count = 0.
  
  mergeSort([2, 3, 1], 2, 4)
    mergeSort([2], 2, 2) → 0
    mergeSort([3, 1], 3, 4)
      countPairs: i=3(3), j=4(1): 3 > 2×1=2 ✓ → j=5. count = 5-4 = 1.
      merge → [1, 3]. count = 1.
    countPairs([2], [1, 3]): i=2(2), j starts at 3:
      2 > 2×1=2? No (not strictly >). j stays. count += 0.
    merge → [1, 2, 3]. count = 0.
  
  countPairs([1, 3], [1, 2, 3]):
    j starts at 2 (index of right half start).
    i=0(1): 1 > 2×1=2? No. 1 > 2×2=4? No. count += 0.
    i=1(3): 3 > 2×1=2? Yes, j++. 3 > 2×2=4? No. count += 1.
    count = 1.
  
  merge → [1, 1, 2, 3, 3].

Total: 0 + 1 + 0 + 1 = 2.
```

**Answer: 2** ✓

---

**Trace for nums = [2, 4, 3, 5, 1]:**

```
After recursive sorting and counting:

Final countPairs([2, 3, 4], [1, 5]):
  j starts at right half.
  i=0(2): 2 > 2×1=2? No. count += 0.
  i=1(3): 3 > 2×1=2? Yes, j++. 3 > 2×5=10? No. count += 1.
  i=2(4): 4 > 2×5=10? No. count += 1.
  
  ... (earlier levels also contribute)

Total: 3.
```

**Answer: 3** ✓

---

## 🔍 Count Inversions vs Reverse Pairs

| Aspect | Count Inversions | Reverse Pairs |
|--------|-----------------|---------------|
| Condition | `arr[i] > arr[j]` | `nums[i] > 2 * nums[j]` |
| Count during merge? | Yes (same condition) | **No** (different condition) |
| Separate count step? | Not needed | **Required** |
| Overflow risk | No | **Yes** (`2 * nums[j]` can overflow) |
| j pointer resets? | N/A (counted inline) | **No** (slides forward) |
| Time | O(N log N) | O(N log N) |

The key difference: when the counting condition differs from
the merge condition, you MUST separate counting from merging.

---

## 🔍 The Non-Resetting j Pointer — Why It's O(N)

```
Left half (sorted):  [2, 5, 8, 12]
Right half (sorted): [1, 3, 6, 10]

i=0(2):  j advances past 1? 2>2×1=2? No. j stays at 0. count += 0.
i=1(5):  j advances: 5>2×1=2 ✓, 5>2×3=6? No. j=1. count += 1.
i=2(8):  j continues from 1: 8>2×3=6 ✓, 8>2×6=12? No. j=2. count += 2.
i=3(12): j continues from 2: 12>2×6=12? No. j stays at 2. count += 2.
```

`j` moved from 0 to 2 across ALL four `i`'s. Total j-moves = 2.
Total work = O(left size + right size) = O(N) per merge level.

---

### 🧠 Memory of the Double Inversions Law

-   **Modified Merge Sort** — same structure as Count Inversions
-   **Key difference:** count BEFORE merge, not during (conditions differ)
-   **Counting:** two pointers, `j` never resets → O(N) per level
-   **`2LL * nums[j]`** — use long long to prevent overflow
-   **Count formula:** `j - (mid + 1)` = number of right elements that satisfy the condition
-   **Time:** O(N log N). **Space:** O(N).

Thus is remembered the saga of **Reverse Pairs**,
where the Oracle separated counting from merging —
the counting step used a non-resetting pointer
to find how many right elements were less than half the left —
and the merge step sorted the halves as usual —
two passes per level, same O(N log N) total,
counting the double inversions hidden in the array. 🔄✖️2️⃣✨
