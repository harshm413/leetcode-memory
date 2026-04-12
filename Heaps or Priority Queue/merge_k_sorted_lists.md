## 🔗🏆 _The Grand Tournament of K Processions: The Merge K Sorted Lists (Heap) Saga_

> \_"The Kingdom had once witnessed the Weaving of Two Processions --
> comparing two fronts, picking the smaller, advancing.
>
> But now **K processions** stood at the gates --
> each perfectly sorted within itself.
> Each waiting to be woven into one great unified chain.
>
> The naive Weaver panicked:
> 'Do I compare the fronts of all K processions every step?
> That is O(K) per soldier -- O(N × K) total!'
>
> But the Tournament Master stepped forward:
>
> **'We maintain a living tournament bracket --
> a Min-Heap -- that always knows
> which procession's front soldier is the smallest.
>
> At the start, push the front of every procession.
> The heap serves the smallest in O(log K).
> Pop it, attach to the result,
> then push its successor from the same procession.
>
> Every soldier is touched exactly once.
> Every comparison is O(log K).
> Total: O(N log K).'**
>
> The Ghost Sentinel anchored the result chain.
> The Min-Heap ran the tournament.
> K processions dissolved into one."\_

---

This is the saga of **Merge K Sorted Lists (Heap Approach)**.

Given an array of `k` linked lists, each sorted in ascending order:

-   Merge all into one sorted linked list.
-   Return the head of the merged list.

```
Input:
  list 1:  1 → 4 → 5
  list 2:  1 → 3 → 4
  list 3:  2 → 6

Output:  1 → 1 → 2 → 3 → 4 → 4 → 5 → 6
```

---

## 🧠 The Oracle's Core Insight -- Min-Heap as Tournament Bracket

Merging two lists: compare two fronts → O(1) per step → O(N) total.

For K lists, comparing K fronts naively at every step costs O(K) per node.
With N total nodes: **O(N × K)** -- too slow for large K.

The Min-Heap changes everything:

```
Push all K front nodes into the heap.
Each step:
  1. Pop the minimum node.           O(log K)
  2. Attach it to the result chain.  O(1)
  3. Push its successor (if exists). O(log K)

Total: O(N log K) -- N nodes, each pushed and popped once.
```

The heap acts as an automatic tournament bracket --
always serving the smallest among the K current fronts.

```
Time:  O(N log K) -- N total nodes, O(log K) per node
Space: O(K) -- heap holds at most K nodes at any time
```

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

---

## 🏆 The Tournament Comparator

```cpp
struct Compare {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val;
    }
};
```

C++ `priority_queue` is a max-heap by default.
`a->val > b->val` reverses the comparison --
the node with the **smallest value** floats to the top.

This single comparator transforms the max-heap into the min-heap we need.

---

## ⚔️ The Oracle's Merge Ritual

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    ListNode dummy(0);
    ListNode* tail = &dummy;

    priority_queue<ListNode*, vector<ListNode*>, Compare> minHeap;
```

The Ghost Sentinel anchored the result chain.
`tail` always pointed to the last attached node.

The Min-Heap was opened -- the tournament arena.

---

### 🚀 Load the Front of Every Procession

```cpp
    for (ListNode* list : lists) {
        if (list != nullptr) {
            minHeap.push(list);
        }
    }
```

The front node of each non-empty list was pushed into the heap.
Null lists were skipped.

After this, the heap held at most K nodes --
one from each non-empty list.
The smallest was already at the top.

---

### 🔁 Pop, Attach, Push Successor

```cpp
    while (!minHeap.empty()) {
        ListNode* node = minHeap.top();
        minHeap.pop();

        tail->next = node;
        tail = tail->next;

        if (node->next != nullptr) {
            minHeap.push(node->next);
        }
    }
```

**Pop** the smallest node from the heap.
**Attach** it to the result chain behind `tail`.
**Push** its successor from the same list (if it exists).

The heap shrank by one (the popped node)
and grew by one (the successor) -- staying at size K.

When a list was exhausted (no successor),
the heap shrank permanently. Eventually it emptied.

> _"Each champion falls. Their successor enters the arena.
> When a procession runs out of soldiers,
> the arena grows quieter --
> until silence means the war is won."_

---

### 🏁 The Ghost Departs

```cpp
    tail->next = nullptr;
    return dummy.next;
}
```

The Ghost was discarded. `dummy.next` was the merged head.
`tail->next = nullptr` sealed the end cleanly.

---

### 🎺 The Trial of the Three Processions

```cpp
int main() {
    ListNode* l1 = new ListNode(1);
    l1->next = new ListNode(4);
    l1->next->next = new ListNode(5);

    ListNode* l2 = new ListNode(1);
    l2->next = new ListNode(3);
    l2->next->next = new ListNode(4);

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

---

**Full trace:**

Initial heap after loading fronts: `{1(l1), 1(l2), 2(l3)}`

| Step | Heap top | Attached | Successor pushed | Heap after        |
| ---- | -------- | -------- | ---------------- | ----------------- |
| 1    | 1 (l1)   | 1        | 4 (l1)           | {1(l2), 2(l3), 4} |
| 2    | 1 (l2)   | 1        | 3 (l2)           | {2(l3), 3, 4}     |
| 3    | 2 (l3)   | 2        | 6 (l3)           | {3, 4, 6}         |
| 4    | 3 (l2)   | 3        | 4 (l2)           | {4(l1), 4(l2), 6} |
| 5    | 4 (l1)   | 4        | 5 (l1)           | {4(l2), 5, 6}     |
| 6    | 4 (l2)   | 4        | none             | {5, 6}            |
| 7    | 5 (l1)   | 5        | none             | {6}               |
| 8    | 6 (l3)   | 6        | none             | {}                |

**Result: 1 → 1 → 2 → 3 → 4 → 4 → 5 → 6 → NULL** ✓

Eight nodes. Eight pops. Every comparison done in O(log 3).

---

**Trace for `[[1,2,3]]` (single list, k=1):**

Heap always has 1 element. Pop, push successor. No real comparison.
Result = the list itself. O(N log 1) = O(N).

---

**Trace for `[[], [1], []]` (empty lists mixed in):**

Null lists skipped during loading. Heap starts with just `{1}`.
Pop 1, no successor. Heap empty. Result = `1 → NULL`.

---

**Trace for `[[1,4],[2,3]]` (two lists, k=2):**

| Step | Heap top | Attached | Successor | Heap after |
| ---- | -------- | -------- | --------- | ---------- |
| 1    | 1        | 1        | 4         | {2, 4}     |
| 2    | 2        | 2        | 3         | {3, 4}     |
| 3    | 3        | 3        | none      | {4}        |
| 4    | 4        | 4        | none      | {}         |

**Result: 1 → 2 → 3 → 4 → NULL** ✓

For k=2, this is equivalent to merge-two-sorted-lists
but using a heap instead of direct comparison.
The heap adds O(log 2) overhead per step -- negligible.

---

## 🔍 Why Heap and Not Merge Pairs?

An alternative approach: merge lists pairwise, like merge sort.

```
Round 1: merge list 0+1, merge list 2+3, ...  → K/2 lists
Round 2: merge pairs again                     → K/4 lists
...
After log K rounds: 1 merged list
```

This is also O(N log K) but doesn't use a heap.
It's the **divide and conquer** approach.

Both are equally valid. The heap approach is more intuitive --
"always pick the smallest front" -- and easier to code.
The pairwise approach avoids the heap overhead
but requires managing the merging rounds.

---

## ⚠️ Why Store Node Pointers, Not Values?

The heap stores `ListNode*` pointers, not integer values.

This is critical because:
-   We need to know which LIST the minimum came from
    (to push its successor).
-   The node's `->next` pointer gives us the successor directly.
-   No need for a separate index or list-tracking structure.

The comparator only reads `node->val` for ordering.
The pointer carries all the context we need.

---

### 🧠 Memory of the Grand Tournament Law

-   **Min-Heap** of `ListNode*` -- smallest value on top via custom comparator
-   **Comparator:** `a->val > b->val` flips max-heap to min-heap
-   **Ghost Sentinel** anchors result chain; `tail` grows it forward
-   **Init:** push front node of every non-null list → heap has at most K nodes
-   **Each step:** pop min → attach to tail → push its `->next` (if exists)
-   **Loop ends** when heap is empty -- all nodes attached
-   Heap holds at most K nodes at any time (one per list)
-   **Time:** O(N log K) -- N total nodes, O(log K) per push/pop
-   **Space:** O(K) -- the heap
-   **Edge cases:**
    -   Empty lists array → return nullptr
    -   All lists empty → return nullptr
    -   Single list → return it as-is (heap has 1 element)
    -   Two lists → equivalent to merge-two-sorted-lists with heap

Thus is remembered the saga of **Merge K Sorted Lists (Heap)**,
where the Tournament Master refused to compare K fronts one by one --
instead maintaining a Min-Heap that always knew
which soldier deserved to step forward next --
popping champions, attaching them to the growing chain,
and immediately crowning their successors --
until all K processions had dissolved
into one perfectly ordered march. 🔗🏆✨
