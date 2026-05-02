## 📈🖨️ _Recovering the Rising Chain: The Print Longest Increasing Subsequence Saga_

> \_"The Oracle had found the LENGTH of the LIS.
> But the King demanded more:
>
> **'Don't just tell me how long it is.
> SHOW me the actual subsequence.
> Print the elements.'**
>
> Finding the length was the DP.
> Printing the actual LIS was the **backtracking**.
>
> The Oracle realized she needed one extra tool:
>
> **A parent array.**
>
> For each element, she recorded WHO gave it the best chain.
> Which predecessor extended into the longest chain ending here?
>
> After filling the dp table, she found the element
> where the LIS ended — the index with the maximum dp value.
> Then she followed the parent chain backward,
> collecting elements one by one,
> until she reached the beginning of the chain."\_

---

This is the saga of **Print Longest Increasing Subsequence**.

Given an integer array `nums`:
-   Find and **print** the actual longest strictly increasing subsequence.

```
Input:  nums = [10, 9, 2, 5, 3, 7, 101, 18]
Output: [2, 5, 7, 101]  (or [2, 5, 7, 18] or [2, 3, 7, 101] — any valid LIS)

Input:  nums = [0, 1, 0, 3, 2, 3]
Output: [0, 1, 2, 3]

Input:  nums = [5, 4, 11, 1, 16, 8]
Output: [5, 11, 16]  (or [4, 11, 16])
```

---

## 🧠 The Two-Step Process

**Step 1 — Build the dp table (same as LIS tabulation).**
`dp[i]` = length of LIS ending at index `i`.

**Step 2 — Track parents and backtrack.**
`parent[i]` = index of the element that comes BEFORE `i` in the best chain.
Follow the parent chain from the best ending index back to the start.

---

## 🧠 What the Parent Array Records

When we compute `dp[i] = dp[j] + 1`, it means element `j`
is the best predecessor for element `i` in the LIS.

We record `parent[i] = j`.

If no valid predecessor exists (element starts its own chain),
`parent[i] = -1`.

After filling both arrays, find the index with the maximum `dp` value.
That's where the LIS ends. Follow parents backward to reconstruct it.

---

### 📜 The Scroll of the Recovered Rising Chain

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🖨️ Step 1 — Build dp and parent arrays

### Initialize

```cpp
vector<int> printLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp(n, 1);
    vector<int> parent(n, -1);
```

`dp[i] = 1` — every element alone is a chain of length 1.
`parent[i] = -1` — no predecessor yet. Each element starts its own chain.

---

### Fill the dp table with parent tracking

```cpp
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
```

Standard O(N²) LIS. For each `i`, check all `j < i`.

---

### If valid predecessor AND gives a longer chain

```cpp
            if (nums[j] < nums[i] && dp[j] + 1 > dp[i]) {
                dp[i] = dp[j] + 1;
                parent[i] = j;
            }
```

Two conditions must BOTH be true:
-   `nums[j] < nums[i]` — strictly increasing (valid predecessor).
-   `dp[j] + 1 > dp[i]` — this predecessor gives a LONGER chain than what we already have.

Why the second condition? Without it, we'd overwrite `parent[i]`
even when `j` gives the same length. We only update when we find
something strictly better.

When both hold: update `dp[i]` and record `parent[i] = j`.

> _"Element i found a better predecessor.
> Not just any valid one — the one that gives the LONGEST chain.
> It records this predecessor in its parent slot."_

---

```cpp
        }
    }
```

---

## 🖨️ Step 2 — Find where the LIS ends

```cpp
    int maxLen = 0, maxIdx = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] > maxLen) {
            maxLen = dp[i];
            maxIdx = i;
        }
    }
```

Scan the dp array for the maximum value.
`maxIdx` = the index where the LIS ends.

The LIS could end at ANY index — not necessarily the last one.

---

## 🖨️ Step 3 — Follow the parent chain backward

```cpp
    vector<int> lis;
    for (int i = maxIdx; i != -1; i = parent[i]) {
        lis.push_back(nums[i]);
    }
```

Start at `maxIdx`. Add `nums[maxIdx]` to the result.
Jump to `parent[maxIdx]`. Add that element.
Jump to its parent. Add that element.
Continue until `parent` is -1 (start of the chain).

> _"The Oracle follows the chain backward.
> Each parent points to the previous link.
> She collects each element until the chain begins."_

---

### Reverse to get the correct order

```cpp
    reverse(lis.begin(), lis.end());
    return lis;
}
```

Elements were collected backward (end to start). Reverse for correct order.

---

### 🎺 The Trial of the Recovered Rising Chain

```cpp
int main() {
    vector<int> n1 = {10, 9, 2, 5, 3, 7, 101, 18};
    vector<int> r1 = printLIS(n1);
    for (int x : r1) cout << x << " ";
    cout << endl; // expected: 2 5 7 101 (or another valid LIS)

    vector<int> n2 = {0, 1, 0, 3, 2, 3};
    vector<int> r2 = printLIS(n2);
    for (int x : r2) cout << x << " ";
    cout << endl; // expected: 0 1 2 3

    vector<int> n3 = {5, 4, 11, 1, 16, 8};
    vector<int> r3 = printLIS(n3);
    for (int x : r3) cout << x << " ";
    cout << endl; // expected: 5 11 16 (or 4 11 16)

    return 0;
}
```

---

**Full trace for nums = [10, 9, 2, 5, 3, 7, 101, 18]:**

**dp and parent arrays after filling:**

| i | nums[i] | dp[i] | parent[i] | Best chain ending here |
|---|---------|-------|-----------|----------------------|
| 0 | 10      | 1     | -1        | [10] |
| 1 | 9       | 1     | -1        | [9] |
| 2 | 2       | 1     | -1        | [2] |
| 3 | 5       | 2     | 2         | [2, 5] |
| 4 | 3       | 2     | 2         | [2, 3] |
| 5 | 7       | 3     | 3         | [2, 5, 7] |
| 6 | 101     | 4     | 5         | [2, 5, 7, 101] |
| 7 | 18      | 4     | 5         | [2, 5, 7, 18] |

**maxLen = 4, maxIdx = 6** (first index with dp=4).

**Backtracking from index 6:**
```
i = 6: nums[6] = 101. parent = 5.  lis = [101]
i = 5: nums[5] = 7.   parent = 3.  lis = [101, 7]
i = 3: nums[3] = 5.   parent = 2.  lis = [101, 7, 5]
i = 2: nums[2] = 2.   parent = -1. lis = [101, 7, 5, 2]
```

**Reverse: [2, 5, 7, 101]** ✓

---

**Trace for nums = [0, 1, 0, 3, 2, 3]:**

| i | nums[i] | dp[i] | parent[i] |
|---|---------|-------|-----------|
| 0 | 0       | 1     | -1        |
| 1 | 1       | 2     | 0         |
| 2 | 0       | 1     | -1        |
| 3 | 3       | 3     | 1         |
| 4 | 2       | 3     | 1         |
| 5 | 3       | 4     | 4         |

**maxIdx = 5. Backtrack: 5→4→1→0.**
Elements: nums[5]=3, nums[4]=2, nums[1]=1, nums[0]=0.
**Reverse: [0, 1, 2, 3]** ✓

---

## 🔍 Multiple Valid LIS

If `maxIdx = 6` gives [2, 5, 7, 101] and `maxIdx = 7` gives [2, 5, 7, 18],
both are valid LIS of length 4.

Our code picks the FIRST index with the maximum dp value.
Different tie-breaking gives different valid LIS.
All are correct — the problem asks for ANY one.

---

## 🔍 Print LIS vs Print LCS — Same Pattern

| Aspect | Print LCS | Print LIS |
|--------|-----------|-----------|
| DP table | 2D `dp[i][j]` | 1D `dp[i]` |
| Tracking | Follow diagonal/up/left | Follow `parent[i]` |
| Start backtrack | `dp[m][n]` (fixed cell) | `maxIdx` (scan for max) |
| Collect order | Backward → reverse | Backward → reverse |

Same idea: fill the DP, track decisions, backtrack to recover.

---

## 🔍 Why Not Just Sort and Compare?

Sorting gives the longest non-decreasing subsequence, not LIS.
And sorting destroys the original order — subsequences must
preserve the original positions.

The parent array preserves the exact indices used.

---

### 🧠 Memory of the Recovered Rising Chain Law

-   **Step 1:** fill `dp[i]` and `parent[i]` with standard O(N²) LIS
-   **Step 2:** find `maxIdx` = index with maximum `dp` value
-   **Step 3:** follow `parent` chain from `maxIdx` to -1, collect elements
-   **Step 4:** reverse the collected elements
-   **Update condition:** `nums[j] < nums[i] AND dp[j]+1 > dp[i]` (both required)
-   **Multiple valid LIS may exist** — this returns one of them
-   **Time:** O(N²) + O(N). **Space:** O(N).

Thus is remembered the saga of **Print Longest Increasing Subsequence**,
where the Oracle did not just measure the rising chain
but walked the parent array backward --
from the peak where the longest chain ended,
following each link to its predecessor,
collecting elements in reverse --
until the full chain was spelled out,
the longest rising sequence made visible. 📈🖨️✨
