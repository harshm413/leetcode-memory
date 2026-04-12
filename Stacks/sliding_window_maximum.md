## 🪟👑 _The King of Every Window: The Sliding Window Maximum Saga_

> \_"Along the Great Wall of Numbers,
> a window of fixed size `k` slid from left to right,
> one step at a time.
>
> At every position, the King demanded:
>
> **'Who is the greatest number within my window?'**
>
> The naive approach would scan the entire window
> at every position -- O(N × K). Too slow.
>
> A max-heap could track the maximum --
> but removing elements that slid out of the window
> was O(log N) per step, and stale entries lingered.
>
> The Oracle chose a different weapon:
>
> **A Monotonic Decreasing Deque.**
>
> Not a stack. A **deque** -- double-ended queue --
> where elements could be added and removed
> from BOTH ends.
>
> The deque stored indices in decreasing order of value.
> The front was always the current window's maximum.
>
> When a new element arrived:
> **Pop from the back** every index whose value was smaller --
> they could never be the maximum while the new element existed.
>
> When the window slid forward:
> **Pop from the front** if the front index fell outside the window.
>
> The front of the deque was always the king.
> O(1) amortized per slide."\_

---

This is the saga of **Sliding Window Maximum**.

Given an array `nums` and a window size `k`:

-   Slide a window of size `k` from left to right.
-   Return the **maximum** in each window position.

```
Input:  nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]

Input:  nums = [1], k = 1
Output: [1]
```

---

## 🧠 The Oracle's Core Insight -- Monotonic Decreasing Deque

The deque stores **indices** (not values) in a specific order:

> **Values at the deque's indices are monotonically decreasing
> from front to back.**

The front always holds the index of the current window's maximum.

Two maintenance rules:

```
1. BACK cleanup: before pushing index i,
   pop from the back every index whose value <= nums[i].
   They can never be the max while nums[i] is in the window.

2. FRONT cleanup: if the front index is outside the window
   (front < i - k + 1), pop it from the front.
```

After both cleanups, `deque.front()` is the maximum for the current window.

Why a deque and not a stack?
A stack only allows removal from one end.
We need to remove from the **back** (smaller elements)
AND from the **front** (expired elements).
Only a deque supports both.

```
Time:  O(N) -- each index pushed once, popped once
Space: O(K) -- deque holds at most K indices
```

---

### 📜 The Scroll of the Sliding Kingdom

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;
```

---

## ⚔️ The Oracle's Window-Maximum Ritual

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;
    vector<int> result;
```

The Oracle prepared:
-   `dq` -- a deque of indices, maintaining decreasing values.
-   `result` -- the maximum for each window position.

---

## 🔁 Walk the Array Element by Element

```cpp
    for (int i = 0; i < (int)nums.size(); i++) {
```

Each element was processed as it entered the window.

---

### 🧹 Front Cleanup -- Remove Expired Indices

```cpp
        while (!dq.empty() && dq.front() < i - k + 1) {
            dq.pop_front();
        }
```

If the front of the deque had slid out of the window
(its index was before the window's left boundary `i - k + 1`),
it was removed.

> _"A king who has left the window
> has no authority here.
> Remove him from the front."_

---

### 🧹 Back Cleanup -- Remove Weaker Elements

```cpp
        while (!dq.empty() && nums[dq.back()] <= nums[i]) {
            dq.pop_back();
        }
```

Before the new element entered,
every index at the back whose value was **<=** the new element
was removed.

Why? Because as long as `nums[i]` is in the window,
those smaller elements can never be the maximum.
They are permanently overshadowed.

> _"A warrior who is both older AND weaker
> than the newcomer will never rule.
> Remove them from the back."_

---

### 📥 Push the New Element

```cpp
        dq.push_back(i);
```

The new index joined the deque at the back.
The decreasing order was maintained --
everything remaining in the deque was larger than `nums[i]`.

---

### 👑 Record the Window Maximum

```cpp
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    return result;
}
```

Once the first full window was formed (`i >= k - 1`),
the front of the deque was the maximum.

`nums[dq.front()]` was recorded for this window position.

---

### 🎺 The Trial of the Sliding Window

```cpp
int main() {
    vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
    auto res = maxSlidingWindow(nums, 3);
    for (int x : res) cout << x << " "; cout << endl;
    // expected: 3 3 5 5 6 7

    vector<int> nums2 = {1};
    auto res2 = maxSlidingWindow(nums2, 1);
    for (int x : res2) cout << x << " "; cout << endl;
    // expected: 1

    return 0;
}
```

---

**Full trace for `[1, 3, -1, -3, 5, 3, 6, 7]`, k = 3:**

| i | nums[i] | Front cleanup | Back cleanup        | Deque after (indices) | Values at deque | Window [i-2..i] | Max    |
|---|---------|---------------|---------------------|-----------------------|-----------------|-----------------|--------|
| 0 | 1       | --            | --                  | [0]                   | [1]             | --              | --     |
| 1 | 3       | --            | pop 0 (1<=3)        | [1]                   | [3]             | --              | --     |
| 2 | -1      | --            | --                  | [1, 2]                | [3, -1]         | [1,3,-1]        | **3**  |
| 3 | -3      | --            | --                  | [1, 2, 3]             | [3, -1, -3]     | [3,-1,-3]       | **3**  |
| 4 | 5       | pop 1 (1<2)   | pop 3,2 (-3<=5,-1<=5)| [4]                  | [5]             | [-1,-3,5]       | **5**  |
| 5 | 3       | --            | --                  | [4, 5]                | [5, 3]          | [-3,5,3]        | **5**  |
| 6 | 6       | --            | pop 5,4 (3<=6,5<=6)| [6]                   | [6]             | [5,3,6]         | **6**  |
| 7 | 7       | --            | pop 6 (6<=7)        | [7]                   | [7]             | [3,6,7]         | **7**  |

**Answer: [3, 3, 5, 5, 6, 7]** ✓

At i=4: front index 1 expired (1 < 4-3+1=2), popped.
Then back cleanup removed indices 3 and 2 (values -3 and -1, both <= 5).
Deque became [4]. Front = index 4, value 5.

At i=6: back cleanup removed indices 5 and 4 (values 3 and 5, both <= 6).
The new king was 6.

---

**Trace for `[9, 8, 7, 6, 5]`, k = 3 (strictly decreasing):**

| i | nums[i] | Back cleanup | Deque after | Max |
|---|---------|-------------|-------------|-----|
| 0 | 9       | --          | [0]         | --  |
| 1 | 8       | --          | [0,1]       | --  |
| 2 | 7       | --          | [0,1,2]     | **9** |
| 3 | 6       | front 0 expires | [1,2,3] | **8** |
| 4 | 5       | front 1 expires | [2,3,4] | **7** |

**Answer: [9, 8, 7]** ✓

No back cleanup ever happens -- each new element is smaller.
The deque grows to size k, then the front expires each step.

---

**Trace for `[1, 2, 3, 4, 5]`, k = 3 (strictly increasing):**

| i | nums[i] | Back cleanup      | Deque after | Max |
|---|---------|-------------------|-------------|-----|
| 0 | 1       | --                | [0]         | --  |
| 1 | 2       | pop 0             | [1]         | --  |
| 2 | 3       | pop 1             | [2]         | **3** |
| 3 | 4       | pop 2             | [3]         | **4** |
| 4 | 5       | pop 3             | [4]         | **5** |

**Answer: [3, 4, 5]** ✓

Every new element is the largest -- it clears the entire deque.
The deque always has exactly one element.

---

## 🔍 Why `<=` and Not Just `<` in Back Cleanup?

Using `<=` means equal elements are also popped.

This is correct because if two elements have the same value,
the newer one (further right) will stay in the window longer.
The older equal element will expire first and can never be
the maximum after the newer one -- so it's safe to remove.

Using strict `<` would also work but would leave
unnecessary duplicates in the deque.

---

## 🔄 Why Deque and Not Stack or Queue?

-   **Stack** -- can only remove from one end (back). Cannot expire the front.
-   **Queue** -- can only remove from one end (front). Cannot clean the back.
-   **Deque** -- removes from BOTH ends. Front for expiry, back for weakness.

The deque is the only structure that supports both operations in O(1).

---

### 🧠 Memory of the Sliding King Law

-   **Monotonic decreasing deque** of indices (values decrease front→back)
-   **Front** = index of the current window's maximum
-   **Front cleanup:** pop front if `dq.front() < i - k + 1` (expired)
-   **Back cleanup:** pop back while `nums[dq.back()] <= nums[i]` (overshadowed)
-   **Push** `i` to the back after cleanups
-   **Record** `nums[dq.front()]` once `i >= k - 1` (first full window)
-   **Time:** O(N) -- each index pushed once, popped once (amortized O(1))
-   **Space:** O(K) -- deque holds at most K indices
-   **Edge cases:**
    -   k = 1 → every element is its own max
    -   Strictly increasing → deque always has 1 element
    -   Strictly decreasing → deque fills to k, front expires each step
    -   All equal → deque has 1 element (back cleanup pops equals)

Thus is remembered the saga of **Sliding Window Maximum**,
where the Oracle carried a monotonic deque across the wall --
expiring old kings from the front,
banishing weaker warriors from the back,
and always reading the current king
from the deque's front --
so that at every window position,
the greatest number was known instantly,
without ever scanning the window again. 🪟👑✨
