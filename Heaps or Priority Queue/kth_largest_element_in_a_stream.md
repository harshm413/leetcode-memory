## 🌊🏆 _The K Champions of the Endless River: The Kth Largest Element in a Stream Saga_

> \_"Along the banks of the Endless River,
> numbers flowed ceaselessly --
> one after another, without pause,
> without warning, without end.
>
> The Royal Analyst stood at the bank
> with a single mandate:
>
> **'At any moment -- after any new number arrives --
> tell me the Kth largest number
> among all numbers seen so far.'**
>
> The Analyst thought carefully.
>
> She could not sort all numbers every time a new one arrived.
> She could not keep all numbers in memory and re-rank them.
> The river never stopped. The stream never ended.
>
> Then she had a revelation:
>
> **'I do not need all the numbers.
> I only need the TOP K largest ones.
> And among those K, the smallest of them
> is always the Kth largest overall.'**
>
> She built a small **Min-Heap of size exactly K** --
> a tightly guarded leaderboard of the top K champions.
>
> The smallest champion in the leaderboard
> -- always sitting at the top of the min-heap --
> was the answer at every moment.
>
> When a new number arrived from the river:
> push it into the heap.
> If the heap exceeded K -- expel the weakest.
>
> The heap never grew beyond K.
> The answer was always at the top.
> Every addition was O(log K).
> Every query was O(1)."\_

---

This is the saga of **Kth Largest Element in a Stream**.

Design a class that finds the **Kth largest element** in a stream.

-   `KthLargest(int k, vector<int>& nums)` -- initialize with k and an initial list.
-   `int add(int val)` -- add val to the stream, return the Kth largest.

```
k = 3, nums = [4, 5, 8, 2]

add(3)  → 4
add(5)  → 5
add(10) → 5
add(9)  → 8
add(4)  → 8
```

---

## 🧠 The Oracle's Core Insight -- Min-Heap of Size K

The key insight:

> **The Kth largest element overall
> is the SMALLEST element among the top K largest.**

If we maintain exactly the K largest elements seen so far
in a **min-heap** -- the heap's top is always the Kth largest.

Why a min-heap and not a max-heap?

A max-heap would give us the 1st largest (the maximum) at the top.
But we need the Kth largest -- the smallest among the top K.
A min-heap of size K keeps the weakest champion on top --
and that weakest champion IS the Kth largest.

When a new number `val` arrives:

```
Push val into the heap.
If heap.size() > k → pop the minimum (too weak for top K).
Return heap.top() → the current Kth largest.
```

The "push then pop if oversized" approach is simpler
than checking `val > heap.top()` first --
it handles all cases uniformly, including when the heap
has fewer than K elements.

```
Time:  O(log K) per add -- push and pop on a heap of size K
Space: O(K) -- the heap never exceeds K elements
```

---

### 📜 The Scroll of the Endless River

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏆 The Leaderboard Is Built

```cpp
class KthLargest {
    int k;
    priority_queue<int, vector<int>, greater<int>> minHeap;
```

-   `k` -- the rank we must always track.
-   `minHeap` -- a **min-heap** using `greater<int>` to flip C++'s default max-heap.
    It holds the top K largest numbers seen so far.
    Its top is always the **smallest among the top K** = the Kth largest.

---

```cpp
public:
    KthLargest(int k, vector<int>& nums) : k(k) {
        for (int num : nums) {
            add(num);
        }
    }
```

The Analyst processed every initial number through `add` --
reusing the same logic for initialization and streaming.
No special constructor code needed.

---

## 🌊 The ADD Ritual -- Receive a Number, Maintain the Leaderboard

```cpp
    int add(int val) {
        minHeap.push(val);
```

Every incoming number was pushed into the heap.
No questions asked. No pre-filtering.

The heap temporarily held K+1 elements.

---

### 🗡️ Expel the Weakest If Oversized

```cpp
        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
```

If the heap exceeded K elements,
the weakest -- the minimum at the top -- was expelled.

After this step, the heap held exactly K elements
(or fewer if the stream hasn't reached K numbers yet).

> _"Only K champions may stand in the leaderboard.
> The weakest is always the first to fall."_

---

### 👑 Return the Kth Largest

```cpp
        return minHeap.top();
    }
};
```

The top of the min-heap was the answer --
the smallest among the top K = the Kth largest overall.

O(1) to read. Always correct.

---

### 🎺 The Trial of the Endless River

```cpp
int main() {
    vector<int> nums = {4, 5, 8, 2};
    KthLargest kth(3, nums);

    cout << kth.add(3)  << endl; // 4
    cout << kth.add(5)  << endl; // 5
    cout << kth.add(10) << endl; // 5
    cout << kth.add(9)  << endl; // 8
    cout << kth.add(4)  << endl; // 8
    return 0;
}
```

---

**Initialization trace with `[4, 5, 8, 2]`, k=3:**

| Number | Heap after push | Size > 3? | Pop  | Heap final | Top |
| ------ | --------------- | --------- | ---- | ---------- | --- |
| 4      | {4}             | No        | --   | {4}        | 4   |
| 5      | {4, 5}          | No        | --   | {4, 5}     | 4   |
| 8      | {4, 5, 8}       | No        | --   | {4, 5, 8}  | 4   |
| 2      | {2, 4, 5, 8}    | Yes       | 2    | {4, 5, 8}  | 4   |

After init: heap = `{4, 5, 8}`, top = 4 (the 3rd largest).

The `2` was pushed then immediately expelled -- too weak for the top 3.

---

**Subsequent add() calls:**

| add(val) | Heap after push  | Pop?  | Heap after pop | Answer |
| -------- | ---------------- | ----- | -------------- | ------ |
| add(3)   | {3, 4, 5, 8}    | pop 3 | {4, 5, 8}      | **4**  |
| add(5)   | {4, 5, 5, 8}    | pop 4 | {5, 5, 8}      | **5**  |
| add(10)  | {5, 5, 8, 10}   | pop 5 | {5, 8, 10}     | **5**  |
| add(9)   | {5, 8, 9, 10}   | pop 5 | {8, 9, 10}     | **8**  |
| add(4)   | {4, 8, 9, 10}   | pop 4 | {8, 9, 10}     | **8**  |

**Answers: 4, 5, 5, 8, 8** ✓

At add(3): `3` entered but was immediately expelled (weaker than all top 3).
At add(5): `5` entered, `4` was expelled. New 3rd largest = 5.
At add(9): `9` entered, `5` was expelled. New 3rd largest = 8.
At add(4): `4` entered but was expelled. Top 3 unchanged.

---

**Trace for k=1, nums=[], add(1), add(2), add(3):**

| add(val) | Heap after push | Pop? | Heap final | Answer |
| -------- | --------------- | ---- | ---------- | ------ |
| add(1)   | {1}             | No   | {1}        | **1**  |
| add(2)   | {1, 2}          | pop 1| {2}        | **2**  |
| add(3)   | {2, 3}          | pop 2| {3}        | **3**  |

**Answers: 1, 2, 3** ✓

k=1 means "always return the maximum." The heap holds exactly 1 element.

---

**Trace for k=2, nums=[0], add(-1), add(1), add(-2):**

Init: push 0 → heap = {0}. Size 1 < k=2, no pop.

| add(val) | Heap after push | Pop?  | Heap final | Answer |
| -------- | --------------- | ----- | ---------- | ------ |
| add(-1)  | {-1, 0}         | No    | {-1, 0}    | **-1** |
| add(1)   | {-1, 0, 1}      | pop -1| {0, 1}     | **0**  |
| add(-2)  | {-2, 0, 1}      | pop -2| {0, 1}     | **0**  |

**Answers: -1, 0, 0** ✓

Negative numbers work identically. The heap doesn't care about sign.
At add(-1): heap has only 2 elements (exactly k), no pop. Top = -1.

---

## 🔍 Why Not Sort Every Time?

Sorting all N elements after every add: O(N log N) per call.
With the min-heap of size K: O(log K) per call.

For a stream of 1 million numbers with K=10:
-   Sorting: ~20 million operations per call.
-   Heap: ~3 operations per call.

The heap is astronomically faster for streaming data.

---

## 🔄 Why Push-Then-Pop Instead of Check-Then-Push?

An alternative approach checks first:

```cpp
if (minHeap.size() < k || val > minHeap.top()) {
    minHeap.push(val);
    if (minHeap.size() > k) minHeap.pop();
}
```

This avoids pushing values that would be immediately popped.
But the simple "push then pop if oversized" is cleaner,
handles all edge cases uniformly (including heap.size() < k),
and the performance difference is negligible --
both are O(log K) per call.

---

### 🧠 Memory of the K Champions Law

-   **Min-Heap of size K** = leaderboard of the top K largest numbers
-   **Heap's top** = smallest of the top K = the **Kth largest overall**
-   Use `greater<int>` in C++ `priority_queue` to create a min-heap
-   **Constructor:** process every initial number through `add`
-   **`add(val)`:**
    1. Push `val` into heap
    2. If `heap.size() > k` → pop the minimum
    3. Return `heap.top()`
-   Heap never exceeds K elements (after each add)
-   **Time:** O(log K) per `add` -- push/pop on heap of size K
-   **Space:** O(K) -- the heap
-   **Edge cases:**
    -   Fewer than K numbers seen → heap has < K elements, top is still valid
    -   k = 1 → heap holds only the maximum
    -   Negative numbers → handled identically
    -   All same values → heap holds K copies, top = that value

Thus is remembered the saga of **Kth Largest Element in a Stream**,
where the Analyst stood at the bank of the Endless River
and maintained a leaderboard of only K champions --
pushing every newcomer into the min-heap,
expelling the weakest whenever the roster overflowed --
so that at every instant,
the Kth largest among all numbers ever seen
sat quietly at the very top of the heap,
ready to be returned
in the blink of an eye. 🌊🏆✨
