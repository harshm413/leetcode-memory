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
> **'At every stone, ask one question:
> Does the path behind me make me stronger?
> If the accumulated sum is positive — carry it forward.
> If it has turned negative — abandon it.
> A negative past can only weaken the future.'**
>
> The Oracle carried two torches:
>
> **`curr`** — the strength of the current path ending here.
> **`best`** — the greatest strength ever witnessed.
>
> At every stone, she chose the stronger option:
> extend the existing path, or begin anew.
> And she always remembered the brightest moment.
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
```

---

## 🧠 The Oracle's Core Insight — Drop the Burden, Carry the Strength

At every index `i`, the Oracle faces a choice:

```
Option A: Extend the current path → curr + nums[i]
Option B: Start a new path here   → nums[i]

curr = max(nums[i], curr + nums[i])
```

If `curr` was negative before adding `nums[i]`,
then `nums[i]` alone is stronger than `curr + nums[i]`.
The old path is abandoned. A new path begins.

If `curr` was non-negative, extending is at least as good.
The old path is carried forward.

After every step, `best` remembers the greatest `curr` ever seen.

This is **Kadane's Algorithm** — a dynamic programming solution
compressed into O(1) space:

```
dp[i] = max(nums[i], dp[i-1] + nums[i])
answer = max(dp[0], dp[1], ..., dp[n-1])
```

Every subarray must end somewhere.
If we know the best subarray ending at every position,
the global best is simply the maximum among them.

---

### 📜 The Scroll of the Burning Valley

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Kadane Ritual

_Carry strength, drop weakness, remember the brightest moment_

```cpp
int maxSubArray(vector<int>& nums) {
    int curr = nums[0];
    int best = nums[0];
```

The Oracle stood at the first stone of the valley.

`curr` — the maximum sum of any subarray ending at the current position.
`best` — the global maximum across all subarrays seen so far.

Both torches were lit with the first stone's value —
for it was the only path known.

> _"Both begin at nums[0] — not at 0.
> If all numbers are negative, initializing to 0
> would falsely claim an empty subarray exists.
> The problem demands at least one element."_

---

## 🧭 Walk the Burning Path — Stone by Stone

```cpp
    for (int i = 1; i < nums.size(); i++) {
```

The Oracle began her march from the second stone onward.
The first stone had already been claimed.

---

### 🔥 The Choice — Extend or Abandon

```cpp
        curr = max(nums[i], curr + nums[i]);
```

At every stone, the Oracle asked:

> **"Does the path behind me make me stronger?"**

**If `curr + nums[i]` ≥ `nums[i]`** —
the accumulated path was still helpful.
The Oracle carried the past forward.

**If `curr + nums[i]` < `nums[i]`** —
the accumulated path had become a burden.
Starting fresh at `nums[i]` alone was stronger.
The Oracle abandoned the past without hesitation.

This single line is the entire heart of Kadane's Algorithm.

> _"A negative past can only weaken the future.
> The moment the burden outweighs the stone,
> the Oracle drops everything
> and begins a new march."_

---

### 🏆 Remember the Brightest Moment

```cpp
        best = max(best, curr);
    }
```

After every choice, the Oracle compared
the current path's strength against the greatest ever seen.

If `curr` surpassed `best` — a new champion was crowned.
If not — `best` held firm, remembering a brighter moment from the past.

---

### 🏁 Return the Mightiest Path

```cpp
    return best;
}
```

When the last stone had been judged,
`best` held the answer —
the maximum sum of any contiguous subarray in the entire valley.

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

| i | nums[i] | curr + nums[i] | nums[i] alone | curr (chosen)   | best  |
| - | ------- | --------------- | ------------- | --------------- | ----- |
| 0 | -2      | —               | —             | -2              | -2    |
| 1 | 1       | -2 + 1 = -1     | 1             | **1** (fresh)   | 1     |
| 2 | -3      | 1 + (-3) = -2   | -3            | **-2** (extend) | 1     |
| 3 | 4       | -2 + 4 = 2      | 4             | **4** (fresh)   | 4     |
| 4 | -1      | 4 + (-1) = 3    | -1            | **3** (extend)  | 4     |
| 5 | 2       | 3 + 2 = 5       | 2             | **5** (extend)  | 5     |
| 6 | 1       | 5 + 1 = 6       | 1             | **6** (extend)  | **6** |
| 7 | -5      | 6 + (-5) = 1    | -5            | **1** (extend)  | 6     |
| 8 | 4       | 1 + 4 = 5       | 4             | **5** (extend)  | 6     |

**Answer: 6** ✓

At index 1: the Oracle abandoned `-2` and started fresh at `1`.
At index 3: she abandoned `-2` again and started fresh at `4`.
From index 3 to 6: the path `[4, -1, 2, 1]` grew to sum `6` — the peak.
At index 7: `-5` hurt, but `curr` stayed positive (`1`), so the path continued.
The path never surpassed `6` again. `best` remembered the peak.

---

**Full trace for `[5, 4, -1, 7, 8]`:**

| i | nums[i] | curr + nums[i] | nums[i] alone | curr (chosen)    | best   |
| - | ------- | --------------- | ------------- | ---------------- | ------ |
| 0 | 5       | —               | —             | 5                | 5      |
| 1 | 4       | 5 + 4 = 9       | 4             | **9** (extend)   | 9      |
| 2 | -1      | 9 + (-1) = 8    | -1            | **8** (extend)   | 9      |
| 3 | 7       | 8 + 7 = 15      | 7             | **15** (extend)  | 15     |
| 4 | 8       | 15 + 8 = 23     | 8             | **23** (extend)  | **23** |

**Answer: 23** ✓

The entire array was the strongest path.
The `-1` was a minor dip — but the accumulated `9` easily absorbed it.
The Oracle never abandoned the path.

---

**Trace for `[-3, -2, -5]`:**

| i | nums[i] | curr + nums[i] | nums[i] alone | curr (chosen)    | best  |
| - | ------- | --------------- | ------------- | ---------------- | ----- |
| 0 | -3      | —               | —             | -3               | -3    |
| 1 | -2      | -3 + (-2) = -5  | -2            | **-2** (fresh)   | **-2** |
| 2 | -5      | -2 + (-5) = -7  | -5            | **-5** (fresh)   | -2    |

**Answer: -2** ✓

All numbers were negative. The Oracle correctly returned the least negative.
Because `best` was initialized to `nums[0]` — not `0` —
the algorithm never falsely claimed an empty subarray.

---

### 🧠 Memory of the Burning Path Law

-   **Kadane's Algorithm** — dynamic programming compressed into O(1) space
-   `curr` = maximum subarray sum ending at the current position
-   `best` = maximum subarray sum seen across all positions
-   **At each step:** `curr = max(nums[i], curr + nums[i])`
    -   If `curr` was negative → start fresh (abandon the past)
    -   If `curr` was non-negative → extend (carry the strength)
-   **After each step:** `best = max(best, curr)`
-   **Initialize both** `curr` and `best` to `nums[0]` — never to `0`
-   **Why not 0?** All-negative arrays must return the least negative element, not an empty sum
-   **DP interpretation:** `dp[i] = max(nums[i], dp[i-1] + nums[i])`, answer = `max(dp[0..n-1])`
-   **Time:** O(N) — single pass through the array
-   **Space:** O(1) — only two variables, no extra arrays

Thus is remembered the saga of **Maximum Subarray**,
where the Oracle walked a valley of gains and losses
carrying two torches —
one for the current path's strength,
one for the greatest strength ever witnessed —
casting aside weakening burdens the moment they turned negative,
extending the march when the past still served the future —
and uncovering the single contiguous path
that burned brighter than all others
in a single, unstoppable pass. 🔥📜✨
