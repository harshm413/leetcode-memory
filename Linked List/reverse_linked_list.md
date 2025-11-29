## 🔄 _The River of Arrows: The Reverse Linked List Saga_

> _"A river once flowed forward with certainty —
> each arrow pointing to the next,
> past leading to future,
> head leading to tail.
> But the Oracle whispered:
> ‘Turn the river around.
> Make the last arrow point first.’"_

---

A chain of warriors stood along the riverbank — each holding a single arrow that pointed to the next warrior. This was the **Linked Line**, flowing always forward.
Yet the Oracle was asked to reverse fate itself:

> _“Let the last become first.
> Let the future point to the past.
> Restore the chain in the opposite direction.”_

To achieve this, she could not rely on magic. She would walk the line once, carrying two companions: **prev** (the one behind) and **curr** (the one in front).
With each step, she would catch an arrow, turn it backwards, and march on.

Thus began the saga of the **Reverse Linked List**.

---

### 📜 The Scroll of Nodes

```cpp
#include <iostream>
using namespace std;

struct ListNode {
    int val;
    ListNode* next;
    ListNode(int v) : val(v), next(NULL) {}
};
```

Each warrior was a `ListNode`, holding a value and an arrow to the next warrior.
Changing direction meant seizing that arrow and making it point backward instead.

---

### 🔄 The Oracle’s Reversal Ritual

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = NULL;
    ListNode* curr = head;
```

`prev` began as **nothing** — the void behind the first warrior.
`curr` stood at the start of the chain, the first warrior whose arrow must be turned.

---

```cpp
    while (curr != NULL) {
        ListNode* nextNode = curr->next;
```

The Oracle looked ahead and secured the **nextNode**,
for once she reversed the arrow, the old future would be lost without this memory.

---

```cpp
        curr->next = prev;
```

Here the miracle occurred:
The warrior `curr` turned his arrow **backward**, now pointing to the one behind him.
Step by step, the river began to flow upstream.

---

```cpp
        prev = curr;
        curr = nextNode;
    }
    return prev;
}
```

The Oracle then advanced:

-   `prev` became the current warrior (now correctly reversed),
-   `curr` marched forward to the next in the old flow.

When `curr` reached the end, the last warrior stood as the new head — `prev` — completing the reversal of the entire river.

---

### 🎺 The Trial of the Chain

```cpp
int main() {
    // create 1 -> 2 -> 3 -> 4 -> 5
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    ListNode* newHead = reverseList(head); // expect: 5 -> 4 -> 3 -> 2 -> 1

    while (newHead) {
        cout << newHead->val << " ";
        newHead = newHead->next;
    }
    cout << endl;
    return 0;
}
```

The warriors once marched:
`1 → 2 → 3 → 4 → 5`

After the Oracle’s ritual, they turned and strode the opposite way:
`5 → 4 → 3 → 2 → 1`

The river had reversed.

---

### 🧠 Memory of the Arrows

-   **prev** — the warrior behind; initially empty.
-   **curr** — the warrior whose arrow you will reverse.
-   **nextNode** — the saved future, so it isn’t lost when arrows turn.
-   **curr->next = prev** — the heart of the reversal.
-   **Move prev and curr forward** — rebuilding the river in reverse.
-   **Time:** O(n), **Space:** O(1).

Thus is remembered the saga of the **Reverse Linked List**,
where arrows are seized, turned backward one by one,
until the river that once flowed from head to tail
flows proudly from tail to head. 🔄✨
