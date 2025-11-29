## 🌌 _The Convergence of Many Rivers: The Merge K Sorted Lists Saga_

> _"From every corner of the realm they flowed —
> slender rivers already calm and sorted.
> But the High Cartographer desired one mighty river,
> forged from the harmony of many streams.
> To weave them without losing order,
> summon the Min-Heap, the Spirit of Smallest Stones."_

---

Across the vast lands, **k** rivers traveled from distant mountains.
Each river was already sorted — small stones becoming larger as their waters flowed.
But the realm needed **one unified river**, a single ascending stream made by merging them all.

The High Cartographer could not afford to sort everything again; instead, she called forth a magical device:
the **Min-Heap**, a mystical bowl that always floated the smallest stone to the top.

Thus began the saga of **Merge K Sorted Lists**.

---

### 📜 The Scroll of River Stones

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

struct ListNode {
    int val;
    ListNode* next;
    ListNode(int v) : val(v), next(NULL) {}
};
```

Each river stone was a `ListNode`, and a bundle of pointers to heads of rivers formed the kingdom’s **k** sorted streams.

---

## 🌬️ The Summoning of the Min-Heap

_Always choose the smallest leading stone_

```cpp
struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val; // min-heap (smallest first)
    }
};
```

The Cartographer forged a comparator:
the heap spirit would always deliver the smallest stone first.

---

### 🌊 The Great Convergence Ritual

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    priority_queue<ListNode*, vector<ListNode*>, Compare> pq;
```

The Min-Heap was summoned — a sacred bowl holding one stone from each river, always bubbling up the smallest among them.

---

```cpp
    for (auto node : lists) {
        if (node) pq.push(node); // push the head stone of each river
    }
```

The first stone of every river was placed into the bowl.
Even if some rivers were dry, others contributed their leading stones.

---

### 🧵 The Weaving of the Grand River

```cpp
    ListNode dummy(0);
    ListNode* tail = &dummy;
```

A **dummy stone** was laid to anchor the new river.
`tail` tracked the end of the merged waterway.

---

```cpp
    while (!pq.empty()) {
        ListNode* smallest = pq.top();
        pq.pop();
```

The bowl revealed the **smallest stone** remaining among all rivers.
This stone was placed next in the merged river.

---

```cpp
        tail->next = smallest;
        tail = tail->next;
```

The river grew stone by stone — always in ascending order.

---

```cpp
        if (smallest->next) {
            pq.push(smallest->next);
        }
    }
```

If the chosen stone had a successor in its original river, that next stone was placed into the bowl,
waiting its turn to rise to the top.

This cycle continued until all rivers emptied — and all stones found their place.

---

```cpp
    return dummy.next;
}
```

The unified river began at `dummy.next`, smooth and perfectly sorted.

---

### 🎺 The Trial of Many Streams

```cpp
int main() {
    // list1: 1 -> 4 -> 5
    ListNode* a = new ListNode(1);
    a->next = new ListNode(4);
    a->next->next = new ListNode(5);

    // list2: 1 -> 3 -> 4
    ListNode* b = new ListNode(1);
    b->next = new ListNode(3);
    b->next->next = new ListNode(4);

    // list3: 2 -> 6
    ListNode* c = new ListNode(2);
    c->next = new ListNode(6);

    vector<ListNode*> lists = {a, b, c};

    ListNode* merged = mergeKLists(lists);
    // expected: 1 1 2 3 4 4 5 6

    while (merged) {
        cout << merged->val << " ";
        merged = merged->next;
    }
    cout << endl;
    return 0;
}
```

Three rivers flowed:
`1→4→5`, `1→3→4`, `2→6`

Their convergence created a majestic single river:
`1 → 1 → 2 → 3 → 4 → 4 → 5 → 6`.

The kingdom rejoiced at the flawless merge.

---

### 🧠 Memory of the Convergence

-   **Min-Heap** always gives the smallest available stone.
-   Insert the **head of each river** into the heap.
-   Repeatedly pull the smallest stone and attach it to the merged river.
-   Push the next stone from the same river into the heap.
-   **Time:** O(N log k), where N is total nodes, k is number of lists.
-   **Space:** O(k) for the heap.

Thus is remembered the saga of **Merge K Sorted Lists**,
where many calm rivers — sorted from birth —
are woven into one majestic stream
by the patient bubbling of the Min-Heap Spirit. 🌌💧
