## 🔥📜 _The Burning Path of Strength: The Maximum Subarray Saga_

> \_"In the Valley of Numbers,
> a path of integers stretched endlessly —
> some stones burned with strength,
> others drained the soul with bitter loss.
>
> The Oracle was summoned with a fierce decree:
>
> **'Find the contiguous path
> whose total strength is the greatest of all.
> The path may begin anywhere.
> It may end anywhere.
> But it must be continuous —
> a single, unbroken march through the valley.'**
>
> She could try every possible start and end —
> but that was O(N²) at best. The valley was vast.
>
> Then she remembered the ancient teaching
> of the sage **Kadane** —
> a law so elegant it reduced the problem
> to a single walk:
>
> **'Walk the valley. Keep a running sum.
> If the sum is positive — it helps the future. Carry it.
> If the sum turns negative — it hurts the future. Drop it.
> Always remember the best sum you've ever seen.'**
>
> One walk. Two variables. The mightiest path revealed."\_

---

This is the saga of **Maximum Subarray**.

You are given an integer array `nums`.
Find the **maximum possible sum** of any **contiguous subarray**.
Numbers may be positive, negative, or zero.
The subarray must contain at least one element.

```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6   (subarray [4, -1, 2, 1])

Input:  [5, 4, -1, 7, 8]
Output: 23  (the entire array)

Input:  [-3, -2, -5]
Output: -2  (least negative — must pick at least one)
```

---

## 🧠 The Oracle's Core Insight — Drop the Burden, Carry the Strength

Walk the array. Maintain a running `sum`.

At each element, add it to `sum`.
If `sum` is the best we've ever seen — update `maxi`.
If `sum` drops below 0 — reset to 0 and start fresh.

Why reset at 0? A negative prefix can only HURT any future subarray.
Starting fresh is always better than carrying a negative sum forward.

This is **Kadane's Algorithm**.

---

### 📜 The Scroll of the Burning Valley

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## ⚔️ The Oracle's Kadane Ritual

### Initialize

```cpp
long long maxSubArray(vector<int>& nums) {
    long long maxi = LONG_MIN;
    long long sum = 0;
```

`maxi` = the greatest subarray sum ever seen. Starts at `LONG_MIN`
so that even a single negative element will beat it.

`sum` = the running sum of the current subarray. Starts at 0
because no elements have been added yet.

> _"Why LONG_MIN and not 0? Because if all numbers are negative,
> we still need to return the least negative one.
> LONG_MIN ensures any real element will update maxi."_

---

### Walk the valley

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

---

### Add the current element to the running sum

```cpp
        sum += nums[i];
```

Extend the current subarray by including `nums[i]`.

---

### Update the global maximum

```cpp
        if (sum > maxi) {
            maxi = sum;
        }
```

If the current running sum is the best we've ever seen — remember it.

This is checked BEFORE the reset below. Why? Because even if `sum`
is about to go negative, the value RIGHT NOW might be the best.
We capture the peak before dropping.

---

### If sum goes negative — reset

```cpp
        if (sum < 0) {
            sum = 0;
        }
    }
```

A negative sum means the subarray ending here is a net loss.
Carrying it forward would only hurt future subarrays.
Drop everything. Start fresh from the next element.

> _"The Oracle checks: 'Is my accumulated path a burden?'
> If the sum has turned negative — she drops it.
> A fresh start from the next stone is always better
> than dragging a negative weight."_

---

```cpp
    return maxi;
}
```

---

### 🎺 The Trial of the Burning Valley

```cpp
int main() {
    vector<int> nums1 = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << maxSubArray(nums1) << endl; // expected: 6

    vector<int> nums2 = {5, 4, -1, 7, 8};
    cout << maxSubArray(nums2) << endl; // expected: 23

    vector<int> nums3 = {-3, -2, -5};
    cout << maxSubArray(nums3) << endl; // expected: -2

    return 0;
}
```

---

**Full trace for `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`:**

| i | nums[i] | sum after add | maxi | sum < 0? | sum after reset |
|---|---------|---------------|------|----------|-----------------|
| 0 | -2      | -2            | -2   | Yes → reset | 0 |
| 1 | 1       | 1             | 1    | No       | 1 |
| 2 | -3      | -2            | 1    | Yes → reset | 0 |
| 3 | 4       | 4             | 4    | No       | 4 |
| 4 | -1      | 3             | 4    | No       | 3 |
| 5 | 2       | 5             | 5    | No       | 5 |
| 6 | 1       | 6             | **6**| No       | 6 |
| 7 | -5      | 1             | 6    | No       | 1 |
| 8 | 4       | 5             | 6    | No       | 5 |

**Answer: 6** ✓ (subarray [4, -1, 2, 1])

At index 0: sum = -2, negative → reset. Fresh start.
At index 2: sum = -2, negative → reset. Fresh start.
At index 3: sum = 4, new subarray begins here.
Indices 3-6: sum grows to 6 — the peak. maxi captures it.
Index 7: -5 hurts but sum stays positive (1). No reset.
The path continues but never beats 6 again.

---

**Full trace for `[5, 4, -1, 7, 8]`:**

| i | nums[i] | sum | maxi |
|---|---------|-----|------|
| 0 | 5       | 5   | 5    |
| 1 | 4       | 9   | 9    |
| 2 | -1      | 8   | 9    |
| 3 | 7       | 15  | 15   |
| 4 | 8       | 23  | **23** |

**Answer: 23** ✓ (entire array)

Sum never goes negative. The Oracle never resets. One continuous path.

---

**Trace for `[-3, -2, -5]`:**

| i | nums[i] | sum | maxi | Reset? |
|---|---------|-----|------|--------|
| 0 | -3      | -3  | -3   | Yes → 0 |
| 1 | -2      | -2  | **-2** | Yes → 0 |
| 2 | -5      | -5  | -2   | Yes → 0 |

**Answer: -2** ✓

Every element is negative. Sum resets after every element.
But `maxi` captures each element individually before the reset.
The least negative (-2) wins. This is why `maxi = LONG_MIN` — not 0.

---

## 🔍 Why Dropping a Negative Sum Is Always Safe

This is the most common doubt about Kadane's:

> _"If sum from 0..j was positive, then adding j+1 makes it negative,
> and we reset — but what if the max subarray was i..end
> (starting somewhere in the middle of what we dropped)?"_

**It can't be.** Here's the proof:

When `sum` goes negative at position `j+1`, it means:
```
sum(0..j+1) < 0
```

Now consider ANY starting point `i` where `0 ≤ i ≤ j+1`:
```
sum(i..j+1) = sum(0..j+1) - sum(0..i-1)
```

`sum(0..j+1)` is negative (that's why we reset).
`sum(0..i-1)` is non-negative (if it were negative, we would have
reset EARLIER — before reaching `i`).

So: `sum(i..j+1) = negative - non-negative = STILL NEGATIVE`.

**Every subarray ending at j+1 has a negative sum.** All of them.

Now what about `sum(i..end)` where `end > j+1`?
```
sum(i..end) = sum(i..j+1) + sum(j+2..end)
              ← negative →   ← whatever →
```

Since `sum(i..j+1)` is negative, including it makes things WORSE:
```
sum(j+2..end) > sum(i..end)   always
```

**Starting fresh from j+2 is ALWAYS better than keeping any prefix
that includes the negative portion.** That's exactly what the reset does.

> _"A negative prefix is dead weight.
> No matter where you started within it,
> dropping it and starting fresh gives a better result.
> The proof: every subarray through the negative point
> is worse than the subarray that skips it."_

---

## 🔍 The DP Interpretation

Kadane's is really DP compressed into O(1) space:

```
dp[i] = max subarray sum ending at index i
dp[i] = max(nums[i], dp[i-1] + nums[i])
answer = max(dp[0], dp[1], ..., dp[n-1])
```

Our code does the same thing:
-   `sum += nums[i]` is `dp[i-1] + nums[i]`.
-   `if (sum < 0) sum = 0` is equivalent to `max(nums[i], dp[i-1] + nums[i])`
    because resetting to 0 means the next element starts fresh.
-   `maxi` tracks the max across all dp values.

---

### 🧠 Memory of the Burning Path Law

-   **Kadane's Algorithm** — DP compressed into O(1) space
-   **`sum`** = running sum of current subarray
-   **`maxi`** = best sum ever seen (initialized to `LONG_MIN`)
-   **At each step:** add element, update maxi, reset if negative
-   **Update maxi BEFORE reset** — capture the peak before dropping
-   **Why reset is safe:** every subarray through a negative prefix
    is worse than starting fresh after it (proven above)
-   **All-negative arrays:** maxi captures each element individually,
    returns the least negative
-   **Time:** O(N) — single pass. **Space:** O(1) — two variables.

Thus is remembered the saga of **Maximum Subarray**,
where the Oracle walked a valley of gains and losses
carrying two torches —
one for the running sum,
one for the greatest sum ever witnessed —
resetting the moment the path turned negative,
knowing that a fresh start always beats
dragging a burden from the past —
and uncovering the single contiguous path
that burned brighter than all others
in a single, unstoppable pass. 🔥📜✨
