## 🪟🔢 _The Odd Census: The Count Number of Nice Subarrays Saga_

> \_"The Oracle was given an array and a number K.
>
> She was commanded:
>
> **'Count the number of subarrays with EXACTLY K odd numbers.'**
>
> The Oracle recognized the pattern immediately:
>
> **Same as Binary Subarrays With Sum.**
>
> Treat each element as 1 (odd) or 0 (even).
> 'Exactly K odd numbers' = 'sum of parities = K.'
>
> **exactly(K) = atMost(K) - atMost(K-1)**
>
> Same trick. Same code structure.
> Just count odd numbers instead of summing values."\_

---

This is the saga of **Count Number of Nice Subarrays (LeetCode 1248)**.

Given an array `nums` and integer `k`:
-   Count subarrays with exactly `k` odd numbers.

```
Input:  nums = [1, 1, 2, 1, 1], k = 3
Output: 2   ([1,1,2,1] and [1,2,1,1])

Input:  nums = [2, 4, 6], k = 1
Output: 0   (no odd numbers at all)

Input:  nums = [2, 2, 2, 1, 2, 2, 1, 2, 2, 2], k = 2
Output: 16
```

---

## 🧠 The Reduction — Odd Numbers = 1, Even = 0

Each element is either odd (contributes 1) or even (contributes 0).
"Exactly K odd numbers" = "sum of (element % 2) equals K."

This is IDENTICAL to Binary Subarrays With Sum where the binary array
is `[nums[i] % 2 for each i]` and goal = K.

We don't even need to create the binary array.
Just count odds as we go: `if (nums[right] % 2 == 1) odds++`.

---

## 🧠 The atMost Trick (Same as Before)

```
exactly(K) = atMost(K) - atMost(K-1)
```

`atMost(K)` = subarrays with ≤ K odd numbers.
`atMost(K-1)` = subarrays with ≤ K-1 odd numbers.
Difference = subarrays with EXACTLY K odd numbers.

---

### 📜 The Scroll of the Odd Census

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔢 The atMost Helper

```cpp
int atMost(vector<int>& nums, int k) {
    if (k < 0) return 0;
```

If k is negative, no subarray can have ≤ negative odds. Return 0.

---

```cpp
    int left = 0, count = 0, odds = 0;
```

`odds` = count of odd numbers in the current window.

---

```cpp
    for (int right = 0; right < nums.size(); right++) {
        if (nums[right] % 2 == 1) odds++;
```

If the new element is odd, increment the odd counter.
Even elements don't affect the count.

---

### Shrink when too many odds

```cpp
        while (odds > k) {
            if (nums[left] % 2 == 1) odds--;
            left++;
        }
```

More than K odds in the window. Shrink from left.
If the element leaving is odd — one fewer odd in the window.

---

### Count valid subarrays

```cpp
        count += (right - left + 1);
    }
    return count;
}
```

Window `[left, right]` has ≤ K odds.
All `right - left + 1` subarrays ending at `right` are valid.

---

## 🔢 The Main Function

```cpp
int numberOfSubarrays(vector<int>& nums, int k) {
    return atMost(nums, k) - atMost(nums, k - 1);
}
```

Two calls. One subtraction. Done.

---

### 🎺 The Trial of the Odd Census

```cpp
int main() {
    vector<int> n1 = {1, 1, 2, 1, 1};
    cout << numberOfSubarrays(n1, 3) << endl; // expected: 2

    vector<int> n2 = {2, 4, 6};
    cout << numberOfSubarrays(n2, 1) << endl; // expected: 0

    vector<int> n3 = {2, 2, 2, 1, 2, 2, 1, 2, 2, 2};
    cout << numberOfSubarrays(n3, 2) << endl; // expected: 16

    return 0;
}
```

---

**Trace for nums = [1, 1, 2, 1, 1], k = 3:**

**atMost(3):**

| right | nums[r] | odd? | odds | odds>3? | left | count += | total |
|-------|---------|------|------|---------|------|----------|-------|
| 0 | 1 | Yes | 1 | No | 0 | 1 | 1 |
| 1 | 1 | Yes | 2 | No | 0 | 2 | 3 |
| 2 | 2 | No  | 2 | No | 0 | 3 | 6 |
| 3 | 1 | Yes | 3 | No | 0 | 4 | 10 |
| 4 | 1 | Yes | 4 | **Yes** → shrink | | | |
|   |   |     | nums[0]=1 odd, odds=3, left=1 | No | 1 | 4 | 14 |

**atMost(3) = 14**

---

**atMost(2):**

| right | nums[r] | odds | odds>2? | left | count += | total |
|-------|---------|------|---------|------|----------|-------|
| 0 | 1 | 1 | No | 0 | 1 | 1 |
| 1 | 1 | 2 | No | 0 | 2 | 3 |
| 2 | 2 | 2 | No | 0 | 3 | 6 |
| 3 | 1 | 3 | **Yes** → shrink. nums[0]=1, odds=2, left=1 | 1 | 3 | 9 |
| 4 | 1 | 3 | **Yes** → shrink. nums[1]=1, odds=2, left=2 | 2 | 3 | 12 |

**atMost(2) = 12**

---

**Answer: 14 - 12 = 2** ✓

The two nice subarrays: [1,1,2,1] (indices 0-3) and [1,2,1,1] (indices 1-4).
Both have exactly 3 odd numbers.

---

**Trace for nums = [2,2,2,1,2,2,1,2,2,2], k = 2:**

The array has odds at indices 3 and 6 only.

**atMost(2):** every subarray has ≤ 2 odds (since there are only 2 total).
All subarrays are valid. Count = N×(N+1)/2 = 10×11/2 = **55**.

**atMost(1):** subarrays with ≤ 1 odd.

The window shrinks when it contains both odds (indices 3 and 6).
After careful counting: **atMost(1) = 39**.

**Answer: 55 - 39 = 16** ✓

---

## 🔍 Binary Subarrays With Sum vs Nice Subarrays

| Aspect | Binary Subarrays (LC 930) | Nice Subarrays (this) |
|--------|--------------------------|----------------------|
| Array type | Binary (0s and 1s) | Any integers |
| What to count | Sum of elements | Count of odd elements |
| Tracking | `sum += nums[right]` | `if (nums[right] % 2) odds++` |
| Shrink | `sum -= nums[left]` | `if (nums[left] % 2) odds--` |
| Everything else | Identical | Identical |

The ONLY difference: what you track in the window.
Binary Subarrays tracks the sum directly.
Nice Subarrays tracks the count of odds (ignoring even values).

---

## 🔍 The atMost Family — All Use the Same Template

| Problem | What atMost counts | Budget |
|---------|-------------------|--------|
| Binary Subarrays With Sum | sum of elements | ≤ goal |
| **Nice Subarrays (this)** | **count of odds** | **≤ k** |
| Subarrays with K Different Integers | distinct elements | ≤ k |
| Max Consecutive Ones III | count of zeros | ≤ k |

Same template. Different "what to track." Same `atMost(K) - atMost(K-1)` trick.

---

### 🧠 Memory of the Odd Census Law

-   **Reduce:** "exactly K odds" = `atMost(K) - atMost(K-1)`
-   **atMost(K):** sliding window, track odd count, shrink when odds > K
-   **Count per step:** `right - left + 1`
-   **Odd check:** `nums[right] % 2 == 1` (or `nums[right] & 1`)
-   **Same as Binary Subarrays With Sum** — just count odds instead of sum
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Count Number of Nice Subarrays**,
where the Oracle counted odd numbers in a sliding window —
shrinking when the odd count exceeded the budget,
counting valid subarrays at each step —
then subtracted atMost(K-1) from atMost(K)
to isolate the subarrays with exactly K odds. 🪟🔢✨
