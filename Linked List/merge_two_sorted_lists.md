## 🔗👑 _The Weaving of Two Processions: The Merge Two Sorted Lists Saga_

> \_"From the East came the first procession —
> soldiers marching in perfect ascending order,
> each one smaller than the one behind.
>
> From the West came the second procession —
> also ordered, also disciplined,
> marching with the same quiet pride.
>
> The Queen stood at the crossroads and declared:
>
> **'Weave these two processions into one.
> The smallest soldier steps forward first.
> Order must never be broken.
> No new soldiers may be created —
> only the existing ones, relinked.'**
>
> The Weaver was summoned.
>
> She did not panic.
> She did not sort from scratch.
>
> She placed a **Ghost Sentinel** at the front —
> a placeholder soldier with no value,
> whose only purpose was to anchor the new chain
> so the Weaver's hands were always free
> to compare, attach, and advance.
>
> And so the weaving began —
> two fingers, two processions,
> one growing chain behind the Ghost."\_

---

This is the saga of **Merge Two Sorted Lists**.

You are given the heads of two sorted linked lists `list1` and `list2`:

```
list1: 1 → 2 → 4 → NULL
list2: 1 → 3 → 4 → NULL
```

Your task:

-   Merge them into one sorted linked list
-   Return the head of the merged list

```
result: 1 → 1 → 2 → 3 → 4 → 4 → NULL
```

---

## 🧠 The Oracle's Core Insight — The Ghost Sentinel and Two Fingers

Merging two sorted chains is simple in idea:

> Always pick the smaller front soldier
> from either procession,
> attach them to the growing result chain,
> and advance that procession forward.

But there is a practical problem:

> **Who does the first soldier attach to?**
> The result chain has no head yet.

This is where the **Ghost Sentinel** — the dummy node — saves everything.

The Weaver creates one fake soldier at the start.
It holds no meaningful value.
Its only job is to be the **anchor** —
so the Weaver can always say `tail->next = chosen soldier`
without special-casing the very first attachment.

At the end, the Ghost is discarded.
The real merged chain starts at `dummy->next`.

---

### 📜 The Scroll of the Two Processions

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

Each soldier in both processions carried a number (`val`)
and a single hand stretched forward (`next`) —
pointing to the next soldier in their line.

---

## 👻 The Weaver Summons the Ghost Sentinel

_The anchor that makes everything clean_

```cpp
ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
    ListNode dummy(0);
    ListNode* tail = &dummy;
```

The Weaver conjured the **Ghost Sentinel** —
a soldier with value `0`, standing at the very front of the result.

`tail` was the Weaver's working hand —
always pointing to the **last soldier attached**
so the next soldier had somewhere to be linked.

At the start, `tail` pointed to the Ghost itself.

> _"The Ghost asks nothing.
> It gives the Weaver a place to begin
> before the first real soldier is chosen."_

---

## 🔁 The Two-Finger Walk — Weaving the Processions

```cpp
    while (list1 != nullptr && list2 != nullptr) {
```

The Weaver extended two fingers —
one into each procession —
and began the walk.

As long as **both processions still had soldiers**,
the comparison continued.

---

### 👈 If the Left Procession Steps Forward

```cpp
        if (list1->val <= list2->val) {
            tail->next = list1;
            list1 = list1->next;
        }
```

The Weaver glanced at the front of both lines.

If `list1`'s soldier was smaller or equal —
he was the rightful next in the merged chain.

The Weaver attached him behind `tail`,
then advanced the left finger:
`list1` moved one step forward in the East procession.

---

### 👉 If the Right Procession Steps Forward

```cpp
        else {
            tail->next = list2;
            list2 = list2->next;
        }
```

Otherwise, `list2`'s soldier was smaller.

He was attached behind `tail` instead,
and the right finger advanced:
`list2` moved one step forward in the West procession.

---

### 👣 The Tail Advances

```cpp
        tail = tail->next;
    }
```

After every attachment,
`tail` stepped forward to the newly linked soldier —
always standing at the very end of the growing merged chain,
ready to receive the next one.

---

## 🏁 One Procession Finishes First

```cpp
    if (list1 != nullptr) {
        tail->next = list1;
    } else {
        tail->next = list2;
    }
```

When one procession was exhausted,
the other still had soldiers remaining —
all of them already sorted,
all of them guaranteed to be larger than everything attached so far.

The Weaver did not loop through them one by one.

She simply attached the **entire remaining procession**
to the end of the chain in one stroke.

> _"A sorted tail needs no further judgment.
> Let them march in as they are."_

---

### 👻 Discard the Ghost, Return the Real Head

```cpp
    return dummy.next;
}
```

The Ghost Sentinel had served its purpose.

The real merged chain began at `dummy.next` —
the very first soldier chosen during the weaving.

The Ghost was left behind, forgotten,
as the united procession marched forward.

---

### 🎺 The Trial of the Two Processions

```cpp
int main() {
    // list1: 1 → 2 → 4 → NULL
    ListNode* list1 = new ListNode(1);
    list1->next = new ListNode(2);
    list1->next->next = new ListNode(4);

    // list2: 1 → 3 → 4 → NULL
    ListNode* list2 = new ListNode(1);
    list2->next = new ListNode(3);
    list2->next->next = new ListNode(4);

    ListNode* merged = mergeTwoLists(list1, list2);

    while (merged != nullptr) {
        cout << merged->val;
        if (merged->next) cout << " → ";
        merged = merged->next;
    }
    cout << " → NULL" << endl;
    // expected: 1 → 1 → 2 → 3 → 4 → 4 → NULL
    return 0;
}
```

The East procession: `1 → 2 → 4`
The West procession: `1 → 3 → 4`

The Weaver compared front soldiers at every step:

-   `1` vs `1` → left steps forward → chain: `1`
-   `1` vs `1` → right steps forward → chain: `1 → 1`
-   `2` vs `1` → right steps forward → chain: `1 → 1 → 2`

Wait — the Weaver re-checks:

-   `2` vs `3` → left steps forward → chain: `1 → 1 → 2`
-   `4` vs `3` → right steps forward → chain: `1 → 1 → 2 → 3`
-   `4` vs `4` → left steps forward → chain: `1 → 1 → 2 → 3 → 4`
-   West exhausted → attach remaining `4` → chain: `1 → 1 → 2 → 3 → 4 → 4`

The two processions had become one.

---

### 🧠 Memory of the Weaving Law

-   **Ghost Sentinel** (`dummy`) = the anchor that removes all edge case pain
-   **`tail`** = always the last soldier in the growing merged chain
-   **While both lists live** → compare fronts, attach the smaller, advance that list and `tail`
-   **When one list ends** → attach the entire remaining list in one stroke
-   **Return `dummy.next`** — the Ghost is discarded, the real head is revealed
-   **Time:** O(n + m) — every soldier from both processions is visited once
-   **Space:** O(1) — no new soldiers created, only pointers relinked

Thus is remembered the saga of **Merge Two Sorted Lists**,
where the Weaver stood at the crossroads of two orderly processions,
guided by a Ghost that asked nothing and gave everything —
comparing, attaching, advancing,
until both lines dissolved into one
perfectly ordered, unbroken chain. 🔗👑✨
