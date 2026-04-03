## 🌌📜 _The River of Shifting Tides: The Longest Subarray with Sum K (General) Saga_

> _"Beyond the Land of Pure Positives lay a far wilder river —
> a stream where currents surged forward
> and then pulled back without warning.
>
> Positives pushed the waters onward.
> Negatives dragged them back.
> Zeros left the flow unchanged.
>
> The Oracle was summoned with a harder decree:
>
> **'Find the longest continuous stretch of this river
> whose total flow equals exactly K.'**
>
> The old sliding-window maps failed here —
> for shrinking the banks did not always reduce the flow,
> and expanding them did not always increase it.
>
> A negative number could decrease the sum.
> The monotonic property was broken.
>
> So the Oracle forged a different tool:
> a **Chronicle of Prefix Sums** —
> remembering the first time each cumulative sum appeared,
> so that distance — not repetition —
> would reveal the longest balanced stretch.
>
> The insight was elegant:
>
> **If prefixSum[j] - prefixSum[i] = k,
> then the subarray from i+1 to j has sum k.
> Rearranged: prefixSum[i] = prefixSum[j] - k.**
>
> So at each index j, check if (currentSum - k) was seen before.
> If yes — the distance from that earlier index to j
> is a valid subarray.
> Track the longest such distance."_

---

This is the saga of **Longest Subarray with Sum K (General)**.

You are given:

- An array `nums` containing **positive, negative, and zero values**
- An integer target `k`

Your task:

- Find the **maximum length** of a subarray
- whose sum is **exactly `k`**

```
Input:  nums = [1, -1, 5, -2, 3], k = 3
Output: 4
Explanation: [1, -1, 5, -2] has sum 3 and length 4

Input:  nums = [1, 2, 3], k = 3
Output: 2
Explanation: [1, 2] has sum 3 and length 2

Input:  nums = [-2, -1, 2, 1], k = 1
Output: 4
Explanation: [-2, -1, 2, 1] has sum 0... wait, k=1
Actually: [2, -1] has sum 1 and length 2
```

---

## 🧠 The Oracle's Core Insight — Prefix Sums Reveal Distance

The key insight:

> **If we know the prefix sum at every index,
> we can find any subarray sum using subtraction.**

Let `prefixSum[i]` = sum of elements from index 0 to i.

For a subarray from index `i+1` to `j`:
```
sum(i+1 to j) = prefixSum[j] - prefixSum[i]
```

We want this to equal `k`:
```
prefixSum[j] - prefixSum[i] = k
Rearranged: prefixSum[i] = prefixSum[j] - k
```

So at each index `j`, we ask:
**"Have I seen the prefix sum (currentSum - k) before?"**

If yes — the subarray from that earlier index to `j` has sum `k`.

The algorithm:

```
1. Maintain a running prefix sum
2. Use a HashMap to store: prefixSum → first index where it occurred
3. At each index j:
   - If currentSum == k → subarray [0...j] has sum k
   - If (currentSum - k) exists in map → subarray from that index to j has sum k
   - Record currentSum in map (only if not already present)
4. Track the longest valid subarray found
```

**Time:** O(N) — single pass with O(1) hash lookups
**Space:** O(N) — the hash map stores at most N prefix sums

---

### 📜 The Scroll of Unstable Currents

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🔮 The Oracle's Chronicle Ritual

_Record the first sighting of every current_

```cpp
int longestSubarrayWithSumK(vector<int>& nums, int k) {
    unordered_map<int, int> firstSeen;
    int sum = 0;
    int maxLen = 0;
```

The Oracle opened the Chronicle — `firstSeen` —
a map from each prefix sum to the earliest index where it appeared.

- `sum` — the running prefix sum
- `maxLen` — the longest valid subarray found so far

---

## 🌊 Walk the River One Step at a Time

```cpp
    for (int i = 0; i < nums.size(); i++) {
        sum += nums[i];
```

The Oracle walked the river from left to right,
accumulating the prefix sum at each step.

The river's flow changed unpredictably —
sometimes rising, sometimes falling.

---

## ✨ Case 1 — From the Beginning to Here

```cpp
        if (sum == k) {
            maxLen = i + 1;
        }
```

If the entire stretch from index `0` to `i` summed to `k`,
the Oracle recorded its full length: `i + 1`.

This handles the case where no earlier prefix sum needs to be subtracted.

---

## 🔍 Case 2 — Look for a Matching Past Current

```cpp
        if (firstSeen.count(sum - k)) {
            maxLen = max(maxLen, i - firstSeen[sum - k]);
        }
```

The Oracle asked:
**"Have I seen the prefix sum (sum - k) before?"**

If yes — let's say it was at index `j` —
then the subarray from `j+1` to `i` has sum `k`.

Why?
```
prefixSum[i] - prefixSum[j] = k
sum - firstSeen[sum - k] gives the length
```

The length of this subarray is `i - j`.

The Oracle compared it with the longest found so far.

> _"The earlier the matching sum appeared,
> the longer the distance to now."_

---

## 📘 Record the First Appearance Only

```cpp
        if (!firstSeen.count(sum)) {
            firstSeen[sum] = i;
        }
    }
    return maxLen;
}
```

The Oracle recorded the current prefix sum in the Chronicle —
but **only if it hadn't been seen before**.

Why only the first occurrence?

Because we want the **longest** subarray.
An earlier index gives a longer distance to any future match.

If we overwrote with later indices,
we'd get shorter subarrays.

---

### 🎺 The Trial of the Shifting River

```cpp
int main() {
    vector<int> nums1 = {1, -1, 5, -2, 3};
    int k1 = 3;
    cout << longestSubarrayWithSumK(nums1, k1) << endl; // expected: 4

    vector<int> nums2 = {1, 2, 3};
    int k2 = 3;
    cout << longestSubarrayWithSumK(nums2, k2) << endl; // expected: 2

    return 0;
}
```

---

**Trace for `nums = [1, -1, 5, -2, 3], k = 3`:**

| i | nums[i] | sum | sum == k? | sum - k | (sum-k) in map? | Length | firstSeen after | maxLen |
|---|---------|-----|-----------|---------|-----------------|--------|-----------------|--------|
| 0 | 1       | 1   | No        | -2      | No              | —      | {1:0}           | 0      |
| 1 | -1      | 0   | No        | -3      | No              | —      | {1:0, 0:1}      | 0      |
| 2 | 5       | 5   | No        | 2       | No              | —      | {1:0, 0:1, 5:2} | 0      |
| 3 | -2      | 3   | Yes       | 0       | Yes (at 1)      | 3-1=2  | {1:0, 0:1, 5:2, 3:3} | 2 |
| 4 | 3       | 6   | No        | 3       | Yes (at 3)      | 4-3=1  | {1:0, 0:1, 5:2, 3:3, 6:4} | 2 |

Wait, let me recalculate:

At i=3: sum=3, sum==k → maxLen = 3+1 = 4
At i=3: sum-k = 0, which is at index 1 → length = 3-1 = 2

So maxLen should be max(4, 2) = 4.

Let me redo the trace:

| i | nums[i] | sum | sum == k? | maxLen from case 1 | sum - k | (sum-k) at? | Length from case 2 | firstSeen after | maxLen |
|---|---------|-----|-----------|--------------------|---------|--------------|--------------------|-----------------|--------|
| 0 | 1       | 1   | No        | —                  | -2      | No           | —                  | {1:0}           | 0      |
| 1 | -1      | 0   | No        | —                  | -3      | No           | —                  | {1:0, 0:1}      | 0      |
| 2 | 5       | 5   | No        | —                  | 2       | No           | —                  | {1:0, 0:1, 5:2} | 0      |
| 3 | -2      | 3   | Yes       | 4                  | 0       | Yes (1)      | 3-1=2              | {1:0, 0:1, 5:2, 3:3} | 4 |
| 4 | 3       | 6   | No        | —                  | 3       | Yes (3)      | 4-3=1              | {1:0, 0:1, 5:2, 3:3, 6:4} | 4 |

**Answer: 4** ✓

The subarray `[1, -1, 5, -2]` from index 0 to 3 has sum 3 and length 4.

---

**Trace for `nums = [1, 2, 3], k = 3`:**

| i | nums[i] | sum | sum == k? | maxLen from case 1 | sum - k | (sum-k) at? | Length from case 2 | firstSeen after | maxLen |
|---|---------|-----|-----------|--------------------|---------|--------------|--------------------|-----------------|--------|
| 0 | 1       | 1   | No        | —                  | -2      | No           | —                  | {1:0}           | 0      |
| 1 | 2       | 3   | Yes       | 2                  | 0       | No           | —                  | {1:0, 3:1}      | 2      |
| 2 | 3       | 6   | No        | —                  | 3       | Yes (1)      | 2-1=1              | {1:0, 3:1, 6:2} | 2      |

**Answer: 2** ✓

The subarray `[1, 2]` from index 0 to 1 has sum 3 and length 2.

---

## 🔄 Why Not Sliding Window?

With negatives, the sliding window breaks:

```
nums = [1, -1, 1, 1], k = 1
```

- Window `[1]` → sum = 1 ✓
- Expand to `[1, -1]` → sum = 0 (less than k!)
- Expand to `[1, -1, 1]` → sum = 1 ✓

The sliding window would shrink when sum > k,
but with negatives, expanding can decrease the sum,
and shrinking might not help.

The prefix sum approach handles all cases correctly.

---

### 🧠 Memory of the Shifting-Tide Law

- **Works for positive, negative, and zero values**
- Use **prefix sum + hash map**
- Store **first occurrence** of each prefix sum: `firstSeen[sum] = i`
- At each index, check two cases:
  1. If `sum == k` → subarray from 0 to i has sum k
  2. If `(sum - k)` exists in map → subarray from that index to i has sum k
- Record only first occurrence (earlier index → longer subarray)
- **Time:** O(N) — single pass with O(1) hash operations
- **Space:** O(N) — hash map stores at most N prefix sums
- **Edge cases:**
  - No subarray with sum k → return 0
  - Multiple subarrays → return the longest
  - Entire array sums to k → handled by case 1

Thus is remembered the saga of **Longest Subarray with Sum K (General)**,
where the Oracle mastered a river of shifting tides,
tracking echoes of past prefix sums in her Chronicle —
and measuring the longest distance
between two perfectly balanced moments in time. 🌌📜✨
