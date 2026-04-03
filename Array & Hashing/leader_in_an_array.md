## 👑🌅 _The Watchers of the Right Horizon: The Leaders in an Array Saga_

> _"In the Line of Warriors,
> each fighter stood facing the horizon to the **right** —
> gazing at all who stood beyond them.
>
> Some were mighty but overshadowed,
> their strength hidden by greater warriors ahead.
>
> Others stood unchallenged —
> no one to their right was stronger.
> These were the **Leaders** —
> the watchers of the horizon,
> the unmatched champions.
>
> The Oracle was commanded:
>
> **'Find all the true leaders —
> those who are greater than everyone
> standing to their right.'**
>
> The naive approach was clear:
> for each warrior, scan everyone to their right
> and check if any were stronger.
> O(N²) — slow, wasteful, unworthy.
>
> The Oracle knew a better way.
>
> She realized:
>
> **The rightmost warrior is always a leader —
> for no one stands beyond them.**
>
> And as you walk backward from the end,
> you always know the strongest warrior seen so far.
>
> If the current warrior is stronger than that maximum —
> they are a leader.
> If not — they are overshadowed.
>
> One backward march. One running maximum.
> O(N) time. No wasted comparisons.
>
> The Oracle chose to walk **from the end**,
> where the horizon is always clear."_

---

This is the saga of **Leaders in an Array**.

You are given an integer array `nums`.

An element is called a **leader** if:

```
it is greater than ALL elements to its right
```

Your task:

- Find all leaders in the array.
- Return them in the order they appear in the array.

```
Input:  [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]

Input:  [1, 2, 3, 4, 5]
Output: [5]

Input:  [5, 4, 3, 2, 1]
Output: [5, 4, 3, 2, 1]
```

---

## 🧠 The Oracle's Core Insight — Walk Backward, Track the Maximum

The naive approach:

```
For each element at index i:
  Check all elements from i+1 to n-1
  If nums[i] is greater than all of them → leader
Time: O(N²)
```

This works but is slow for large arrays.

The key insight:

> **The rightmost element is always a leader.**
> **As you walk backward (right to left),
> maintain the maximum element seen so far.
> Any element greater than this maximum is a leader.**

Why does this work?

```
If nums[i] > max(nums[i+1], nums[i+2], ..., nums[n-1])
then nums[i] is a leader.

As we walk backward, we maintain maxRight = max of all elements to the right.
If nums[i] > maxRight → nums[i] is a leader.
Update maxRight = nums[i] and continue.
```

**Time:** O(N) — single pass from right to left
**Space:** O(1) extra space (output array doesn't count)

---

### 📜 The Scroll of the Silent Horizon

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle's Reverse March Ritual

_Walk from the horizon inward_

```cpp
vector<int> leaders(vector<int>& nums) {
    int n = nums.size();
    vector<int> result;
```

The Oracle prepared a scroll
to record the true leaders.

`result` would collect all leaders as they were discovered.

---

## 🌅 Begin from the Rightmost Warrior

```cpp
    int maxRight = nums[n - 1];
    result.push_back(maxRight);
```

The Oracle began at the **rightmost warrior** — index `n-1`.

This warrior stood at the edge of the world —
no one beyond them, no rival to challenge them.

They were a leader by destiny.

`maxRight` was initialized to this warrior's strength —
the strongest seen so far (which is just this one warrior).

The rightmost warrior was immediately added to `result`.

---

## 🧭 March Leftward and Judge Each Warrior

```cpp
    for (int i = n - 2; i >= 0; i--) {
```

The Oracle turned and began walking backward —
from index `n-2` all the way to index `0`.

At each position, she would judge:
**Is this warrior stronger than everyone to their right?**

---

```cpp
        if (nums[i] > maxRight) {
```

The Oracle compared the current warrior's strength (`nums[i]`)
against the strongest warrior seen so far to the right (`maxRight`).

**If `nums[i] > maxRight`** —
this warrior was stronger than everyone beyond them.
They were a leader.

**If `nums[i] <= maxRight`** —
this warrior was overshadowed by someone to their right.
They were not a leader. Skip them.

---

```cpp
            maxRight = nums[i];
            result.push_back(nums[i]);
        }
    }
```

When a new leader was found:

1. **Update `maxRight`** — this warrior is now the strongest seen so far.
2. **Record them** — add `nums[i]` to the `result`.

The Oracle continued marching backward,
always knowing the strongest warrior to the right,
always able to judge leadership in O(1) time.

---

## 🔄 Restore Original Order

```cpp
    reverse(result.begin(), result.end());
    return result;
}
```

Since leaders were discovered from right to left,
they were added to `result` in reverse order.

The Oracle reversed the list
to present them in the original left-to-right order.

> _"The leaders were found by walking backward,
> but they are presented as they stood —
> from the front of the line to the back."_

---

### 🎺 The Trial of the Watchful Warriors

```cpp
int main() {
    vector<int> nums1 = {16, 17, 4, 3, 5, 2};
    vector<int> ans1 = leaders(nums1);
    for (int x : ans1) cout << x << " ";
    cout << endl; // expected: 17 5 2

    vector<int> nums2 = {1, 2, 3, 4, 5};
    vector<int> ans2 = leaders(nums2);
    for (int x : ans2) cout << x << " ";
    cout << endl; // expected: 5

    vector<int> nums3 = {5, 4, 3, 2, 1};
    vector<int> ans3 = leaders(nums3);
    for (int x : ans3) cout << x << " ";
    cout << endl; // expected: 5 4 3 2 1

    return 0;
}
```

---

**Full trace for `[16, 17, 4, 3, 5, 2]` (n=6):**

**Backward march from right to left:**

| i | nums[i] | maxRight (before) | nums[i] > maxRight? | Action           | maxRight (after) | result (before reverse) |
|---|---------|-------------------|---------------------|------------------|------------------|-------------------------|
| 5 | 2       | —                 | —                   | Always leader    | 2                | [2]                     |
| 4 | 5       | 2                 | Yes (5 > 2)         | Leader, update   | 5                | [2, 5]                  |
| 3 | 3       | 5                 | No (3 ≤ 5)          | Skip             | 5                | [2, 5]                  |
| 2 | 4       | 5                 | No (4 ≤ 5)          | Skip             | 5                | [2, 5]                  |
| 1 | 17      | 5                 | Yes (17 > 5)        | Leader, update   | 17               | [2, 5, 17]              |
| 0 | 16      | 17                | No (16 ≤ 17)        | Skip             | 17               | [2, 5, 17]              |

**After reverse:** `[17, 5, 2]` ✓

**Leaders:**
- `17` at index 1 — greater than all to its right: `[4, 3, 5, 2]`
- `5` at index 4 — greater than all to its right: `[2]`
- `2` at index 5 — rightmost, always a leader

---

**Full trace for `[1, 2, 3, 4, 5]` (n=5):**

| i | nums[i] | maxRight (before) | nums[i] > maxRight? | Action         | maxRight (after) | result (before reverse) |
|---|---------|-------------------|---------------------|----------------|------------------|-------------------------|
| 4 | 5       | —                 | —                   | Always leader  | 5                | [5]                     |
| 3 | 4       | 5                 | No (4 ≤ 5)          | Skip           | 5                | [5]                     |
| 2 | 3       | 5                 | No (3 ≤ 5)          | Skip           | 5                | [5]                     |
| 1 | 2       | 5                 | No (2 ≤ 5)          | Skip           | 5                | [5]                     |
| 0 | 1       | 5                 | No (1 ≤ 5)          | Skip           | 5                | [5]                     |

**After reverse:** `[5]` ✓

Only the rightmost element is a leader — everyone else is overshadowed.

---

**Full trace for `[5, 4, 3, 2, 1]` (n=5):**

| i | nums[i] | maxRight (before) | nums[i] > maxRight? | Action         | maxRight (after) | result (before reverse) |
|---|---------|-------------------|---------------------|----------------|------------------|-------------------------|
| 4 | 1       | —                 | —                   | Always leader  | 1                | [1]                     |
| 3 | 2       | 1                 | Yes (2 > 1)         | Leader, update | 2                | [1, 2]                  |
| 2 | 3       | 2                 | Yes (3 > 2)         | Leader, update | 3                | [1, 2, 3]               |
| 1 | 4       | 3                 | Yes (4 > 3)         | Leader, update | 4                | [1, 2, 3, 4]            |
| 0 | 5       | 4                 | Yes (5 > 4)         | Leader, update | 5                | [1, 2, 3, 4, 5]         |

**After reverse:** `[5, 4, 3, 2, 1]` ✓

Every element is a leader — the array is strictly decreasing,
so each element is greater than all to its right.

---

## 🔍 Why Not Walk Forward?

A common question: why walk backward? Why not forward?

If you walk forward (left to right):

```
At index i, you need to know: max(nums[i+1], nums[i+2], ..., nums[n-1])
But you haven't seen those elements yet!
You'd need to scan right every time → O(N²)
```

If you walk backward (right to left):

```
At index i, you need to know: max(nums[i+1], nums[i+2], ..., nums[n-1])
You've already seen all those elements!
You maintain maxRight as you go → O(1) per element → O(N) total
```

Walking backward lets you maintain the maximum to the right
without ever needing to look ahead.

---

### 🧠 Memory of the Horizon Law

- **Leader** = element greater than ALL elements to its right
- **Rightmost element** is always a leader (no one beyond it)
- **Walk backward** from index `n-1` to `0`
- Maintain `maxRight` = maximum element seen so far to the right
- If `nums[i] > maxRight` → `nums[i]` is a leader → update `maxRight` and record it
- If `nums[i] <= maxRight` → `nums[i]` is overshadowed → skip it
- **Reverse the result** at the end to restore original order
- **Time:** O(N) — single backward pass + O(N) reverse
- **Space:** O(1) extra space (output array doesn't count toward space complexity)
- **Edge cases:**
  - Single element → always a leader
  - All elements equal → only rightmost is a leader
  - Strictly increasing → only rightmost is a leader
  - Strictly decreasing → all elements are leaders

Thus is remembered the saga of **Leaders in an Array**,
where the Oracle walked backward from the horizon,
always knowing the strongest warrior to the right,
judging each warrior in a single glance —
recording only those who stood unmatched,
unchallenged by any who followed them —
the true watchers of the right horizon. 👑🌅✨
