## 🌊📊 _The Two Kingdoms of the Median River: The Find Median from Data Stream Saga_

> \_"Along the banks of the Endless River,
> numbers flowed in one by one —
> arriving at random, in no particular order.
>
> The Royal Statistician was given one command:
>
> **'At any moment, after any number arrives,
> tell me the median — the middle value
> of all numbers seen so far.'**
>
> The Statistician thought carefully.
>
> She could not sort all numbers every time a new one arrived.
> She could not scan from the beginning each time.
>
> Then she conceived a brilliant division of the kingdom:
>
> **Split all numbers seen so far into two groups —
> the LOWER HALF and the UPPER HALF.**
>
> The Lower Half would be ruled by a **Max-Heap** —
> its king was the largest of the small numbers,
> always sitting at the top, always visible.
>
> The Upper Half would be ruled by a **Min-Heap** —
> its king was the smallest of the large numbers,
> always sitting at the top, always visible.
>
> The two kings faced each other across a border.
>
> **The median lived exactly at that border.**
>
> If both halves had equal size —
> the median was the average of the two kings.
>
> If one half was larger by one —
> its king alone was the median.
>
> Two heaps. Two kings. One border.
> Every number arriving from the river
> would join one of the two kingdoms —
> and the kingdoms would rebalance if needed
> so they were always equal in size or differ by at most one.
>
> The median was always visible.
> Always at the top of one or both kings.
> Always in O(1) time."\_

---

This is the saga of **Find Median from Data Stream**.

Implement the `MedianFinder` class:

-   `MedianFinder()` — initialize the data structure.
-   `void addNum(int num)` — add an integer from the data stream.
-   `double findMedian()` — return the median of all numbers added so far.

```
addNum(1) → stream: [1]    → median: 1.0
addNum(2) → stream: [1,2]  → median: 1.5
addNum(3) → stream: [1,2,3]→ median: 2.0
```

---

## 🧠 The Oracle's Core Insight — Two Heaps, One Border

The key structure:

```
maxHeap (lower half):   [... smaller numbers ...]  MAX at top
minHeap (upper half):   [... larger numbers  ...]  MIN at top

                maxHeap.top() ≤ minHeap.top()
                        ↑             ↑
                   left king     right king
                      (the border = the median zone)
```

**Balance rule:**

```
sizes differ by at most 1.
If total N is odd  → one heap has N/2+1 elements, the other has N/2.
                     The larger heap's king IS the median.
If total N is even → both heaps have N/2 elements.
                     Median = (maxHeap.top() + minHeap.top()) / 2.0
```

**Insertion rule (always add to maxHeap first, then rebalance):**

```
Step 1: Push num into maxHeap.
Step 2: Move maxHeap's top to minHeap
        (ensures all elements in maxHeap ≤ all in minHeap).
Step 3: If minHeap is now larger than maxHeap,
        move minHeap's top back to maxHeap
        (rebalance so maxHeap has equal or one extra).
```

Three lines. Two heaps. The median is always accessible in O(1).

---

### 📜 The Scroll of the Two Kingdoms

```cpp
#include <iostream>
#include <queue>
using namespace std;
```

---

## 🏛️ The Two Kingdoms are Established

```cpp
class MedianFinder {
    priority_queue<int> maxHeap;
    priority_queue<int, vector<int>, greater<int>> minHeap;
```

-   `maxHeap` — the **Lower Kingdom** — a max-heap by default in C++.
    Holds the smaller half of all numbers.
    Its king — `maxHeap.top()` — is the **largest of the small**.

-   `minHeap` — the **Upper Kingdom** — a min-heap (using `greater<int>`).
    Holds the larger half of all numbers.
    Its king — `minHeap.top()` — is the **smallest of the large**.

The two kings face each other at the median border.
The lower king is always ≤ the upper king.

---

```cpp
public:
    MedianFinder() {}
```

The kingdoms began empty — no numbers had arrived yet.

---

## 🌊 The addNum Ritual — Three Steps, Always Balanced

```cpp
    void addNum(int num) {
```

A new number arrived from the river.
Three steps would place it correctly and maintain balance.

---

### 🔽 Step 1 — Always Enter Through the Lower Kingdom

```cpp
        maxHeap.push(num);
```

Every incoming number was pushed into the **Lower Kingdom** first —
regardless of whether it belonged there or not.

Why? Because step 2 immediately corrects any misplacement.

---

### 🔀 Step 2 — Send the Lower Kingdom's King Upward

```cpp
        minHeap.push(maxHeap.top());
        maxHeap.pop();
```

The top of the Lower Kingdom — its largest element —
was immediately sent to the Upper Kingdom.

This step is the **guardian of the border law**:
after this, every element in `maxHeap` is guaranteed
to be ≤ every element in `minHeap`.

Why? Because the largest of the lower half
naturally belongs at the boundary.
If `num` itself was the new maximum of `maxHeap`,
it gets pushed up and becomes `minHeap`'s new minimum —
keeping the border clean.

---

### ⚖️ Step 3 — Rebalance if Upper Kingdom Grew Too Large

```cpp
        if (minHeap.size() > maxHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
```

After step 2, the Upper Kingdom may now have one more element
than the Lower Kingdom.

If so — the Upper Kingdom's king (the smallest of the large)
was moved back down to the Lower Kingdom.

After this rebalance:

-   `maxHeap.size() == minHeap.size()` (even total), or
-   `maxHeap.size() == minHeap.size() + 1` (odd total, lower holds extra)

The Lower Kingdom always has equal or one more element than the Upper.
This is the **invariant** that makes `findMedian` trivial.

> _"The Lower Kingdom is the tie-breaker.
> When numbers cannot be split evenly —
> the extra sits in the lower half,
> and its king alone is the median."_

---

## 🔍 The findMedian Ritual — Read the Two Kings

```cpp
    double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.top();
        }
        return (maxHeap.top() + minHeap.top()) / 2.0;
    }
};
```

**If the Lower Kingdom is larger** (odd total N) —
its king is the single middle element — the median.

**If both kingdoms are equal in size** (even total N) —
the median is the average of both kings —
the average of the largest-of-small and smallest-of-large.

No scanning. No searching. Just two `.top()` calls.
O(1) always.

---

### 🎺 The Trial of the Endless River

```cpp
int main() {
    MedianFinder mf;

    mf.addNum(1);
    cout << mf.findMedian() << endl; // 1.0

    mf.addNum(2);
    cout << mf.findMedian() << endl; // 1.5

    mf.addNum(3);
    cout << mf.findMedian() << endl; // 2.0

    mf.addNum(7);
    cout << mf.findMedian() << endl; // 2.5

    mf.addNum(5);
    cout << mf.findMedian() << endl; // 3.0
    return 0;
}
```

**Full trace — both heap states after every addNum:**

**addNum(1):**

-   Push 1 → maxHeap: `{1}`, minHeap: `{}`
-   Move top (1) up → maxHeap: `{}`, minHeap: `{1}`
-   minHeap > maxHeap → move top (1) back → maxHeap: `{1}`, minHeap: `{}`
-   State: max=`{1}`, min=`{}`
-   findMedian: maxHeap larger → **1.0** ✓

**addNum(2):**

-   Push 2 → maxHeap: `{2,1}`
-   Move top (2) up → maxHeap: `{1}`, minHeap: `{2}`
-   Sizes equal → no rebalance
-   State: max=`{1}`, min=`{2}`
-   findMedian: equal sizes → (1+2)/2.0 = **1.5** ✓

**addNum(3):**

-   Push 3 → maxHeap: `{3,1}`
-   Move top (3) up → maxHeap: `{1}`, minHeap: `{2,3}`
-   minHeap(2) > maxHeap(1) → move top (2) back → maxHeap:`{2,1}`, minHeap:`{3}`
-   State: max=`{2,1}`, min=`{3}`
-   findMedian: maxHeap larger → **2.0** ✓

**addNum(7):**

-   Push 7 → maxHeap: `{7,2,1}`
-   Move top (7) up → maxHeap:`{2,1}`, minHeap:`{3,7}`
-   Sizes equal → no rebalance
-   State: max=`{2,1}`, min=`{3,7}`
-   findMedian: equal → (2+3)/2.0 = **2.5** ✓

**addNum(5):**

-   Push 5 → maxHeap: `{5,2,1}`
-   Move top (5) up → maxHeap:`{2,1}`, minHeap:`{3,5,7}`
-   minHeap(3) > maxHeap(2) → move top (3) back → maxHeap:`{3,2,1}`, minHeap:`{5,7}`
-   State: max=`{3,2,1}`, min=`{5,7}`
-   findMedian: maxHeap larger → **3.0** ✓

All five medians correct. The two kings never lied.

---

### 🧠 Memory of the Two Kingdom Law

-   **maxHeap** = lower half — max-heap — top is the largest small number
-   **minHeap** = upper half — min-heap (`greater<int>`) — top is the smallest large number
-   **Invariant:** `maxHeap.size() >= minHeap.size()` and differ by at most 1
-   **Three-step addNum:** push to maxHeap → move maxHeap top to minHeap → if minHeap > maxHeap, move minHeap top to maxHeap
-   **findMedian:** if sizes unequal → `maxHeap.top()`; if equal → `(maxHeap.top() + minHeap.top()) / 2.0`
-   The border between the two kings is always the median zone — no searching ever needed
-   **Time:** O(log N) per `addNum` — two heap pushes and one pop at most
-   **Time:** O(1) per `findMedian` — just read two heap tops
-   **Space:** O(N) — all numbers stored across the two heaps

Thus is remembered the saga of **Find Median from Data Stream**,
where the Statistician split the endless river
into two kingdoms — lower and upper —
each ruled by a king who always stood at the border,
always the largest of the small
and the smallest of the large —
so that at any moment,
with a single glance at one or both kings,
the median of all numbers ever seen
was instantly revealed. 🌊📊✨
