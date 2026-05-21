## 🔄📈 _The Ring of Sums: The Maximum Sum Circular Subarray Saga_

> \_"The Oracle was given a circular array.
> The last element connected back to the first.
>
> She was commanded:
>
> **'Find the maximum sum subarray in this circular array.'**
>
> In a normal (non-circular) array, Kadane's algorithm suffices.
> But in a circular array, the maximum subarray might WRAP AROUND --
> taking elements from the end AND the beginning.
>
> The Oracle saw two cases:
>
> **Case 1 -- No wrapping:**
> The maximum subarray lies entirely within the array.
> Standard Kadane's finds it.
>
> **Case 2 -- Wrapping:**
> The maximum subarray wraps around (takes from both ends).
> The elements NOT in this subarray form a CONTIGUOUS subarray
> in the middle -- and that middle part is the MINIMUM subarray.
>
> **Wrapped max = totalSum - minSubarray.**
>
> If we remove the minimum contiguous subarray from the total,
> what remains (the two ends) is the wrapped maximum.
>
> **Answer = max(Kadane's max, totalSum - Kadane's min).**
>
> One pass for max. One pass for min. Compare.
> The circular problem reduced to two linear problems."\_

---

This is the saga of **Maximum Sum Circular Subarray**.

Given a circular integer array `nums`:

-   Find the maximum sum of a non-empty subarray.
-   The subarray may wrap around (end connects to beginning).

```
Input:  nums = [1, -2, 3, -2]
Output: 3   (subarray [3], no wrapping needed)

Input:  nums = [5, -3, 5]
Output: 10  (subarray [5, 5] wrapping: take both 5s, skip the -3)

Input:  nums = [-3, -2, -1]
Output: -1  (all negative, best is the least negative)
```

---

## 🧠 The Oracle's Core Insight -- Max of Two Cases

**Case 1 (no wrap):** maximum subarray is contiguous in the middle.
Found by standard Kadane's (maximum subarray sum).

**Case 2 (wrap):** maximum subarray wraps around the ends.
The "gap" in the middle is the minimum contiguous subarray.
`wrappedMax = totalSum - minSubarraySum`.

**Answer = max(case1, case2).**

**Edge case:** if ALL elements are negative, `minSubarray = totalSum`,
so `case2 = totalSum - totalSum = 0`. But an empty subarray isn't allowed.
In this case, only case 1 is valid (the least negative element).

```
If maxSum == totalSum - minSum (i.e., minSum == totalSum, all negative):
  return maxSum (case 1 only).
Else:
  return max(maxSum, totalSum - minSum).
```

```
Time:  O(n) -- single pass (or two passes)
Space: O(1)
```

---

### 📜 The Scroll of the Ring of Sums

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 🔄 The Combined Kadane's Ritual

```cpp
int maxSubarraySumCircular(vector<int>& nums) {
    int n = nums.size();
    int totalSum = 0;
```

---

### 📈 Kadane's for Maximum Subarray

```cpp
    int maxSum = INT_MIN;
    int currentMax = 0;
```

Standard Kadane's: track the maximum ending at each position.
Reset to 0 when it goes negative.

---

### 📉 Kadane's for Minimum Subarray

```cpp
    int minSum = INT_MAX;
    int currentMin = 0;
```

Inverted Kadane's: track the minimum ending at each position.
Reset to 0 when it goes positive.

---

### 🔁 Single Pass -- Compute All Three

```cpp
    for (int i = 0; i < n; i++) {
        totalSum += nums[i];
```

Accumulate the total sum (needed for the wrap case).

---

### 📈 Update Maximum Subarray (Kadane's)

```cpp
        currentMax += nums[i];
        maxSum = max(maxSum, currentMax);
        if (currentMax < 0) currentMax = 0;
```

Standard Kadane's:
-   Add current element to running sum.
-   Update global max.
-   If running sum goes negative → reset (start fresh).

> _"The maximum subarray ending here
> is either extended by this element
> or starts fresh from this element.
> If the accumulated sum is a burden (negative),
> drop it and start over."_

---

### 📉 Update Minimum Subarray (Inverted Kadane's)

```cpp
        currentMin += nums[i];
        minSum = min(minSum, currentMin);
        if (currentMin > 0) currentMin = 0;
    }
```

Inverted Kadane's:
-   Add current element to running sum.
-   Update global min.
-   If running sum goes positive → reset (start fresh).

This finds the minimum contiguous subarray sum.
The "gap" that, when removed from the total, gives the wrapped max.

> _"The minimum subarray ending here
> is either extended by this element
> or starts fresh.
> If the accumulated sum becomes a benefit (positive),
> drop it -- we want the worst, not the best."_

---

### 🏁 Combine the Two Cases

```cpp
    if (maxSum < 0) return maxSum;
    return max(maxSum, totalSum - minSum);
}
```

**If `maxSum < 0`:** ALL elements are negative.
The minimum subarray = the entire array.
`totalSum - minSum = 0` (empty subarray -- not allowed).
Return `maxSum` (the least negative element).

**Otherwise:** return the better of:
-   `maxSum` -- best non-wrapping subarray (case 1).
-   `totalSum - minSum` -- best wrapping subarray (case 2).

> _"If every element is negative,
> the best we can do is the least negative one.
> Otherwise, compare the straight path
> with the wrapped path --
> and take whichever yields more."_

---

### 🎺 The Trial of the Ring of Sums

```cpp
int main() {
    vector<int> n1 = {1, -2, 3, -2};
    cout << maxSubarraySumCircular(n1) << endl; // expected: 3

    vector<int> n2 = {5, -3, 5};
    cout << maxSubarraySumCircular(n2) << endl; // expected: 10

    vector<int> n3 = {-3, -2, -1};
    cout << maxSubarraySumCircular(n3) << endl; // expected: -1

    vector<int> n4 = {3, -1, 2, -1};
    cout << maxSubarraySumCircular(n4) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for nums = [5, -3, 5]:**

| i | nums[i] | totalSum | currentMax | maxSum | currentMin | minSum |
|---|---------|----------|------------|--------|------------|--------|
| 0 | 5       | 5        | 5          | 5      | 5→**0**    | 5      |
| 1 | -3      | 2        | -3→**0**   | 5      | -3         | -3     |
| 2 | 5       | 7        | 5          | 5      | -3+5=2→**0**| -3    |

Wait, let me redo more carefully:

| i | nums[i] | totalSum | currentMax→maxSum | currentMin→minSum |
|---|---------|----------|-------------------|-------------------|
| 0 | 5       | 5        | 0+5=5. maxSum=5. (5>0, no reset) | 0+5=5. minSum=5. (5>0, reset to 0) |
| 1 | -3      | 2        | 5+(-3)=2. maxSum=5. (2>0, no reset) | 0+(-3)=-3. minSum=-3. (-3<0, no reset) |
| 2 | 5       | 7        | 2+5=7. maxSum=7. (7>0, no reset) | -3+5=2. minSum=-3. (2>0, reset to 0) |

Hmm, maxSum=7? But expected is 10. Let me re-check...

Actually wait: `maxSum = 7` is the non-wrapping case (subarray [5,-3,5] = 7).
`totalSum - minSum = 7 - (-3) = 10` is the wrapping case (take both 5s, skip -3).

`max(7, 10) = 10`. **Answer: 10** ✓

The wrapped subarray [5, 5] (wrapping from end to beginning) has sum 10.
This is `totalSum(7) - minSubarray(-3) = 10`.

---

**Full trace for nums = [1, -2, 3, -2]:**

totalSum = 0.

| i | nums[i] | totalSum | currentMax | maxSum | currentMin | minSum |
|---|---------|----------|------------|--------|------------|--------|
| 0 | 1       | 1        | 1          | 1      | 1→0        | 1      |
| 1 | -2      | -1       | -2→0       | 1      | -2         | -2     |
| 2 | 3       | 2        | 3          | 3      | -2+3=1→0   | -2     |
| 3 | -2      | 0        | 3-2=1      | 3      | -2         | -2     |

maxSum = 3. totalSum - minSum = 0 - (-2) = 2.
max(3, 2) = **3**. ✓

No wrapping needed. The subarray [3] is the best.

---

**Trace for nums = [-3, -2, -1]:**

| i | nums[i] | totalSum | currentMax | maxSum | currentMin | minSum |
|---|---------|----------|------------|--------|------------|--------|
| 0 | -3      | -3       | -3         | -3     | -3         | -3     |
| 1 | -2      | -5       | -2         | -2     | -5         | -5     |
| 2 | -1      | -6       | -1         | -1     | -6         | -6     |

maxSum = -1. Since maxSum < 0 (all negative) → return maxSum = **-1**. ✓

(If we used `totalSum - minSum = -6 - (-6) = 0`, that's an empty subarray -- invalid.)

---

**Trace for nums = [3, -1, 2, -1]:**

totalSum = 3.

maxSum (Kadane's): subarray [3,-1,2] = 4. maxSum = 4.
minSum (inverted Kadane's): subarray [-1] = -1. minSum = -1.

totalSum - minSum = 3 - (-1) = 4.
max(4, 4) = **4**. ✓

Both cases give 4. Non-wrapping [3,-1,2] = 4. Wrapping [2,-1,3] = 4. Same.

---

## 🔍 Why `totalSum - minSubarray` Gives the Wrapped Max

```
Array: [a, b, c, d, e, f]

Non-wrapping max: some contiguous subarray in the middle.

Wrapping max: takes from the END and the BEGINNING.
  = [d, e, f] + [a, b]  (wraps around)
  = totalSum - [c]       (the gap in the middle)
  = totalSum - minSubarray
```

The elements NOT in the wrapped subarray form a contiguous block
in the middle of the array. That block is the minimum subarray.
Removing it from the total gives the wrapped sum.

---

## 🔍 Why the All-Negative Edge Case

If all elements are negative:
-   `maxSum` = the least negative element (correct answer).
-   `minSum` = the entire array sum = `totalSum`.
-   `totalSum - minSum = 0` = empty subarray (invalid -- must be non-empty).

So when `maxSum < 0`, we return `maxSum` directly and ignore case 2.

---

## 🔍 Connection to Standard Kadane's

| Standard Kadane's                 | Circular Kadane's (this)          |
| --------------------------------- | --------------------------------- |
| Find max subarray                 | Find max subarray (case 1)        |
| --                                | Find min subarray (case 2)        |
| --                                | Answer = max(case1, total - case2)|
| O(n), O(1)                        | O(n), O(1)                        |

This problem = Kadane's × 2 (one for max, one for min) + one comparison.

---

### 🧠 Memory of the Ring of Sums Law

-   **Two cases:** non-wrapping (standard Kadane's max) vs wrapping (total - Kadane's min)
-   **Answer:** `max(maxSum, totalSum - minSum)`
-   **Edge case:** if ALL negative (`maxSum < 0`) → return `maxSum` (can't use empty subarray)
-   **Kadane's for max:** `currentMax += nums[i]`, reset if < 0, track maxSum
-   **Kadane's for min:** `currentMin += nums[i]`, reset if > 0, track minSum
-   **Why total - min = wrapped max?** The gap (middle) is the min subarray
-   **Single pass** computes totalSum, maxSum, and minSum simultaneously
-   **Time:** O(n). **Space:** O(1).
-   **Edge cases:**
    -   All negative → return least negative (maxSum)
    -   All positive → return totalSum (entire array)
    -   Single element → return that element

Thus is remembered the saga of **Maximum Sum Circular Subarray**,
where the Oracle ran Kadane's twice in one pass --
once to find the maximum subarray (the straight path),
once to find the minimum subarray (the gap to remove) --
and compared the straight path with the wrapped path
(total minus the gap) --
taking the greater of the two,
unless all elements were negative,
in which case only the straight path was valid. 🔄📈✨
