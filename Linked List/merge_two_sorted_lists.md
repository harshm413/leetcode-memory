## 🤝 _The Binding of Two Rivers: The Merge Two Sorted Lists Saga_

> _"Two rivers flow from distant mountains —
> each calm, each already in order.
> Yet the kingdom asks for one greater river,
> formed by weaving their currents together
> without breaking the order of their stones."_

---

From the north flowed the **First River**, its stones arranged from smallest to largest.
From the east flowed the **Second River**, likewise sorted by nature.

The Steward of Streams was tasked to unite them into **one** ordered river — not by sorting anew, but by merging stone by stone, always choosing the smaller stone to keep the flow unbroken.

Thus began the saga of **Merge Two Sorted Lists**.

---

### 📜 The Scroll of River Stones

```cpp
#include <iostream>
using namespace std;

struct ListNode {
    int val;
    ListNode* next;
    ListNode(int v) : val(v), next(NULL) {}
};
```

Each stone was a node in a linked list — carrying its value and pointing to the next.
Two sorted rivers — `list1` and `list2` — awaited unification.

---

### 🌊 The Steward’s Merging Ritual

```cpp
ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
    ListNode dummy(0);
    ListNode* tail = &dummy;
```

The Steward summoned a **dummy stone**, a placeholder to begin the merged river.
`tail` marked the end of the river built so far.

---

### ⚖️ Choosing the Next Stone

```cpp
    while (list1 != NULL && list2 != NULL) {
        if (list1->val < list2->val) {
            tail->next = list1;
            list1 = list1->next;
        } else {
            tail->next = list2;
            list2 = list2->next;
        }
        tail = tail->next;
    }
```

The two rivers flowed side by side.
At every turn, the Steward compared the leading stones:

-   If `list1` carried the smaller stone, he guided it into the merged river.
-   Otherwise, the stone from `list2` flowed next.

After choosing, he moved the pointer forward in that river and advanced the merged river’s tail.

Thus the new river formed — sorted by the natural order of its tributaries.

---

### 🪢 Binding the Remaining Current

```cpp
    if (list1 != NULL) tail->next = list1;
    else tail->next = list2;

    return dummy.next;
}
```

When one river dried up, the Steward simply attached the remaining stones from the other —
for they were already sorted and needed no further work.

The river of unity began at `dummy.next`.

---

### 🎺 The Trial of the Twin Streams

```cpp
int main() {
    // list1: 1 -> 2 -> 4
    ListNode* list1 = new ListNode(1);
    list1->next = new ListNode(2);
    list1->next->next = new ListNode(4);

    // list2: 1 -> 3 -> 4
    ListNode* list2 = new ListNode(1);
    list2->next = new ListNode(3);
    list2->next->next = new ListNode(4);

    ListNode* merged = mergeTwoLists(list1, list2); // expect: 1 1 2 3 4 4

    while (merged) {
        cout << merged->val << " ";
        merged = merged->next;
    }
    cout << endl;
    return 0;
}
```

Two rivers —
`1 → 2 → 4`
and
`1 → 3 → 4`

flowed into the Steward’s hands.
The unified river emerged as:
`1 → 1 → 2 → 3 → 4 → 4`
A perfect mingling of waters.

---

### 🧠 Memory of the Rivers

-   **dummy node** — a simple anchor so the merged river builds cleanly.
-   **tail** — the end of the merged river at any moment.
-   **Compare list1->val and list2->val** — always choose the smaller stone first.
-   **Attach the rest** — once one list ends, the other is already sorted.
-   **Time:** O(n + m), **Space:** O(1).

Thus is remembered the saga of **Merge Two Sorted Lists**,
where two calm rivers join as one,
stone by stone,
never breaking the sacred order of the flow. 🌊🤝
