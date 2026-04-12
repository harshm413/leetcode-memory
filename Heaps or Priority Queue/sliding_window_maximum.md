## 🪟🏆 _The Lazy King's Throne: The Sliding Window Maximum (Heap) Saga_

> \_"The Oracle had conquered the Sliding Window Maximum
> with a monotonic deque -- elegant, O(N), perfect.
>
> But another path existed --
> one that used a **Max-Heap** (priority queue)
> to always know the largest element.
>
> The challenge with a heap:
> when the window slides forward,
> the element that leaves the window
> might be buried deep inside the heap --
> not at the top, not easily removable.
>
> The Oracle devised a **lazy deletion** strategy:
>
> **Push every element into the max-heap as (value, index).
> When reading the maximum,
> check if the top's index is still inside the window.
> If it has expired -- pop it and check again.
> Only accept a top that lives within the current window.**
>
> Expired elements were not removed eagerly.
> They were removed lazily -- only when they rose to the top
> and were found to be stale.
>
> The heap always served the true maximum
> among elements still in the window.
> O(N log N) total -- slightly slower than the deque,
> but intuitive and easy to implement."\_

---

This is the saga of **Sliding Window Maximum (Heap Approach)**.

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

## 🧠 The Oracle's Core Insight -- Max-Heap with Lazy Deletion

A max-heap always serves the largest element at the top.
But when the window slides, old elements linger in the heap.

**Eager deletion** (removing expired elements immediately)
is expensive -- finding an arbitrary element in a heap is O(N).

**Lazy deletion** is the trick:

```
Don't remove expired elements proactively.
Instead, when you read the top:
  If top's index is outside the window → pop it (stale).
  Keep popping until the top is valid.
  The first valid top is the true window maximum.
```

Stale elements waste some space but are cleaned up
when they naturally rise to the top.

Store `(value, index)` pairs so we can check
whether the top is still inside the window.

```
Time:  O(N log N) -- each element pushed once, popped at most once, each O(log N)
Space: O(N) -- the heap may hold stale elements
```

---

### 📜 The Scroll of the Lazy Throne

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## ⚔️ The Oracle's Heap-Window Ritual

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    priority_queue<pair<int, int>> maxHeap;
    vector<int> result;
```

The Oracle prepared:

-   `maxHeap` -- a max-heap of `{value, index}` pairs.
    C++ `priority_queue` is a max-heap by default.
    `pair` compares by first element (value) first --
    so the largest value always sits on top.

-   `result` -- the maximum for each window position.

---

## 🔁 Walk the Array Element by Element

```cpp
    for (int i = 0; i < (int)nums.size(); i++) {
```

Each element entered the window as it was processed.

---

### 📥 Push the New Element

```cpp
        maxHeap.push({nums[i], i});
```

Every element was pushed into the heap with its index.
No questions asked. No cleanup yet.

The heap grew -- possibly holding stale elements
from windows long past. That was fine.
Lazy deletion would handle them later.

---

### 🧹 Lazy Cleanup -- Evict Stale Kings

```cpp
        while (maxHeap.top().second < i - k + 1) {
            maxHeap.pop();
        }
```

Before reading the maximum,
the Oracle checked the heap's top.

If the top's index was **outside** the current window
(`index < i - k + 1`), it was stale -- popped and discarded.

The while loop continued until the top was a valid
element within the current window.

> _"The lazy king sits on the throne
> long after his reign has ended.
> Only when someone checks the throne
> is the impostor discovered and removed."_

---

### 👑 Record the Window Maximum

```cpp
        if (i >= k - 1) {
            result.push_back(maxHeap.top().first);
        }
    }
    return result;
}
```

Once the first full window was formed (`i >= k - 1`),
the heap's top -- now guaranteed to be valid --
was the window's maximum.

---

### 🎺 The Trial of the Lazy Throne

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

| i | nums[i] | Push to heap | Heap top (val,idx) | Stale? | Window [i-2..i] | Max    |
|---|---------|-------------|--------------------|---------|-----------------| -------|
| 0 | 1       | (1,0)       | (1,0)              | --      | --              | --     |
| 1 | 3       | (3,1)       | (3,1)              | --      | --              | --     |
| 2 | -1      | (-1,2)      | (3,1)              | 1>=0 ✓ | [1,3,-1]        | **3**  |
| 3 | -3      | (-3,3)      | (3,1)              | 1>=1 ✓ | [3,-1,-3]       | **3**  |
| 4 | 5       | (5,4)       | (5,4)              | 4>=2 ✓ | [-1,-3,5]       | **5**  |
| 5 | 3       | (3,5)       | (5,4)              | 4>=3 ✓ | [-3,5,3]        | **5**  |
| 6 | 6       | (6,6)       | (6,6)              | 6>=4 ✓ | [5,3,6]         | **6**  |
| 7 | 7       | (7,7)       | (7,7)              | 7>=5 ✓ | [3,6,7]         | **7**  |

**Answer: [3, 3, 5, 5, 6, 7]** ✓

At i=3: heap top is (3,1). Index 1 >= 3-3+1=1 → valid. Max = 3.
At i=4: after pushing (5,4), heap top is (5,4). Valid. Max = 5.
No stale pops were needed in this trace -- the maximum happened
to stay within the window each time.

---

**Trace with stale eviction -- `[9, 1, 2, 3, 4]`, k = 2:**

| i | nums[i] | Push     | Heap top before cleanup | Stale pops       | Valid top | Max   |
|---|---------|----------|------------------------|------------------|-----------|-------|
| 0 | 9       | (9,0)    | (9,0)                  | --               | (9,0)     | --    |
| 1 | 1       | (1,1)    | (9,0)                  | 0 >= 0 ✓         | (9,0)     | **9** |
| 2 | 2       | (2,2)    | (9,0)                  | 0 < 1 → pop!     |           |       |
|   |         |          | (2,2)                  | 2 >= 1 ✓         | (2,2)     | **2** |
| 3 | 3       | (3,3)    | (3,3)                  | 3 >= 2 ✓         | (3,3)     | **3** |
| 4 | 4       | (4,4)    | (4,4)                  | 4 >= 3 ✓         | (4,4)     | **4** |

**Answer: [9, 2, 3, 4]** ✓

At i=2: the heap's top was (9,0) -- index 0 is outside window [1,2].
Stale! Popped. Next top (2,2) was valid.

This is lazy deletion in action -- the `9` lingered in the heap
until it was naturally checked and found expired.

---

**Trace for `[5, 4, 3, 2, 1]`, k = 3 (strictly decreasing):**

| i | Push    | Heap top | Stale pops | Max   |
|---|---------|----------|------------|-------|
| 0 | (5,0)   | (5,0)    | --         | --    |
| 1 | (4,1)   | (5,0)    | --         | --    |
| 2 | (3,2)   | (5,0)    | 0>=0 ✓     | **5** |
| 3 | (2,3)   | (5,0)    | 0<1 → pop  |       |
|   |         | (4,1)    | 1>=1 ✓     | **4** |
| 4 | (1,4)   | (4,1)    | 1<2 → pop  |       |
|   |         | (3,2)    | 2>=2 ✓     | **3** |

**Answer: [5, 4, 3]** ✓

Each old maximum lingered one extra step before being evicted.

---

## 🔄 Heap vs Deque -- When to Choose Which

| Max-Heap (Lazy Deletion)          | Monotonic Deque                   |
| --------------------------------- | --------------------------------- |
| O(N log N) time                   | O(N) time                         |
| O(N) space (stale elements)       | O(K) space                        |
| Intuitive -- "just use a heap"    | Requires understanding monotonic deques |
| Easy to implement                 | Slightly trickier                 |
| Good enough for most constraints  | Optimal for tight time limits     |

The deque approach is strictly better in complexity.
But the heap approach is often the first idea that comes to mind
and is perfectly acceptable in interviews
unless the interviewer specifically asks for O(N).

---

## ⚠️ Why Not Eager Deletion?

Removing an arbitrary element from a heap is O(N) --
you'd need to find it first (linear scan), then re-heapify.

Lazy deletion avoids this entirely.
Stale elements sit harmlessly in the heap
until they happen to reach the top.
At that point, popping them is O(log N) -- cheap.

The total number of pops across the entire algorithm is at most N
(each element is pushed once and popped at most once),
so the amortized cost is manageable.

---

### 🧠 Memory of the Lazy Throne Law

-   **Max-heap** of `{value, index}` pairs -- largest value on top
-   **Push** every element as it enters the window
-   **Lazy deletion:** before reading the top, pop while `top.index < i - k + 1`
-   **Record** `heap.top().first` once `i >= k - 1`
-   Stale elements are NOT removed eagerly -- only when they reach the top
-   **Time:** O(N log N) -- each push/pop is O(log N)
-   **Space:** O(N) -- heap may hold stale elements beyond window size
-   **Comparison:** deque approach is O(N) time, O(K) space -- strictly better
-   **When to use heap:** when deque logic feels too complex, or as a first approach

Thus is remembered the saga of **Sliding Window Maximum (Heap)**,
where the Oracle let every element enter the max-heap freely
and never bothered to remove the expired --
instead checking the throne each time the window moved,
evicting stale kings only when they dared sit on top --
a lazy but effective strategy
that revealed the true maximum of every window
at the cost of a logarithmic toll per step. 🪟🏆✨
