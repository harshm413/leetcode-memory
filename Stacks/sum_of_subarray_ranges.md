## 🏔️🕳️ _The Gap Between Peaks and Valleys: The Sum of Subarray Ranges Saga_

> \_"In the Kingdom of Subarrays,
> every contiguous stretch of numbers
> had a highest peak and a deepest valley.
>
> The **range** of a subarray was the difference:
> `max - min`.
>
> The King commanded:
>
> **'Sum the ranges of ALL possible subarrays.'**
>
> The naive approach would enumerate every subarray,
> find its max and min, compute the range.
> O(N²) at best. O(N³) at worst.
>
> The Oracle saw a brilliant decomposition:
>
> **Sum of ranges = Sum of all subarray maximums
>                 - Sum of all subarray minimums.**
>
> Because `range = max - min` for each subarray,
> summing all ranges equals
> `Σ(max) - Σ(min)` across all subarrays.
>
> Both `Σ(max)` and `Σ(min)` could be computed in O(N)
> using monotonic stacks --
> the same Previous/Next Greater/Smaller Element patterns.
>
> Two monotonic stack passes for minimums.
> Two monotonic stack passes for maximums.
> Subtract. Done."\_

---

This is the saga of **Sum of Subarray Ranges**.

Given an integer array `nums`:

-   For every subarray, compute `max - min` (the range).
-   Return the **sum of all ranges** across all subarrays.

```
Input:  [1, 2, 3]
Output: 4
Subarrays: [1]=0, [2]=0, [3]=0, [1,2]=1, [2,3]=1, [1,2,3]=2 → sum=4

Input:  [1, 3, 3]
Output: 4

Input:  [4, -2, -3, 4, 1]
Output: 59
```

---

## 🧠 The Oracle's Core Insight -- Decompose into Sum of Maxs and Mins

```
Sum of ranges = Σ (max of subarray) - Σ (min of subarray)
              = sumOfSubarrayMaximums - sumOfSubarrayMinimums
```

**Sum of Subarray Minimums** is a classic monotonic stack problem.
For each element, count how many subarrays it is the minimum of:

```
count[i] = leftSpan × rightSpan
contribution[i] = nums[i] × count[i]
sumOfMins = Σ contribution[i]
```

Where:
-   `leftSpan` = distance to the Previous Smaller Element (PSE).
-   `rightSpan` = distance to the Next Smaller or Equal Element (NSE).

**Sum of Subarray Maximums** is the mirror:

```
count[i] = leftSpan × rightSpan
contribution[i] = nums[i] × count[i]
sumOfMaxs = Σ contribution[i]
```

Where:
-   `leftSpan` = distance to the Previous Greater Element (PGE).
-   `rightSpan` = distance to the Next Greater or Equal Element (NGE).

The "or equal" on one side prevents double-counting
when duplicate values exist.

```
Time:  O(N) -- four monotonic stack passes (or two combined)
Space: O(N)
```

---

### 📜 The Scroll of the Ranges

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## 🕳️ Sum of Subarray Minimums

_For each element: how many subarrays is it the minimum of?_

```cpp
long long sumOfSubarrayMins(vector<int>& nums) {
    int n = nums.size();
    vector<int> pse(n), nse(n);
    stack<int> st;
```

**PSE (Previous Smaller Element):**

```cpp
    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] >= nums[i]) st.pop();
        pse[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }
```

Pop while top is `>=` current (strictly smaller survives).
PSE[i] = index of nearest strictly smaller element to the left.
If none → -1.

**NSE (Next Smaller or Equal Element):**

```cpp
    while (!st.empty()) st.pop();
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && nums[st.top()] > nums[i]) st.pop();
        nse[i] = st.empty() ? n : st.top();
        st.push(i);
    }
```

Pop while top is `>` current (smaller **or equal** survives).
NSE[i] = index of nearest smaller-or-equal element to the right.
If none → n.

The asymmetry (`>=` left, `>` right) ensures each subarray's minimum
is counted exactly once when duplicates exist.

**Compute the sum:**

```cpp
    long long sum = 0;
    for (int i = 0; i < n; i++) {
        long long left = i - pse[i];
        long long right = nse[i] - i;
        sum += (long long)nums[i] * left * right;
    }
    return sum;
}
```

`left` = number of choices for the subarray's left boundary.
`right` = number of choices for the right boundary.
`left × right` = total subarrays where `nums[i]` is the minimum.

---

## 🏔️ Sum of Subarray Maximums

_Mirror image -- Previous Greater and Next Greater or Equal_

```cpp
long long sumOfSubarrayMaxs(vector<int>& nums) {
    int n = nums.size();
    vector<int> pge(n), nge(n);
    stack<int> st;
```

**PGE (Previous Greater Element):**

```cpp
    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] <= nums[i]) st.pop();
        pge[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }
```

Pop while top is `<=` current (strictly greater survives).

**NGE (Next Greater or Equal Element):**

```cpp
    while (!st.empty()) st.pop();
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && nums[st.top()] < nums[i]) st.pop();
        nge[i] = st.empty() ? n : st.top();
        st.push(i);
    }
```

Pop while top is `<` current (greater **or equal** survives).

**Compute the sum:**

```cpp
    long long sum = 0;
    for (int i = 0; i < n; i++) {
        long long left = i - pge[i];
        long long right = nge[i] - i;
        sum += (long long)nums[i] * left * right;
    }
    return sum;
}
```

Same formula -- `nums[i] × left × right` = contribution as maximum.

---

## ⚖️ The Final Answer -- Subtract

```cpp
long long subArrayRanges(vector<int>& nums) {
    return sumOfSubarrayMaxs(nums) - sumOfSubarrayMins(nums);
}
```

Sum of all ranges = sum of all maxs - sum of all mins.

---

### 🎺 The Trial of the Ranges

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3};
    cout << subArrayRanges(nums1) << endl; // expected: 4

    vector<int> nums2 = {1, 3, 3};
    cout << subArrayRanges(nums2) << endl; // expected: 4

    vector<int> nums3 = {4, -2, -3, 4, 1};
    cout << subArrayRanges(nums3) << endl; // expected: 59

    return 0;
}
```

---

**Full trace for `[1, 2, 3]`:**

**Sum of Subarray Minimums:**

| i | nums[i] | PSE | NSE | left | right | count | contribution |
|---|---------|-----|-----|------|-------|-------|-------------|
| 0 | 1       | -1  | 3   | 1    | 3     | 3     | 3           |
| 1 | 2       | 0   | 3   | 1    | 2     | 2     | 4           |
| 2 | 3       | 1   | 3   | 1    | 1     | 1     | 3           |

sumOfMins = 3 + 4 + 3 = **10**

**Sum of Subarray Maximums:**

| i | nums[i] | PGE | NGE | left | right | count | contribution |
|---|---------|-----|-----|------|-------|-------|-------------|
| 0 | 1       | -1  | 1   | 1    | 1     | 1     | 1           |
| 1 | 2       | -1  | 2   | 2    | 1     | 2     | 4           |
| 2 | 3       | -1  | 3   | 3    | 1     | 3     | 9           |

sumOfMaxs = 1 + 4 + 9 = **14**

**Answer: 14 - 10 = 4** ✓

Verification: subarrays [1]=0, [2]=0, [3]=0, [1,2]=1, [2,3]=1, [1,2,3]=2 → sum=4.

---

**Trace for `[1, 3, 3]`:**

**sumOfMins:**

| i | nums[i] | PSE | NSE | left | right | contribution |
|---|---------|-----|-----|------|-------|-------------|
| 0 | 1       | -1  | 3   | 1    | 3     | 3           |
| 1 | 3       | 0   | 2   | 1    | 1     | 3           |
| 2 | 3       | 0   | 3   | 2    | 1     | 6           |

sumOfMins = 3 + 3 + 6 = **12**

**sumOfMaxs:**

| i | nums[i] | PGE | NGE | left | right | contribution |
|---|---------|-----|-----|------|-------|-------------|
| 0 | 1       | -1  | 1   | 1    | 1     | 1           |
| 1 | 3       | -1  | 3   | 2    | 1     | 6           |
| 2 | 3       | -1  | 3   | 3    | 1     | 9           |

sumOfMaxs = 1 + 6 + 9 = **16**

**Answer: 16 - 12 = 4** ✓

The asymmetric `>=`/`>` handling ensures the two `3`s
don't double-count any subarray.

---

## 🔍 Why the Asymmetric Comparison for Duplicates?

For minimums: PSE uses `>=` (strict smaller), NSE uses `>` (smaller or equal).
For maximums: PGE uses `<=` (strict greater), NGE uses `<` (greater or equal).

This ensures that when duplicates exist,
each subarray's min (or max) is attributed to exactly ONE index.

Without this asymmetry, a subarray like `[3, 3]`
would have its minimum counted twice -- once for each `3`.

The rule: **strict on the left, non-strict on the right**
(or vice versa -- as long as one side is strict and the other isn't).

---

### 🧠 Memory of the Range Gap Law

-   **Sum of ranges = sumOfMaxs - sumOfMins** across all subarrays
-   **Sum of Subarray Minimums:** PSE (`>=` pop) + NSE (`>` pop)
-   **Sum of Subarray Maximums:** PGE (`<=` pop) + NGE (`<` pop)
-   **Contribution:** `nums[i] × (i - PSE[i]) × (NSE[i] - i)`
-   **Asymmetric comparisons** handle duplicates -- strict one side, non-strict other
-   Use `long long` for accumulation -- products can overflow int
-   **Time:** O(N) -- four monotonic stack passes
-   **Space:** O(N) -- PSE/NSE/PGE/NGE arrays + stack
-   **Edge cases:**
    -   Single element → range = 0 for every subarray
    -   All equal → every range = 0, answer = 0
    -   Two elements → one subarray with range = |a-b|

Thus is remembered the saga of **Sum of Subarray Ranges**,
where the Oracle decomposed the sum of all ranges
into the sum of all maximums minus the sum of all minimums --
computing each with monotonic stacks
that found how many subarrays each element ruled as king or knelt as servant --
multiplying contribution by span,
subtracting valleys from peaks,
and revealing the total gap between
every summit and every depth
across every subarray in the realm. 🏔️🕳️✨
