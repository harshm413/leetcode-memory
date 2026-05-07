## 🪟🎨 _The Exact Palette: The Subarrays with K Different Integers Saga_

> \_"The Oracle was given an array and a number K.
>
> She was commanded:
>
> **'Count subarrays with EXACTLY K different integers.'**
>
> The Oracle recognized the pattern:
>
> **Same atMost trick. Different tracking.**
>
> 'Exactly K distinct' is hard for a sliding window.
> 'At most K distinct' is easy — shrink when distinct > K.
>
> **exactly(K) = atMost(K) - atMost(K-1)**
>
> The atMost function uses a hashmap to track frequencies.
> `map.size()` = number of distinct elements in the window.
> Shrink when `map.size() > K`."\_

---

This is the saga of **Subarrays with K Different Integers (LeetCode 992)**.

Given an array `nums` and integer `k`:
-   Count subarrays with exactly `k` different integers.

```
Input:  nums = [1, 2, 1, 2, 3], k = 2
Output: 7   ([1,2], [2,1], [1,2], [2,1,2], [1,2,1], [1,2,1,2], [2,3])

Input:  nums = [1, 2, 1, 3, 4], k = 3
Output: 3   ([1,2,1,3], [2,1,3], [1,3,4])
```

---

## 🧠 Why Sliding Window Can't Do "Exactly K Distinct" Directly

When the window has exactly K distinct elements:
-   Expand right → might add a new distinct (now K+1, invalid).
-   Shrink left → might remove a distinct (now K-1, also invalid).
-   Stay → found one, but how to count all valid subarrays?

The window can't decide when to shrink. Ambiguous.

**The fix: atMost(K) - atMost(K-1).**

`atMost(K)` = subarrays with ≤ K distinct. Easy — shrink when > K.
`atMost(K-1)` = subarrays with ≤ K-1 distinct.
Difference = subarrays with EXACTLY K distinct.

---

## 🧠 The atMost Function — Hashmap for Distinct Count

Track element frequencies in a hashmap.
`map.size()` = number of distinct elements in the window.

**Expand:** add `nums[right]` to the map. `freq[nums[right]]++`.
**Shrink:** when `map.size() > K`, remove from left.
`freq[nums[left]]--`. If count hits 0 → erase from map.
**Count:** `right - left + 1` valid subarrays at each step.

Same as Fruit Into Baskets — but with variable K instead of fixed 2.

---

### 📜 The Scroll of the Exact Palette

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🎨 The atMost Helper

```cpp
int atMost(vector<int>& nums, int k) {
    if (k < 0) return 0;
```

---

```cpp
    unordered_map<int, int> freq;
    int left = 0, count = 0;
```

`freq` maps each value to its count in the current window.
`freq.size()` = distinct elements in the window.

---

```cpp
    for (int right = 0; right < nums.size(); right++) {
        freq[nums[right]]++;
```

Add the new element. If it's new, `freq.size()` increases.

---

### Shrink when too many distinct elements

```cpp
        while (freq.size() > k) {
            freq[nums[left]]--;
            if (freq[nums[left]] == 0) {
                freq.erase(nums[left]);
            }
            left++;
        }
```

More than K distinct in the window. Shrink from left.
Decrement the leaving element's count.
If count hits 0 — that element is gone from the window. Erase it.
`freq.size()` decreases.

> _"A color leaves the palette.
> Its count drops. If it reaches zero — it's erased entirely.
> The palette shrinks back to K colors."_

---

### Count valid subarrays

```cpp
        count += (right - left + 1);
    }
    return count;
}
```

---

## 🎨 The Main Function

```cpp
int subarraysWithKDistinct(vector<int>& nums, int k) {
    return atMost(nums, k) - atMost(nums, k - 1);
}
```

---

### 🎺 The Trial of the Exact Palette

```cpp
int main() {
    vector<int> n1 = {1, 2, 1, 2, 3};
    cout << subarraysWithKDistinct(n1, 2) << endl; // expected: 7

    vector<int> n2 = {1, 2, 1, 3, 4};
    cout << subarraysWithKDistinct(n2, 3) << endl; // expected: 3

    return 0;
}
```

---

**Trace for nums = [1, 2, 1, 2, 3], k = 2:**

**atMost(2):**

| right | nums[r] | freq | size | size>2? | left | count += | total |
|-------|---------|------|------|---------|------|----------|-------|
| 0 | 1 | {1:1} | 1 | No | 0 | 1 | 1 |
| 1 | 2 | {1:1,2:1} | 2 | No | 0 | 2 | 3 |
| 2 | 1 | {1:2,2:1} | 2 | No | 0 | 3 | 6 |
| 3 | 2 | {1:2,2:2} | 2 | No | 0 | 4 | 10 |
| 4 | 3 | {1:2,2:2,3:1} | 3 | **Yes** | | | |
|   |   | remove 1: {1:1,2:2,3:1}. size=3. left=1 | | | | |
|   |   | remove 2: {1:1,2:1,3:1}. size=3. left=2 | | | | |
|   |   | remove 1: {2:1,3:1}. size=2. ✓ left=3 | | 3 | 2 | 12 |

**atMost(2) = 12**

---

**atMost(1):**

| right | nums[r] | freq | size | size>1? | left | count += | total |
|-------|---------|------|------|---------|------|----------|-------|
| 0 | 1 | {1:1} | 1 | No | 0 | 1 | 1 |
| 1 | 2 | {1:1,2:1} | 2 | **Yes** → shrink | | | |
|   |   | remove 1: {2:1}. left=1 | 1 | No | 1 | 1 | 2 |
| 2 | 1 | {2:1,1:1} | 2 | **Yes** → shrink | | | |
|   |   | remove 2: {1:1}. left=2 | 2 | No | 2 | 1 | 3 |
| 3 | 2 | {1:1,2:1} | 2 | **Yes** → shrink | | | |
|   |   | remove 1: {2:1}. left=3 | 3 | No | 3 | 1 | 4 |
| 4 | 3 | {2:1,3:1} | 2 | **Yes** → shrink | | | |
|   |   | remove 2: {3:1}. left=4 | 4 | No | 4 | 1 | 5 |

**atMost(1) = 5**

---

**Answer: 12 - 5 = 7** ✓

The 7 subarrays with exactly 2 distinct:
[1,2], [1,2,1], [1,2,1,2], [2,1], [2,1,2], [1,2] (indices 2-3), [2,3].

---

## 🔍 This vs Fruit Into Baskets

| Aspect | Fruit Into Baskets (LC 904) | This problem (LC 992) |
|--------|---------------------------|----------------------|
| Goal | LONGEST with ≤ 2 distinct | COUNT with exactly K distinct |
| K value | Fixed at 2 | Variable |
| Tracking | `maxLen = max(...)` | `count += (right-left+1)` |
| atMost trick | Not needed (just find longest) | **Required** (exactly = atMost - atMost) |

Fruit Into Baskets finds the longest window with ≤ 2 distinct.
This problem counts ALL windows with exactly K distinct.
Same hashmap window. Different goal.

---

## 🔍 The Complete atMost Family

| Problem | What atMost tracks | Budget |
|---------|-------------------|--------|
| Binary Subarrays With Sum | sum | ≤ goal |
| Nice Subarrays | odd count | ≤ k |
| **K Different Integers (this)** | **distinct count (map.size())** | **≤ k** |
| Max Consecutive Ones III | zero count | ≤ k |

All use `exactly = atMost(K) - atMost(K-1)`.
The only difference: what you track and how you shrink.

---

## 🔍 Why Erase at Count 0?

```cpp
if (freq[nums[left]] == 0) freq.erase(nums[left]);
```

Without erasing, `freq.size()` would still count that key.
The `size > k` check would be wrong.
A zero-count entry is a ghost — it must be removed.

---

### 🧠 Memory of the Exact Palette Law

-   **`exactly(K) = atMost(K) - atMost(K-1)`**
-   **atMost(K):** hashmap tracks frequencies, `map.size()` = distinct count
-   **Shrink when:** `freq.size() > K`
-   **Erase at 0:** critical for correct `map.size()`
-   **Count per step:** `right - left + 1`
-   **Same as Fruit Into Baskets** but counts instead of finding longest
-   **Time:** O(N). **Space:** O(K) for the hashmap.

Thus is remembered the saga of **Subarrays with K Different Integers**,
where the Oracle couldn't count "exactly K" directly —
so she counted "at most K" and "at most K-1" and subtracted —
the hashmap tracking distinct colors in the window,
erasing ghosts when their count hit zero,
shrinking when the palette grew too large —
until every subarray with exactly K colors was tallied. 🪟🎨✨
