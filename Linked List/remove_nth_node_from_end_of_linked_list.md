## 🗡️ _The Silent Strike: The Remove Nth Node From End of List Saga_

> _"A line of warriors marches toward destiny —
> yet the Oracle commands that the nth warrior from the end
> must vanish without disturbing the formation.
> To accomplish this, send two scouts:
> one races ahead, the other follows —
> and when the first reaches the horizon,
> the second stands before the target."_

---

In the kingdom of Linked Lists, the warriors stood in single file.
But one day, the Oracle gave a secret order:

> _“The warrior that stands **n steps from the end**… remove him.
> None must know who was targeted until the moment arrives.”_

To obey this command without counting the whole line, the Royal Scoutmaster used a clever ritual with two pointers —
one fast, one slow — moving so the slow arrives exactly at the warrior **before** the one to be removed.

Thus began the saga of **Remove Nth Node From End of List**.

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

Each warrior held a number and pointed to the next — a chain whose fate could change with one careful cut.

---

## 🏹 Phase I — The Two-Scout Strategy

_Fast goes ahead, Slow follows._

```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0);
    dummy.next = head;
```

The Scoutmaster placed a **dummy** warrior before all others —
a safe place to begin the mission, even if the first actual warrior is the one to be removed.

---

```cpp
    ListNode* slow = &dummy;
    ListNode* fast = &dummy;
```

Two scouts were chosen:

-   **Fast**, who sprints ahead.
-   **Slow**, who marches steadily behind.

---

### 🏃 Phase II — Fast Steps Forward by `n`

```cpp
    for (int i = 0; i < n; i++) {
        fast = fast->next;
    }
```

Fast ran ahead **n steps**, leaving Slow behind.
When Fast reaches the horizon, Slow will stand right before the target.

---

### 🔍 Phase III — Walk Together

```cpp
    while (fast->next != NULL) {
        fast = fast->next;
        slow = slow->next;
    }
```

Both scouts marched now — Fast still leading.
When Fast reached the final warrior, Slow stood precisely **before the one to be removed**.
This is the magic of the ritual.

---

### ⚔️ Phase IV — The Silent Removal

```cpp
    ListNode* target = slow->next;
    slow->next = slow->next->next;
    delete target;
```

With one clean strike, Slow severed the arrow pointing to the target warrior,
removing him from the line without chaos.

The chain closed seamlessly.

---

```cpp
    return dummy.next;
}
```

The procession continued — but now without the fallen warrior.

---

### 🎺 The Trial of the Line

```cpp
int main() {
    // Build list: 1 -> 2 -> 3 -> 4 -> 5
    ListNode* head = new ListNode(1);
    head->next = new ListNode(2);
    head->next->next = new ListNode(3);
    head->next->next->next = new ListNode(4);
    head->next->next->next->next = new ListNode(5);

    head = removeNthFromEnd(head, 2);
    // expected: 1 -> 2 -> 3 -> 5

    while (head) {
        cout << head->val << " ";
        head = head->next;
    }
    cout << endl;
    return 0;
}
```

In the procession `1 → 2 → 3 → 4 → 5`,
the Oracle ordered the **2nd from the end** to vanish — the warrior `4`.

The scouts moved as commanded,
and the final formation became:
`1 → 2 → 3 → 5`

Balance restored. Mission complete.

---

### 🧠 Memory of the Silent Strike

-   **Dummy node** — protects the head and simplifies edge cases.
-   **Fast moves n steps first** — creating the perfect spacing.
-   **Move slow and fast together** — until fast reaches the end.
-   **Slow stands before the target** — allowing clean removal.
-   **Time:** O(n), **Space:** O(1).

Thus is remembered the saga of **Remove Nth Node From End of List**,
where two scouts walk the line with cunning precision,
striking only when the exact warrior stands revealed by distance and fate. 🗡️✨
