## 🪟📏 _The Shortest Sufficient Window: The Minimum Size Subarray Sum Saga_

> \_"The Oracle was given an array of positive integers and a target.
>
> She was commanded:
>
> **'Find the SHORTEST subarray whose sum is ≥ target.
> If no such subarray exists, return 0.'**
>
> The Oracle used a variable-size sliding window:
>
> **Expand right until sum ≥ target.**
> **Then shrink left to find the shortest valid window.**
> **Record the minimum length. Continue expanding.**
>
> Because all elements are POSITIVE:
> -   Expanding always INCREASES the sum.
> -   Shrinking always DECREASES the sum.
>
> This monotonicity makes the sliding window work."\_

---

This is the saga of **Minimum Size Subarray Sum (LeetCode 209)**.

Given an array `nums` of positive integers and integer `target`:
-   Find the minimal length subarray with sum ≥ target.
-   Return 0 if no such subarray exists.

```
Input:  nums = [2, 3, 1, 2, 4, 3], target = 7
Output: 2   ([4, 3] sums to 7)

Input:  nums = [1, 4, 4], target = 4
Output: 1   ([4] alone is ≥ 4)

Input:  nums = [1, 1, 1, 1, 1, 1, 1, 1], target = 11
Output: 0   (total sum = 8 < 11, impossible)
```

---

## 🧠 The Sliding Window — Expand Then Shrink

**Expand:** move `right` forward, adding elements to the sum.
Keep expanding until `sum >= target`.

**Shrink:** once valid, shrink from the left to find the SHORTEST
valid window. Record the length. Keep shrinking while still valid.

**Continue:** after shrinking makes the window invalid,
expand right again to find the next valid window.

This is the opposite of "longest" problems:
-   Longest: expand as much as possible, shrink only when invalid.
-   **Shortest: shrink as much as possible once valid.**

---

## 🧠 Why Positive Elements Are Required

If elements can be negative, shrinking might INCREASE the sum
(removing a negative element). The monotonicity breaks.

With all positives:
-   Adding an element always increases sum → expanding helps.
-   Removing an element always decreases sum → shrinking hurts.

This guarantees the window behaves predictably.

---

### 📜 The Scroll of the Shortest Sufficient Window

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 📏 The Sliding Window Solution

### Initialize

```cpp
int minSubArrayLen(int target, vector<int>& nums) {
    int left = 0;
    int sum = 0;
    int minLen = INT_MAX;
```

`sum` = running sum of the current window.
`minLen` = shortest valid window found. Starts at INT_MAX (nothing found yet).

---

### Expand with right pointer

```cpp
    for (int right = 0; right < nums.size(); right++) {
        sum += nums[right];
```

Add the new element. Sum grows.

---

### Shrink while valid — find the shortest

```cpp
        while (sum >= target) {
            minLen = min(minLen, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }
```

The window is valid (sum ≥ target). Record its length.
Then shrink: remove the leftmost element. Check again.
Keep shrinking as long as the window remains valid.

Each shrink might give a SHORTER valid window.
We want the shortest — so we shrink aggressively.

> _"The window is sufficient. But is it the shortest?
> The Oracle shrinks from the left, recording each valid length,
> until the window breaks. The shortest is remembered."_

---

### Return the result

```cpp
    return minLen == INT_MAX ? 0 : minLen;
}
```

If `minLen` was never updated → no valid subarray exists. Return 0.

---

### 🎺 The Trial of the Shortest Sufficient Window

```cpp
int main() {
    vector<int> n1 = {2, 3, 1, 2, 4, 3};
    cout << minSubArrayLen(7, n1) << endl; // expected: 2

    vector<int> n2 = {1, 4, 4};
    cout << minSubArrayLen(4, n2) << endl; // expected: 1

    vector<int> n3 = {1, 1, 1, 1, 1, 1, 1, 1};
    cout << minSubArrayLen(11, n3) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for nums = [2, 3, 1, 2, 4, 3], target = 7:**

| right | nums[r] | sum | sum≥7? | Shrink | minLen |
|-------|---------|-----|--------|--------|--------|
| 0 | 2 | 2 | No | — | ∞ |
| 1 | 3 | 5 | No | — | ∞ |
| 2 | 1 | 6 | No | — | ∞ |
| 3 | 2 | 8 | **Yes** | len=4. sum-=2, left=1. sum=6 < 7. Stop. | 4 |
| 4 | 4 | 10 | **Yes** | len=4. sum-=3, left=2. sum=7 ≥ 7. | 4→3 |
|   |   |    | **Yes** | len=3. sum-=1, left=3. sum=6 < 7. Stop. | 3 |
| 5 | 3 | 9 | **Yes** | len=3. sum-=2, left=4. sum=7 ≥ 7. | 3 |
|   |   |   | **Yes** | len=2. sum-=4, left=5. sum=3 < 7. Stop. | **2** |

**Answer: 2** ✓ (subarray [4, 3])

---

**Trace for nums = [1, 4, 4], target = 4:**

| right | sum | sum≥4? | Shrink | minLen |
|-------|-----|--------|--------|--------|
| 0 | 1 | No | — | ∞ |
| 1 | 5 | **Yes** | len=2. sum-=1, left=1. sum=4 ≥ 4. | 2 |
|   |   |   | len=1. sum-=4, left=2. sum=0 < 4. Stop. | **1** |
| 2 | 4 | **Yes** | len=1. sum-=4, left=3. sum=0 < 4. Stop. | 1 |

**Answer: 1** ✓ (single element [4] is ≥ 4)

---

**Trace for nums = [1,1,1,1,1,1,1,1], target = 11:**

Total sum = 8 < 11. The window never reaches sum ≥ 11.
`minLen` stays INT_MAX.

**Answer: 0** ✓ (impossible)

---

## 🔍 Shortest vs Longest — The Shrink Difference

| Goal | When to shrink | Record when |
|------|---------------|-------------|
| **Shortest (this)** | **While valid (sum ≥ target)** | **Each valid shrink** |
| Longest | While invalid (sum > budget) | After shrinking (when valid) |

Shortest: shrink WHILE valid → find the tightest fit.
Longest: shrink WHILE invalid → find the widest fit.

Opposite shrink conditions. Opposite goals.

---

## 🔍 What If Target Is Very Large?

If the total sum of the array < target → no valid subarray exists.
The window expands to include everything but never reaches target.
Return 0.

We don't need to check this explicitly — `minLen == INT_MAX` handles it.

---

## 🔍 The O(N log N) Binary Search Alternative

For each index, binary search for the shortest subarray starting there
with sum ≥ target (using prefix sums).

```
For each left:
  Binary search for smallest right where prefixSum[right+1] - prefixSum[left] >= target.
```

O(N log N). Works but slower than the O(N) sliding window.
Only useful when elements can be negative (sliding window fails).

---

## 🔍 Connection to Other Problems

| Problem | Condition | Goal |
|---------|-----------|------|
| **Min Size Subarray Sum (this)** | sum ≥ target | **Shortest** |
| Longest Subarray Sum ≤ K | sum ≤ K | Longest |
| Count Subarrays Sum ≤ K | sum ≤ K | Count |
| Subarray Sum = K | sum == K | Count (prefix sum + hashmap) |

All are subarray sum problems. Different conditions, different goals.

---

### 🧠 Memory of the Shortest Sufficient Window Law

-   **Expand right** until `sum >= target`
-   **Shrink left WHILE valid** — record minimum length at each step
-   **Shrink aggressively** — we want the SHORTEST, not the longest
-   **All positive elements required** — monotonicity guarantee
-   **Return 0** if no valid subarray exists (`minLen == INT_MAX`)
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Minimum Size Subarray Sum**,
where the Oracle expanded until the window was sufficient —
then shrank aggressively to find the tightest fit —
recording the shortest valid window at each step —
knowing that with positive elements,
shrinking always weakens and expanding always strengthens —
until the minimal sufficient subarray was found. 🪟📏✨
