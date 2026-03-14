## 🔗⚔️ _The Partial Rebellion of the Chain: The Reverse Linked List II Saga_

> \_"In the Kingdom of Chains,
> the Great Reversal had once turned an entire procession around.
> Every soldier. Every link. The whole chain.
>
> But now the Queen issued a more precise decree:
>
> **'I do not want the entire chain reversed.
> Only a portion of it — from position left to position right.
> The soldiers before left must remain untouched.
> The soldiers after right must remain untouched.
> Only the middle segment shall be turned around.'**
>
> The Architect studied the chain.
>
> A full reversal was easy — start at the head, reverse everything.
> But a partial reversal required surgical precision:
>
> First, walk to the soldier just before position left —
> the one who would need to reconnect after the rebellion.
> Call him the **Predecessor**.
>
> Then, reverse only the soldiers from left to right —
> but do it cleverly, without losing the tail connection.
>
> There was an elegant way —
> a technique called the **Front-Insert Reversal**.
>
> Instead of the classic three-hand reversal,
> the Architect would pull each soldier
> from the middle segment
> and plant them one by one
> directly behind the Predecessor —
> like inserting cards at the front of a hand.
>
> The segment would reverse itself
> without ever losing the connection to what came after.
>
> One Ghost. One Predecessor. One moving insertion.
> The partial rebellion would be crushed —
> and order restored to the exact specification."\_

---

This is the saga of **Reverse Linked List II**.

You are given the `head` of a linked list
and two integers `left` and `right`.

Your task:

-   Reverse the nodes from position `left` to position `right`.
-   Return the head of the modified list.
-   Positions are **1-indexed**.

```
Input:  1 → 2 → 3 → 4 → 5 → NULL,  left = 2,  right = 4
Output: 1 → 4 → 3 → 2 → 5 → NULL
```

Only nodes `2`, `3`, `4` were reversed. The rest stayed.

---

## 🧠 The Oracle's Core Insight — Front-Insert Reversal

The naive approach would be:
walk to `left`, cut the segment, reverse it classically, reconnect.
That requires careful bookkeeping of four pointers.

The elegant approach is the **Front-Insert Reversal**:

> Position the Predecessor just before `left`.
> Then, for each of the `(right - left)` steps,
> take the node **immediately after** the current reversed segment's tail,
> and insert it **immediately after** the Predecessor.

Watch it happen on `1 → 2 → 3 → 4 → 5`, `left=2`, `right=4`:

```
Predecessor = node(1),  tail of segment = node(2)

Step 1: Pull node(3), insert after Predecessor
  1 → 3 → 2 → 4 → 5

Step 2: Pull node(4), insert after Predecessor
  1 → 4 → 3 → 2 → 5
```

Done in exactly `right - left` steps.
No cutting. No separate reversal. No reconnection puzzle.
The Ghost Sentinel handles `left = 1` where there is no real Predecessor.

---

### 📜 The Scroll of Surgical Precision

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

## 👻 The Ghost Sentinel Guards the Front

_Because the Predecessor must always exist_

```cpp
ListNode* reverseBetween(ListNode* head, int left, int right) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* pred = &dummy;
```

The Ghost Sentinel was placed silently before the real `head`.

`pred` — the **Predecessor** — began at the Ghost.

Why the Ghost?

> If `left = 1`, the reversal begins at the very head.
> There is no real node before it.
> The Ghost **becomes** the Predecessor,
> so the insertion logic works identically
> without any special case.

---

## 👣 Walk to the Predecessor

_Stop one step before position left_

```cpp
    for (int i = 0; i < left - 1; i++) {
        pred = pred->next;
    }
```

The Predecessor marched forward `left - 1` steps —
stopping at the soldier **just before** the rebellion zone.

For `left = 2`: Predecessor walks 1 step → lands on node `1`.
For `left = 1`: Predecessor walks 0 steps → stays at Ghost.

The Predecessor was now anchored.
He would not move again.
Everything would revolve around him.

---

## 🔄 The Front-Insert Reversal

_Pull the next node and plant it behind the Predecessor — repeat_

```cpp
    ListNode* tail = pred->next;
```

`tail` pointed to the **first node of the segment** —
the soldier at position `left`.

He was called `tail` because as each new node was inserted
in front of him, he naturally became the tail
of the growing reversed segment.

He would stay in place throughout the entire process —
a fixed anchor at the back of the reversed zone,
always pointing to what came after.

---

```cpp
    for (int i = 0; i < right - left; i++) {
```

The loop ran exactly `right - left` times —
once for each soldier that needed to be moved
in front of the previous one.

For `left=2, right=4`: the loop runs `4 - 2 = 2` times.

---

### 🎯 Step 1 — Identify the Node to Move

```cpp
        ListNode* mover = tail->next;
```

`mover` was the soldier standing immediately after `tail` —
the next one to be plucked from its current position
and inserted at the front of the reversed segment.

---

### ✂️ Step 2 — Detach the Mover from Its Current Spot

```cpp
        tail->next = mover->next;
```

`tail`'s hand released `mover`
and reached forward to grab `mover`'s successor.

The mover was now floating — detached from the chain.
The chain behind `tail` closed the gap seamlessly.

---

### 📌 Step 3 — Insert the Mover Behind the Predecessor

```cpp
        mover->next = pred->next;
        pred->next = mover;
    }
```

The mover was planted directly behind the Predecessor:

First — `mover->next` was set to whoever was currently
sitting behind the Predecessor (the current front of the reversed segment).

Then — `pred->next` was updated to point to `mover`,
making him the new front of the reversed segment.

The mover had leapt from the back to the front in one move.

> _"Each soldier pulled from the right
> and planted at the left —
> the segment reversed itself from the inside out."_

---

## 🏁 The Ghost Departs — Order Restored

```cpp
    return dummy.next;
}
```

The Ghost was discarded.

`dummy.next` returned the real head of the chain —
unchanged if `left > 1`,
or pointing to the new front soldier if `left = 1`.

The rebellion zone had been perfectly reversed.
Everything before and after it remained exactly as it was.

---

### 🎺 The Trial of the Partial Rebellion

```cpp
int main() {
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    ListNode* result = reverseBetween(head, 2, 4);

    ListNode* curr = result;
    while (curr) {
        cout << curr->val;
        if (curr->next) cout << " → ";
        curr = curr->next;
    }
    cout << " → NULL" << endl;
    // expected: 1 → 4 → 3 → 2 → 5 → NULL
    return 0;
}
```

The Architect performed the ritual:

**Ghost placed. Predecessor walks 1 step → lands on node `1`.**

`tail` = node `2` (first of the segment).

**Loop runs 2 times (`right - left = 4 - 2 = 2`):**

_Iteration 1:_

-   `mover` = node `3`
-   `tail->next` = node `4` (detach `3`)
-   `mover->next` = node `2` (current front of segment)
-   `pred->next` = node `3` (insert `3` at front)
-   Chain: `1 → 3 → 2 → 4 → 5`

_Iteration 2:_

-   `mover` = node `4`
-   `tail->next` = node `5` (detach `4`)
-   `mover->next` = node `3` (current front of segment)
-   `pred->next` = node `4` (insert `4` at front)
-   Chain: `1 → 4 → 3 → 2 → 5`

**Result:** `1 → 4 → 3 → 2 → 5 → NULL` ✓

---

### 🧠 Memory of the Partial Rebellion Law

-   **Ghost Sentinel** (`dummy`) → Predecessor always exists, even when `left = 1`
-   **Walk `left - 1` steps** to reach the Predecessor — the node just before the segment
-   **`tail`** = first node of the segment — stays fixed, becomes the segment's tail
-   **Loop runs `right - left` times** — one insertion per step
-   **Each step:** `mover = tail->next` → detach mover (`tail->next = mover->next`) → insert at front (`mover->next = pred->next`, `pred->next = mover`)
-   No separate reconnection needed — the tail naturally stays connected to what follows
-   **Return `dummy.next`** — the real head, changed or unchanged
-   **Time:** O(n) — one walk to the predecessor + one pass through the segment
-   **Space:** O(1) — only a handful of pointers, no extra memory

Thus is remembered the saga of
**Reverse Linked List II**,
where the Architect did not cut the segment and reverse it separately —
but instead pulled each soldier from the back of the rebellion zone
and planted them one by one at the front,
behind the unmoved Predecessor —
reversing the segment from within,
leaving the rest of the chain
perfectly, surgically untouched. 🔗⚔️✨
