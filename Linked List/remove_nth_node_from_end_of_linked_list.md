## 🔗🗡️ _The Scout and the Executioner: The Remove Nth Node From End of List Saga_

> \_"In the Kingdom of Chains,
> a long procession of soldiers marched in a single line.
>
> The Queen pointed a cold finger and declared:
>
> **'Remove the Nth soldier from the end.
> The chain must remain unbroken.
> One pass only — no counting twice,
> no walking the road a second time.'**
>
> The Executioner frowned.
>
> To remove a soldier from the end,
> he must find the soldier **just before** the target —
> so he could unlink the target
> and seal the chain behind it.
>
> But counting from the end on a linked list
> meant walking to the finish first —
> and that was forbidden.
>
> So the Executioner devised a cunning trick:
>
> **Send a Scout exactly N steps ahead.**
>
> Then march both the Scout and the Executioner together.
>
> When the Scout falls off the edge into NULL —
> the Executioner stands exactly
> one step before the soldier to be removed.
>
> One pass. One gap. One clean removal.
>
> But first — a Ghost must be placed at the front,
> for the soldier to be removed
> might be the very first in line."\_

---

This is the saga of **Remove Nth Node From End of List**.

You are given the `head` of a linked list and an integer `n`.

Your task:

-   Remove the **Nth node from the end** of the list.
-   Return the head of the modified list.

```
Input:  1 → 2 → 3 → 4 → 5 → NULL,  n = 2
Output: 1 → 2 → 3 → 5 → NULL
```

The 2nd from the end is node `4`. It is removed.

---

## 🧠 The Oracle's Core Insight — The Gap Trick

The problem forbids two passes.
But to remove the Nth node from the end,
we need to find the node **just before it**.

The Executioner's revelation:

> **If the Scout starts N steps ahead of the Executioner,
> they maintain a fixed gap of N nodes between them.
> When the Scout reaches NULL —
> the Executioner is exactly at the node before the target.**

This is the **Gap Trick** — the entire algorithm in one idea.

Visual proof for `n = 2` on `1 → 2 → 3 → 4 → 5`:

```
After gap setup:   Scout at 3,  Executioner at Ghost(0)
March together:    Scout at 4,  Executioner at 1
March together:    Scout at 5,  Executioner at 2
March together:    Scout at NULL, Executioner at 3
```

Executioner at `3` — exactly one before `4` (the 2nd from end).
Unlink `4`. Done.

The **Ghost Sentinel** handles the edge case where `n` equals
the length of the list — meaning the head itself must be removed.
Without the Ghost, the Executioner would have no node to stand on.

---

### 📜 The Scroll of the Procession

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
and a single hand pointing to the soldier ahead (`next`).

---

## 👻 The Ghost Sentinel Takes Its Place

_The anchor that survives even when the head must be deleted_

```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* scout = &dummy;
    ListNode* exec  = &dummy;
```

The Executioner summoned the **Ghost Sentinel** —
a valueless soldier placed silently at the very front,
pointing to the real `head`.

Both the Scout and the Executioner began standing at the Ghost.

Why the Ghost?

> If `n` equals the list length,
> the head soldier must be removed.
> Without the Ghost, the Executioner would have nowhere to stand —
> no node behind the head exists.
> The Ghost **becomes** that missing node,
> allowing the Executioner to unlink the head cleanly.

---

## 🏃 The Scout Marches N Steps Ahead

_Creating the sacred gap_

```cpp
    for (int i = 0; i < n; i++) {
        scout = scout->next;
    }
```

The Scout marched forward exactly `n` steps —
opening a gap of exactly `n` soldiers between them.

For `n = 2` on a 5-node list, starting from the Ghost:

```
After loop:  Scout at node 2,  Executioner at Ghost
Gap = 2 nodes between them
```

The Executioner stood still.
The gap had been set.

---

## 👣 The March Together Begins

_Maintain the gap until the Scout hits NULL_

```cpp
    while (scout->next != nullptr) {
        scout = scout->next;
        exec  = exec->next;
    }
```

Now both marched in lockstep —
Scout one step, Executioner one step —
the gap of `n` nodes preserved at all times.

The march continued until `scout->next` became `nullptr` —
meaning the Scout had reached the **last soldier** in the chain.

At this exact moment, the Executioner stood
at the node **just before the target** to be removed.

For `n = 2` on `1 → 2 → 3 → 4 → 5`:

```
Scout at 5 (last),  Executioner at 3
Target to remove: exec->next = node 4
```

---

## 🗡️ The Execution — Unlink the Target

```cpp
    exec->next = exec->next->next;
```

The Executioner reached past the target soldier
and gripped the one beyond —
linking directly to the soldier after the condemned.

The target was bypassed.
Removed from the chain.
The procession closed seamlessly behind it.

> _"No announcement. No ceremony.
> The chain simply continued
> as if the soldier had never existed."_

---

## 🏁 The Ghost Departs — The Real Head Returns

```cpp
    return dummy.next;
}
```

The Ghost Sentinel was discarded.

`dummy.next` pointed to the real head —
whether the original head survived
or was itself the soldier that was removed.

Either way, the Ghost had ensured the Executioner
always had a node to stand on.

---

### 🎺 The Trial of the Five-Soldier Procession

```cpp
int main() {
    // Build: 1 → 2 → 3 → 4 → 5 → NULL
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    ListNode* result = removeNthFromEnd(head, 2);

    ListNode* curr = result;
    while (curr) {
        cout << curr->val;
        if (curr->next) cout << " → ";
        curr = curr->next;
    }
    cout << " → NULL" << endl;
    // expected: 1 → 2 → 3 → 5 → NULL
    return 0;
}
```

The Executioner performed the ritual:

**Ghost placed.** Scout and Executioner both at Ghost.

**Scout marches 2 steps:** Scout at `2`, Executioner at Ghost.

**March together:**

-   Scout→`3`, Executioner→`1`
-   Scout→`4`, Executioner→`2`
-   Scout→`5`, Executioner→`3`
-   Scout's next is NULL — **stop.**

**Execute:** `exec->next = exec->next->next`
Node `4` unlinked. Node `3` now points to node `5`.

**Result:** `1 → 2 → 3 → 5 → NULL` ✓

Node `4` — the 2nd from the end — was silently removed.

---

### 🧠 Memory of the Scout and Executioner Law

-   **Ghost Sentinel** (`dummy`) placed before `head` — handles deletion of the head itself
-   Both Scout and Executioner start at the Ghost
-   **Scout marches `n` steps first** — creates the gap
-   **Both march together** until `scout->next == nullptr`
-   Executioner now sits **one before the target** — `exec->next` is the condemned node
-   **Unlink:** `exec->next = exec->next->next`
-   **Return `dummy.next`** — the real head, whether changed or not
-   **Time:** O(n) — one single pass through the list
-   **Space:** O(1) — only two pointers and a Ghost

Thus is remembered the saga of
**Remove Nth Node From End of List**,
where the Executioner never walked the road twice —
instead sending a Scout exactly N steps ahead,
marching together in lockstep until the Scout hit the edge,
and removing the condemned soldier
in one clean, precise stroke —
guided always by the silent Ghost
who stood ready at the front
so no removal was ever impossible. 🔗🗡️✨
