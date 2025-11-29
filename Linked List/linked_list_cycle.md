## 🔄 _The Endless Loop: The Linked List Cycle Saga_

> _"A path should lead ever forward,
> each step bringing you closer to the horizon.
> But sometimes a curse bends the road,
> trapping travelers in a circle where no end can be found.
> To break the illusion, send two trackers —
> one swift, one steady —
> and watch if their fates collide."_

---

A chain of warriors formed a Linked Line — each pointing toward the next, marching toward what should be a final destination, an honorable end marked by `NULL`.
But rumors spread: some chains were **cursed**, looping back in a circle so travelers wandered forever, never reaching peace.

To detect such a curse, the Oracle summoned **two trackers**:

-   **Tortoise** — slow, steady, moving one step at a time.
-   **Hare** — quick, relentless, moving two steps at a time.

If ever the Hare caught the Tortoise, the Oracle knew the chain was cursed — a **Linked List Cycle**.

Thus began the saga.

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

Every warrior (`ListNode`) carried a number and an arrow to the next.
Cycles lurked when these arrows looped backward to earlier warriors.

---

### 🐢🐇 The Oracle’s Cycle-Detection Ritual

(Floyd’s Cycle-Finding Algorithm)

```cpp
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
```

The Oracle placed the Tortoise (`slow`) and the Hare (`fast`) at the chain’s start.

---

```cpp
    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;           // tortoise: one step
        fast = fast->next->next;     // hare: two steps
```

The warriors moved:

-   The Tortoise advanced patiently.
-   The Hare leapt ahead with speed.

If the road was straight, the Hare would eventually reach the end (`NULL`).
But if cursed — if a loop existed — the Hare would eventually circle and collide with the Tortoise.

---

```cpp
        if (slow == fast) return true;  // curse detected
    }
    return false; // reached end: no cycle
}
```

Should the two trackers ever meet, the Oracle instantly declared:
**“A cycle lives here.”**

If the Hare escaped the path and touched the horizon (`NULL`), the Oracle pronounced the chain safe.

---

### 🎺 The Trial of the Cursed Chain

```cpp
int main() {
    // create cycle: 3 -> 2 -> 0 -> -4
    //                     ^             |
    //                     |_____________|
    ListNode* head = new ListNode(3);
    head->next = new ListNode(2);
    head->next->next = new ListNode(0);
    head->next->next->next = new ListNode(-4);

    // create the cycle manually
    head->next->next->next->next = head->next; // tail connects to node 2

    cout << (hasCycle(head) ? "true" : "false") << endl; // expected: true
    return 0;
}
```

The chain twisted back onto itself at value `2`, forming an inescapable loop.
The Hare sprinted, the Tortoise marched, and within moments the Hare overtook him —
revealing the curse.

---

### 🧠 Memory of the Endless Loop

-   **Two pointers:**

    -   `slow` moves one step,
    -   `fast` moves two.

-   **If they meet:** a cycle exists.
-   **If fast reaches NULL:** no cycle — the road ends.
-   **Time:** O(n) — both pointers traverse only what exists.
-   **Space:** O(1) — no extra structures needed.

Thus is remembered the saga of the **Linked List Cycle**,
where the Oracle sends two trackers into the chain,
and their collision — or lack of it —
reveals whether the road flows free or loops endlessly under a silent curse. 🔄✨
