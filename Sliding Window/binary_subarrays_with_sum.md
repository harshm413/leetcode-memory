## 🪟🔢 _The Exact Count Trick: The Binary Subarrays With Sum Saga_

> \_"The Oracle was given a binary array (only 0s and 1s)
> and a goal value.
>
> She was commanded:
>
> **'Count the number of subarrays whose sum equals goal.'**
>
> The Oracle knew the prefix sum + hashmap approach (like Subarray Sum = K).
> But she also knew a pure sliding window approach:
>
> **The 'atMost' trick.**
>
> Counting subarrays with sum EXACTLY equal to goal is hard
> with a sliding window — the window can't tell you 'exactly.'
>
> But counting subarrays with sum AT MOST goal is easy:
> shrink when sum > goal. Every valid window of size `w`
> contributes `w` subarrays (all subarrays ending at right).
>
> **exactly(goal) = atMost(goal) - atMost(goal - 1)**
>
> Subarrays with sum ≤ goal, minus subarrays with sum ≤ goal-1,
> equals subarrays with sum EXACTLY goal.
>
> Two sliding window passes. One subtraction. Done."\_

---

This is the saga of **Binary Subarrays With Sum (LeetCode 930)**.

Given a binary array `nums` and integer `goal`:
-   Count subarrays whose sum equals `goal`.

```
Input:  nums = [1,0,1,0,1], goal = 2
Output: 4   (subarrays: [1,0,1], [1,0,1,0], [0,1,0,1], [1,0,1])

Input:  nums = [0,0,0,0,0], goal = 0
Output: 15  (every subarray sums to 0)
```

---

## 🧠 Why Sliding Window Can't Do "Exactly" Directly

Standard sliding window shrinks when the condition is VIOLATED.
For "sum ≤ goal": shrink when sum > goal. Clear boundary.

For "sum == goal": when do you shrink?
-   If sum < goal → expand (need more).
-   If sum > goal → shrink (too much).
-   If sum == goal → found one! But... should you shrink or expand?
    Both might give more valid subarrays. Ambiguous.

The window can't decide. That's why "exactly" is hard.

**The fix: convert "exactly" into "atMost" subtraction.**

---

## 🧠 The atMost Trick — The Core Insight

```
exactly(goal) = atMost(goal) - atMost(goal - 1)
```

**atMost(goal)** counts subarrays with sum ≤ goal.
This includes subarrays with sum = 0, 1, 2, ..., goal.

**atMost(goal - 1)** counts subarrays with sum ≤ goal - 1.
This includes subarrays with sum = 0, 1, 2, ..., goal - 1.

Subtracting removes everything except sum = goal. Exactly what we want.

> _"Count everything up to goal.
> Count everything up to goal minus one.
> The difference = exactly goal."_

---

## 🧠 How atMost Works — Counting Subarrays in a Valid Window

In a valid window `[left, right]` (sum ≤ goal):
every subarray ENDING at `right` that starts at `left` or later is valid.

How many such subarrays? `right - left + 1`.

```
Window [left=2, right=5]: valid subarrays ending at 5:
  [2..5], [3..5], [4..5], [5..5] → 4 subarrays = right - left + 1.
```

We add `right - left + 1` to the count at every step.

---

### 📜 The Scroll of the Exact Count Trick

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔢 The atMost Helper

```cpp
int atMost(vector<int>& nums, int goal) {
    if (goal < 0) return 0;
```

If goal is negative, no subarray can have sum ≤ negative (all elements ≥ 0).
Return 0. This handles the `atMost(goal - 1)` call when `goal = 0`.

---

```cpp
    int left = 0, count = 0, sum = 0;
```

Standard sliding window variables.

---

```cpp
    for (int right = 0; right < nums.size(); right++) {
        sum += nums[right];
```

Expand: add the new element to the running sum.

---

### Shrink when sum exceeds the budget

```cpp
        while (sum > goal) {
            sum -= nums[left];
            left++;
        }
```

Sum exceeded the budget. Shrink from left until sum ≤ goal.

---

### Count all valid subarrays ending at right

```cpp
        count += (right - left + 1);
    }
    return count;
}
```

The window `[left, right]` is valid (sum ≤ goal).
Every subarray starting from `left` to `right` and ending at `right`
has sum ≤ goal. There are `right - left + 1` of them.

> _"In a valid window of size w,
> there are w subarrays ending at the right pointer.
> Each one has sum ≤ goal. Count them all."_

---

## 🔢 The Main Function — Subtract

```cpp
int numSubarraysWithSum(vector<int>& nums, int goal) {
    return atMost(nums, goal) - atMost(nums, goal - 1);
}
```

That's it. Two calls. One subtraction. The answer.

---

### 🎺 The Trial of the Exact Count Trick

```cpp
int main() {
    vector<int> n1 = {1, 0, 1, 0, 1};
    cout << numSubarraysWithSum(n1, 2) << endl; // expected: 4

    vector<int> n2 = {0, 0, 0, 0, 0};
    cout << numSubarraysWithSum(n2, 0) << endl; // expected: 15

    return 0;
}
```

---

**Trace of atMost([1,0,1,0,1], goal=2):**

| right | nums[r] | sum | sum>2? | left | window size | count += |
|-------|---------|-----|--------|------|-------------|----------|
| 0 | 1 | 1 | No | 0 | 1 | 1 |
| 1 | 0 | 1 | No | 0 | 2 | 2 |
| 2 | 1 | 2 | No | 0 | 3 | 3 |
| 3 | 0 | 2 | No | 0 | 4 | 4 |
| 4 | 1 | 3 | **Yes** → shrink | | | |
|   |   | sum-=1, left=1. sum=2 ≤ 2 ✓ | | 1 | 4 | 4 |

**atMost(2) = 1+2+3+4+4 = 14**

---

**Trace of atMost([1,0,1,0,1], goal=1):**

| right | nums[r] | sum | sum>1? | left | window size | count += |
|-------|---------|-----|--------|------|-------------|----------|
| 0 | 1 | 1 | No | 0 | 1 | 1 |
| 1 | 0 | 1 | No | 0 | 2 | 2 |
| 2 | 1 | 2 | **Yes** → shrink. sum-=1, left=1. sum=1 ✓ | 1 | 2 | 2 |
| 3 | 0 | 1 | No | 1 | 3 | 3 |
| 4 | 1 | 2 | **Yes** → shrink. sum-=0, left=2. sum=2. Still>1. sum-=1, left=3. sum=1 ✓ | 3 | 2 | 2 |

**atMost(1) = 1+2+2+3+2 = 10**

---

**Answer: atMost(2) - atMost(1) = 14 - 10 = 4** ✓

---

**Trace for nums = [0,0,0,0,0], goal = 0:**

**atMost(0):** sum never exceeds 0 (all zeros). Window always valid.
count = 1+2+3+4+5 = **15**.

**atMost(-1):** goal < 0 → return **0**.

**Answer: 15 - 0 = 15** ✓ (every subarray of all-zeros has sum 0)

---

## 🔍 Why `right - left + 1` Counts Subarrays

In a valid window `[left, right]`, the subarrays ending at `right` are:

```
[left, right], [left+1, right], [left+2, right], ..., [right, right]
```

That's `right - left + 1` subarrays. ALL have sum ≤ goal
because they're subsets of the valid window (binary array — removing
elements from the left can only decrease or maintain the sum).

---

## 🔍 The atMost Trick — Where It Applies

| Problem | Exactly K = | atMost(K) - atMost(K-1) |
|---------|-------------|-------------------------|
| **Binary Subarrays With Sum** | sum == goal | ✓ |
| Count Nice Subarrays | odd count == k | ✓ |
| Subarrays with K Different Integers | distinct == k | ✓ |

Any "count subarrays with EXACTLY K" problem on non-negative data
can use this trick. It converts an ambiguous "exactly" condition
into two clean "at most" sliding windows.

---

## 🔍 Sliding Window vs Prefix Sum + HashMap

| Approach | Time | Space | When to use |
|----------|------|-------|-------------|
| Prefix Sum + HashMap | O(N) | O(N) | Works for any integers (negatives too) |
| **atMost Sliding Window** | **O(N)** | **O(1)** | Only for non-negative elements |

The sliding window approach only works when elements are non-negative
(so that adding elements only increases the sum, and removing decreases it).
For arrays with negatives, use the prefix sum + hashmap approach.

Binary arrays are always non-negative → sliding window works perfectly.

---

### 🧠 Memory of the Exact Count Trick Law

-   **`exactly(goal) = atMost(goal) - atMost(goal - 1)`**
-   **atMost(goal):** sliding window, shrink when sum > goal
-   **Count per step:** `right - left + 1` (all subarrays ending at right)
-   **`if (goal < 0) return 0`** — handles the edge case
-   **Only works for non-negative elements** (binary arrays qualify)
-   **Time:** O(N) × 2 = O(N). **Space:** O(1).

Thus is remembered the saga of **Binary Subarrays With Sum**,
where the Oracle couldn't count "exactly" with a sliding window —
so she counted "at most" twice and subtracted —
the difference revealing the exact count,
two clean passes replacing one ambiguous one,
the atMost trick turning an impossible window
into an elegant subtraction. 🪟🔢✨
