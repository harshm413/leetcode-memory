## 📈📉 _The Mountain Sequence: The Longest Bitonic Subsequence Saga_

> \_"The Oracle was given an array of numbers.
>
> She was commanded:
>
> **'Find the longest subsequence that first INCREASES
> then DECREASES — a mountain shape.'**
>
> A bitonic sequence rises to a peak, then falls.
> `[1, 3, 5, 4, 2]` — rises to 5, then falls. Bitonic ✓.
> `[1, 2, 3, 4, 5]` — only rises, never falls. NOT bitonic ✗.
> `[5, 4, 3, 2, 1]` — only falls, never rises. NOT bitonic ✗.
>
> The Oracle saw two LIS problems hiding inside:
>
> **LIS from the LEFT = how long can the sequence rise TO each element?**
> **LIS from the RIGHT = how long can the sequence rise FROM each element?**
> (Rising from the right = falling from the left = the decreasing part.)
>
> For each element as the PEAK:
> `bitonic length = LIS_left[i] + LIS_right[i] - 1`
> (subtract 1 because the peak is counted in both).
>
> The answer = the maximum bitonic length across all peaks.
> But ONLY if both sides have length ≥ 2
> (a valid bitonic must actually rise AND fall)."\_

---

This is the saga of **Longest Bitonic Subsequence**.

Given an integer array `nums`:
-   Find the length of the longest **bitonic** subsequence.
-   A bitonic subsequence strictly increases then strictly decreases.
-   The increasing and decreasing parts must both be non-empty
    (pure increasing or pure decreasing is NOT bitonic).

```
Input:  nums = [1, 11, 2, 10, 4, 5, 2, 1]
Output: 6   ([1, 2, 10, 4, 2, 1] — rises to 10, falls to 1)

Input:  nums = [12, 11, 40, 5, 3, 1]
Output: 5   ([12, 40, 5, 3, 1] — rises to 40, falls to 1)

Input:  nums = [80, 60, 30, 40, 20, 10]
Output: 5   ([80, 60, 30, 20, 10] or [80, 60, 40, 20, 10])
```

Wait — `[80, 60, 30, 20, 10]` is purely decreasing, not bitonic.
Let me reconsider: `[60, 30, 40, 20, 10]` — rises? No, 60→30 falls.
Actually for this input: `[80, 60, 30, 20, 10]` has LIS_left=[1,1,1,2,1,1]
and LIS_right varies. Let me use cleaner examples.

```
Input:  nums = [1, 11, 2, 10, 4, 5, 2, 1]
Output: 6

Input:  nums = [12, 11, 40, 5, 3, 1]
Output: 5

Input:  nums = [1, 2, 3, 4, 5]
Output: 0   (only increasing — no bitonic exists)
```

---

## 🧠 The Two-Pass Insight

**Pass 1 — LIS from the LEFT:**
Standard LIS. `lis[i]` = length of longest increasing subsequence
ending at index `i`, considering elements from the left.

**Pass 2 — LIS from the RIGHT:**
Run LIS backward. `lds[i]` = length of longest increasing subsequence
ending at index `i`, considering elements from the RIGHT.

LIS from the right is the same as the Longest DECREASING Subsequence
from the left. It tells us how long the falling part can be.

**Combine:**
For each index `i` as the peak:
```
bitonic[i] = lis[i] + lds[i] - 1
```
Subtract 1 because element `i` is counted in both `lis` and `lds`.

**Valid bitonic:** both `lis[i] >= 2` AND `lds[i] >= 2`.
The peak must have elements rising TO it AND falling FROM it.
A pure increasing or pure decreasing sequence is NOT bitonic.

---

### 📜 The Scroll of the Mountain Sequence

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📈 Pass 1 — LIS from the Left

```cpp
int longestBitonicSubsequence(vector<int>& nums) {
    int n = nums.size();
    vector<int> lis(n, 1);
```

Standard LIS. Every element starts as a chain of length 1.

---

```cpp
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                lis[i] = max(lis[i], lis[j] + 1);
            }
        }
    }
```

For each `i`, find the longest increasing chain ending at `i`.
This is the RISING part of the mountain up to each element.

> _"How high can the mountain rise to reach this element?
> That's what lis[i] measures."_

---

## 📉 Pass 2 — LIS from the Right (= LDS from the Left)

```cpp
    vector<int> lds(n, 1);
```

---

```cpp
    for (int i = n - 2; i >= 0; i--) {
        for (int j = n - 1; j > i; j--) {
            if (nums[j] < nums[i]) {
                lds[i] = max(lds[i], lds[j] + 1);
            }
        }
    }
```

Same as LIS but scanning RIGHT to LEFT.
`lds[i]` = longest DECREASING subsequence starting at index `i`.

Why `nums[j] < nums[i]`? We're looking at elements AFTER `i`
that are SMALLER. That's the falling part of the mountain.

Equivalently: this is LIS on the reversed array.

> _"How far can the mountain fall from this element?
> That's what lds[i] measures."_

---

## 🏔️ Combine — Find the Best Peak

```cpp
    int ans = 0;
    for (int i = 0; i < n; i++) {
        if (lis[i] >= 2 && lds[i] >= 2) {
            ans = max(ans, lis[i] + lds[i] - 1);
        }
    }
    return ans;
}
```

For each element as the peak:
-   `lis[i] >= 2`: at least one element rises TO this peak.
-   `lds[i] >= 2`: at least one element falls FROM this peak.
-   Both must hold — otherwise it's not a mountain.

`lis[i] + lds[i] - 1`: total bitonic length (peak counted once).

> _"A mountain needs both a climb and a descent.
> A flat plateau or a one-sided slope is not a mountain."_

---

### 🎺 The Trial of the Mountain Sequence

```cpp
int main() {
    vector<int> n1 = {1, 11, 2, 10, 4, 5, 2, 1};
    cout << longestBitonicSubsequence(n1) << endl; // expected: 6

    vector<int> n2 = {12, 11, 40, 5, 3, 1};
    cout << longestBitonicSubsequence(n2) << endl; // expected: 5

    vector<int> n3 = {1, 2, 3, 4, 5};
    cout << longestBitonicSubsequence(n3) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for nums = [1, 11, 2, 10, 4, 5, 2, 1]:**

**LIS (left to right):**

| i | nums[i] | lis[i] | Best increasing chain ending here |
|---|---------|--------|----------------------------------|
| 0 | 1       | 1      | [1] |
| 1 | 11      | 2      | [1, 11] |
| 2 | 2       | 2      | [1, 2] |
| 3 | 10      | 3      | [1, 2, 10] |
| 4 | 4       | 3      | [1, 2, 4] |
| 5 | 5       | 4      | [1, 2, 4, 5] |
| 6 | 2       | 2      | [1, 2] |
| 7 | 1       | 1      | [1] |

**LDS (right to left):**

| i | nums[i] | lds[i] | Best decreasing chain starting here |
|---|---------|--------|-------------------------------------|
| 7 | 1       | 1      | [1] |
| 6 | 2       | 2      | [2, 1] |
| 5 | 5       | 3      | [5, 2, 1] |
| 4 | 4       | 3      | [4, 2, 1] |
| 3 | 10      | 4      | [10, 4, 2, 1] or [10, 5, 2, 1] |
| 2 | 2       | 2      | [2, 1] |
| 1 | 11      | 5      | [11, 10, 4, 2, 1] |
| 0 | 1       | 1      | [1] |

**Combine:**

| i | nums[i] | lis[i] | lds[i] | Valid? | bitonic |
|---|---------|--------|--------|--------|---------|
| 0 | 1       | 1      | 1      | ✗ (lis<2) | — |
| 1 | 11      | 2      | 5      | ✓      | 2+5-1 = 6 |
| 2 | 2       | 2      | 2      | ✓      | 2+2-1 = 3 |
| 3 | 10      | 3      | 4      | ✓      | 3+4-1 = **6** |
| 4 | 4       | 3      | 3      | ✓      | 3+3-1 = 5 |
| 5 | 5       | 4      | 3      | ✓      | 4+3-1 = **6** |
| 6 | 2       | 2      | 2      | ✓      | 2+2-1 = 3 |
| 7 | 1       | 1      | 1      | ✗ (lis<2) | — |

**Answer: max = 6** ✓

Multiple peaks give 6: index 1 (peak 11), index 3 (peak 10), index 5 (peak 5).
One valid bitonic: [1, 2, 10, 4, 2, 1] (rises to 10, falls to 1).

---

**Trace for nums = [12, 11, 40, 5, 3, 1]:**

| i | nums[i] | lis[i] | lds[i] | bitonic |
|---|---------|--------|--------|---------|
| 0 | 12      | 1      | 2      | — (lis<2) |
| 1 | 11      | 1      | 2      | — (lis<2) |
| 2 | 40      | 2      | 4      | 2+4-1 = **5** |
| 3 | 5       | 1      | 3      | — (lis<2) |
| 4 | 3       | 1      | 2      | — (lis<2) |
| 5 | 1       | 1      | 1      | — |

**Answer: 5** ✓ (chain: [12, 40, 5, 3, 1] — rises to 40, falls to 1)

---

**Trace for nums = [1, 2, 3, 4, 5]:**

lis = [1, 2, 3, 4, 5]. lds = [1, 1, 1, 1, 1].
No element has lds ≥ 2. No valid peak exists.

**Answer: 0** ✓ (purely increasing — not bitonic)

---

## 🔍 Edge Cases

**Purely increasing:** lds is all 1s. No valid peak. Answer = 0.
**Purely decreasing:** lis is all 1s. No valid peak. Answer = 0.
**Single element:** both lis and lds = 1. Answer = 0.
**Two elements:** one rises, one falls. lis=[1,2], lds=[2,1].
Peak at index 0: lis=1 (< 2). Peak at index 1: lds=1 (< 2). Answer = 0.
Minimum valid bitonic needs at least 3 elements.

---

## 🔍 The LIS Family -- Complete View

| Problem | What to compute | Combine |
|---------|----------------|---------|
| LIS | lis[i] from left | max(lis) |
| LDS | lds[i] from right | max(lds) |
| **Bitonic** | **Both lis + lds** | **max(lis[i]+lds[i]-1) where both ≥ 2** |
| Print LIS | lis + parent | Backtrack |
| Divisible Subset | lis with % | Sort + backtrack |
| String Chain | lis with predecessor | Sort by length |
| Number of LIS | lis + count | Sum counts at max length |

---

### 🧠 Memory of the Mountain Sequence Law

-   **Two passes:** LIS from left (`lis[i]`), LIS from right (`lds[i]`)
-   **Combine:** `lis[i] + lds[i] - 1` for each peak `i`
-   **Valid peak:** both `lis[i] >= 2` AND `lds[i] >= 2`
-   **Pure increasing or decreasing = NOT bitonic** → answer 0
-   **Minimum bitonic length:** 3 (one rise, peak, one fall)
-   **Time:** O(N²) + O(N²) = O(N²). **Space:** O(N).

Thus is remembered the saga of **Longest Bitonic Subsequence**,
where the Oracle computed two LIS arrays --
one measuring how high the mountain could rise to each element,
the other measuring how far it could fall --
and at each potential peak, she combined both measurements,
subtracting one for the shared summit,
finding the tallest mountain hidden in the array. 📈📉✨
