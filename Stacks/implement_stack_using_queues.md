## 📚🔄 _The Reversed Queue: The Implement Stack Using Queues Saga_

> \_"The Oracle was given only queues — FIFO structures.
>
> She was commanded:
>
> **'Build a stack (LIFO) using only queue operations:
> push to back, pop from front, peek front, empty check.'**
>
> A queue gives you the OLDEST element first.
> A stack gives you the NEWEST element first.
> Opposite behaviors.
>
> The Oracle discovered a trick:
>
> **After pushing a new element to the back of the queue,
> rotate all previous elements behind it.**
>
> Push 'x' to the back. Then pop and re-push everything else.
> Now 'x' is at the FRONT — the next to come out.
> The queue now behaves like a stack.
>
> **Push is O(N). Pop/Top are O(1).**
> One queue is enough."\_

---

This is the saga of **Implement Stack Using Queues (LeetCode 225)**.

Implement a LIFO stack using only FIFO queue operations:
-   `push(x)` — push element to top of stack.
-   `pop()` — remove and return the top element.
-   `top()` — return the top element without removing.
-   `empty()` — return true if stack is empty.

```
push(1), push(2), top() → 2, pop() → 2, empty() → false
```

---

## 🧠 The Core Insight — Rotate After Push

A queue is FIFO: first in, first out.
A stack is LIFO: last in, first out.

To make the LAST pushed element come out FIRST,
we need it at the FRONT of the queue.

**After pushing x to the back, rotate the queue N-1 times:**
pop from front, push to back. Repeat for all elements except x.
Now x is at the front.

```
Queue before push(3): [1, 2]  (front=1, back=2)
Push 3 to back:       [1, 2, 3]
Rotate 2 times:
  pop 1, push 1:      [2, 3, 1]
  pop 2, push 2:      [3, 1, 2]
Queue after:           [3, 1, 2]  (front=3 = most recent = stack top)
```

Now `front()` gives 3 — the last pushed element. Stack behavior.

> _"The new element enters at the back.
> Then everyone else cycles behind it.
> The newcomer takes the front — the throne of the stack."_

---

### 📜 The Scroll of the Reversed Queue

```cpp
#include <iostream>
#include <queue>
using namespace std;
```

---

## 🔄 The Single-Queue Stack

### The data structure

```cpp
class MyStack {
    queue<int> q;
```

One queue. That's all we need.

---

### Push — add and rotate

```cpp
public:
    void push(int x) {
        q.push(x);
```

Push the new element to the back of the queue.

---

```cpp
        int n = q.size() - 1;
        while (n--) {
            q.push(q.front());
            q.pop();
        }
    }
```

Rotate `size - 1` times: pop from front, push to back.
After this, the new element is at the front.

Every element that was already in the queue gets cycled behind x.
The queue's front is now the most recently pushed element.

> _"The queue rotates. Everyone steps aside.
> The newcomer reaches the front.
> Now the queue's front = the stack's top."_

---

### Pop — just dequeue from front

```cpp
    int pop() {
        int val = q.front();
        q.pop();
        return val;
    }
```

The front IS the stack's top (thanks to the rotation during push).
Just pop it. O(1).

---

### Top — peek the front

```cpp
    int top() {
        return q.front();
    }
```

O(1). The front is always the most recently pushed element.

---

### Empty — check if queue is empty

```cpp
    bool empty() {
        return q.empty();
    }
};
```

---

### 🎺 The Trial of the Reversed Queue

```cpp
int main() {
    MyStack st;
    st.push(1);
    st.push(2);
    st.push(3);
    cout << st.top() << endl;   // expected: 3
    cout << st.pop() << endl;   // expected: 3
    cout << st.top() << endl;   // expected: 2
    cout << st.pop() << endl;   // expected: 2
    cout << st.empty() << endl; // expected: 0 (false)
    cout << st.pop() << endl;   // expected: 1
    cout << st.empty() << endl; // expected: 1 (true)
    return 0;
}
```

---

**Full trace:**

```
push(1): q = [1]. Rotate 0 times. q = [1]. front = 1.
push(2): q = [1, 2]. Rotate 1 time: pop 1, push 1 → [2, 1]. front = 2.
push(3): q = [2, 1, 3]. Rotate 2 times:
  pop 2, push 2 → [1, 3, 2].
  pop 1, push 1 → [3, 2, 1]. front = 3.

top() → 3 ✓ (most recent push)
pop() → 3. q = [2, 1]. front = 2.
top() → 2 ✓
pop() → 2. q = [1]. front = 1.
empty() → false ✓
pop() → 1. q = []. 
empty() → true ✓
```

---

## 🔍 The Two Approaches

### Approach 1 — Costly Push, O(1) Pop (this approach)

Push: O(N) — rotate after every push.
Pop/Top: O(1) — front is always the answer.

**Best when:** pops/tops are more frequent than pushes.

### Approach 2 — O(1) Push, Costly Pop

Push: O(1) — just enqueue normally.
Pop: O(N) — dequeue all except last, re-enqueue them, return the last.

```cpp
int pop() {
    int n = q.size() - 1;
    while (n--) {
        q.push(q.front());
        q.pop();
    }
    int val = q.front();
    q.pop();
    return val;
}
```

**Best when:** pushes are more frequent than pops.

Both are valid. The costly-push approach is more commonly presented
because it keeps the invariant simple: front = top, always.

---

## 🔍 Why Not Two Queues?

You CAN use two queues (transfer elements between them).
But one queue with rotation achieves the same result.
One queue is simpler and uses less space.

The two-queue approach:
-   Push to q2. Transfer all of q1 to q2. Swap q1 and q2.
-   Same O(N) push, O(1) pop. Just more verbose.

---

## 🔍 Stack vs Queue — The Fundamental Difference

| Operation | Stack (LIFO) | Queue (FIFO) |
|-----------|-------------|--------------|
| Insert | Push to top | Enqueue to back |
| Remove | Pop from top | Dequeue from front |
| Access | Top (newest) | Front (oldest) |
| Order | Last in, first out | First in, first out |

To simulate LIFO with FIFO: make the newest element reach the front.
That's what the rotation does.

---

## 🔍 The Reverse Problem — Queue Using Stacks

The reverse (implement queue using stacks) uses TWO stacks:
-   Push stack and pop stack.
-   Transfer from push to pop (reverses order) when pop is empty.
-   Amortized O(1) per operation.

That's a separate problem (LC 232) with a different trick.

---

### 🧠 Memory of the Reversed Queue Law

-   **One queue** is sufficient
-   **Push:** enqueue x, then rotate `size - 1` times (pop front, push back)
-   **Pop/Top:** O(1) — front is always the stack's top
-   **Invariant:** after every push, the front = most recently pushed element
-   **Alternative:** O(1) push, O(N) pop (rotate during pop instead)
-   **Push is O(N), Pop/Top are O(1)**

Thus is remembered the saga of **Implement Stack Using Queues**,
where the Oracle rotated the queue after every push —
cycling all previous elements behind the newcomer —
so the front always held the most recent element,
turning FIFO into LIFO
with nothing but a single queue and a rotation trick. 📚🔄✨
