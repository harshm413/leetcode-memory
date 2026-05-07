## 📈✖️ _The Double-Edged Blade: The Maximum Product Subarray Saga_

> \_"The Oracle was given an array of integers.
>
> She was commanded:
>
> **'Find the contiguous subarray with the largest PRODUCT.'**
>
> This was NOT Kadane's Algorithm with multiplication.
> Products behave differently from sums:
>
> **A negative × negative = positive.**
>
> A huge negative product can become the MAXIMUM
> if it meets another negative number.
> You can't just drop negatives like Kadane's drops negative sums.
>
> The Oracle needed to track TWO values at each position:
>
> **`maxProd`** — the maximum product ending here.
> **`minProd`** — the minimum product ending here.
>
> Why the minimum? Because the minimum (most negative) product
> could become the maximum after multiplying by a negative number.
>
> **Negatives are not enemies — they're opportunities in disguise.**"\_

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

## 🧠 Why Kadane's Doesn't Work Directly

In Maximum Subarray (sum), a negative prefix is always bad.
Drop it and start fresh. Simple.

In Maximum Product, a negative prefix is NOT always bad:

```
nums = [-2, 3, -4]

After -2: product = -2. Negative. Kadane's would "drop" it.
After 3:  product = -6. Still negative. Kadane's drops again.
After -4: product = 24! The two negatives multiplied to make a huge positive.
```

If we dropped the negative prefix, we'd miss the answer.

**The problem:** negatives can FLIP. A minimum can become a maximum.

---

## 🧠 The Two-Tracker Insight

At each position, track BOTH:

**`maxProd`** = maximum product of any subarray ending here.
**`minProd`** = minimum product of any subarray ending here.

Why both? Because when we multiply by a NEGATIVE number:
-   `maxProd × negative` becomes very negative (new min).
-   `minProd × negative` becomes very positive (new max!).

The max and min SWAP roles when hitting a negative.

**At each element, three candidates for the new max:**
1. `maxProd × nums[i]` — extend the best subarray.
2. `minProd × nums[i]` — the worst becomes the best (negative flip).
3. `nums[i]` alone — start fresh.

Same three candidates for the new min (just take min instead of max).

---

## 🧠 The Role of Zero

Zero kills any product. `anything × 0 = 0`.

When we hit a zero:
-   `maxProd × 0 = 0`, `minProd × 0 = 0`, `nums[i] = 0`.
-   Both max and min reset to 0.
-   The subarray effectively restarts after the zero.

This is handled naturally by the three-candidate approach.
`nums[i] = 0` is one of the candidates, and it wins when
both `maxProd × 0` and `minProd × 0` are also 0.

---

### 📜 The Scroll of the Double-Edged Blade

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ✖️ The Two-Tracker Solution

### Initialize

```cpp
int maxProduct(vector<int>& nums) {
    int maxProd = nums[0];
    int minProd = nums[0];
    int result = nums[0];
```

All three start at `nums[0]`.
The first element is both the max and min product so far.
`result` tracks the global best across all positions.

---

### Walk the array

```cpp
    for (int i = 1; i < nums.size(); i++) {
```

---

### Save maxProd before overwriting

```cpp
        int tempMax = maxProd;
```

We need the OLD `maxProd` to compute `minProd`.
But we're about to overwrite `maxProd`.
Save it first.

> _"The Oracle saves the old maximum before computing the new one.
> Both the new max and new min need the old max's value.
> Without saving, the min calculation would use the already-updated max."_

---

### Compute new maxProd — three candidates

```cpp
        maxProd = max({nums[i], maxProd * nums[i], minProd * nums[i]});
```

**`nums[i]`** — start a fresh subarray here.
**`maxProd × nums[i]`** — extend the best subarray.
**`minProd × nums[i]`** — the negative flip: worst becomes best.

Take the maximum of all three.

> _"Three futures compete:
> start fresh, extend the best, or let the worst flip to the best.
> The largest wins."_

---

### Compute new minProd — same three candidates

```cpp
        minProd = min({nums[i], tempMax * nums[i], minProd * nums[i]});
```

**Note: `tempMax`** — we use the SAVED old maxProd, not the new one.

Same three candidates, but take the MINIMUM.
The minimum is kept alive because it might flip to a maximum later.

> _"The minimum is not discarded — it's preserved.
> A deeply negative product is one negative number away
> from becoming the answer."_

---

### Update global result

```cpp
        result = max(result, maxProd);
    }
    return result;
}
```

---

### 🎺 The Trial of the Double-Edged Blade

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

| i | nums[i] | Candidates (max) | maxProd | Candidates (min) | minProd | result |
|---|---------|-------------------|---------|-------------------|---------|--------|
| 0 | 2       | —                 | 2       | —                 | 2       | 2      |
| 1 | 3       | 3, 2×3=6, 2×3=6  | **6**   | 3, 2×3=6, 2×3=6  | **3**   | **6**  |
| 2 | -2      | -2, 6×-2=-12, 3×-2=-6 | **-2** | -2, 6×-2=-12, 3×-2=-6 | **-12** | 6 |
| 3 | 4       | 4, -2×4=-8, -12×4=-48 | **4** | 4, -2×4=-8, -12×4=-48 | **-48** | 6 |

**Answer: 6** ✓ (subarray [2, 3])

At index 2: the negative -2 flips maxProd to -2 and minProd to -12.
At index 3: minProd × 4 = -48 (even worse), but maxProd = 4 (fresh start).
The best was 6 from [2,3], captured at index 1.

---

**Trace for nums = [-2, 3, -4]:**

| i | nums[i] | maxProd | minProd | result |
|---|---------|---------|---------|--------|
| 0 | -2      | -2      | -2      | -2     |
| 1 | 3       | max(3, -2×3=-6, -2×3=-6) = **3** | min(3, -6, -6) = **-6** | 3 |
| 2 | -4      | max(-4, 3×-4=-12, -6×-4=**24**) = **24** | min(-4, -12, 24) = **-12** | **24** |

**Answer: 24** ✓

The magic moment: at index 2, `minProd × nums[i] = -6 × -4 = 24`.
The most negative product flipped to the most positive.
This is exactly why we track the minimum.

---

**Trace for nums = [-2, 0, -1]:**

| i | nums[i] | maxProd | minProd | result |
|---|---------|---------|---------|--------|
| 0 | -2      | -2      | -2      | -2     |
| 1 | 0       | max(0, 0, 0) = **0** | min(0, 0, 0) = **0** | 0 |
| 2 | -1      | max(-1, 0, 0) = **0** | min(-1, 0, 0) = **-1** | 0 |

**Answer: 0** ✓

Zero resets both trackers. After the zero, the best we can do is 0 itself.

---

## 🔍 Maximum Sum vs Maximum Product

| Aspect | Max Subarray Sum | Max Subarray Product |
|--------|-----------------|---------------------|
| Track | `curr` (one value) | `maxProd` AND `minProd` (two values) |
| Negative element | Always bad (drop) | Might flip min to max |
| Zero element | Doesn't exist in sum context | Kills the product, resets both |
| Reset condition | `sum < 0` → reset to 0 | Handled by `nums[i]` candidate |
| Core operation | `max(nums[i], curr + nums[i])` | `max(nums[i], max*nums[i], min*nums[i])` |

The product version needs the min tracker because negatives flip signs.
The sum version doesn't — a negative sum is always bad.

---

## 🔍 Why Save tempMax?

```cpp
int tempMax = maxProd;
maxProd = max({nums[i], maxProd * nums[i], minProd * nums[i]});
minProd = min({nums[i], tempMax * nums[i], minProd * nums[i]});
//                       ↑ uses OLD maxProd, not the new one
```

Without saving: `minProd` would use the ALREADY UPDATED `maxProd`.
That's wrong — both new values should be computed from the OLD values.

Alternative: compute both into temps, then assign:
```cpp
int newMax = max({nums[i], maxProd * nums[i], minProd * nums[i]});
int newMin = min({nums[i], maxProd * nums[i], minProd * nums[i]});
maxProd = newMax;
minProd = newMin;
```

Both approaches work. The `tempMax` version saves one variable.

---

### 🧠 Memory of the Double-Edged Blade Law

-   **Track TWO values:** `maxProd` and `minProd` at each position
-   **Three candidates each:** `nums[i]`, `maxProd × nums[i]`, `minProd × nums[i]`
-   **Why min?** Negative × negative = positive. Min can become max.
-   **Save `tempMax`** before overwriting (both need old values)
-   **Zero resets both** — handled naturally by the `nums[i]` candidate
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Maximum Product Subarray**,
where the Oracle tracked both the maximum and minimum products —
knowing that negatives were not enemies but opportunities,
that the most negative product was one flip away
from becoming the answer —
three candidates at every step,
the double-edged blade of multiplication
cutting both ways through the array. 📈✖️✨
