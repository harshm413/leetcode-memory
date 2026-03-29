## 🌊🏆 _The K Champions of the Endless River: The Kth Largest Element in a Stream Saga_

> \_"Along the banks of the Endless River,
> numbers flowed ceaselessly —
> one after another, without pause,
> without warning, without end.
>
> The Royal Analyst stood at the bank
> with a single mandate:
>
> **'At any moment — after any new number arrives —
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
> She built a small **Min-Heap of size exactly K** —
> a tightly guarded leaderboard of the top K champions.
>
> The smallest champion in the leaderboard
> — always sitting at the top of the min-heap —
> was the answer at every moment.
>
> When a new number arrived from the river:
> If it was larger than the weakest champion —
> it displaced them and entered the leaderboard.
> If it was too weak — it was ignored.
>
> The heap never grew beyond K.
> The answer was always at the top.
> Every addition was O(log K).
> Every query was O(1)."\_

---

This is the saga of **Kth Largest Element in a Stream**.

Design a class that finds the **Kth largest element** in a stream.

```
KthLargest(int k, vector<int>& nums)
    — initialize with k and an initial list of numbers.

int add(int val)
    — add val to the stream, return the Kth largest element.
```

```
k = 3, nums = [4, 5, 8, 2]

add(3)  → [2,3,4,5,8] → 3rd largest = 4
add(5)  → [2,3,4,5,5,8] → 3rd largest = 5
add(10) → [2,3,4,5,5,8,10] → 3rd largest = 5
add(9)  → [2,3,4,5,5,8,9,10] → 3rd largest = 8 ← wait let's trace properly
add(4)  → ...
```

---

## 🧠 The Oracle's Core Insight — A Min-Heap of Size K

The key insight that makes everything O(log K):

> **The Kth largest element overall
> is the SMALLEST element among the top K largest.**

If we maintain exactly the **K largest elements** seen so far
in a **min-heap** — the heap's top is always the Kth largest.

```
Stream so far: [4, 5, 8, 2, 3]   k = 3
Top 3 largest: {3, 4, 5} → wait, {4, 5, 8}
Min-heap top = 4 = 3rd largest ✓
```

When a new number `val` arrives:

```
If heap.size() < k  → always push val into the heap
Else if val > heap.top() → pop the min (too small for top K), push val
Else → val is too small to be in top K, ignore it
```

After every operation — `heap.top()` is the answer.

The heap never grows beyond K.
The min at the top is the Kth largest.
Always.

---

### 📜 The Scroll of the Endless River

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏆 The Leaderboard is Built

```cpp
class KthLargest {
    int k;
    priority_queue<int, vector<int>, greater<int>> minHeap;
```

-   `k` — the rank we must always track.
-   `minHeap` — a **min-heap** (using `greater<int>` to flip C++'s default max-heap).
    It holds exactly the top K largest numbers seen so far.
    Its top is always the **smallest among the top K** = the Kth largest overall.

---

```cpp
public:
    KthLargest(int k, vector<int>& nums) : k(k) {
        for (int num : nums) {
            add(num);
        }
    }
```

The Analyst stood at the river's bank
and processed every initial number by calling `add`.

Rather than writing special initialization logic,
she reused the same `add` ritual for every number —
keeping the code clean and the heap correctly sized from the start.

---

## 🌊 The ADD Ritual — Receive a Number, Maintain the Leaderboard

```cpp
    int add(int val) {
        minHeap.push(val);
```

Every incoming number was pushed into the heap without hesitation.

The heap temporarily held one extra element —
one more than its target size of K.

---

```cpp
        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
```

If the heap now exceeded K elements,
the weakest champion — the minimum — was immediately expelled.

> _"Only K champions may stand in the leaderboard.
> The weakest challenger to enter
> was also the first to leave —
> for they were not worthy of the top K."_

After this step, the heap held exactly K elements
(or fewer than K if the stream hasn't reached K elements yet).

---

```cpp
        return minHeap.top();
    }
};
```

The top of the min-heap was the answer —
the smallest among the top K largest numbers seen so far.
That is always the Kth largest.

Return it instantly. O(1).

---

### 🎺 The Trial of the Endless River

```cpp
int main() {
    vector<int> nums = {4, 5, 8, 2};
    KthLargest kth(3, nums);
    // Initial stream: [4, 5, 8, 2]
    // Heap after init: {4, 5, 8} (top=4, the 3rd largest)

    cout << kth.add(3)  << endl; // stream: [2,3,4,5,8]   heap:{3,4,5,8}→pop2? No
                                  // heap after: {3,4,5} wait — push 3: {2,3,4,5,8}?
                                  // Let's trace cleanly:
    return 0;
}
```

Let us trace the heap step by step cleanly:

**Initialization with `[4, 5, 8, 2]`, k=3:**

| Number Added | Heap After Push | Size > 3? | Pop | Heap Final | Top (Answer) |
| ------------ | --------------- | --------- | --- | ---------- | ------------ |
| 4            | {4}             | No        | —   | {4}        | 4            |
| 5            | {4,5}           | No        | —   | {4,5}      | 4            |
| 8            | {4,5,8}         | No        | —   | {4,5,8}    | 4            |
| 2            | {2,4,5,8}       | Yes       | 2   | {4,5,8}    | 4            |

Heap after init: `{4, 5, 8}`, top = 4.

**Subsequent `add` calls:**

| add(val) | Push → Heap | Pop?  | Heap After | Answer |
| -------- | ----------- | ----- | ---------- | ------ |
| add(3)   | {3,4,5,8}   | pop 3 | {4,5,8}    | **4**  |
| add(5)   | {4,5,5,8}   | pop 4 | {5,5,8}    | **5**  |
| add(10)  | {5,5,8,10}  | pop 5 | {5,8,10}   | **5**  |
| add(9)   | {5,8,9,10}  | pop 5 | {8,9,10}   | **8**  |
| add(4)   | {4,8,9,10}  | pop 4 | {8,9,10}   | **8**  |

The leaderboard held exactly 3 champions at all times.
The weakest champion was always the Kth largest answer.

---

### 🧠 Memory of the K Champions Law

-   **Min-Heap of size K** = the leaderboard of the top K largest numbers
-   The **heap's top** = the smallest of the top K = the **Kth largest overall**
-   Use `greater<int>` in C++ `priority_queue` to create a min-heap
-   **Initialization:** process every initial number through `add`
-   **`add(val)`:**
    1. Push `val` into heap
    2. If `heap.size() > k` → pop the minimum (too weak for top K)
    3. Return `heap.top()` — the current Kth largest
-   If fewer than K numbers have been seen so far — the heap has fewer than K elements;
    `heap.top()` is still the Kth largest among what exists
-   **Time:** O(log K) per `add` — push and pop are both O(log K) on a heap of size K
-   **Space:** O(K) — the heap never grows beyond K elements

Thus is remembered the saga of **Kth Largest Element in a Stream**,
where the Analyst stood at the bank of the Endless River
and maintained a leaderboard of only K champions —
evicting the weakest the moment a stronger challenger arrived —
so that at every instant,
the Kth largest among all numbers ever seen
sat quietly and visibly at the very top of the heap,
ready to be returned in the blink of an eye. 🌊🏆✨
