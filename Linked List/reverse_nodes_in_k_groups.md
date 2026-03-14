## 🔗🏰 _The Regiment Reversal: The Reverse Nodes in K-Group Saga_

> \_"The Kingdom of Chains had seen many reversals.
> A full chain reversed. A segment reversed between two positions.
>
> But now the General issued the most disciplined decree yet:
>
> **'Divide the entire procession into regiments of exactly K soldiers.
> Reverse each regiment completely.
> Leave them in their reversed form, one after the other.
> If the final regiment has fewer than K soldiers —
> they are not worthy of reversal.
> Leave them as they are.'**
>
> The Architect studied the chain.
>
> He knew how to reverse a segment — he had done it before.
> But doing it regiment by regiment,
> connecting each reversed regiment to the next,
> without losing the tail and without corrupting the head —
> that required a precise ritual.
>
> His plan had three steps — repeated for every regiment:
>
> **Step 1 — Scout ahead K steps.**
> Can a full regiment of K be formed?
> If not — stop. Leave the remainder untouched.
>
> **Step 2 — Reverse the K soldiers.**
> The classic three-hand reversal, but for exactly K nodes.
>
> **Step 3 — Reconnect the reversed regiment.**
> The Predecessor must point to the new head of the reversed segment.
> The old head — now the new tail — must point forward
> to wherever the next regiment begins.
>
> A Ghost Sentinel stood at the front —
> so the first regiment always had a Predecessor to connect to,
> just like every regiment after it.
>
> Regiment by regiment.
> K at a time.
> The entire chain was systematically reversed."\_

---

This is the saga of **Reverse Nodes in k-Group**.

You are given the `head` of a linked list and an integer `k`.

Your task:

-   Reverse every **k nodes** as a group.
-   If the remaining nodes at the end are fewer than k — leave them as-is.
-   Return the new head.

```
Input:  1 → 2 → 3 → 4 → 5,  k = 2
Output: 2 → 1 → 4 → 3 → 5

Input:  1 → 2 → 3 → 4 → 5,  k = 3
Output: 3 → 2 → 1 → 4 → 5
```

---

## 🧠 The Oracle's Core Insight — Scout, Reverse, Reconnect. Repeat.

The problem is solved regiment by regiment.

For each regiment, three questions must be answered:

```
1. Does a full regiment of K exist from here?
   → Scout K steps ahead. If we fall off the chain — stop.

2. How do we reverse exactly K nodes?
   → Classic three-hand reversal (prev, curr, next) for K iterations.

3. How do we reconnect the reversed regiment?
   → predecessor->next  = new head of reversed segment (was the Kth node)
   → old head of segment (now tail) ->next = start of next regiment
```

The **Ghost Sentinel** ensures the first regiment always has
a Predecessor — making all K groups identical in treatment.

After reversing K nodes, we do not restart from the beginning.
We simply advance the Predecessor to the tail of the just-reversed regiment
and repeat.

---

### 📜 The Scroll of the Regiment Orders

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

---

## 👻 The Ghost Sentinel — The Universal Predecessor

```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* pred = &dummy;
```

The Ghost Sentinel was placed before the real `head`.

`pred` — the **Predecessor** — began at the Ghost.

After every regiment reversal,
`pred` would advance to the tail of the just-reversed group —
becoming the predecessor of the next regiment to be reversed.

The Ghost made the first regiment no different from any subsequent one.

---

## 🔁 The Regiment Loop — Repeat Until Chain is Exhausted

```cpp
    while (true) {
```

The Architect marched regiment by regiment —
the loop would break only when
fewer than K soldiers remained.

---

## 🔭 Step 1 — Scout Exactly K Steps Ahead

_Can a full regiment be formed?_

```cpp
        ListNode* scout = pred;
        for (int i = 0; i < k; i++) {
            scout = scout->next;
            if (scout == nullptr) return dummy.next;
        }
```

The Scout walked K steps forward from the current Predecessor.

At every step, if the Scout fell into `nullptr` —
there were fewer than K soldiers remaining.

The General's decree was clear:
**do not reverse an incomplete regiment.**

Return `dummy.next` immediately — the work was done.

If the Scout successfully took all K steps,
it now stood at the **Kth node** — the last soldier of this regiment.

A full regiment existed. The reversal could begin.

---

## ⚔️ Step 2 — Reverse Exactly K Nodes

_The three-hand reversal, but for exactly K steps_

```cpp
        ListNode* prev = nullptr;
        ListNode* curr = pred->next;
        ListNode* groupTail = pred->next;
```

Three roles were assigned:

-   `prev` started as `nullptr` — the new tail of the reversed segment
    would point here (connecting it to what comes after, momentarily).
-   `curr` started at `pred->next` — the first soldier of this regiment,
    who would become the tail after reversal.
-   `groupTail` was also `pred->next` — a bookmark on this first soldier,
    because after reversal we need to reconnect it forward.

---

```cpp
        for (int i = 0; i < k; i++) {
            ListNode* nextNode = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextNode;
        }
```

The classic three-hand reversal — executed exactly K times.

At every step:

-   `nextNode` saved the future before cutting the link.
-   `curr->next` was redirected backward to `prev`.
-   `prev` advanced into `curr`'s position.
-   `curr` advanced into the saved future.

After K iterations:

-   `prev` pointed to the **new head** of the reversed regiment
    (the Kth node — the one the Scout had stood on).
-   `curr` pointed to the **first soldier of the next regiment**
    (whatever came after this group).
-   `groupTail` still pointed to the **new tail** of the reversed regiment
    (the soldier who was originally first, now last).

---

## 🔗 Step 3 — Reconnect the Reversed Regiment

_Stitch the reversed segment back into the chain_

```cpp
        pred->next   = prev;
        groupTail->next = curr;
```

Two surgical connections closed the chain:

**Connection 1:** `pred->next = prev`
The Predecessor now pointed to the new head of the reversed regiment —
the soldier who was Kth, now first.

**Connection 2:** `groupTail->next = curr`
The new tail of the reversed regiment (once the first, now last)
pointed forward to the start of the next regiment —
wherever `curr` had landed after the reversal loop.

The regiment was stitched back into the chain.
Order was restored — in reverse.

---

## 👣 Advance the Predecessor and Repeat

```cpp
        pred = groupTail;
    }
    return dummy.next;
}
```

`pred` advanced to `groupTail` —
the tail of the just-reversed regiment.

From here, the next iteration would scout K steps ahead again,
reverse the next regiment, and reconnect it.

The loop marched forward through the chain,
regiment by regiment, until the Scout could not complete K steps.

`dummy.next` returned the new head — the head of the first reversed regiment.

---

### 🎺 The Trial of the Five-Soldier Chain

```cpp
int main() {
    // Build: 1 → 2 → 3 → 4 → 5
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    ListNode* result = reverseKGroup(head, 2);

    ListNode* curr = result;
    while (curr) {
        cout << curr->val;
        if (curr->next) cout << " → ";
        curr = curr->next;
    }
    cout << " → NULL" << endl;
    // expected: 2 → 1 → 4 → 3 → 5 → NULL
    return 0;
}
```

The Architect processed two regiments:

**Regiment 1: nodes `1→2`, k=2**

-   Scout walks 2 steps: lands on node `2`. Full regiment confirmed.
-   Reverse `1→2`: becomes `2→1`
-   `pred(Ghost)->next = 2` (new head)
-   `groupTail(1)->next = 3` (start of next regiment)
-   Chain: `Ghost → 2 → 1 → 3 → 4 → 5`
-   `pred` advances to node `1`

**Regiment 2: nodes `3→4`, k=2**

-   Scout walks 2 steps: lands on node `4`. Full regiment confirmed.
-   Reverse `3→4`: becomes `4→3`
-   `pred(1)->next = 4` (new head)
-   `groupTail(3)->next = 5` (start of next regiment)
-   Chain: `Ghost → 2 → 1 → 4 → 3 → 5`
-   `pred` advances to node `3`

**Remaining: node `5`, k=2**

-   Scout walks 1 step → `nullptr` at step 2.
-   Fewer than K remain. Return immediately.

**Result:** `2 → 1 → 4 → 3 → 5 → NULL` ✓

---

### 🧠 Memory of the Regiment Reversal Law

-   **Ghost Sentinel** (`dummy`) → `pred` always exists, even for the first regiment
-   **Scout first:** walk K steps from `pred` — if `nullptr` hit, return `dummy.next` immediately
-   **`groupTail = pred->next`** — bookmark the first node of the group (it becomes the tail after reversal)
-   **Reverse K nodes** using the classic three-hand loop: `nextNode`, `curr->next = prev`, advance both
-   After reversal: `prev` = new head of group, `curr` = start of next group, `groupTail` = new tail
-   **Reconnect:** `pred->next = prev` and `groupTail->next = curr`
-   **Advance:** `pred = groupTail` — move predecessor to the new tail for the next iteration
-   If remainder < K → left untouched (the Scout handles this with its early return)
-   **Time:** O(n) — every node is reversed exactly once
-   **Space:** O(1) — only a handful of pointers, no extra memory

Thus is remembered the saga of **Reverse Nodes in k-Group**,
where the Architect marched through the chain regiment by regiment —
always scouting ahead to confirm a full group existed,
reversing exactly K soldiers with three precise hands,
stitching the reversed segment back into the chain with two connections,
and advancing the Predecessor forward —
until the remaining soldiers were too few to form a regiment,
and the chain stood proudly reorganized,
K soldiers at a time. 🔗🏰✨
