## 📈🔢 _Counting the Rising Chains: The Number of Longest Increasing Subsequences Saga_

> \_"The Oracle had found the LENGTH of the LIS.
>
> Now the King demanded:
>
> **'How MANY longest increasing subsequences are there?
> Not just the length — the COUNT.'**
>
> The Oracle realized she needed a second array.
>
> `dp[i]` = length of LIS ending at index `i`. (Already known.)
> `count[i]` = NUMBER of LIS of that length ending at index `i`. (New.)
>
> When element `j` extends to element `i`:
>
> **If `dp[j] + 1 > dp[i]`:** found a LONGER chain.
> Reset: `dp[i] = dp[j] + 1`, `count[i] = count[j]`.
> All previous chains are obsolete. Start fresh with j's count.
>
> **If `dp[j] + 1 == dp[i]`:** found ANOTHER chain of the SAME length.
> Add: `count[i] += count[j]`.
> More ways to reach the same length. Accumulate.
>
> The answer = sum of `count[i]` for all `i` where `dp[i]` equals
> the maximum LIS length."\_

---

This is the saga of **Number of Longest Increasing Subsequences (LeetCode 673)**.

Given an integer array `nums`:
-   Find the **number** of longest strictly increasing subsequences.

```
Input:  nums = [1,3,5,4,7]
Output: 2   (LIS length=4: [1,3,5,7] and [1,3,4,7])

Input:  nums = [2,2,2,2,2]
Output: 5   (LIS length=1: each element alone)

Input:  nums = [1,2,4,3,5,4,7,2]
Output: 3
```

---

## 🧠 The Two-Array Insight

Standard LIS uses one array: `dp[i]` = length of LIS ending at `i`.

This problem needs a second array: `count[i]` = how many LIS
of length `dp[i]` end at index `i`.

**Three cases when checking predecessor `j` for element `i`:**

**Case 1: `dp[j] + 1 > dp[i]` — found a LONGER chain.**

A new best. Everything we knew about `dp[i]` is obsolete.
Reset `dp[i]` to this new length.
Reset `count[i]` to `count[j]` — the number of ways to reach `j`
is now the number of ways to reach `i` (through `j`).

> _"A longer chain was found. The old count is thrown away.
> The new count inherits everything from the predecessor."_

**Case 2: `dp[j] + 1 == dp[i]` — found ANOTHER chain of SAME length.**

We already have chains of this length ending at `i`.
Now we found MORE chains (through `j`) of the same length.
Add `count[j]` to `count[i]`.

> _"Another path to the same peak.
> The count grows. More ways to reach the same length."_

**Case 3: `dp[j] + 1 < dp[i]` — shorter chain. Ignore.**

This predecessor gives a shorter chain than what we already have.
Skip it.

---

### 📜 The Scroll of the Counted Rising Chains

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔢 Initialize Both Arrays

```cpp
int findNumberOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp(n, 1);
    vector<int> count(n, 1);
```

`dp[i] = 1` — every element alone is a chain of length 1.
`count[i] = 1` — there's exactly ONE way to have a chain of length 1
ending at `i`: the element itself.

---

## 🔢 Fill Both Arrays

```cpp
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
```

Standard LIS outer/inner loop. Only proceed if `j` is a valid predecessor.

---

### Case 1: Longer chain found — RESET

```cpp
                if (dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                    count[i] = count[j];
                }
```

New best length. Reset count to `count[j]`.

Why `count[j]` and not 1? Because there might be MULTIPLE chains
of length `dp[j]` ending at `j`. Each of those chains can extend to `i`.
So the number of chains of the new length ending at `i` = `count[j]`.

> _"The old chains are obsolete. The new count starts fresh —
> inheriting all the ways the predecessor could be reached."_

---

### Case 2: Same length — ACCUMULATE

```cpp
                else if (dp[j] + 1 == dp[i]) {
                    count[i] += count[j];
                }
```

Same length as what we already have. More ways to get here.
Add `count[j]` to the running total.

> _"Another predecessor offers the same length.
> The count grows — more paths to the same destination."_

---

```cpp
            }
        }
    }
```

---

## 🔢 Sum Counts at the Maximum Length

```cpp
    int maxLen = *max_element(dp.begin(), dp.end());
    int result = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] == maxLen) {
            result += count[i];
        }
    }
    return result;
}
```

Find the maximum LIS length.
Sum `count[i]` for every index where `dp[i]` equals that maximum.

The LIS can end at multiple indices. Each contributes its count.

> _"The longest chains might end at different places.
> Sum all their counts — that's the total number of LIS."_

---

### 🎺 The Trial of the Counted Rising Chains

```cpp
int main() {
    vector<int> n1 = {1, 3, 5, 4, 7};
    cout << findNumberOfLIS(n1) << endl; // expected: 2

    vector<int> n2 = {2, 2, 2, 2, 2};
    cout << findNumberOfLIS(n2) << endl; // expected: 5

    vector<int> n3 = {1, 2, 4, 3, 5, 4, 7, 2};
    cout << findNumberOfLIS(n3) << endl; // expected: 3

    return 0;
}
```

---

**Full trace for nums = [1, 3, 5, 4, 7]:**

| i | nums[i] | Check j < i | dp[i] | count[i] | Reasoning |
|---|---------|-------------|-------|----------|-----------|
| 0 | 1       | —           | 1     | 1        | [1] — one way |
| 1 | 3       | j=0: 1<3 ✓, dp[0]+1=2 > 1 → RESET | **2** | **1** | [1,3] — one way |
| 2 | 5       | j=0: 2>1 RESET dp=2 cnt=1. j=1: 3<5, dp[1]+1=3 > 2 RESET | **3** | **1** | [1,3,5] — one way |
| 3 | 4       | j=0: 1<4, dp=2 cnt=1. j=1: 3<4, dp[1]+1=3 > 2 RESET dp=3 cnt=1. j=2: 5>4 ✗ | **3** | **1** | [1,3,4] — one way |
| 4 | 7       | j=0: dp=2 cnt=1. j=1: dp=3 cnt=1. j=2: 5<7, dp[2]+1=4 > 3 RESET dp=4 cnt=1. j=3: 4<7, dp[3]+1=4 == 4 ACCUMULATE cnt=1+1=2 | **4** | **2** | [1,3,5,7] and [1,3,4,7] |

**maxLen = 4. Only dp[4] = 4. count[4] = 2.**
**Answer: 2** ✓

The key moment: at index 4 (value 7), both index 2 (value 5) and index 3 (value 4)
offer chains of length 3. Extending either gives length 4.
So count[4] = count[2] + count[3] = 1 + 1 = 2.

---

**Trace for nums = [2, 2, 2, 2, 2]:**

No element is strictly less than another (all equal).
Every dp[i] = 1, every count[i] = 1.
maxLen = 1. Sum of all counts = 5.

**Answer: 5** ✓ (each element alone is an LIS of length 1)

---

**Trace for nums = [1, 2, 4, 3, 5, 4, 7, 2]:**

| i | nums[i] | dp[i] | count[i] | Best chains ending here |
|---|---------|-------|----------|------------------------|
| 0 | 1       | 1     | 1        | [1] |
| 1 | 2       | 2     | 1        | [1,2] |
| 2 | 4       | 3     | 1        | [1,2,4] |
| 3 | 3       | 3     | 1        | [1,2,3] |
| 4 | 5       | 4     | 2        | [1,2,4,5] and [1,2,3,5] |
| 5 | 4       | 4     | 1        | [1,2,3,4] |
| 6 | 7       | 5     | 3        | extends from i=4 (cnt 2) and i=5 (cnt 1) → 2+1=3 |
| 7 | 2       | 2     | 1        | [1,2] |

**maxLen = 5. Only dp[6] = 5. count[6] = 3.**
**Answer: 3** ✓

The three LIS of length 5:
[1,2,4,5,7], [1,2,3,5,7], [1,2,3,4,7].

---

## 🔍 The Reset vs Accumulate Pattern

This is the core insight of the problem:

```
if (dp[j] + 1 > dp[i]):     ← LONGER: reset count
    dp[i] = dp[j] + 1
    count[i] = count[j]

if (dp[j] + 1 == dp[i]):    ← SAME: accumulate count
    count[i] += count[j]
```

**Reset** = found a new best. Old ways are irrelevant.
**Accumulate** = found more ways to the same best. Add them up.

This pattern appears whenever you need to COUNT optimal solutions,
not just find the optimal VALUE.

---

## 🔍 Why count[j] and Not Just +1?

When we reset `count[i] = count[j]`, why not `count[i] = 1`?

Because `j` itself might be reachable in MULTIPLE ways.
If there are 3 different chains of length `dp[j]` ending at `j`,
then extending any of them to `i` gives 3 chains of length `dp[j]+1`.

`count[j]` carries forward all the ways to reach `j`.

---

## 🔍 LIS Length vs LIS Count — What Changes

| Aspect | LIS Length | LIS Count (this) |
|--------|-----------|-------------------|
| Arrays | `dp[i]` only | `dp[i]` + `count[i]` |
| Longer found | `dp[i] = dp[j]+1` | + `count[i] = count[j]` (reset) |
| Same found | (implicit in max) | + `count[i] += count[j]` (accumulate) |
| Answer | `max(dp)` | `sum(count[i])` where `dp[i] == max` |

Same O(N²) loop. Two extra lines. One extra array.

---

### 🧠 Memory of the Counted Rising Chains Law

-   **Two arrays:** `dp[i]` (length) and `count[i]` (number of LIS of that length)
-   **Initialize:** `dp[i] = 1`, `count[i] = 1` for all i
-   **Longer chain found:** RESET — `dp[i] = dp[j]+1`, `count[i] = count[j]`
-   **Same length found:** ACCUMULATE — `count[i] += count[j]`
-   **Answer:** sum `count[i]` for all `i` where `dp[i] == maxLen`
-   **Why `count[j]` not 1:** predecessor itself may have multiple paths
-   **Time:** O(N²). **Space:** O(N).

Thus is remembered the saga of **Number of Longest Increasing Subsequences**,
where the Oracle tracked not just how long each chain could grow
but how many ways it could grow to that length --
resetting the count when a longer chain was found,
accumulating when another path reached the same peak --
until the total number of longest rising chains
was summed across all their ending points. 📈🔢✨
