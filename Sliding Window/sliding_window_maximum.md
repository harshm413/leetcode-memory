## 🪟👑 _The Monotonic Throne: The Sliding Window Maximum Saga_

> \_"The Oracle was given an array and a window size K.
>
> She was commanded:
>
> **'Slide a window of size K across the array.
> At each position, report the MAXIMUM element in the window.'**
>
> The naive approach: scan all K elements at each position. O(N×K).
>
> The Oracle used a **monotonic decreasing deque:**
>
> **The deque stores INDICES, not values.**
> **The front of the deque is always the maximum of the current window.**
> **Elements are kept in decreasing order of their values.**
>
> When a new element enters:
> -   Remove all smaller elements from the BACK (they'll never be the max).
> -   Add the new element to the back.
>
> When the window slides:
> -   If the front element is outside the window → remove it.
>
> The front is always the answer. O(1) per query. O(N) total."\_

---

This is the saga of **Sliding Window Maximum (LeetCode 239)**.

Given an array `nums` and integer `k`:
-   Slide a window of size `k` from left to right.
-   Return the maximum in each window position.

```
Input:  nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]

Input:  nums = [1], k = 1
Output: [1]
```

---

## 🧠 Why a Monotonic Decreasing Deque?

The deque maintains indices in DECREASING order of their values.
The front is always the largest. The back is the smallest.

**Why decreasing?** Because we want the MAXIMUM.
The front = the current king. Elements behind it are "heirs" —
they'll become king when the current king leaves the window.

**Why remove smaller elements from the back?**
If a new element is LARGER than elements at the back,
those back elements can NEVER be the maximum while the new element
is in the window. They're permanently overshadowed. Remove them.

> _"A new warrior enters the hall.
> Every weaker warrior behind him is dismissed —
> they'll never rule while he's present.
> Only those stronger than him (ahead in the deque) remain."_

---

## 🧠 The Three Operations Per Element

For each element `nums[i]`:

1. **Expire:** if the front of the deque is outside the window (`deque.front() <= i - k`), remove it.
2. **Clean:** remove all elements from the BACK that are ≤ `nums[i]`. They're useless now.
3. **Add:** push `i` to the back of the deque.
4. **Record:** if the window is fully formed (`i >= k - 1`), the front is the answer.

---

### 📜 The Scroll of the Monotonic Throne

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;
```

---

## 👑 The Monotonic Deque Solution

### Initialize

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;
    vector<int> result;
```

`dq` stores INDICES (not values). Front = index of current max.
`result` collects the maximum for each window position.

---

### Process each element

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

---

### Expire: remove front if outside window

```cpp
        if (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
```

The front element has left the window. It's no longer valid.
Remove it. The next element in the deque becomes the new king.

`i - k` = the leftmost index that's OUTSIDE the current window.
If `dq.front() <= i - k`, it's expired.

> _"The old king has left the window.
> His reign is over. The next in line takes the throne."_

---

### Clean: remove smaller elements from the back

```cpp
        while (!dq.empty() && nums[dq.back()] <= nums[i]) {
            dq.pop_back();
        }
```

Remove all indices from the back whose VALUES are ≤ the new element.
They can never be the maximum while `nums[i]` is in the window.

Why `<=` and not just `<`? Equal elements are also useless —
the newer one (at a later index) will stay in the window longer.

> _"The new warrior is stronger than those at the back.
> They're dismissed. They'll never rule in his presence.
> Only those stronger (at the front) survive."_

---

### Add: push current index to the back

```cpp
        dq.push_back(i);
```

The new element takes its place at the back of the deque.
It might become king later (when those ahead of it expire).

---

### Record: if window is fully formed

```cpp
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    return result;
}
```

The first full window forms at index `k - 1`.
From then on, the front of the deque = the window's maximum.

---

### 🎺 The Trial of the Monotonic Throne

```cpp
int main() {
    vector<int> n1 = {1, 3, -1, -3, 5, 3, 6, 7};
    auto r1 = maxSlidingWindow(n1, 3);
    for (int x : r1) cout << x << " ";
    cout << endl; // expected: 3 3 5 5 6 7

    vector<int> n2 = {1};
    auto r2 = maxSlidingWindow(n2, 1);
    for (int x : r2) cout << x << " ";
    cout << endl; // expected: 1

    return 0;
}
```

---

**Full trace for nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3:**

| i | nums[i] | Expire? | Clean back | dq (indices) | dq (values) | i≥2? | max |
|---|---------|---------|-----------|--------------|-------------|------|-----|
| 0 | 1 | — | — | [0] | [1] | No | — |
| 1 | 3 | — | 1≤3, pop 0 | [1] | [3] | No | — |
| 2 | -1 | — | -1<3, stop | [1, 2] | [3, -1] | Yes | **3** |
| 3 | -3 | front=1, 1>0? yes, stay | -3<-1, stop | [1, 2, 3] | [3,-1,-3] | Yes | **3** |
| 4 | 5 | front=1, 1≤1? yes, pop | -3≤5 pop, -1≤5 pop | [4] | [5] | Yes | **5** |
| 5 | 3 | front=4, 4>2? yes, stay | 3<5, stop | [4, 5] | [5, 3] | Yes | **5** |
| 6 | 6 | front=4, 4≤3? yes, pop | 3≤6 pop | [6] | [6] | Yes | **6** |
| 7 | 7 | front=6, 6>4? yes, stay | 6≤7 pop | [7] | [7] | Yes | **7** |

**Result: [3, 3, 5, 5, 6, 7]** ✓

Key moments:
-   i=1: element 3 kills element 1 (3 > 1). Deque = [3].
-   i=4: element 5 kills everything (-3, -1). Front 1 also expires. Deque = [5].
-   i=6: element 6 kills 3. Front 4 expires. Deque = [6].
-   i=7: element 7 kills 6. Deque = [7].

---

## 🔍 Why O(N) Total — Amortized Analysis

Each element is pushed to the deque ONCE and popped at most ONCE.
Total pushes = N. Total pops ≤ N.
Total work = O(2N) = O(N).

Even though the `while` loop can pop multiple elements in one step,
across ALL steps, the total pops can't exceed N.

---

## 🔍 Why a Deque and Not a Stack or Queue?

**Stack:** can only access/remove from one end. Can't expire the front.
**Queue:** can only access/remove from one end. Can't clean the back.
**Deque:** access/remove from BOTH ends. Can expire front AND clean back.

The deque is the only data structure that supports both operations.

---

## 🔍 The Heap Alternative

A max-heap of `{value, index}` pairs also works:
-   Push every element.
-   The top is the max. If its index is outside the window → pop (lazy deletion).

O(N log N) time (heap operations). More space. Simpler to code.
The deque approach is O(N) — strictly better.

See `Heaps or Priority Queue/sliding_window_maximum.md` for the heap version.

---

## 🔍 Monotonic Deque Pattern Family

| Problem | Deque type | What it tracks |
|---------|-----------|----------------|
| **Sliding Window Maximum (this)** | **Decreasing** | **Max in window** |
| Sliding Window Minimum | Increasing | Min in window |
| Daily Temperatures | Decreasing stack | Next greater element |
| Largest Rectangle Histogram | Increasing stack | Previous/next smaller |

The deque is a generalization of the monotonic stack —
it supports expiry from the front (window constraint).

---

### 🧠 Memory of the Monotonic Throne Law

-   **Deque stores INDICES** in decreasing order of values
-   **Front = current window maximum**
-   **Expire front:** if `dq.front() <= i - k` → pop front
-   **Clean back:** while `nums[dq.back()] <= nums[i]` → pop back
-   **Add:** push `i` to back
-   **Record:** when `i >= k - 1`, front is the answer
-   **Amortized O(N):** each element pushed/popped at most once
-   **Time:** O(N). **Space:** O(K) for the deque.

Thus is remembered the saga of **Sliding Window Maximum**,
where the Oracle maintained a monotonic decreasing deque —
the front always the king of the current window,
weaker elements dismissed from the back on arrival,
expired kings removed from the front on departure —
the throne always occupied by the true maximum,
reported at each window position in O(1). 🪟👑✨
