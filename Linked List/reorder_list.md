## 🔀 _The Dance of the Twin Paths: The Reorder List Saga_

> _"A procession marches from first to last,
> yet the Queen commands a new formation —
> where the first meets the last,
> then the second meets the second-last,
> weaving two paths into one braided dance."_

---

In the kingdom of Linked Lists, warriors stood in a straight, solemn line:
`L1 → L2 → L3 → … → Ln`.
But the Queen desired a **braided procession** instead of the plain march. She commanded:

> _“Let the first walk with the last,
> the second with the second-last,
> until all are woven into one alternating dance.”_

To fulfill this command, the Court Mechanic devised a three-part ritual:

1. **Split the list** into two halves.
2. **Reverse the second half.**
3. **Weave** the two halves together, alternating nodes.

Thus began the saga of **Reorder List**.

---

### 📜 The Scroll of Warriors

```cpp
#include <iostream>
using namespace std;

struct ListNode {
    int val;
    ListNode* next;
    ListNode(int v) : val(v), next(NULL) {}
};
```

Each warrior held a number and pointed to the next in line.

---

## ⚔️ Phase 1 — The Splitting of the Line

_Finding the middle with Tortoise and Hare_

```cpp
void reorderList(ListNode* head) {
    if (!head || !head->next) return;

    ListNode* slow = head;
    ListNode* fast = head;
```

The Mechanic called forth the Tortoise (`slow`) and the Hare (`fast`) to locate the midpoint.

---

```cpp
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
```

When the Tortoise reached the center, the Hare had already sprinted to the far side.
The list was now split between:

-   **First half:** from `head` to `slow`
-   **Second half:** from `slow->next` to the end

---

### 🪞 Phase 2 — The Reversal of the Second Path

_Turning the later warriors to face backward_

```cpp
    ListNode* second = slow->next;
    slow->next = NULL; // cut the list

    ListNode* prev = NULL;
    while (second) {
        ListNode* nextNode = second->next;
        second->next = prev;
        prev = second;
        second = nextNode;
    }
```

The second half’s warriors were reversed —
their arrows turned backward one by one,
until the tail became the new head of the second path: `prev`.

---

### 🔀 Phase 3 — The Weaving of Two Paths

_The alternating dance_

```cpp
    ListNode* first = head;
    second = prev;

    while (second) {
        ListNode* t1 = first->next;
        ListNode* t2 = second->next;

        first->next = second;
        second->next = t1;

        first = t1;
        second = t2;
    }
}
```

Now came the braid:

-   The front warrior from the first path stepped forward.
-   The front warrior from the reversed second path joined beside him.
-   Then the next from the first,
-   Then the next from the second,
-   And so on…

Like weaving two ribbons into one living lace.

The order became:
`L1 → Ln → L2 → Ln-1 → L3 → Ln-2 → …`
until all warriors were placed.

---

### 🎺 The Trial of the Procession

```cpp
int main() {
    // Build 1->2->3->4->5
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    reorderList(head);
    // expected: 1 -> 5 -> 2 -> 4 -> 3

    while (head) {
        cout << head->val << " ";
        head = head->next;
    }
    cout << endl;
    return 0;
}
```

The plain line
`1 → 2 → 3 → 4 → 5`
was transformed into the Queen’s braided march:
`1 → 5 → 2 → 4 → 3`

A perfect weaving of front and back.

---

### 🧠 Memory of the Braided Dance

-   **Find middle** with slow + fast pointers.
-   **Reverse second half** in-place.
-   **Merge halves** by alternating nodes.
-   **Time:** O(n), **Space:** O(1).
-   No new nodes created — only the arrows change direction.

Thus is remembered the saga of **Reorder List**,
where two paths — the forward and the reversed —
intertwine into one harmonious dance of alternating steps. 🔀✨
