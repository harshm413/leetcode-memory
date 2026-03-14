## 🔗🎭 _The Three-Act Ceremony of the Chain: The Reorder List Saga_

> \_"In the Kingdom of Chains,
> a long procession of soldiers marched in a line —
> first, second, third… all the way to the last.
>
> The Queen summoned the Architect and declared:
>
> **'I want the procession reordered.
> The first soldier pairs with the last.
> The second soldier pairs with the second-to-last.
> And so on, weaving front and back together —
> until the chain meets in the middle.'**
>
> The Architect studied the chain.
>
> He could not simply grab the last soldier —
> for a linked list has no memory of who came before.
> Walking to the end and back would take forever.
>
> So he devised a ceremony of three acts:
>
> **Act I — Find the Middle.**
> Split the chain into two halves.
>
> **Act II — Reverse the Second Half.**
> Turn the back procession around
> so its soldiers face forward in reverse order.
>
> **Act III — Weave the Two Halves.**
> Interleave front and back, one soldier at a time,
> until the chain was perfectly reordered.
>
> Three acts. One chain. No extra soldiers.
> The ceremony could begin."\_

---

This is the saga of **Reorder List**.

You are given the `head` of a linked list:

```
1 → 2 → 3 → 4 → 5 → NULL
```

Your task — reorder it in-place to:

```
1 → 5 → 2 → 4 → 3 → NULL
```

The pattern:
first pairs with last, second pairs with second-to-last,
and so on until the middle is reached.

---

## 🧠 The Oracle's Core Insight — Three Acts, No Extra Memory

The chain cannot be indexed like an array.
Grabbing the last soldier takes O(n) every time.

So the Architect broke the problem into three clean acts,
each one a skill the Kingdom had already mastered:

```
Act I   →  Find Middle         (Tortoise & Hare)
Act II  →  Reverse Second Half (Three-Hand Reversal)
Act III →  Weave Two Halves    (One from each, alternating)
```

Together they transform the chain in O(n) time and O(1) space.

---

### 📜 The Scroll of the Ceremony

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

Each soldier carried a value (`val`)
and a single hand pointing forward (`next`).
No backward glance. No memory of the past.

---

## 🎬 Act I — Find the Middle of the Chain

_Send the Tortoise and the Hare_

```cpp
void reorderList(ListNode* head) {
    if (!head || !head->next) return;

    ListNode* slow = head;
    ListNode* fast = head;
```

The Architect sent two scouts —
the slow **Tortoise** and the fast **Hare** —
both starting at the `head`.

The Tortoise took one step at a time.
The Hare took two.

When the Hare reached the end,
the Tortoise stood exactly at the **middle**.

---

```cpp
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
```

The race ran until the Hare had nowhere left to go.

For a list of length 5: `1 → 2 → 3 → 4 → 5`
The Tortoise stopped at node `3` — the middle.

For a list of length 4: `1 → 2 → 3 → 4`
The Tortoise stopped at node `2` — the end of the first half.

---

```cpp
    ListNode* secondHalf = slow->next;
    slow->next = nullptr;
```

The Architect **cut the chain** at the middle.

`slow->next = nullptr` sealed the first half —
now it was its own complete chain ending at `NULL`.

`secondHalf` pointed to the start of the second half —
ready to be reversed in Act II.

The chain was now two separate processions:

```
First half:  1 → 2 → 3 → NULL
Second half: 4 → 5 → NULL
```

---

## 🎬 Act II — Reverse the Second Half

_The Three-Hand Reversal returns_

```cpp
    ListNode* prev = nullptr;
    ListNode* curr = secondHalf;
```

The Architect summoned the **Three-Hand Reversal** —
the same ritual used to reverse an entire chain.

`prev` began as `nullptr` — the new tail's destination.
`curr` began at the start of the second half.

---

```cpp
    while (curr != nullptr) {
        ListNode* nextNode = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextNode;
    }
    ListNode* reversedSecond = prev;
```

One by one, each soldier in the second half
was turned around to face backward.

After the reversal:

```
First half:           1 → 2 → 3 → NULL
Reversed second half: 5 → 4 → NULL
```

`reversedSecond` now pointed to `5` —
the new head of the reversed back procession.

---

## 🎬 Act III — Weave the Two Halves Together

_One from the front. One from the back. Repeat._

```cpp
    ListNode* first = head;
    ListNode* second = reversedSecond;
```

The Architect held two hands —
one reaching into the front procession (`first`),
one reaching into the reversed back procession (`second`).

---

```cpp
    while (second != nullptr) {
```

The weaving continued as long as the back procession had soldiers.

The front half always has equal or one more soldier than the back —
so the back being exhausted is the natural stopping point.

---

### 🧵 Save, Attach Front, Attach Back, Advance

```cpp
        ListNode* nextFirst = first->next;
        ListNode* nextSecond = second->next;

        first->next = second;
        second->next = nextFirst;

        first = nextFirst;
        second = nextSecond;
    }
}
```

At every step, the Architect performed four precise moves:

**Save the futures first** —
`nextFirst` rescued where `first` was going.
`nextSecond` rescued where `second` was going.

**Attach the back soldier behind the front soldier** —
`first->next = second`
The front soldier now gripped the back soldier's hand.

**Attach the next front soldier behind the back soldier** —
`second->next = nextFirst`
The back soldier now pointed forward to the next front soldier.

**Advance both hands** —
`first = nextFirst` and `second = nextSecond`
Both processions stepped forward, ready for the next weave.

---

### 🎺 The Trial of the Five-Soldier Chain

```cpp
int main() {
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    reorderList(head);

    ListNode* curr = head;
    while (curr) {
        cout << curr->val;
        if (curr->next) cout << " → ";
        curr = curr->next;
    }
    cout << " → NULL" << endl;
    // expected: 1 → 5 → 2 → 4 → 3 → NULL
    return 0;
}
```

The ceremony unfolded:

**Act I — Find Middle:**
`1 → 2 → 3 | 4 → 5`
Tortoise stopped at `3`. Chain cut in two.

**Act II — Reverse Second Half:**
`4 → 5` became `5 → 4`

**Act III — Weave:**

-   Attach `5` after `1` → `1 → 5`, next front: `2`, next back: `4`
-   Attach `4` after `2` → `2 → 4`, next front: `3`, next back: `NULL`
-   Back exhausted. Stop.

Result: `1 → 5 → 2 → 4 → 3 → NULL` ✓

The Queen's ceremony was complete.

---

### 🧠 Memory of the Three-Act Ceremony Law

-   **Act I** — Tortoise & Hare find the middle → cut chain at `slow->next = nullptr`
-   **Act II** — Three-Hand Reversal on the second half → `prev`, `curr`, `nextNode`
-   **Act III** — Weave: save both nexts → attach back behind front → attach next-front behind back → advance both
-   The back half drives the while loop — stop when `second == nullptr`
-   The middle node naturally becomes the new tail (its `next` was cut to `nullptr` in Act I)
-   **Time:** O(n) — three linear passes over the list
-   **Space:** O(1) — no new soldiers, no extra arrays, only pointer manipulation

Thus is remembered the saga of **Reorder List**,
where the Architect performed a three-act ceremony
on a chain that could not be indexed or reversed in one move —
finding the middle with a Tortoise and Hare,
reversing the second half with three hands,
and weaving front and back together
one soldier at a time —
until the Queen's perfectly interleaved procession
stood proud and complete. 🔗🎭✨
