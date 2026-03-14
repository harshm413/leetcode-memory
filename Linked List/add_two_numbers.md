## 🔗🕯️ _The Two Scrolls and the Carry Torch: The Add Two Numbers Saga_

> \_"Two ancient scrolls arrived at the Royal Treasury,
> each carrying a number written in a peculiar way —
> digits stored in reverse order along a chain of nodes,
> the smallest digit first, the largest last.
>
> The Royal Treasurer was summoned and commanded:
>
> **'Add these two numbers together.
> Return the sum as a new chain —
> also in reverse order, digit by digit.'**
>
> The Treasurer studied the scrolls.
>
> She could not flip them —
> for reversing a linked list cost precious time,
> and the numbers could be astronomically large.
>
> But she realized:
> the reverse order was actually a gift.
>
> **Addition begins from the rightmost digit — the ones place.**
> And in these chains, the ones place was already first.
>
> So she could simply walk both chains together,
> adding digit by digit, left to right —
> exactly as a child adds numbers on paper.
>
> But there was one companion she could never abandon:
>
> **The Carry Torch.**
>
> Whenever two digits summed to 10 or more,
> the torch lit up — carrying a 1 forward
> to the next column of addition.
>
> Even after both scrolls were exhausted,
> if the torch still burned — one final node must be created.
>
> Walk both chains. Add the digits. Pass the torch.
> Build the result chain with a Ghost Sentinel.
> The sum would emerge — perfectly formed."\_

---

This is the saga of **Add Two Numbers**.

You are given two non-empty linked lists:

```
l1: 2 → 4 → 3   (represents the number 342)
l2: 5 → 6 → 4   (represents the number 465)
```

Digits are stored in **reverse order**.
Each node contains a single digit.

Your task:

-   Add the two numbers and return the sum as a linked list.
-   The result must also be in reverse order.

```
Output: 7 → 0 → 8   (represents the number 807)
```

Because: `342 + 465 = 807`

---

## 🧠 The Oracle's Core Insight — Walk Together, Carry the Torch

Because digits are stored in reverse order,
the `head` of each list is already the **ones digit** —
the exact place where addition begins.

This means we can walk both lists simultaneously from front to back,
adding column by column, just like long addition on paper:

```
Column 1 (ones):  2 + 5 = 7,        carry = 0
Column 2 (tens):  4 + 6 = 10 → 0,   carry = 1
Column 3 (hundreds): 3 + 4 + 1 = 8, carry = 0
Result: 7 → 0 → 8
```

Three rules govern the march:

```
sum   = digit from l1 (or 0) + digit from l2 (or 0) + carry
node  = sum % 10   (the digit to store)
carry = sum / 10   (0 or 1, passed forward)
```

The **Ghost Sentinel** anchors the growing result chain.
When both lists are exhausted AND carry is 0 — the march ends.

---

### 📜 The Scroll of the Treasury

```cpp
#include <iostream>
using namespace std;
```

---

### 🧱 The Structure of a Digit Node

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

Each node in both scrolls carried a single digit (`val`)
and a hand pointing forward to the next digit (`next`).

The ones digit came first. The largest digit came last.
This reverse storage was the secret gift that made
addition natural without any reversal.

---

## 👻 The Ghost Sentinel and the Carry Torch

_Two tools that make everything clean_

```cpp
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);
    ListNode* tail = &dummy;
    int carry = 0;
```

The Treasurer conjured the **Ghost Sentinel** —
a valueless node anchoring the front of the result chain,
so `tail` always had somewhere to attach the next digit node.

`tail` tracked the last node added to the result —
growing the chain forward with each new digit.

`carry` began at `0` —
the **Carry Torch** was unlit at the start.
It would flame to `1` whenever a column summed to 10 or more.

---

## 🔁 The Walk of Addition — Both Scrolls Together

```cpp
    while (l1 != nullptr || l2 != nullptr || carry != 0) {
```

The Treasurer marched as long as:

-   `l1` still had digits — the first scroll was not exhausted.
-   `l2` still had digits — the second scroll was not exhausted.
-   **OR carry was still burning** — even after both scrolls ended,
    a lingering `1` in the torch needed its own node.

This single condition handles all three cases gracefully.

---

### 🕯️ Gather the Digits — Absent Means Zero

```cpp
        int a = (l1 != nullptr) ? l1->val : 0;
        int b = (l2 != nullptr) ? l2->val : 0;
```

The Treasurer reached into the first scroll for digit `a`.
If the first scroll had run out — `a` was simply `0`.

She reached into the second scroll for digit `b`.
If the second scroll had run out — `b` was simply `0`.

> _"A spent scroll gives nothing.
> Nothing is treated as zero.
> The addition continues undisturbed."_

---

### ➕ The Addition and the Torch

```cpp
        int sum   = a + b + carry;
        carry     = sum / 10;
        int digit = sum % 10;
```

The three values — `a`, `b`, and the carry torch — combined.

`sum / 10` told whether the torch should burn forward.
If the sum reached 10 or beyond — carry became `1`.
Otherwise carry extinguished to `0`.

`sum % 10` gave the actual digit to be recorded —
the remainder after pulling out the carry.

For example:

-   `4 + 6 + 0 = 10` → digit = `0`, carry = `1`
-   `3 + 4 + 1 = 8` → digit = `8`, carry = `0`

---

### 🧱 Forge the New Node and Advance

```cpp
        tail->next = new ListNode(digit);
        tail = tail->next;
```

A new node was forged carrying the computed digit,
and attached behind `tail`.

`tail` stepped forward —
always standing at the last node in the growing result chain,
ready to receive the next digit.

---

### 👣 Advance Both Scrolls

```cpp
        if (l1 != nullptr) l1 = l1->next;
        if (l2 != nullptr) l2 = l2->next;
    }
```

Both scroll pointers stepped forward —
but only if their respective scroll still had nodes.
An exhausted scroll simply stayed at `nullptr`,
and its digit would be read as `0` on the next round.

---

## 🏁 The Ghost Departs — The Result Emerges

```cpp
    return dummy.next;
}
```

The Ghost was discarded.

`dummy.next` pointed to the very first digit node of the result —
the ones digit of the final sum.

The full result chain was returned — complete, clean, correct.

---

### 🎺 The Trial of the Two Scrolls

```cpp
int main() {
    // l1: 2 → 4 → 3  (342)
    ListNode* l1 = new ListNode(2);
    l1->next = new ListNode(4);
    l1->next->next = new ListNode(3);

    // l2: 5 → 6 → 4  (465)
    ListNode* l2 = new ListNode(5);
    l2->next = new ListNode(6);
    l2->next->next = new ListNode(4);

    ListNode* result = addTwoNumbers(l1, l2);

    ListNode* curr = result;
    while (curr) {
        cout << curr->val;
        if (curr->next) cout << " → ";
        curr = curr->next;
    }
    cout << " → NULL" << endl;
    // expected: 7 → 0 → 8 → NULL  (807)
    return 0;
}
```

The Treasurer walked both scrolls together:

| Column | l1 digit | l2 digit | carry in | sum | digit | carry out |
| ------ | -------- | -------- | -------- | --- | ----- | --------- |
| 1st    | 2        | 5        | 0        | 7   | **7** | 0         |
| 2nd    | 4        | 6        | 0        | 10  | **0** | 1         |
| 3rd    | 3        | 4        | 1        | 8   | **8** | 0         |

Result chain: `7 → 0 → 8 → NULL` ✓

`342 + 465 = 807` — the sum emerged in reverse order, perfectly.

---

### 🧠 Memory of the Two Scrolls and Carry Torch Law

-   Digits stored in **reverse** — `head` is already the ones digit — no reversal needed
-   **Ghost Sentinel** (`dummy`) anchors the result chain; `tail` grows it forward
-   `carry` begins at `0` — flames to `1` whenever a column sums ≥ 10
-   **While loop condition:** `l1 != nullptr || l2 != nullptr || carry != 0`
-   Absent list → treat its digit as `0`
-   `digit = sum % 10` → stored in new node; `carry = sum / 10` → passed forward
-   If carry remains after both lists end → one final node is created
-   **Return `dummy.next`** — the Ghost is discarded, the real result head returned
-   **Time:** O(max(m, n)) — walk the length of the longer list, plus one possible extra node
-   **Space:** O(max(m, n)) — the result chain holds one node per digit of the sum

Thus is remembered the saga of **Add Two Numbers**,
where the Treasurer walked two reversed scrolls side by side,
gathering digits column by column,
passing the Carry Torch forward whenever the sum overflowed —
building the result chain one node at a time
behind the silent Ghost —
until both scrolls were spent and the torch went dark,
and the sum of two great numbers
lay perfectly encoded in a new chain. 🔗🕯️✨
