## 🐢🐇 _The Tortoise, The Hare, and The Endless Road: The Linked List Cycle Saga_

> \_"Two travelers were sent down a long road —
> one a slow and steady Tortoise,
> one a swift and restless Hare.
>
> The Tortoise took one step at a time.
> The Hare took two.
>
> If the road was straight and had an end,
> the Hare would reach the darkness of NULL first —
> and the journey would be declared finite.
>
> But if the road curved back upon itself —
> if somewhere, a treacherous loop
> sent travelers back into a road already walked —
> then neither traveler would ever escape.
>
> And in that looping prison,
> something inevitable would happen:
>
> **The Hare, running twice as fast,
> would eventually lap the Tortoise.
> They would meet again on the same road.**
>
> The Oracle was commanded:
>
> **'Send the two travelers.
> If they ever meet — the cycle exists.
> If the Hare falls off the edge into NULL — it does not.'**
>
> Thus began the ancient ritual
> known as **Floyd's Cycle Detection** —
> the race that reveals the shape of the road itself."\_

---

This is the saga of **Linked List Cycle**.

You are given the `head` of a linked list.

Your task:

-   Return `true` if the list contains a **cycle** —
    meaning some node's `next` pointer points back
    to a previously visited node.
-   Return `false` if the list ends cleanly at `NULL`.

```
Cycle:    1 → 2 → 3 → 4 → 2 (loops back)  →  true
No cycle: 1 → 2 → 3 → NULL                 →  false
```

---

## 🧠 The Oracle's Core Insight — Floyd's Tortoise and Hare

The key truth behind this algorithm is beautifully simple:

> **On a straight road, a faster traveler reaches the end first.** > **On a circular road, a faster traveler must eventually lap a slower one.**

Think of a running track:

-   If two runners start together and one is faster —
    on a circular track they **will meet** again.
-   On a straight road, the faster one simply **disappears ahead** and never returns.

The Tortoise moves **one step** at a time: `slow = slow->next`

The Hare moves **two steps** at a time: `fast = fast->next->next`

If `fast` ever hits `NULL` — the road is straight. **No cycle.**

If `fast` ever equals `slow` — they have met on the loop. **Cycle detected.**

This is the entire algorithm.
No extra memory. No visited set. No hash map.
Just two pointers and one profound truth about circular roads.

---

### 📜 The Scroll of the Road

```cpp
#include <iostream>
using namespace std;
```

---

### 🧱 The Structure of a Waypoint

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

Each waypoint on the road held a value (`val`)
and pointed to the next waypoint ahead (`next`).

In a cycle, one waypoint's `next`
pointed **backward** to an earlier waypoint —
creating the invisible loop
that would trap travelers forever.

---

## 🐢🐇 The Oracle Sends the Two Travelers

_One slow. One fast. The road decides the outcome._

```cpp
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
```

Both travelers began at the `head` —
the very first waypoint on the road.

-   `slow` — the **Tortoise** — patient, one step at a time.
-   `fast` — the **Hare** — restless, two steps at a time.

They set off together.

---

## 🔁 The Race Begins

```cpp
    while (fast != nullptr && fast->next != nullptr) {
```

The Oracle watched carefully.

The loop continued only as long as the Hare had road ahead —
both `fast` itself and `fast->next` must exist,
because the Hare takes **two steps** and
stepping into `nullptr` would cause a crash.

The moment either became `nullptr` —
the road had ended.
No cycle existed.

---

### 🐢 The Tortoise Takes One Step

```cpp
        slow = slow->next;
```

The Tortoise moved one waypoint forward.
Steady. Unhurried. Inevitable.

---

### 🐇 The Hare Takes Two Steps

```cpp
        fast = fast->next->next;
```

The Hare leapt two waypoints ahead.
Swift. Hungry. Always pushing forward.

On a straight road, the Hare would reach `nullptr` first
and the while loop would end — **no cycle**.

On a circular road, the Hare would run ahead,
loop around, and close the gap
on the plodding Tortoise from behind.

---

### 🤝 The Inevitable Meeting

```cpp
        if (slow == fast) {
            return true;
        }
    }
```

After every pair of steps, the Oracle checked:

> **Are the Tortoise and the Hare at the same waypoint?**

If yes — they had met inside the loop.
The cycle was confirmed. Return `true`.

Why are they guaranteed to meet and not just skip past each other?

Because the Hare gains exactly **one step** on the Tortoise per iteration.
If the loop has length `L`, the Hare closes the gap by 1 each round —
so within `L` steps of entering the cycle, they meet.
They can never skip past each other — the gap shrinks to zero, not negative.

---

### 🌅 The Road Ends — No Cycle

```cpp
    return false;
}
```

If the while loop exited naturally —
`fast` or `fast->next` had become `nullptr`.

The Hare had fallen off the edge of the road.

No circular trap existed.
The chain was straight and finite.

Return `false`.

---

### 🎺 The Trial of the Looping Road

```cpp
int main() {
    // Build: 3 → 2 → 0 → -4 → (back to node with val 2)
    ListNode* head = new ListNode(3);
    head->next = new ListNode(2);
    head->next->next = new ListNode(0);
    head->next->next->next = new ListNode(-4);
    head->next->next->next->next = head->next; // cycle here!

    cout << hasCycle(head) << endl; // expected: true (1)
    return 0;
}
```

The road was built:
`3 → 2 → 0 → -4 → back to 2`

The Tortoise and Hare were sent in.

-   Step 1: Tortoise at `2`, Hare at `0`
-   Step 2: Tortoise at `0`, Hare at `2` _(Hare looped back!)_
-   Step 3: Tortoise at `-4`, Hare at `-4` ← **They meet!**

The Oracle declared: **cycle detected.**

---

### 🧠 Memory of the Tortoise and Hare Law

-   `slow` = Tortoise — moves **one step** each iteration
-   `fast` = Hare — moves **two steps** each iteration
-   Both start at `head`
-   **Loop condition:** `fast != nullptr && fast->next != nullptr`
-   **Inside loop:** advance slow by 1, fast by 2, then check if equal
-   **If they meet** → cycle exists → `return true`
-   **If fast hits NULL** → road ends → `return false`
-   The Hare gains 1 step on the Tortoise per round — meeting is guaranteed on any loop
-   **Time:** O(n) — at most two full passes over the list
-   **Space:** O(1) — only two pointers, no visited set needed

Thus is remembered the saga of **Linked List Cycle**,
where the Oracle sent a patient Tortoise and a restless Hare
down a road whose shape was unknown —
trusting one eternal truth:
on a straight road, the fast one vanishes.
On a circular road, the fast one always comes back.
And when they meet —
the loop has been found. 🐢🐇✨
