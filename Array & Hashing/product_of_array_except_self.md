## 🌊🐚 _The Tide of Silent Products: The Product of Array Except Self Saga_

> \_"Along the endless shore,
> a line of shells lay in the sand —
> each bearing a number,
> each holding a secret.
>
> The Oracle was summoned with a curious decree:
>
> **'For every shell on this shore,
> tell me the product of ALL other shells —
> every number multiplied together,
> except the shell itself.'**
>
> The naive approach was obvious:
> multiply everything, then divide by each shell's value.
> But the Queen forbade division —
> for when a zero washed ashore,
> division would shatter the calculation.
>
> The Oracle studied the shore.
>
> Then she saw the truth hidden in the tides:
>
> **Every shell sits between two worlds —
> the tide that came BEFORE it (the left product),
> and the tide that follows AFTER it (the right product).
>
> The product of all shells except this one
> is simply: left product × right product.**
>
> She devised a two-pass ritual:
>
> **Pass One — The Incoming Tide (Left to Right):**
> Walk the shore from left to right.
> At each shell, record the product of everything to its left.
>
> **Pass Two — The Outgoing Tide (Right to Left):**
> Walk the shore from right to left.
> At each shell, multiply the stored left product
> by the product of everything to its right.
>
> Two tides. No division. Every shell answered."\_

---

This is the saga of **Product of Array Except Self**.

You are given an integer array `nums`.

Your task:

-   Return an array `result` where `result[i]`
    equals the product of all elements in `nums` except `nums[i]`.
-   You must NOT use the division operator.
-   O(N) time. O(1) extra space (output array doesn't count).

```
Input:  [1, 2, 3, 4]
Output: [24, 12, 8, 6]

Input:  [-1, 1, 0, -3, 3]
Output: [0, 0, 9, 0, 0]
```

---

## 🧠 The Oracle's Core Insight — Left Product × Right Product

For any index `i`:

```
result[i] = (product of all elements to the LEFT of i)
          × (product of all elements to the RIGHT of i)
```

Neither the left product nor the right product includes `nums[i]` itself.
Their multiplication gives the product of everything except `nums[i]`.

The Oracle computes this in two passes using the result array itself:

```
Pass 1 (left to right): result[i] = prefix product of nums[0..i-1]
Pass 2 (right to left): result[i] *= suffix product of nums[i+1..n-1]
```

No division. No extra arrays. Zeros handled naturally —
a zero in the left or right product simply makes the result zero,
which is exactly correct.

---

### 📜 The Scroll of the Two Tides

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🌊 The Oracle's Two-Tide Ritual

```cpp
vector<int> productExceptSelf(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, 1);
```

The Oracle prepared the result array —
every position initialized to `1`,
the neutral element of multiplication.

Each position would first absorb the incoming tide (left product),
then be multiplied by the outgoing tide (right product).

---

## ➡️ Pass One — The Incoming Tide (Left to Right)

_Record the product of everything to the left_

```cpp
    int prefix = 1;
    for (int i = 0; i < n; i++) {
        result[i] = prefix;
        prefix *= nums[i];
    }
```

The Oracle walked the shore from left to right.

`prefix` accumulated the running product of all elements seen so far.

At each position `i`:
1. `result[i] = prefix` — store the product of everything to the left.
2. `prefix *= nums[i]` — absorb the current shell into the running product
   for the benefit of future positions.

For the first shell (`i=0`), `prefix` was `1` —
nothing existed to its left, so the left product was `1`.

After this pass, `result[i]` held the product of `nums[0] × nums[1] × ... × nums[i-1]`.

> _"The incoming tide carries the weight
> of every shell it has already passed."_

---

## ⬅️ Pass Two — The Outgoing Tide (Right to Left)

_Multiply by the product of everything to the right_

```cpp
    int suffix = 1;
    for (int i = n - 1; i >= 0; i--) {
        result[i] *= suffix;
        suffix *= nums[i];
    }
```

The Oracle turned and walked back from right to left.

`suffix` accumulated the running product of all elements to the right.

At each position `i`:
1. `result[i] *= suffix` — multiply the stored left product by the right product.
2. `suffix *= nums[i]` — absorb the current shell for future positions.

For the last shell (`i=n-1`), `suffix` was `1` —
nothing existed to its right.

After this pass, `result[i]` held:
`(product of left) × (product of right)` = product of everything except `nums[i]`.

> _"The outgoing tide carries the weight
> of every shell it has yet to reach."_

---

## 🏁 Return the Answered Shore

```cpp
    return result;
}
```

Every shell now knew the product of all others.
No division was used. Zeros were handled naturally.

---

### 🎺 The Trial of the Shore

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3, 4};
    vector<int> ans1 = productExceptSelf(nums1);
    for (int x : ans1) cout << x << " ";
    cout << endl; // expected: 24 12 8 6

    vector<int> nums2 = {-1, 1, 0, -3, 3};
    vector<int> ans2 = productExceptSelf(nums2);
    for (int x : ans2) cout << x << " ";
    cout << endl; // expected: 0 0 9 0 0

    return 0;
}
```

---

**Full trace for `[1, 2, 3, 4]`:**

**Pass One — Incoming Tide (left to right):**

| i | prefix (before) | result[i] = prefix | prefix *= nums[i] | result after |
| - | --------------- | ------------------ | ------------------ | ------------ |
| 0 | 1               | 1                  | 1 × 1 = 1         | [1, 1, 1, 1] |
| 1 | 1               | 1                  | 1 × 2 = 2         | [1, 1, 1, 1] |
| 2 | 2               | 2                  | 2 × 3 = 6         | [1, 1, 2, 1] |
| 3 | 6               | 6                  | 6 × 4 = 24        | [1, 1, 2, 6] |

After Pass One: `result = [1, 1, 2, 6]` (left products).

**Pass Two — Outgoing Tide (right to left):**

| i | suffix (before) | result[i] *= suffix | suffix *= nums[i] | result after    |
| - | --------------- | ------------------- | ------------------ | --------------- |
| 3 | 1               | 6 × 1 = 6          | 1 × 4 = 4         | [1, 1, 2, 6]   |
| 2 | 4               | 2 × 4 = 8          | 4 × 3 = 12        | [1, 1, 8, 6]   |
| 1 | 12              | 1 × 12 = 12        | 12 × 2 = 24       | [1, 12, 8, 6]  |
| 0 | 24              | 1 × 24 = 24        | 24 × 1 = 24       | [24, 12, 8, 6] |

**Answer: [24, 12, 8, 6]** ✓

Verification: `2×3×4=24`, `1×3×4=12`, `1×2×4=8`, `1×2×3=6`.

---

**Full trace for `[-1, 1, 0, -3, 3]`:**

**Pass One — Incoming Tide:**

| i | prefix | result[i] | prefix after |
| - | ------ | --------- | ------------ |
| 0 | 1      | 1         | -1           |
| 1 | -1     | -1        | -1           |
| 2 | -1     | -1        | 0            |
| 3 | 0      | 0         | 0            |
| 4 | 0      | 0         | 0            |

After Pass One: `result = [1, -1, -1, 0, 0]`.

**Pass Two — Outgoing Tide:**

| i | suffix | result[i] *= suffix | suffix after |
| - | ------ | ------------------- | ------------ |
| 4 | 1      | 0 × 1 = 0          | 3            |
| 3 | 3      | 0 × 3 = 0          | -9           |
| 2 | -9     | -1 × -9 = 9        | 0            |
| 1 | 0      | -1 × 0 = 0         | 0            |
| 0 | 0      | 1 × 0 = 0          | 0            |

**Answer: [0, 0, 9, 0, 0]** ✓

The zero at index 2 made every other position's product zero —
except index 2 itself, whose left and right products
skipped the zero entirely: `(-1) × 1 × (-3) × 3 = 9`.

---

### 🧠 Memory of the Two-Tide Law

-   **No division allowed** — the two-pass prefix/suffix approach avoids it entirely
-   **Pass 1 (left to right):** `result[i] = prefix`, then `prefix *= nums[i]`
    -   Stores the product of all elements to the left of `i`
-   **Pass 2 (right to left):** `result[i] *= suffix`, then `suffix *= nums[i]`
    -   Multiplies by the product of all elements to the right of `i`
-   After both passes: `result[i] = leftProduct × rightProduct = product of all except nums[i]`
-   **Zeros handled naturally** — a zero in the prefix or suffix makes the product zero, which is correct
-   **Time:** O(N) — two linear passes
-   **Space:** O(1) extra — only two variables `prefix` and `suffix` (output array doesn't count)

Thus is remembered the saga of **Product of Array Except Self**,
where the Oracle walked the shore twice —
once with the incoming tide gathering the left,
once with the outgoing tide gathering the right —
and at every shell, the two tides met
to reveal the product of all others,
without ever dividing,
without ever faltering at zero. 🌊🐚✨
