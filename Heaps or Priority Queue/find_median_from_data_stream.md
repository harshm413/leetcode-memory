## 🔥 _The Balance of the Twin Heaps: The Find Median From Data Stream Saga_

> _"In the Kingdom of Streaming Numbers,
> values flowed in endlessly — one by one, like wandering travelers.
> The King demanded to know, at every moment,
> the **median** — the perfect middle of the realm.
> But with no map and no final count,
> how could such balance be found?
> The Oracle answered:
> ‘Divide the kingdom into two heaps —
> one of nobles, one of commoners —
> and keep them forever in balance.
> From their highest and lowest leaders,
> the truth of the middle shall always be known.’"_

---

In a land where numbers arrived one at a time,
the King sought constant knowledge of the realm’s **median**.
But the numbers came unpredictably, endlessly —
never sorted, never resting.

The Oracle devised a brilliant structure:
**Two Heaps**, balanced like twin guardians.

-   **Max-heap** for the _lower half_ of numbers — the _Nobles_, whose strongest stands on top.
-   **Min-heap** for the _upper half_ — the _Commoners_, whose weakest stands on top.

By ensuring their sizes never differed by more than one,
the Oracle could always pluck the median instantly.

Thus began the saga of **Find Median From Data Stream**.

---

### 📜 The Scroll of Heaps

```cpp
#include <iostream>
#include <queue>
using namespace std;

class MedianFinder {
public:
    priority_queue<int> maxHeap; // lower half
    priority_queue<int, vector<int>, greater<int>> minHeap; // upper half
```

Two heaps rose as twin fortresses:

-   The **Max-Heap** (lower half) guarded the largest of the small.
-   The **Min-Heap** (upper half) guarded the smallest of the large.

Between them lay perfect balance.

---

## ⚖️ The Ritual of Adding a Number

_Insert into one heap, then rebalance_

```cpp
    void addNum(int num) {
```

A new traveler arrived with value `num`.

---

### 🏰 Step 1 — Decide Where the Traveler Belongs

```cpp
        if (maxHeap.empty() || num <= maxHeap.top())
            maxHeap.push(num);
        else
            minHeap.push(num);
```

If the traveler was humble (≤ strongest noble),
he joined the **lower half** in the Max-Heap.

Otherwise, he joined the **upper half** in the Min-Heap.

---

### ⚖️ Step 2 — Rebalance the Twin Fortresses

```cpp
        if (maxHeap.size() > minHeap.size() + 1) {
            minHeap.push(maxHeap.top());
            maxHeap.pop();
        }
        else if (minHeap.size() > maxHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
```

Balance was sacred.
If one heap grew too large,
the top warrior was transferred to the other.

Thus, the two halves stayed nearly equal,
preserving the symmetry needed for the median.

---

## 🌗 The Ritual of Retrieving the Median

```cpp
    double findMedian() {
        if (maxHeap.size() > minHeap.size())
            return maxHeap.top();
        return (maxHeap.top() + minHeap.top()) / 2.0;
    }
};
```

If the lower half held one extra noble,
his value alone was the median.

Otherwise, the top noble and the top commoner
shared their values,
giving their average — the perfect middle.

The King received the median instantly,
no sorting, no delay.

---

### 🎺 The Trial of the Streaming Realm

```cpp
int main() {
    MedianFinder mf;
    mf.addNum(1);
    mf.addNum(2);
    cout << mf.findMedian() << endl; // 1.5
    mf.addNum(3);
    cout << mf.findMedian() << endl; // 2
    return 0;
}
```

Numbers arrived: `1`, `2`, `3`.

The twin heaps balanced themselves:

-   After two numbers, the medians were between 1 and 2 → **1.5**
-   After three numbers, the middle was **2**

The King smiled — the Oracle’s method never faltered.

---

### 🧠 Memory of the Twin Heaps

-   Two heaps:

    -   **Max-Heap:** lower half
    -   **Min-Heap:** upper half

-   On each insertion:

    -   Insert into correct heap
    -   Rebalance heaps

-   Median:

    -   If one heap bigger → its root
    -   Else → average of both roots

-   **Time:**

    -   Add → O(log n)
    -   FindMedian → O(1)

-   **Space:** O(n)

Thus is remembered the saga of **Find Median From Data Stream**,
where two mighty heaps guard the ordered flow,
balancing nobles and commoners
to reveal the ever-shifting truth of the median. ⚖️🌗✨
