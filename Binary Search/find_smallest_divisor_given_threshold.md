## 🔢⚖️ _The Gentle Blade of Division: The Find the Smallest Divisor Given a Threshold Saga_

> \_"In the Kingdom of Heavy Numbers,
> each value carried a great weight.
>
> The Oracle was commanded:
>
> **'Choose a divisor.
> Divide every number by it, rounding UP.
> The sum of all these rounded results
> must not exceed the sacred threshold.
> Find the SMALLEST such divisor.'**
>
> Too small a divisor -- and the rounded results stayed large,
> the sum exploding past the threshold.
> Too large a divisor -- and everything shrank to 1,
> the sum easily within bounds but the divisor wastefully big.
>
> The Oracle recognized the ancient pattern:
>
> **Binary search on the answer space.**
>
> The minimum divisor was `1` --
> dividing by 1 changes nothing, sum stays maximum.
> The maximum divisor was `max(nums)` --
> every number becomes 1, sum equals n.
>
> For any candidate divisor, compute the sum of ceilings.
> If the sum fits within the threshold -- try smaller.
> If it doesn't -- try larger.
>
> The same ritual as Koko's bananas --
> ceiling division, binary search, minimize the answer."\_

---

This is the saga of **Find the Smallest Divisor Given a Threshold**.

Given an array `nums` and an integer `threshold`:

-   Choose a positive integer divisor `d`.
-   For each element, compute `ceil(nums[i] / d)`.
-   The sum of all these results must be `<= threshold`.
-   Find the **smallest** such `d`.

It is guaranteed that a valid divisor exists (`threshold >= nums.length`).

```
Input:  nums = [1,2,5,9], threshold = 6
Output: 5

Input:  nums = [44,22,33,11,1], threshold = 5
Output: 44

Input:  nums = [21212,10101,12121], threshold = 1000000
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Binary Search on the Divisor

The answer lies in `[1, max(nums)]`.

-   At divisor `1`: every number stays itself → sum = sum(nums). Maximum possible sum.
-   At divisor `max(nums)`: every number becomes 1 → sum = n. Minimum possible sum.

As the divisor increases, the sum **decreases** (monotonic).
This monotonicity is what makes binary search work.

For any candidate divisor `d`:

```
totalSum = sum of ceil(nums[i] / d) for all i
         = sum of (nums[i] + d - 1) / d for all i

If totalSum <= threshold  →  d is valid, try smaller
If totalSum > threshold   →  d is too small, try larger
```

```
Time:  O(N × log(max(nums)))
Space: O(1)
```

---

### 📜 The Scroll of the Gentle Blade

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🔍 The Feasibility Check -- Does This Divisor Fit?

```cpp
bool canDivide(vector<int>& nums, int threshold, int divisor) {
    int sum = 0;
    for (int x : nums) {
        sum += (x + divisor - 1) / divisor;
    }
    return sum <= threshold;
}
```

For each number, compute the ceiling division:
`(x + divisor - 1) / divisor` = `ceil(x / divisor)` in integer math.

Sum all the results. If within threshold -- the divisor works.

> _"A pile of 9 divided by 5:
> (9 + 5 - 1) / 5 = 13 / 5 = 2.
> Ceiling of 9/5 = 2. Correct."_

---

## ⚔️ The Oracle's Divisor-Finding Ritual

```cpp
int smallestDivisor(vector<int>& nums, int threshold) {
    int left = 1;
    int right = *max_element(nums.begin(), nums.end());
    int answer = right;
```

The search space:

-   `left = 1` -- smallest possible divisor.
-   `right = max(nums)` -- at this divisor every element becomes 1.
-   `answer = right` -- worst case, always valid.

---

### 🔁 Halve the Range

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle tested the middle divisor.

---

### ✅ Sum Fits -- Try Smaller Divisor

```cpp
        if (canDivide(nums, threshold, mid)) {
            answer = mid;
            right = mid - 1;
        }
```

The sum of ceilings was within the threshold.
Record `mid`, then search for an even smaller valid divisor.

---

### ❌ Sum Too Large -- Need Bigger Divisor

```cpp
        else {
            left = mid + 1;
        }
    }
    return answer;
}
```

The divisor was too small -- numbers stayed too large after division.
Push `left` upward to try a bigger blade.

---

### 🎺 The Trial of the Gentle Blade

```cpp
int main() {
    vector<int> nums1 = {1, 2, 5, 9};
    cout << smallestDivisor(nums1, 6) << endl; // expected: 5

    vector<int> nums2 = {44, 22, 33, 11, 1};
    cout << smallestDivisor(nums2, 5) << endl; // expected: 44

    vector<int> nums3 = {21212, 10101, 12121};
    cout << smallestDivisor(nums3, 1000000) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `[1, 2, 5, 9]`, threshold = 6:**

Search range: `left = 1`, `right = 9`.

**Simulation at divisor = 5:**
`ceil(1/5)=1, ceil(2/5)=1, ceil(5/5)=1, ceil(9/5)=2` → sum = 5 <= 6. **Feasible.**

**Simulation at divisor = 4:**
`ceil(1/4)=1, ceil(2/4)=1, ceil(5/4)=2, ceil(9/4)=3` → sum = 7 > 6. **Not feasible.**

| Step | left | right | mid | Sum of ceilings | Feasible? | Action           |
| ---- | ---- | ----- | --- | --------------- | --------- | ---------------- |
| 1    | 1    | 9     | 5   | 1+1+1+2 = 5    | Yes       | ans=5, right=4   |
| 2    | 1    | 4     | 2   | 1+1+3+5 = 10   | No        | left=3           |
| 3    | 3    | 4     | 3   | 1+1+2+3 = 7    | No        | left=4           |
| 4    | 4    | 4     | 4   | 1+1+2+3 = 7    | No        | left=5           |
| 5    | 5    | 4     | --  | left > right    | --        | Exit             |

**Answer: 5** ✓

---

**Full trace for `[44, 22, 33, 11, 1]`, threshold = 5:**

Search range: `left = 1`, `right = 44`.

At threshold = 5 with 5 elements, each element must contribute
at most 1 to the sum → every `ceil(x/d)` must be 1 → `d >= max(nums) = 44`.

| Step | left | right | mid | Sum of ceilings | Feasible? | Action           |
| ---- | ---- | ----- | --- | --------------- | --------- | ---------------- |
| 1    | 1    | 44    | 22  | 2+1+2+1+1 = 7  | No        | left=23          |
| 2    | 23   | 44    | 33  | 2+1+1+1+1 = 6  | No        | left=34          |
| 3    | 34   | 44    | 39  | 2+1+1+1+1 = 6  | No        | left=40          |
| 4    | 40   | 44    | 42  | 2+1+1+1+1 = 6  | No        | left=43          |
| 5    | 43   | 44    | 43  | 2+1+1+1+1 = 6  | No        | left=44          |
| 6    | 44   | 44    | 44  | 1+1+1+1+1 = 5  | Yes       | ans=44, right=43 |
| 7    | 44   | 43    | --  | left > right    | --        | Exit             |

**Answer: 44** ✓

Only when the divisor equals the largest element
does every number shrink to 1, making the sum exactly n = 5.

---

**Trace for `[21212, 10101, 12121]`, threshold = 1000000:**

At divisor = 1: sum = 21212 + 10101 + 12121 = 43434 <= 1000000. **Feasible.**

Binary search converges to `left = 1` immediately.

**Answer: 1** ✓ — threshold is so generous that dividing by 1 already works.

---

## 🔄 This Problem Is Koko's Bananas in Disguise

| Smallest Divisor           | Koko Eating Bananas          |
| -------------------------- | ---------------------------- |
| nums = values              | piles = banana counts        |
| divisor = d                | speed = k                    |
| ceil(x / d) per element    | ceil(pile / k) per pile      |
| Sum of ceilings <= threshold | Total hours <= h           |
| Minimize divisor           | Minimize speed               |

The feasibility check is identical -- ceiling division summed up.
The binary search structure is identical.
Only the story and the direction of "minimize" differ slightly
(Koko minimizes speed, here we minimize divisor -- same thing).

---

### 🧠 Memory of the Gentle Blade Law

-   **Binary search on the answer** -- range `[1, max(nums)]`
-   **Feasibility check:** sum of `ceil(nums[i] / d)` for all i
    -   Ceiling division: `(x + d - 1) / d`
    -   If sum <= threshold → divisor is valid
-   **Binary search logic:**
    -   Feasible → `answer = mid`, `right = mid - 1` (try smaller)
    -   Not feasible → `left = mid + 1` (need bigger divisor)
-   **Monotonic property:** larger divisor → smaller sum (always)
-   **Identical to Koko's Bananas** -- same ceiling-division-sum pattern
-   **Time:** O(N × log(max(nums)))
-   **Space:** O(1)
-   **Edge cases:**
    -   threshold >= sum(nums) → divisor = 1 works
    -   threshold = n → divisor = max(nums) (every element becomes 1)

Thus is remembered the saga of **Find the Smallest Divisor Given a Threshold**,
where the Oracle searched for the gentlest blade --
the smallest divisor that could cut every number down
so their rounded-up remainders summed within the sacred limit --
testing each candidate with ceiling division,
halving the range with binary search,
until the sharpest-yet-smallest blade was found
that kept the kingdom's burden beneath the threshold. 🔢⚖️✨
