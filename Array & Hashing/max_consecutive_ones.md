## ⚡🏹 _The Longest Streak of Victory: The Maximum Consecutive Ones Saga_

> _"Across the Plains of Binary Warriors,
> only two kinds marched —
> **Ones**, fierce and triumphant,
> and **Zeros**, cold and unyielding.
>
> The Oracle was asked a simple yet decisive question:
>
> **'What is the longest unbroken streak
> of victorious Ones?'**
>
> No substitutions were allowed.
> No flips of fate.
> No second chances.
>
> The Oracle would simply walk the line,
> counting triumphs until a defeat reset the march.
>
> The algorithm was elegantly simple:
>
> Maintain a running count of consecutive 1s.
> Every 1 extends the streak.
> Every 0 breaks the streak and resets the count.
> Track the maximum streak seen at any point.
>
> One pass. Two variables. O(1) space."_

---

This is the saga of **Maximum Consecutive Ones**.

You are given a binary array `nums` containing only `0` and `1`.

Your task:

- Find the **maximum number of consecutive `1`s** in the array.

```
Input:  [1, 1, 0, 1, 1, 1]
Output: 3
Explanation: The longest streak is [1, 1, 1]

Input:  [1, 0, 1, 1, 0, 1]
Output: 2

Input:  [0, 0, 0]
Output: 0
```

---

## 🧠 The Oracle's Core Insight — Count and Reset

The algorithm is straightforward:

```
Maintain two variables:
  current = current streak of consecutive 1s
  maxStreak = maximum streak seen so far

For each element:
  If element == 1:
    Increment current
    Update maxStreak if current > maxStreak
  If element == 0:
    Reset current to 0

Return maxStreak
```

**Time:** O(N) — single pass through the array
**Space:** O(1) — only two variables

---

### 📜 The Scroll of Binary Fields

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Single-March Ritual

_Count victories, reset on defeat_

```cpp
int findMaxConsecutiveOnes(vector<int>& nums) {
    int current = 0;
    int maxStreak = 0;
```

The Oracle began with no victories recorded.

`current` — the current streak of consecutive 1s.
`maxStreak` — the longest streak found so far.

Both started at 0.

---

## 🏃 Walk the Binary Field

```cpp
    for (int x : nums) {
```

Each warrior stepped forward —
either a victorious 1 or a defeating 0.

---

## 🟢 Victory Extends the Streak

```cpp
        if (x == 1) {
            current++;
            maxStreak = max(maxStreak, current);
        }
```

Every `1` strengthened the march.

The current streak grew by 1.

The Oracle immediately checked:
**Is this the longest streak seen so far?**

If yes, `maxStreak` was updated.

> _"Every victory is recorded.
> Every triumph is measured against the greatest."_

---

## 🔴 Defeat Resets the March

```cpp
        else {
            current = 0;
        }
    }
```

A `0` shattered the streak.

The current count was reset to 0.

The Oracle did not mourn —
she simply prepared to count the next streak.

> _"A single defeat ends the march.
> But the memory of past victories remains."_

---

## 🏆 Return the Greatest Streak

```cpp
    return maxStreak;
}
```

After walking the entire array,
`maxStreak` held the answer —
the longest unbroken sequence of 1s.

---

### 🎺 The Trial of the Binary Plains

```cpp
int main() {
    vector<int> nums1 = {1, 1, 0, 1, 1, 1};
    cout << findMaxConsecutiveOnes(nums1) << endl; // expected: 3

    vector<int> nums2 = {1, 0, 1, 1, 0, 1};
    cout << findMaxConsecutiveOnes(nums2) << endl; // expected: 2

    vector<int> nums3 = {0, 0, 0};
    cout << findMaxConsecutiveOnes(nums3) << endl; // expected: 0

    vector<int> nums4 = {1, 1, 1, 1, 1};
    cout << findMaxConsecutiveOnes(nums4) << endl; // expected: 5

    return 0;
}
```

---

**Trace for `[1, 1, 0, 1, 1, 1]`:**

| Index | Element | current (before) | Action          | current (after) | maxStreak |
|-------|---------|------------------|-----------------|-----------------|-----------|
| 0     | 1       | 0                | current++       | 1               | 1         |
| 1     | 1       | 1                | current++       | 2               | 2         |
| 2     | 0       | 2                | reset           | 0               | 2         |
| 3     | 1       | 0                | current++       | 1               | 2         |
| 4     | 1       | 1                | current++       | 2               | 2         |
| 5     | 1       | 2                | current++       | 3               | 3         |

**Answer: 3** ✓

The longest streak is `[1, 1, 1]` at indices 3-5.

---

**Trace for `[1, 0, 1, 1, 0, 1]`:**

| Index | Element | current | Action    | current (after) | maxStreak |
|-------|---------|---------|-----------|-----------------|-----------|
| 0     | 1       | 0       | current++ | 1               | 1         |
| 1     | 0       | 1       | reset     | 0               | 1         |
| 2     | 1       | 0       | current++ | 1               | 1         |
| 3     | 1       | 1       | current++ | 2               | 2         |
| 4     | 0       | 2       | reset     | 0               | 2         |
| 5     | 1       | 0       | current++ | 1               | 2         |

**Answer: 2** ✓

The longest streak is `[1, 1]` at indices 2-3.

---

**Trace for `[0, 0, 0]`:**

| Index | Element | current | Action | current (after) | maxStreak |
|-------|---------|---------|--------|-----------------|-----------|
| 0     | 0       | 0       | reset  | 0               | 0         |
| 1     | 0       | 0       | reset  | 0               | 0         |
| 2     | 0       | 0       | reset  | 0               | 0         |

**Answer: 0** ✓

No 1s exist, so the longest streak is 0.

---

**Trace for `[1, 1, 1, 1, 1]`:**

| Index | Element | current | Action    | current (after) | maxStreak |
|-------|---------|---------|-----------|-----------------|-----------|
| 0     | 1       | 0       | current++ | 1               | 1         |
| 1     | 1       | 1       | current++ | 2               | 2         |
| 2     | 1       | 2       | current++ | 3               | 3         |
| 3     | 1       | 3       | current++ | 4               | 4         |
| 4     | 1       | 4       | current++ | 5               | 5         |

**Answer: 5** ✓

The entire array is one continuous streak of 1s.

---

## 🔄 Alternative Approach — Track Start and End Indices

If you need to return the actual subarray (not just the length):

```cpp
pair<int, int> findMaxConsecutiveOnesIndices(vector<int>& nums) {
    int current = 0;
    int maxStreak = 0;
    int start = 0, end = 0;
    int currentStart = 0;
    
    for (int i = 0; i < nums.size(); i++) {
        if (nums[i] == 1) {
            current++;
            if (current > maxStreak) {
                maxStreak = current;
                start = currentStart;
                end = i;
            }
        } else {
            current = 0;
            currentStart = i + 1;
        }
    }
    return {start, end};
}
```

This tracks where each streak starts and updates the best range.

---

### 🧠 Memory of the Streak Law

- **Count consecutive 1s** — increment `current` on each 1
- **Reset on 0** — set `current = 0` when encountering 0
- **Track maximum** — update `maxStreak` whenever `current` exceeds it
- **One pass only** — walk the array once from left to right
- **Time:** O(N) — single pass through the array
- **Space:** O(1) — only two variables
- **Edge cases:**
  - All 0s → return 0
  - All 1s → return n (length of array)
  - Single element → return 1 if it's 1, else 0

Thus is remembered the saga of **Maximum Consecutive Ones**,
where the Oracle walked the binary plains,
counting uninterrupted victories —
resetting on every defeat,
recording every triumph —
and declaring the longest streak of consecutive 1s
written in the march of the victorious. ⚡🏹✨
