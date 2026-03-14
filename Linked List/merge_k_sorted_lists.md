## 🔗⚗️ _The Grand Tournament of K Processions: The Merge K Sorted Lists Saga_

> \_"The Kingdom had once witnessed the Weaving of Two Processions —
> where the Weaver merged two sorted chains
> by comparing their fronts, one soldier at a time.
>
> But now a grander challenge arrived.
>
> **K processions** stood at the gates —
> not two, but many.
> Each perfectly sorted within itself.
> Each waiting to be woven into one great unified chain.
>
> The naive Weaver panicked:
> 'Do I compare the fronts of all K processions every step?
> That is O(K) per soldier — too slow for a great kingdom!'
>
> But the Tournament Master stepped forward and smiled:
>
> \*\*'We do not compare all K fronts by walking through them.
> We maintain a living tournament bracket —
> a Min-Heap — that always knows
> which procession's front soldier is the smallest.
>
> At the start, crown the front soldier of every procession
> into the tournament.
> The heap will always keep the weakest — the smallest —
> at the very top, ready to be chosen.
>
> Pop the smallest. Attach them to the result chain.
> Then push their successor — the next soldier
> from the same procession — into the tournament.
>
> The heap self-adjusts. The smallest is always at the top.
> Every soldier is touched exactly once.
> Every comparison is handled in O(log K) time.'\*\*
>
> The Ghost Sentinel anchored the result chain.
> The Min-Heap ran the tournament.
> K processions dissolved into one
> in perfect sorted order."\_

---

This is the saga of **Merge K Sorted Lists**.

You are given an array of `k` linked lists,
each sorted in ascending order.

Your task:

-   Merge all of them into one sorted linked list.
-   Return the head of the merged list.

```
Input:
  list 1:  1 → 4 → 5
  list 2:  1 → 3 → 4
  list 3:  2 → 6

Output:  1 → 1 → 2 → 3 → 4 → 4 → 5 → 6
```

---

## 🧠 The Oracle's Core Insight — The Min-Heap Tournament

Merging two sorted lists was O(n + m) —
compare two fronts, pick the smaller, advance.

For K lists, comparing K fronts naively at every step costs O(K) per node.
With N total nodes, that is **O(N × K)** — unacceptably slow for large K.

The Min-Heap changes everything:

```
Push all K front soldiers into the heap.
The heap always serves the minimum in O(log K).

Each step:
  1. Pop the minimum node from the heap.       O(log K)
  2. Attach it to the result chain.            O(1)
  3. If it has a next node, push that next.    O(log K)

Total: O(N log K) — N nodes, each pushed and popped once.
```

The heap acts as an **automatic tournament bracket** —
it maintains the sorted order among the K current front soldiers
at all times, silently re-balancing itself after every push and pop.

---

### 📜 The Scroll of the Grand Tournament

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

### 🧱 The Structure of a Soldier

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

Each soldier in every procession carried a value (`val`)
and a single hand pointing to their successor (`next`).

---

## 🏆 The Tournament Comparator

_The rule that makes the Min-Heap always serve the smallest_

```cpp
struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val;
    }
};
```

The Tournament Master defined the ranking rule:

> **The soldier with the LARGER value has LOWER priority.**

In C++, `priority_queue` is a **max-heap** by default —
it places the largest on top.

By reversing the comparison (`a->val > b->val`),
we flip it into a **min-heap** —
the soldier with the **smallest value** always floats to the top,
ready to be the next chosen for the result chain.

This comparator is the single line that transforms
a max-heap into the min-heap tournament we need.

---

## 👻 The Ghost Sentinel and the Tournament Begin

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    ListNode dummy(0);
    ListNode* tail = &dummy;

    priority_queue<ListNode*, vector<ListNode*>, Compare> minHeap;
```

The Ghost Sentinel was placed at the front of the result chain —
`tail` always pointing to the last attached soldier,
ready to receive the next champion from the tournament.

The **Min-Heap** was opened — the tournament arena —
empty and awaiting its first competitors.

---

## 🚀 Crown the Front Soldiers of Every Procession

_Load the heap with K initial competitors_

```cpp
    for (ListNode* list : lists) {
        if (list != nullptr) {
            minHeap.push(list);
        }
    }
```

The Tournament Master walked to the front of each procession
and pushed the first soldier into the heap.

Null processions were skipped gracefully —
an empty list has no soldier to enter.

After this loop, the heap held at most K nodes —
one from the front of each non-empty procession.

The smallest among all K fronts
was already silently sitting at the top of the heap,
ready to be crowned.

---

## 🔁 The Tournament Runs — Pop, Attach, Push Successor

```cpp
    while (!minHeap.empty()) {
```

The tournament continued as long as any soldier
remained in the arena.

---

### 🥇 Pop the Champion — The Current Smallest

```cpp
        ListNode* node = minHeap.top();
        minHeap.pop();
```

The Tournament Master reached into the heap
and pulled out the current champion —
the soldier with the smallest value among all remaining front soldiers.

The heap self-adjusted in O(log K) —
the next smallest automatically rose to the top.

---

### 🔗 Attach the Champion to the Result Chain

```cpp
        tail->next = node;
        tail = tail->next;
```

The champion was linked behind `tail` —
extending the growing result chain by one soldier.

`tail` stepped forward to the newly attached champion,
ready to receive the next one.

---

### 🔄 Push the Champion's Successor into the Tournament

```cpp
        if (node->next != nullptr) {
            minHeap.push(node->next);
        }
    }
```

The champion had a successor in their original procession —
the soldier who stood behind them in their sorted chain.

That successor was now pushed into the tournament heap,
taking the champion's place as their procession's representative.

If the champion had no successor —
their procession was exhausted,
and no new soldier entered the arena from that line.

The heap shrank by one. The tournament grew smaller.
Eventually the heap would empty — and the war would be won.

---

## 🏁 The Ghost Departs — The United Chain Emerges

```cpp
    tail->next = nullptr;
    return dummy.next;
}
```

The Ghost was discarded.

`dummy.next` pointed to the very first champion chosen —
the smallest node across all K processions —
now the head of the perfectly sorted merged chain.

`tail->next = nullptr` sealed the end cleanly,
ensuring no stale pointer remained.

---

### 🎺 The Trial of the Three Processions

```cpp
int main() {
    // list1: 1 → 4 → 5
    ListNode* l1 = new ListNode(1);
    l1->next = new ListNode(4);
    l1->next->next = new ListNode(5);

    // list2: 1 → 3 → 4
    ListNode* l2 = new ListNode(1);
    l2->next = new ListNode(3);
    l2->next->next = new ListNode(4);

    // list3: 2 → 6
    ListNode* l3 = new ListNode(2);
    l3->next = new ListNode(6);

    vector<ListNode*> lists = {l1, l2, l3};
    ListNode* result = mergeKLists(lists);

    ListNode* curr = result;
    while (curr) {
        cout << curr->val;
        if (curr->next) cout << " → ";
        curr = curr->next;
    }
    cout << " → NULL" << endl;
    // expected: 1 → 1 → 2 → 3 → 4 → 4 → 5 → 6 → NULL
    return 0;
}
```

The tournament unfolded:

**Initial heap after loading fronts:** `{1(l1), 1(l2), 2(l3)}`

| Step | Heap Top | Attached | Successor Pushed | Heap After    |
| ---- | -------- | -------- | ---------------- | ------------- |
| 1    | 1 (l1)   | 1        | 4 (l1)           | {1(l2), 2, 4} |
| 2    | 1 (l2)   | 1        | 3 (l2)           | {2, 3, 4}     |
| 3    | 2 (l3)   | 2        | 6 (l3)           | {3, 4, 6}     |
| 4    | 3 (l2)   | 3        | 4 (l2)           | {4, 4, 6}     |
| 5    | 4 (l1)   | 4        | 5 (l1)           | {4(l2), 5, 6} |
| 6    | 4 (l2)   | 4        | none             | {5, 6}        |
| 7    | 5 (l1)   | 5        | none             | {6}           |
| 8    | 6 (l3)   | 6        | none             | {}            |

Result: `1 → 1 → 2 → 3 → 4 → 4 → 5 → 6 → NULL` ✓

Eight soldiers. Eight pops. Every comparison done in O(log 3).

---

### 🧠 Memory of the Grand Tournament Law

-   **Min-Heap** = the living tournament bracket — always serves the smallest in O(log K)
-   **Comparator** `a->val > b->val` — flips C++ max-heap into a min-heap
-   **Ghost Sentinel** (`dummy`) — anchors result chain; `tail` grows it forward
-   **Initialization:** push the front node of every non-null list into the heap
-   **Each iteration:** pop min → attach to tail → push its `next` (if exists)
-   **Loop ends** when heap is empty — all soldiers have been chosen and attached
-   **Time:** O(N log K) — N total nodes, each pushed and popped once at O(log K) cost
-   **Space:** O(K) — the heap holds at most one node per list at any time

Thus is remembered the saga of **Merge K Sorted Lists**,
where the Tournament Master refused to compare K fronts one by one —
instead maintaining a Min-Heap that always knew
which soldier deserved to step forward next —
popping champions, attaching them to the growing chain,
and immediately crowning their successors into the arena —
until all K processions had dissolved
into one perfectly ordered, unified march. 🔗⚗️✨
