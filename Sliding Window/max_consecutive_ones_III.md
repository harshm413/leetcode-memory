## 🪟1️⃣ _The Flip Budget: The Max Consecutive Ones III Saga_

> \_"The Oracle was given a binary array and a number `k`.
>
> She was commanded:
>
> **'You may flip at most k zeros to ones.
> What is the longest subarray of all ones you can achieve?'**
>
> The Oracle reframed the problem:
>
> **'Find the longest subarray containing at most k zeros.'**
>
> She didn't actually flip anything.
> She just found the longest window where the number of zeros ≤ k.
> That window, after flipping its zeros, would be all ones.
>
> Sliding window. Expand right. Count zeros.
> When zeros exceed k — shrink left until zeros ≤ k again.
> Track the maximum window size."\_

---

This is the saga of **Max Consecutive Ones III (LeetCode 1004)**.

Given a binary array `nums` and integer `k`:
-   You may flip at most `k` zeros to ones.
-   Return the maximum number of consecutive ones.

```
Input:  nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2
Output: 6   (flip zeros at index 5,10 → [1,1,1,0,0,1,1,1,1,1,1]... 
             actually: longest window with ≤2 zeros)

Input:  nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], k = 3
Output: 10

Input:  nums = [1,1,1,1], k = 0
Output: 4   (no flips needed)
```

---

## 🧠 The Reframing — Don't Flip, Just Count

Instead of actually flipping zeros:

**Find the longest subarray with at most k zeros.**

That subarray, if we flipped its zeros, would be all ones.
The length of that subarray = the answer.

This transforms a "modification" problem into a "counting" problem.
And counting problems are perfect for sliding windows.

> _"The Oracle doesn't flip.
> She finds the longest window where the zero count stays within budget.
> The flips are imaginary — only the window length matters."_

---

## 🧠 The Sliding Window Mechanics

**Expand:** move `right` forward. If `nums[right] == 0`, increment zero count.
**Shrink:** when zero count > k, move `left` forward.
If `nums[left] == 0`, decrement zero count. Keep shrinking until zeros ≤ k.
**Track:** maximum window size = `right - left + 1`.

---

### 📜 The Scroll of the Flip Budget

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 1️⃣ The Sliding Window Solution

### Initialize

```cpp
int longestOnes(vector<int>& nums, int k) {
    int left = 0;
    int zeros = 0;
    int maxLen = 0;
```

`left` = left boundary of the window.
`zeros` = count of zeros in the current window.
`maxLen` = longest valid window found.

---

### Expand with right pointer

```cpp
    for (int right = 0; right < nums.size(); right++) {
```

---

### Count zeros as they enter the window

```cpp
        if (nums[right] == 0) {
            zeros++;
        }
```

A zero entered the window. Our flip budget is being used.

---

### If budget exceeded — shrink from the left

```cpp
        while (zeros > k) {
            if (nums[left] == 0) {
                zeros--;
            }
            left++;
        }
```

Too many zeros in the window. We've exceeded our flip budget.

Shrink by moving `left` forward. If the element leaving the window
is a zero — we get one flip back (`zeros--`).
Keep shrinking until `zeros ≤ k`.

> _"The window has too many zeros — more than the budget allows.
> The Oracle shrinks from the left, releasing zeros one by one,
> until the budget is balanced again."_

---

### Update the maximum

```cpp
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

After shrinking (if needed), the window `[left, right]` is valid.
Its size is a candidate for the answer.

---

### 🎺 The Trial of the Flip Budget

```cpp
int main() {
    vector<int> n1 = {1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 0};
    cout << longestOnes(n1, 2) << endl; // expected: 6

    vector<int> n2 = {0, 0, 1, 1, 0, 0, 1, 1, 1, 0, 1, 1, 0, 0, 0, 1, 1, 1, 1};
    cout << longestOnes(n2, 3) << endl; // expected: 10

    vector<int> n3 = {1, 1, 1, 1};
    cout << longestOnes(n3, 0) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2:**

| right | nums[r] | zeros | zeros>k? | left | window size | maxLen |
|-------|---------|-------|----------|------|-------------|--------|
| 0 | 1 | 0 | No | 0 | 1 | 1 |
| 1 | 1 | 0 | No | 0 | 2 | 2 |
| 2 | 1 | 0 | No | 0 | 3 | 3 |
| 3 | 0 | 1 | No | 0 | 4 | 4 |
| 4 | 0 | 2 | No | 0 | 5 | 5 |
| 5 | 0 | 3 | **Yes** → shrink | | | |
|   |   |   | nums[0]=1, left=1. zeros=3 | 1 | | |
|   |   |   | nums[1]=1, left=2. zeros=3 | 2 | | |
|   |   |   | nums[2]=1, left=3. zeros=3 | 3 | | |
|   |   |   | nums[3]=0, zeros=2, left=4. zeros≤k ✓ | 4 | 2 | 5 |
| 6 | 1 | 2 | No | 4 | 3 | 5 |
| 7 | 1 | 2 | No | 4 | 4 | 5 |
| 8 | 1 | 2 | No | 4 | 5 | 5 |
| 9 | 1 | 2 | No | 4 | 6 | **6** |
| 10| 0 | 3 | **Yes** → shrink | | | |
|   |   |   | nums[4]=0, zeros=2, left=5. ✓ | 5 | 6 | 6 |

**Answer: 6** ✓

The best window: indices 4-9 = `[0, 0, 1, 1, 1, 1]` with 2 zeros flipped.
After flipping: `[1, 1, 1, 1, 1, 1]`. Length 6.

---

**Trace for nums = [1,1,1,1], k = 0:**

| right | nums[r] | zeros | left | window size | maxLen |
|-------|---------|-------|------|-------------|--------|
| 0 | 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 0 | 0 | 2 | 2 |
| 2 | 1 | 0 | 0 | 3 | 3 |
| 3 | 1 | 0 | 0 | 4 | **4** |

**Answer: 4** ✓ (no zeros, no flips needed, entire array)

---

## 🔍 The Non-Shrinking Window Optimization

Instead of shrinking with `while`, we can use `if`:

```cpp
    for (int right = 0; right < nums.size(); right++) {
        if (nums[right] == 0) zeros++;
        if (zeros > k) {
            if (nums[left] == 0) zeros--;
            left++;
        }
        maxLen = max(maxLen, right - left + 1);
    }
```

With `if` instead of `while`, the window NEVER shrinks — it only
slides forward. When zeros > k, left moves by exactly 1 (same as right).
The window size stays the same or grows. Never decreases.

This works because we only care about the MAXIMUM window.
A smaller window can never beat the current best.
So we don't need to shrink — just slide.

Both approaches give the same answer. The `while` version is
more intuitive. The `if` version is a common optimization.

---

## 🔍 The "At Most K" Sliding Window Pattern

This problem is the template for a whole family:

| Problem | What to count in window | Budget |
|---------|------------------------|--------|
| **Max Consecutive Ones III** | Zeros | ≤ k |
| Fruit Into Baskets | Distinct fruits | ≤ 2 |
| Longest Repeating Char Replacement | Non-majority chars | ≤ k |
| Longest Substring K Distinct | Distinct chars | ≤ k |

Same skeleton: expand right, count something, shrink left when over budget.

---

### 🧠 Memory of the Flip Budget Law

-   **Reframe:** "flip k zeros" = "find longest window with ≤ k zeros"
-   **Expand right:** if `nums[right] == 0`, `zeros++`
-   **Shrink left:** while `zeros > k`, if `nums[left] == 0` then `zeros--`, `left++`
-   **Answer:** max `right - left + 1`
-   **Non-shrinking optimization:** use `if` instead of `while` — window only grows or slides
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Max Consecutive Ones III**,
where the Oracle didn't flip a single zero —
she simply found the longest window
where the zero count stayed within budget —
expanding right to explore,
shrinking left to balance the books —
and the length of that window
was the answer all along. 🪟1️⃣✨
