## 🔗⚔️ _The Great Reversal of the Chain: The Reverse Linked List Saga_

> \_"In the Kingdom of Chains,
> soldiers stood in a long line —
> each one gripping the shoulder of the soldier ahead.
>
> Every soldier knew only one thing:
> **who stood in front of them.**
>
> No one looked back.
> No one remembered who came before.
>
> Then the Queen issued a royal decree:
>
> **'Reverse the entire chain.
> Every soldier must now face
> the one who was once behind them.'**
>
> The chain could not be broken.
> No new soldiers could be summoned.
>
> Only three hands were allowed —
> one to remember the past,
> one to stand in the present,
> one to save the future
> before it was lost forever.
>
> And so the Great Reversal began —
> one soldier at a time,
> from the front of the line
> to the very last."\_

---

This is the saga of **Reverse Linked List**.

You are given the `head` of a singly linked list:

```
1 → 2 → 3 → 4 → 5 → NULL
```

Your task:

-   Reverse the chain so it becomes:

```
5 → 4 → 3 → 2 → 1 → NULL
```

---

## 🧠 The Oracle's Core Insight — Three Hands, One Pass

A linked list soldier only knows **who is in front**.
There is no way to look backward.

So to reverse a connection — to make a soldier face back —
the chain ahead must first be **saved**,
or it will be lost the moment we redirect the pointer.

Thus the Oracle assigned **three sacred roles**:

```
prev   → the soldier already reversed (behind us)
curr   → the soldier being reversed right now
next   → the soldier ahead (saved before we cut the link)
```

At every step:

1. **Save** `next` — rescue the future before cutting
2. **Redirect** `curr` to point back at `prev` — the reversal
3. **Advance** `prev` to `curr` — move the past forward
4. **Advance** `curr` to `next` — step into the future

One step. Repeat until `curr` is `NULL`.
The chain is fully reversed.

---

### 📜 The Scroll of the Chain

```cpp
#include <iostream>
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

Each soldier carried a number (`val`)
and a single hand stretched forward (`next`) —
pointing to whoever stood ahead.

No hand reached backward.
That was the rule of the chain.

---

## ⚔️ The Oracle's Three-Hand Reversal Ritual

_One pass. Three roles. No soldier left behind._

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;
```

Before the march began:

-   `prev` was set to `nullptr` —
    for behind the first soldier, there was nothing.
    This `nullptr` would become the new tail's destination.
-   `curr` stood at the `head` — the first soldier in line.

The three hands were ready.

---

### 🔁 The March of Reversal

```cpp
    while (curr != nullptr) {
```

The Oracle marched forward,
soldier by soldier,
until no one remained.

---

### 🖐️ Hand One — Save the Future

```cpp
        ListNode* next = curr->next;
```

Before anything else —
`curr` still held the hand of the soldier ahead.

The Oracle grabbed that connection
and stored it in `next`.

> _"Do not let go of the future
> until the present has been redirected."_

If this step were skipped,
the moment `curr->next` was changed,
the rest of the chain would be lost —
adrift, unreachable, gone forever.

---

### 🔄 Hand Two — Reverse the Connection

```cpp
        curr->next = prev;
```

Now the reversal happened.

`curr` released the hand of the soldier ahead
and reached **backward** —
gripping `prev`, the soldier already reversed.

For the first soldier in line,
`prev` was `nullptr` —
so `curr->next` became `nullptr`,
marking the new end of the chain.

For every soldier after,
`prev` was the last reversed soldier —
so the chain grew backward, link by link.

---

### 👣 Hand Three — Advance Both Roles

```cpp
        prev = curr;
        curr = next;
    }
```

The roles shifted:

-   `prev` stepped forward into `curr`'s place —
    the soldier just reversed became the new "past".
-   `curr` stepped forward into `next`'s place —
    the saved future became the new "present".

The march continued.

---

### 🏁 The New Head of the Reversed Chain

```cpp
    return prev;
}
```

When `curr` finally reached `nullptr`,
the march was over.

`prev` stood at the last soldier —
the one who was once at the tail,
now crowned as the new **head** of the reversed chain.

---

### 🎺 The Trial of the Five-Soldier Chain

```cpp
int main() {
    // Build: 1 → 2 → 3 → 4 → 5 → NULL
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    ListNode* reversed = reverseList(head);

    // Print: 5 → 4 → 3 → 2 → 1 → NULL
    while (reversed != nullptr) {
        cout << reversed->val;
        if (reversed->next) cout << " → ";
        reversed = reversed->next;
    }
    cout << " → NULL" << endl;
    return 0;
}
```

The five soldiers stood in line:
`1 → 2 → 3 → 4 → 5 → NULL`

The Oracle walked the chain with three hands.
One by one, each soldier was turned around.

When the march ended,
soldier `5` stood at the front,
soldier `1` held `nullptr` —
the new tail of the reversed chain:

`5 → 4 → 3 → 2 → 1 → NULL`

The Queen's decree had been fulfilled.

---

### 🧠 Memory of the Three-Hand Law

-   `prev` = the already-reversed portion (starts as `nullptr`)
-   `curr` = the soldier being processed right now (starts at `head`)
-   `next` = the saved future (rescued before cutting the link)
-   **Every step:** save `next` → redirect `curr` → advance `prev` → advance `curr`
-   Loop ends when `curr == nullptr`
-   Return `prev` — the new head of the reversed chain
-   **Time:** O(n) — one pass through every soldier
-   **Space:** O(1) — only three pointers, no extra memory

Thus is remembered the saga of **Reverse Linked List**,
where the Oracle marched through a chain of forward-facing soldiers
with nothing but three hands —
saving the future before cutting each link,
redirecting each soldier to face the past,
and stepping forward until the entire chain
stood proud in perfect reversal. 🔗✨
