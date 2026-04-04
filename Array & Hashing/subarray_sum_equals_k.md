## 🔮🧮 _The Ledger of Balanced Currents: The Subarray Sum Equals K Saga_

> \_"In the River of Numbers,
> values flowed forward without pause --
> some rising, some falling,
> positives and negatives entwined like unseen currents.
>
> The Oracle was asked a subtle question:
>
> **'How many continuous stretches of this river
> sum exactly to the sacred value K?'**
>
> The naive approach would examine every possible subarray --
> every start, every end -- O(N^2) at best.
> The river was too long for such brute force.
>
> The Oracle could not use a sliding window either --
> negatives broke the monotonic property.
> Expanding the window might decrease the sum.
> Shrinking it might increase it.
> The window had no reliable direction.
>
> So the Oracle forged a **Ledger of Past Currents** --
> a hash map remembering how many times
> each prefix sum had appeared.
>
> The insight was elegant:
>
> **If prefixSum[j] - prefixSum[i] = k,
> then the subarray from i+1 to j sums to k.
> Rearranged: prefixSum[i] = prefixSum[j] - k.**
>
> So at each index j, the Oracle asked:
> 'How many times has (currentSum - k) appeared before?'
>
> Each such occurrence was a valid subarray ending right here.
>
> One pass. One map. Every balanced stretch counted."\_

---

This is the saga of **Subarray Sum Equals K**.

You are given an integer array `nums` and an integer `k`.

Your task:

-   Return the **total number of continuous subarrays**
    whose sum equals `k`.

Numbers may be positive, negative, or zero.

```
Input:  nums = [1, 1, 1], k = 2
Output: 2   (subarrays [1,1] starting at index 0 and [1,1] starting at index 1)

Input:  nums = [1, 2, 3], k = 3
Output: 2   (subarrays [1,2] and [3])

Input:  nums = [1, -1, 0], k = 0
Output: 3   (subarrays [1,-1], [-1,0], [1,-1,0])
```

---

## 🧠 The Oracle's Core Insight -- Prefix Sum + Frequency Map

The key identity:

```
sum of subarray (i+1 ... j) = prefixSum[j] - prefixSum[i]
```

We want this to equal `k`:

```
prefixSum[j] - prefixSum[i] = k
Rearranged: prefixSum[i] = prefixSum[j] - k
```

So at each index `j`, the number of valid subarrays ending at `j`
equals the number of times `(currentSum - k)` has appeared
as a prefix sum before index `j`.

A hash map `freq` stores how many times each prefix sum has occurred.

**Critical initialization:** `freq[0] = 1` --
because a prefix sum of 0 exists before the array starts
(the empty prefix). Without this, subarrays starting at index 0
whose sum equals `k` would be missed.

```
Time:  O(N) -- single pass, O(1) hash lookups
Space: O(N) -- the frequency map
```

---

### 📜 The Scroll of Flowing Numbers

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🔮 The Oracle's Ledger Ritual

```cpp
int subarraySum(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    freq[0] = 1;
```

The Oracle opened the Ledger -- `freq` --
a map from each prefix sum to the number of times it has appeared.

`freq[0] = 1` was recorded before the river began flowing.
This represented the empty prefix -- a cumulative sum of zero
that existed before any element was processed.

> _"Without this seed, the Oracle would miss
> every subarray starting at index 0 whose sum equals k.
> If currentSum itself equals k, then currentSum - k = 0,
> and freq[0] must be 1 to count it."_

---

```cpp
    int sum = 0;
    int count = 0;
```

`sum` -- the running prefix sum, accumulating as the Oracle walks.
`count` -- the total number of valid subarrays found so far.

---

## 🌊 Walk the River -- One Number at a Time

```cpp
    for (int x : nums) {
        sum += x;
```

Each number was absorbed into the running prefix sum.
The river's cumulative flow changed with every step.

---

### ⚖️ How Many Valid Subarrays End Right Here?

```cpp
        if (freq.count(sum - k)) {
            count += freq[sum - k];
        }
```

The Oracle asked:

> **"How many times has the prefix sum `(sum - k)` appeared before?"**

If `sum - k` appeared `t` times before,
then there are `t` different starting points
where a subarray ending at the current index sums to exactly `k`.

All `t` subarrays were counted at once.

This is the entire magic of the algorithm --
one lookup, potentially multiple subarrays discovered.

---

### 📒 Record the Current Prefix Sum

```cpp
        freq[sum]++;
    }
```

The current prefix sum was written into the Ledger.
Its frequency increased by one.

Future indices would look back at this entry
when searching for their own balanced stretches.

---

## 🏁 Return the Total Count

```cpp
    return count;
}
```

Every valid subarray across the entire river had been counted.

---

### 🎺 The Trial of the Balanced River

```cpp
int main() {
    vector<int> nums1 = {1, 1, 1};
    cout << subarraySum(nums1, 2) << endl; // expected: 2

    vector<int> nums2 = {1, 2, 3};
    cout << subarraySum(nums2, 3) << endl; // expected: 2

    vector<int> nums3 = {1, -1, 0};
    cout << subarraySum(nums3, 0) << endl; // expected: 3

    return 0;
}
```

---

**Full trace for `[1, 1, 1]`, k = 2:**

| i | x | sum | sum - k | freq[sum-k] | count | freq after          |
| - | - | --- | ------- | ----------- | ----- | ------------------- |
| - | - | 0   | -       | -           | 0     | {0:1}               |
| 0 | 1 | 1   | -1      | 0           | 0     | {0:1, 1:1}          |
| 1 | 1 | 2   | 0       | 1           | 1     | {0:1, 1:1, 2:1}     |
| 2 | 1 | 3   | 1       | 1           | 2     | {0:1, 1:1, 2:1, 3:1}|

**Answer: 2** ✓

Subarrays: `[1,1]` (indices 0-1) and `[1,1]` (indices 1-2).

At index 1: `sum=2`, `sum-k=0`, `freq[0]=1` -- the subarray from index 0 to 1.
At index 2: `sum=3`, `sum-k=1`, `freq[1]=1` -- the subarray from index 1 to 2.

---

**Full trace for `[1, 2, 3]`, k = 3:**

| i | x | sum | sum - k | freq[sum-k] | count | freq after          |
| - | - | --- | ------- | ----------- | ----- | ------------------- |
| - | - | 0   | -       | -           | 0     | {0:1}               |
| 0 | 1 | 1   | -2      | 0           | 0     | {0:1, 1:1}          |
| 1 | 2 | 3   | 0       | 1           | 1     | {0:1, 1:1, 3:1}     |
| 2 | 3 | 6   | 3       | 1           | 2     | {0:1, 1:1, 3:1, 6:1}|

**Answer: 2** ✓

Subarrays: `[1,2]` (sum=3) and `[3]` (sum=3).

At index 1: `sum=3`, `sum-k=0`, `freq[0]=1` -- subarray from index 0 to 1.
At index 2: `sum=6`, `sum-k=3`, `freq[3]=1` -- subarray from index 2 to 2.

---

**Full trace for `[1, -1, 0]`, k = 0:**

| i | x  | sum | sum - k | freq[sum-k] | count | freq after       |
| - | -- | --- | ------- | ----------- | ----- | ---------------- |
| - | -  | 0   | -       | -           | 0     | {0:1}            |
| 0 | 1  | 1   | 1       | 0           | 0     | {0:1, 1:1}       |
| 1 | -1 | 0   | 0       | 1           | 1     | {0:2, 1:1}       |
| 2 | 0  | 0   | 0       | 2           | 3     | {0:3, 1:1}       |

**Answer: 3** ✓

At index 1: `sum=0`, `sum-k=0`, `freq[0]=1` -- subarray `[1,-1]`.
At index 2: `sum=0`, `sum-k=0`, `freq[0]=2` -- subarrays `[1,-1,0]` and `[-1,0]`.

Notice how `freq[0]` grew to 2 after index 1,
so at index 2 it contributed 2 subarrays in one lookup.
This is why we store **frequencies**, not just first occurrences --
we need to COUNT all valid subarrays, not just find the longest.

---

## 🔍 Why Not Sliding Window?

With negatives, the sliding window breaks:

```
nums = [1, -1, 1], k = 1
```

Expanding the window can decrease the sum (adding -1).
Shrinking the window can increase the sum (removing -1).
There is no monotonic relationship between window size and sum.

The prefix sum + hash map approach handles all cases correctly
regardless of the sign of elements.

---

### 🧠 Memory of the Balanced Ledger Law

-   **Prefix sum identity:** `sum(i+1..j) = prefixSum[j] - prefixSum[i]`
-   **Rearranged:** if `prefixSum[j] - k` appeared before, those are valid subarrays
-   **Hash map `freq`:** stores how many times each prefix sum has occurred
-   **`freq[0] = 1`** -- critical initialization for subarrays starting at index 0
-   At each index: `count += freq[sum - k]`, then `freq[sum]++`
-   **Order matters:** check BEFORE recording (to avoid counting the current index as its own prefix)
-   Unlike the "longest subarray" variant, here we store **frequencies** (not first occurrence)
    because we need to COUNT all valid subarrays, not find the longest one
-   **Time:** O(N) -- single pass with O(1) hash operations
-   **Space:** O(N) -- hash map stores at most N+1 prefix sums

Thus is remembered the saga of **Subarray Sum Equals K**,
where the Oracle did not chase every possible stretch of the river
but instead kept a Ledger of past currents --
recording how many times each prefix sum had appeared --
so that at every step forward,
a single lookup into the Ledger
revealed exactly how many balanced stretches
ended right here, right now,
summing to the sacred value K. 🔮🧮✨
