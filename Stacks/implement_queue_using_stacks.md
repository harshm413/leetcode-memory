## 📚🔄 _The Two-Stack Relay: The Implement Queue Using Stacks Saga_

> \_"The Oracle was given only stacks — LIFO structures.
>
> She was commanded:
>
> **'Build a queue (FIFO) using only stack operations:
> push to top, pop from top, peek top, empty check.'**
>
> A stack gives you the NEWEST element first.
> A queue gives you the OLDEST element first.
> Opposite behaviors.
>
> The Oracle discovered the two-stack trick:
>
> **Use an INPUT stack and an OUTPUT stack.**
>
> Push always goes to the input stack.
> Pop/Peek always comes from the output stack.
>
> When the output stack is EMPTY and we need to pop/peek:
> transfer ALL elements from input to output.
> This REVERSES the order — making the oldest element
> land on top of the output stack.
>
> **Amortized O(1) per operation.**
> Each element is moved at most ONCE from input to output."\_

---

This is the saga of **Implement Queue Using Stacks (LeetCode 232)**.

Implement a FIFO queue using only LIFO stack operations:
-   `push(x)` — enqueue element to back.
-   `pop()` — dequeue and return the front element.
-   `peek()` — return the front element without removing.
-   `empty()` — return true if queue is empty.

```
push(1), push(2), peek() → 1, pop() → 1, empty() → false
```

---

## 🧠 The Two-Stack Insight — Input and Output

**Input stack:** where new elements are pushed. Top = newest.
**Output stack:** where elements are popped from. Top = oldest.

The key: transferring from input to output REVERSES the order.

```
Input stack (top→bottom): [3, 2, 1]  (3 is newest, 1 is oldest)

Transfer to output (pop from input, push to output):
  pop 3, push 3 to output.
  pop 2, push 2 to output.
  pop 1, push 1 to output.

Output stack (top→bottom): [1, 2, 3]  (1 is on top = oldest = queue front)
```

After transfer, the output stack has elements in FIFO order.
Popping from output gives the oldest element first. Queue behavior.

> _"The input stack collects newcomers.
> When the output stack runs dry,
> the input stack pours its contents into the output —
> reversing the order, putting the oldest on top.
> The output stack then serves elements in queue order."_

---

## 🧠 When to Transfer?

**Only when the output stack is EMPTY and we need to pop/peek.**

If the output stack still has elements — just pop from it.
No transfer needed. The elements there are already in the right order.

If the output stack is empty — transfer everything from input.
This is the "lazy" approach. Transfer only when necessary.

---

## 🧠 Why Amortized O(1)?

Each element goes through exactly TWO moves in its lifetime:
1. Pushed onto the input stack. (O(1))
2. Transferred to the output stack. (O(1) per element, happens once)
3. Popped from the output stack. (O(1))

Total cost per element = O(1) + O(1) + O(1) = O(1).
Even though a single transfer might move N elements (O(N)),
each element is only transferred ONCE. Amortized = O(1) per operation.

---

### 📜 The Scroll of the Two-Stack Relay

```cpp
#include <iostream>
#include <stack>
using namespace std;
```

---

## 🔄 The Two-Stack Queue

### The data structures

```cpp
class MyQueue {
    stack<int> input, output;
```

Two stacks. `input` for pushing. `output` for popping.

---

### Push — always to input stack

```cpp
public:
    void push(int x) {
        input.push(x);
    }
```

O(1). Just push to the input stack. No transfer.
The newest element sits on top of input.

---

### Transfer — move input to output (only when output is empty)

```cpp
    void transfer() {
        while (!input.empty()) {
            output.push(input.top());
            input.pop();
        }
    }
```

Pop everything from input, push to output.
This reverses the order. The oldest element ends up on top of output.

Called ONLY when output is empty. Not on every pop.

> _"The relay happens only when the output stack is dry.
> The input stack hands over all its elements, reversed.
> The oldest rises to the top. Queue order restored."_

---

### Pop — from output stack

```cpp
    int pop() {
        if (output.empty()) transfer();
        int val = output.top();
        output.pop();
        return val;
    }
```

If output has elements — pop directly. O(1).
If output is empty — transfer first, then pop.

The transfer is amortized O(1) because each element transfers only once.

---

### Peek — top of output stack

```cpp
    int peek() {
        if (output.empty()) transfer();
        return output.top();
    }
```

Same logic as pop. Transfer if needed. Return top without removing.

---

### Empty — both stacks must be empty

```cpp
    bool empty() {
        return input.empty() && output.empty();
    }
};
```

The queue is empty only when BOTH stacks are empty.
Elements might be in either stack at any time.

---

### 🎺 The Trial of the Two-Stack Relay

```cpp
int main() {
    MyQueue q;
    q.push(1);
    q.push(2);
    q.push(3);
    cout << q.peek() << endl;   // expected: 1
    cout << q.pop() << endl;    // expected: 1
    cout << q.pop() << endl;    // expected: 2
    q.push(4);
    cout << q.pop() << endl;    // expected: 3
    cout << q.pop() << endl;    // expected: 4
    cout << q.empty() << endl;  // expected: 1 (true)
    return 0;
}
```

---

**Full trace:**

```
push(1): input = [1], output = [].
push(2): input = [2, 1], output = [].
push(3): input = [3, 2, 1], output = [].

peek(): output empty → transfer!
  input [3,2,1] → output [1,2,3]. input = [].
  output.top() = 1. Return 1. ✓

pop(): output = [1,2,3]. Pop 1. output = [2,3]. Return 1. ✓
pop(): output = [2,3]. Pop 2. output = [3]. Return 2. ✓

push(4): input = [4], output = [3].

pop(): output = [3]. Pop 3. output = []. Return 3. ✓
pop(): output empty → transfer!
  input [4] → output [4]. input = [].
  Pop 4. output = []. Return 4. ✓

empty(): input empty AND output empty → true. ✓
```

---

## 🔍 The Key Insight — Why NOT Transfer on Every Push?

**Naive approach:** transfer on every push or pop. O(N) per operation.

**Smart approach (this):** transfer ONLY when output is empty.
Elements sit in the output stack until they're all consumed.
Only then do we refill from input.

This is what makes it amortized O(1). If we transferred every time,
it would be O(N) per operation — much worse.

---

## 🔍 Stack Using Queues vs Queue Using Stacks

| Aspect | Stack Using Queues | Queue Using Stacks (this) |
|--------|-------------------|---------------------------|
| Data structures | 1 queue | 2 stacks |
| Costly operation | Push O(N) | Amortized O(1) all ops |
| Trick | Rotate after push | Lazy transfer when output empty |
| True amortized O(1)? | No (push is always O(N)) | **Yes** |

Queue using stacks is more elegant — truly amortized O(1).
Stack using queues always has O(N) push (no way around it with one queue).

---

## 🔍 Why Two Stacks and Not One?

With one stack, the oldest element is at the BOTTOM.
You can't access it without popping everything above it.
That's O(N) every time.

With two stacks, the transfer reverses the order ONCE.
After that, the output stack serves elements in O(1) until empty.
The cost is spread across all operations — amortized O(1).

---

## 🔍 Real-World Analogy

Think of a restaurant with a kitchen (input) and a serving counter (output).

-   Orders come into the kitchen (push to input).
-   When the serving counter is empty, the kitchen sends out a batch
    (transfer — oldest orders first).
-   Customers pick up from the counter (pop from output).

The kitchen doesn't send one order at a time — it batches.
That's the amortized efficiency.

---

### 🧠 Memory of the Two-Stack Relay Law

-   **Two stacks:** `input` (for push) and `output` (for pop/peek)
-   **Push:** always to input. O(1).
-   **Pop/Peek:** from output. If output empty → transfer all from input.
-   **Transfer:** pop all from input, push to output. Reverses order.
-   **Amortized O(1):** each element transfers exactly once in its lifetime.
-   **Empty:** both stacks must be empty.
-   **Key:** transfer ONLY when output is empty (lazy, not eager).

Thus is remembered the saga of **Implement Queue Using Stacks**,
where the Oracle used two stacks in relay —
the input stack collecting newcomers,
the output stack serving the oldest —
transferring only when the output ran dry,
reversing the order in one batch —
each element moving exactly once,
achieving amortized O(1) for every operation. 📚🔄✨
