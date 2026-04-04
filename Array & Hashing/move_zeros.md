## 🏃‍♂️🪨 _The March of the Silent Stones: The Move Zeroes Saga_

> \_"In the Valley of Values,
> numbers marched in a long line —
> some strong and non-zero,
> others silent as stone,
> carrying the weight of nothingness.
>
> The Oracle was commanded:
>
> **'Move all silent stones — the zeroes —
> to the end of the line,
> while preserving the order
> of all living numbers.
> No extra land may be used.
> The march must happen in place.'**
>
> The Oracle studied the line.
>
> She could not create a new array —
> the decree demanded in-place transformation.
>
> She could not simply swap zeroes to the end randomly —
> the relative order of non-zero elements must be preserved.
>
> So she devised a two-phase march:
>
> **Phase One — The Compaction:**
> Walk the entire line with a scout pointer `i`.
> Maintain a builder pointer `k` at the front.
> Every time the scout finds a living number,
> place it at position `k` and advance `k`.
> Zeroes are simply skipped — left behind.
>
> After this phase, positions `0` to `k-1`
> hold all non-zero numbers in their original order.
>
> **Phase Two — The Silencing:**
> Fill every position from `k` to `n-1` with zero.
> The silent stones take their rightful place at the rear.
>
> Two passes. Two pointers. Perfect order preserved."\_

---

This is the saga of **Move Zeroes**.

You are given an integer array `nums`.

Your task:

-   Move all `0`s to the **end** of the array.
-   Keep the **relative order** of non-zero elements.
-   Do it **in-place** with no extra array.

```
Input:  [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]

Input:  [0]
Output: [0]

Input:  [1, 0, 0, 0, 5]
Output: [1, 5, 0, 0, 0]
```

---

## 🧠 The Oracle's Core Insight — Compact First, Zero-Fill After

The naive approach might be:

```
Find each zero, shift everything left to fill the gap.
Time: O(N²) — each shift costs O(N).
```

The Oracle's approach is far cleaner:

> **Treat it as two separate tasks:
> First, pull all non-zero elements to the front.
> Then, fill the rest with zeroes.**

The builder pointer `k` always marks
the next position where a non-zero element should land.

Since `k` only advances when a non-zero is placed,
and the scout `i` always moves forward,
`k <= i` at all times — so writing to `nums[k]`
never overwrites an unprocessed element ahead of `i`.

```
Time:  O(N) — two passes, each linear
Space: O(1) — only one extra variable k
```

---

### 📜 The Scroll of the Ordered March

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Two-Pointer March

```cpp
void moveZeroes(vector<int>& nums) {
    int k = 0;
```

The Oracle placed the builder `k` at position `0` —
the very front of the line.

`k` marked the next open slot
where a living number deserved to stand.

---

## 🔍 Phase One — The Compaction

_Scout the line, pull the living forward_

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

The scout `i` began walking the entire line —
inspecting every element from front to back.

---

### 🏃‍♂️ Call Forward the Living Numbers

```cpp
        if (nums[i] != 0) {
            nums[k] = nums[i];
            k++;
        }
    }
```

When the scout found a non-zero number —
a living soldier — it was immediately placed
at position `k`, the next open slot.

`k` then advanced, ready for the next living number.

When the scout found a zero —
a silent stone — it was simply ignored.
No placement. No advancement of `k`.
The stone would be dealt with in Phase Two.

After this loop, positions `0` to `k-1`
held all non-zero numbers in their original relative order.
Everything from `k` onward was leftover garbage —
about to be overwritten.

> _"The living march forward.
> The silent are left behind.
> Order is preserved because the scout
> encounters non-zero elements
> in the same order they originally stood."_

---

## 🪨 Phase Two — The Silencing

_Fill the rear with zeroes_

```cpp
    while (k < nums.size()) {
        nums[k] = 0;
        k++;
    }
}
```

The builder continued from where it stopped —
filling every remaining position with `0`.

The silent stones were placed at the rear,
one by one, until the array was complete.

No sorting. No shifting. No swapping.
Just a clean two-phase march.

---

### 🎺 The Trial of the Silent Stones

```cpp
int main() {
    vector<int> nums1 = {0, 1, 0, 3, 12};
    moveZeroes(nums1);
    for (int x : nums1) cout << x << " ";
    cout << endl; // expected: 1 3 12 0 0

    vector<int> nums2 = {0};
    moveZeroes(nums2);
    for (int x : nums2) cout << x << " ";
    cout << endl; // expected: 0

    vector<int> nums3 = {1, 0, 0, 0, 5};
    moveZeroes(nums3);
    for (int x : nums3) cout << x << " ";
    cout << endl; // expected: 1 5 0 0 0

    return 0;
}
```

---

**Full trace for `[0, 1, 0, 3, 12]`:**

**Phase One — Compaction:**

| i | nums[i] | Non-zero? | Action           | k after | Array state         |
| - | ------- | --------- | ---------------- | ------- | ------------------- |
| 0 | 0       | No        | Skip             | 0       | [0, 1, 0, 3, 12]   |
| 1 | 1       | Yes       | nums[0] = 1, k++ | 1       | [1, 1, 0, 3, 12]   |
| 2 | 0       | No        | Skip             | 1       | [1, 1, 0, 3, 12]   |
| 3 | 3       | Yes       | nums[1] = 3, k++ | 2       | [1, 3, 0, 3, 12]   |
| 4 | 12      | Yes       | nums[2] = 12, k++| 3       | [1, 3, 12, 3, 12]  |

After Phase One: `k = 3`, positions 0-2 hold `[1, 3, 12]`.

**Phase Two — Zero-fill from k=3:**

| k | Action       | Array state        |
| - | ------------ | ------------------ |
| 3 | nums[3] = 0  | [1, 3, 12, 0, 12] |
| 4 | nums[4] = 0  | [1, 3, 12, 0, 0]  |

**Answer: [1, 3, 12, 0, 0]** ✓

---

**Full trace for `[1, 0, 0, 0, 5]`:**

**Phase One — Compaction:**

| i | nums[i] | Non-zero? | Action           | k after | Array state         |
| - | ------- | --------- | ---------------- | ------- | ------------------- |
| 0 | 1       | Yes       | nums[0] = 1, k++ | 1       | [1, 0, 0, 0, 5]    |
| 1 | 0       | No        | Skip             | 1       | [1, 0, 0, 0, 5]    |
| 2 | 0       | No        | Skip             | 1       | [1, 0, 0, 0, 5]    |
| 3 | 0       | No        | Skip             | 1       | [1, 0, 0, 0, 5]    |
| 4 | 5       | Yes       | nums[1] = 5, k++ | 2       | [1, 5, 0, 0, 5]    |

After Phase One: `k = 2`, positions 0-1 hold `[1, 5]`.

**Phase Two — Zero-fill from k=2:**

| k | Action       | Array state        |
| - | ------------ | ------------------ |
| 2 | nums[2] = 0  | [1, 5, 0, 0, 5]   |
| 3 | nums[3] = 0  | [1, 5, 0, 0, 5]   |
| 4 | nums[4] = 0  | [1, 5, 0, 0, 0]   |

**Answer: [1, 5, 0, 0, 0]** ✓

---

## 🔄 Alternative — Single-Pass Swap Approach

Instead of two phases, you can swap in a single pass:

```cpp
void moveZeroes(vector<int>& nums) {
    int k = 0;
    for (int i = 0; i < nums.size(); i++) {
        if (nums[i] != 0) {
            swap(nums[i], nums[k]);
            k++;
        }
    }
}
```

When a non-zero is found at `i`, swap it with position `k`.
This places the non-zero at the front and moves the zero backward.
Same O(N) time, O(1) space — but done in one pass.

Both approaches are valid. The two-phase version is easier to reason about.
The swap version is more concise.

---

### 🧠 Memory of the Silent March Law

-   **Two-phase approach:**
    -   Phase 1: scout `i` walks the array, builder `k` collects non-zeroes at the front
    -   Phase 2: fill positions `k` to `n-1` with zeroes
-   **Single-pass swap approach:**
    -   When `nums[i] != 0`: swap `nums[i]` with `nums[k]`, advance `k`
    -   Zeroes naturally drift backward as non-zeroes swap forward
-   `k <= i` always holds — writing to `nums[k]` never corrupts unprocessed data
-   Relative order of non-zero elements is preserved because the scout encounters them left-to-right
-   **Time:** O(N) — one or two linear passes
-   **Space:** O(1) — only one extra variable `k`
-   **Edge cases:**
    -   All zeroes → Phase 1 does nothing, Phase 2 fills everything with 0
    -   No zeroes → Phase 1 copies everything in place, Phase 2 does nothing
    -   Single element → returned as-is

Thus is remembered the saga of **Move Zeroes**,
where the Oracle led a disciplined two-phase march —
first pulling every living number to the front
in the order they stood,
then silencing the rear with zeroes —
so that the strong marched ahead
and the silent stones drifted quietly
to the back of the line. 🏃‍♂️🪨✨
