## 🔁👑 _The Turning of Lexicographic Fate: The Next Permutation Saga_

> \_"In the Kingdom of Arrangements,
> numbers stood in a precise order —
> one of many possible destinies.
>
> The Oracle was commanded:
>
> **'Transform this arrangement
> into the very next one
> in lexicographic order.
> No new realm may be created.
> No list of all destinies may be written.
> Only a subtle shift —
> the smallest change that moves the sequence
> just one step forward in fate.'**
>
> The Oracle studied the arrangement.
>
> She knew that lexicographic order
> was like dictionary order for numbers —
> `[1,2,3]` came before `[1,3,2]`,
> which came before `[2,1,3]`, and so on.
>
> To find the NEXT permutation,
> she needed to make the smallest possible increase.
>
> She devised a three-step ritual:
>
> **Step One — Find the Pivot:**
> Walk backward from the end.
> Find the first position where `nums[i] < nums[i+1]`.
> This is the rightmost place where the sequence can still grow.
> Everything to its right is already in descending order —
> the largest possible suffix.
>
> **Step Two — Find the Successor:**
> From the right end, find the smallest number
> that is strictly greater than the pivot.
> Swap them. The sequence has now taken one step forward.
>
> **Step Three — Reverse the Suffix:**
> The suffix to the right of the pivot is still descending.
> Reverse it to make it ascending —
> the smallest possible suffix for this new prefix.
>
> If no pivot exists — the entire array is descending —
> the sequence is at its final destiny.
> Reverse the whole array to return to the very first permutation."\_

---

This is the saga of **Next Permutation**.

You are given an array `nums` representing a permutation of integers.

Your task:

-   Rearrange it into the **next lexicographically greater permutation**.
-   If no such permutation exists (array is fully descending),
    rearrange into the **lowest possible order** (ascending).
-   Do everything **in-place** with O(1) extra space.

```
Input:  [1, 2, 3]   →  Output: [1, 3, 2]
Input:  [3, 2, 1]   →  Output: [1, 2, 3]
Input:  [1, 1, 5]   →  Output: [1, 5, 1]
Input:  [2, 3, 1]   →  Output: [3, 1, 2]
```

---

## 🧠 The Oracle's Core Insight — The Descending Suffix

The key observation:

> **A suffix that is entirely in descending order
> is already at its maximum permutation.
> No rearrangement within it can make it larger.**

For example, in `[1, 3, 5, 4, 2]`:
the suffix `[5, 4, 2]` is descending — it's maxed out.
The pivot is `3` (at index 1) — the first element from the right
that is smaller than its neighbor.

To advance the permutation:
1. Replace the pivot with the next larger value from the suffix.
2. Make the suffix as small as possible (reverse it to ascending).

This gives the smallest increase — exactly the next permutation.

---

### 📜 The Scroll of Lexicographic Destiny

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle's Three-Step Ritual

```cpp
void nextPermutation(vector<int>& nums) {
    int n = nums.size();
```

The Oracle surveyed the arrangement — `n` elements awaiting transformation.

---

### 🔍 Step 1 — Find the Pivot

_The rightmost position where the sequence can still grow_

```cpp
    int i = n - 2;
    while (i >= 0 && nums[i] >= nums[i + 1]) {
        i--;
    }
```

The Oracle walked backward from the second-to-last element.

She searched for the first position `i` where `nums[i] < nums[i+1]` —
the point where the descending suffix begins.

Everything to the right of `i` is in non-increasing order —
already the largest possible arrangement for that suffix.

If `i` reached `-1` — the entire array was descending.
No next permutation existed.
The array must wrap around to the smallest permutation.

> _"The pivot is where fate can still turn.
> Beyond it, destiny has already peaked."_

---

### 🔄 Step 2 — Find the Just-Larger Successor and Swap

_The smallest number in the suffix that is greater than the pivot_

```cpp
    if (i >= 0) {
        int j = n - 1;
        while (nums[j] <= nums[i]) {
            j--;
        }
        swap(nums[i], nums[j]);
    }
```

If a pivot was found (`i >= 0`),
the Oracle walked backward from the end again —
searching for the rightmost element `nums[j]`
that was strictly greater than `nums[i]`.

Because the suffix is in descending order,
the first such element from the right
is guaranteed to be the **smallest** value greater than the pivot.

The swap placed this successor at the pivot position —
advancing the prefix by the smallest possible step.

The suffix remained in descending order after the swap —
because we swapped with the rightmost element
that was just barely larger than the pivot.

---

### 🔁 Step 3 — Reverse the Suffix

_Turn the descending suffix into ascending — the smallest possible tail_

```cpp
    reverse(nums.begin() + i + 1, nums.end());
}
```

The suffix to the right of position `i`
was still in descending order (even after the swap).

Reversing it turned it into ascending order —
the smallest possible arrangement for that suffix.

This completed the transformation:
the prefix had been incremented by the smallest step,
and the suffix had been minimized.

The result was the **next lexicographic permutation**.

If no pivot existed (`i == -1`),
this reversed the entire array —
transforming the last permutation `[3,2,1]`
into the first permutation `[1,2,3]`.

---

### 🎺 The Trial of Changing Fate

```cpp
int main() {
    vector<int> a = {1, 2, 3};
    nextPermutation(a);
    for (int x : a) cout << x << " ";
    cout << endl; // expected: 1 3 2

    vector<int> b = {3, 2, 1};
    nextPermutation(b);
    for (int x : b) cout << x << " ";
    cout << endl; // expected: 1 2 3

    vector<int> c = {2, 3, 1};
    nextPermutation(c);
    for (int x : c) cout << x << " ";
    cout << endl; // expected: 3 1 2

    return 0;
}
```

---

**Full trace for `[1, 3, 5, 4, 2]`:**

**Step 1 — Find pivot:**
Walk backward: `nums[3]=4 >= nums[4]=2` → continue.
`nums[2]=5 >= nums[3]=4` → continue.
`nums[1]=3 < nums[2]=5` → **pivot found at i=1**, value `3`.

**Step 2 — Find successor:**
Walk backward: `nums[4]=2 <= 3` → skip.
`nums[3]=4 > 3` → **successor found at j=3**, value `4`.
Swap: `nums[1]` and `nums[3]` → array becomes `[1, 4, 5, 3, 2]`.

**Step 3 — Reverse suffix after i=1:**
Suffix `[5, 3, 2]` → reversed to `[2, 3, 5]`.
Array becomes `[1, 4, 2, 3, 5]`.

**Answer: [1, 4, 2, 3, 5]** ✓

---

**Full trace for `[3, 2, 1]`:**

**Step 1 — Find pivot:**
`nums[1]=2 >= nums[2]=1` → continue.
`nums[0]=3 >= nums[1]=2` → continue.
`i = -1` → **no pivot found**. Entire array is descending.

**Step 2 — Skipped** (i < 0).

**Step 3 — Reverse entire array:**
`[3, 2, 1]` → `[1, 2, 3]`.

**Answer: [1, 2, 3]** ✓ (wrapped to first permutation)

---

**Full trace for `[2, 3, 1]`:**

**Step 1 — Find pivot:**
`nums[1]=3 >= nums[2]=1` → continue.
`nums[0]=2 < nums[1]=3` → **pivot at i=0**, value `2`.

**Step 2 — Find successor:**
`nums[2]=1 <= 2` → skip.
`nums[1]=3 > 2` → **successor at j=1**, value `3`.
Swap: `[3, 2, 1]`.

**Step 3 — Reverse suffix after i=0:**
Suffix `[2, 1]` → reversed to `[1, 2]`.
Array becomes `[3, 1, 2]`.

**Answer: [3, 1, 2]** ✓

---

### 🧠 Memory of the Lexicographic Fate Law

-   **Step 1 — Find Pivot:** walk backward, find first `i` where `nums[i] < nums[i+1]`
    -   Everything right of `i` is descending (maxed out suffix)
    -   If `i == -1` → array is fully descending → wrap to first permutation
-   **Step 2 — Find Successor:** walk backward from end, find first `j` where `nums[j] > nums[i]`
    -   This is the smallest value in the suffix greater than the pivot
    -   Swap `nums[i]` and `nums[j]`
-   **Step 3 — Reverse Suffix:** reverse everything after index `i`
    -   Turns descending suffix into ascending → smallest possible tail
    -   If no pivot existed, reverses entire array
-   **Time:** O(N) — at most three linear scans
-   **Space:** O(1) — all operations are in-place swaps and reversal

Thus is remembered the saga of **Next Permutation**,
where the Oracle did not leap recklessly through all possibilities
but instead found the precise point where fate could still turn —
the pivot where the descending suffix began —
swapped it with the smallest worthy successor,
reversed the suffix into its humblest form,
and nudged destiny forward
by the smallest, most elegant change. 🔁👑✨
