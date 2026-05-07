## 📈✖️ _The Prefix-Suffix Sweep: The Maximum Product Subarray Saga_

> \_"The Oracle was given an array of integers.
>
> She was commanded:
>
> **'Find the contiguous subarray with the largest PRODUCT.'**
>
> The Oracle saw a beautiful observation:
>
> **The maximum product subarray either STARTS from the left
> or ENDS at the right.**
>
> Why? Consider the array split by zeros.
> Within each non-zero segment, the product of the entire segment
> is either positive (take it all) or negative
> (drop one negative from either end to make it positive).
>
> So the answer is always captured by either:
> -   A prefix product (starting from the left), or
> -   A suffix product (starting from the right).
>
> Walk from both ends simultaneously.
> Reset when hitting zero. Track the maximum.
> One pass. Two running products. Done."\_

---

This is the saga of **Maximum Product Subarray (LeetCode 152)**.

Given an integer array `nums`:
-   Find the contiguous subarray with the **largest product**.

```
Input:  nums = [2, 3, -2, 4]
Output: 6   (subarray [2, 3])

Input:  nums = [-2, 0, -1]
Output: 0   (subarray [0])

Input:  nums = [-2, 3, -4]
Output: 24  (entire array: -2 × 3 × -4 = 24)
```

---

## 🧠 The Core Observation -- Why Prefix and Suffix?

Consider a non-zero segment of the array (between zeros).

**Case 1 — Even number of negatives:**
The product of the entire segment is positive.
Both prefix and suffix will capture this at some point.

**Case 2 — Odd number of negatives:**
The total product is negative. But if we drop ONE negative
from either end, the remaining product is positive.

Dropping the leftmost negative = a SUFFIX product captures it.
Dropping the rightmost negative = a PREFIX product captures it.

**Either way, the answer is a prefix or a suffix product.**

**Case 3 — Zeros in the array:**
Zeros split the array into independent segments.
When prefix or suffix hits zero, it becomes zero.
We reset to 1 and start fresh from the next element.

> _"The maximum product subarray is always reachable
> from one end or the other.
> Walk from both ends. The answer reveals itself."_

---

## 🧠 Why Reset to 1 When Hitting Zero?

Zero kills any running product. `anything × 0 = 0`.

After hitting zero, the running product is 0.
The next element should start a FRESH subarray.
Resetting to 1 means the next multiplication gives just `nums[i]` —
effectively starting a new subarray.

We check `if (prefix == 0) prefix = 1` BEFORE multiplying.
This way, the element after a zero starts its own product.

---

### 📜 The Scroll of the Prefix-Suffix Sweep

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;
```

---

## ✖️ The Prefix-Suffix Solution

### Initialize

```cpp
int maxProduct(vector<int>& nums) {
    int n = nums.size();
    double prefix = 1, suffix = 1;
    double ans = INT_MIN;
```

`prefix` = running product from the LEFT.
`suffix` = running product from the RIGHT.
Both start at 1 (multiplicative identity).

`ans` = global maximum. Starts at `INT_MIN` so any real product beats it.

Using `double` to handle potential overflow with large products.

---

### Single pass — both directions simultaneously

```cpp
    for (int i = 0; i < n; i++) {
```

One loop. Index `i` walks left-to-right for prefix.
Index `n - i - 1` walks right-to-left for suffix.

---

### Reset if previous product was zero

```cpp
        if (prefix == 0) prefix = 1;
        if (suffix == 0) suffix = 1;
```

If the running product hit zero on the previous step,
reset to 1. The next element starts a fresh subarray.

This is checked BEFORE multiplying so the current element
becomes the start of a new product chain.

> _"Zero is a wall. The product chain is broken.
> Reset to 1 — the next element begins a new chain.
> 1 is the 'empty product' — multiplying by it changes nothing."_

---

### Extend both products

```cpp
        prefix *= nums[i];
        suffix *= nums[n - i - 1];
```

`prefix` grows from the left: `nums[0] × nums[1] × ... × nums[i]`.
`suffix` grows from the right: `nums[n-1] × nums[n-2] × ... × nums[n-i-1]`.

Both are running products, reset after zeros.

---

### Track the global maximum

```cpp
        ans = max({ans, prefix, suffix});
    }
```

At every step, both prefix and suffix are candidates for the answer.
Take the maximum of all three (ans, prefix, suffix).

---

```cpp
    return (int)ans;
}
```

---

### 🎺 The Trial of the Prefix-Suffix Sweep

```cpp
int main() {
    vector<int> n1 = {2, 3, -2, 4};
    cout << maxProduct(n1) << endl; // expected: 6

    vector<int> n2 = {-2, 0, -1};
    cout << maxProduct(n2) << endl; // expected: 0

    vector<int> n3 = {-2, 3, -4};
    cout << maxProduct(n3) << endl; // expected: 24

    vector<int> n4 = {-1, -2, -3, 0, 3, 5};
    cout << maxProduct(n4) << endl; // expected: 15

    return 0;
}
```

---

**Full trace for nums = [2, 3, -2, 4]:**

| i | nums[i] | nums[n-i-1] | prefix | suffix | ans |
|---|---------|-------------|--------|--------|-----|
| 0 | 2       | 4           | 2      | 4      | 4   |
| 1 | 3       | -2          | 6      | -8     | **6** |
| 2 | -2      | 3           | -12    | -24    | 6   |
| 3 | 4       | 2           | -48    | -48    | 6   |

**Answer: 6** ✓ (prefix captured [2,3] = 6 at step 1)

---

**Trace for nums = [-2, 3, -4]:**

| i | nums[i] | nums[n-i-1] | prefix | suffix | ans |
|---|---------|-------------|--------|--------|-----|
| 0 | -2      | -4          | -2     | -4     | -2  |
| 1 | 3       | 3           | -6     | -12    | -2  |
| 2 | -4      | -2          | **24** | **24** | **24** |

**Answer: 24** ✓

Both prefix and suffix reach 24 at the last step.
Prefix: `-2 × 3 × -4 = 24`. Suffix: `-4 × 3 × -2 = 24`.
The entire array is the answer — two negatives cancel out.

---

**Trace for nums = [-2, 0, -1]:**

| i | nums[i] | nums[n-i-1] | prefix | suffix | ans |
|---|---------|-------------|--------|--------|-----|
| 0 | -2      | -1          | -2     | -1     | -1  |
| 1 | 0       | 0           | 0      | 0      | **0** |
| 2 | -1      | -2          | reset→ -1 | reset→ -2 | 0 |

**Answer: 0** ✓

Zero appears in both directions. After zero, both reset to 1,
then multiply by the next element. But 0 was already captured as ans.

---

**Trace for nums = [-1, -2, -3, 0, 3, 5]:**

| i | nums[i] | nums[n-i-1] | prefix | suffix | ans |
|---|---------|-------------|--------|--------|-----|
| 0 | -1      | 5           | -1     | 5      | 5   |
| 1 | -2      | 3           | 2      | 15     | **15** |
| 2 | -3      | 0           | -6     | 0      | 15  |
| 3 | 0       | -3          | 0      | reset→ -3 | 15 |
| 4 | 3       | -2          | reset→ 3 | 6    | 15  |
| 5 | 5       | -1          | 15     | -6     | 15  |

**Answer: 15** ✓ (suffix captured [3,5] = 15 at step 1)

---

## 🔍 Prefix-Suffix vs Min-Max Tracker

| Aspect | Min-Max Tracker | Prefix-Suffix (this) |
|--------|----------------|---------------------|
| Variables | maxProd, minProd, result | prefix, suffix, ans |
| Concept | Track best and worst at each position | Sweep from both ends |
| Handles negatives | Min flips to max | Prefix or suffix captures the flip |
| Handles zeros | Naturally via `nums[i]` candidate | Reset to 1 |
| Code complexity | Needs tempMax save | Simpler — no save needed |
| Both are | O(N) time, O(1) space | O(N) time, O(1) space |

Both approaches are valid. The prefix-suffix approach is arguably
simpler to code and explain. The min-max approach is more "DP-like."

Know both. The prefix-suffix is easier to write under pressure.

---

## 🔍 Why the Answer Is Always a Prefix or Suffix

**Proof sketch:**

Take any maximum product subarray `[l..r]`.

If it starts at index 0 → it's a prefix. Done.
If it ends at index n-1 → it's a suffix. Done.

If it's in the middle `[l..r]` where `l > 0` and `r < n-1`:
-   The product of `[0..l-1]` must be negative (otherwise extending left helps).
-   The product of `[r+1..n-1]` must be negative (otherwise extending right helps).
-   So `[0..r]` = negative × positive = negative. But `[l..r]` is positive.
-   And `[l..n-1]` = positive × negative = negative.

But the prefix `[0..r]` includes `[0..l-1]` (negative) × `[l..r]` (positive).
The suffix `[l..n-1]` includes `[l..r]` (positive) × `[r+1..n-1]` (negative).

The prefix product at `r` or the suffix product at `l` will capture
the maximum at some point during the sweep.

(Zeros are handled by the reset — each non-zero segment is independent.)

---

### 🧠 Memory of the Prefix-Suffix Sweep Law

-   **Two running products:** prefix (left→right) and suffix (right→left)
-   **Reset to 1 when product is 0** — start fresh after zeros
-   **`ans = max(ans, prefix, suffix)`** at every step
-   **Why it works:** max product subarray is always reachable from one end
-   **Even negatives:** entire segment is the answer (both ends capture it)
-   **Odd negatives:** drop one from either end (prefix or suffix captures it)
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Maximum Product Subarray (Prefix-Suffix)**,
where the Oracle swept from both ends simultaneously —
prefix growing from the left, suffix growing from the right —
resetting after every zero wall —
knowing that the maximum product was always reachable
from one end or the other,
captured in a single pass
by the twin blades of prefix and suffix. 📈✖️✨
