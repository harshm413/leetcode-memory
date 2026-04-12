## 📉🏛️ _The Twin Towers of Memory: The Min Stack Saga_

> \_"In the Kingdom of Stacks,
> the standard tower remembered only order --
> what was pushed last, what should be popped first.
>
> But the King demanded more:
>
> **'I want a stack that does everything
> a normal stack does --
> push, pop, top --
> but ALSO tells me the minimum element
> at any moment, in O(1) time.'**
>
> The Architect thought carefully.
>
> Tracking the current minimum was easy --
> just keep a variable.
> But when the minimum was popped,
> what was the minimum BEFORE it arrived?
>
> The history of minimums had to be preserved.
>
> So the Architect built **two towers**:
>
> **The Main Tower** -- the regular stack,
> holding every element in LIFO order.
>
> **The Min Tower** -- a parallel stack,
> where each entry recorded the minimum
> of the main stack at that moment in time.
>
> When an element was pushed,
> the Min Tower pushed the smaller of
> the new element and its own current top.
>
> When an element was popped,
> the Min Tower popped too --
> revealing the previous minimum beneath.
>
> Two towers. Always in sync.
> O(1) for everything."\_

---

This is the saga of **Min Stack**.

Design a stack that supports:

-   `push(val)` -- push an element onto the stack.
-   `pop()` -- remove the top element.
-   `top()` -- get the top element.
-   `getMin()` -- retrieve the **minimum** element in O(1).

All operations must run in **O(1)** time.

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   // returns -3
minStack.pop();
minStack.getMin();   // returns -2
minStack.top();      // returns 0
```

---

## 🧠 The Oracle's Core Insight -- A Parallel Min History

The challenge is not tracking the current minimum --
it's knowing what the minimum WAS after a pop.

If we only kept a single `minVal` variable:

```
push(-2) → min = -2
push(0)  → min = -2
push(-3) → min = -3
pop()    → min = ???  (-3 is gone, what was min before it?)
```

We'd lose the history. The variable doesn't remember the past.

The solution: **a second stack that mirrors the main stack**,
where each entry records the minimum at that depth.

```
Main stack:  [-2, 0, -3]     Min stack:  [-2, -2, -3]
                                           ↑    ↑    ↑
                                          min  min  min
                                         at    at   at
                                        depth depth depth
                                          1    2     3
```

When `-3` is popped from both stacks:

```
Main stack:  [-2, 0]         Min stack:  [-2, -2]
getMin() = minStack.top() = -2  ✓
```

The previous minimum was preserved beneath.

```
Time:  O(1) for all operations
Space: O(N) -- two stacks of size N
```

---

### 📜 The Scroll of the Twin Towers

```cpp
#include <iostream>
#include <stack>
using namespace std;
```

---

## 🏗️ The Twin Towers Are Built

```cpp
class MinStack {
    stack<int> mainStack;
    stack<int> minStack;
```

Two towers stood side by side:

-   `mainStack` -- the regular stack holding all values.
-   `minStack` -- the parallel stack where each entry
    is the minimum of all elements at or below that depth.

They would always have the **same size** --
every push adds to both, every pop removes from both.

---

```cpp
public:
    MinStack() {}
```

Both towers began empty. No construction needed.

---

## 📥 Push -- Add to Both Towers

```cpp
    void push(int val) {
        mainStack.push(val);

        if (minStack.empty()) {
            minStack.push(val);
        } else {
            minStack.push(min(val, minStack.top()));
        }
    }
```

The value was pushed onto the main stack as usual.

For the min stack, the Oracle asked:

> **"Is this new value smaller than the current minimum?"**

If the min stack was empty -- this was the first element,
so it was trivially the minimum.

Otherwise, push the **smaller** of the new value
and the current min stack top.

This ensured `minStack.top()` always reflected
the minimum of the entire main stack at this moment.

> _"The Min Tower never forgets.
> At every depth, it remembers
> the smallest voice that ever stood
> at or below that level."_

---

## 📤 Pop -- Remove from Both Towers

```cpp
    void pop() {
        mainStack.pop();
        minStack.pop();
    }
```

Both towers shrank together.

When the main stack lost its top element,
the min stack lost its corresponding minimum record.

The new `minStack.top()` automatically reflected
the minimum of the remaining elements --
because it was computed when that depth was the top.

> _"When a soldier falls,
> the Min Tower reveals the minimum
> that existed before that soldier ever arrived."_

---

## 👁️ Top -- Peek at the Main Tower

```cpp
    int top() {
        return mainStack.top();
    }
```

Standard stack peek. Nothing special.

---

## 📉 GetMin -- Peek at the Min Tower

```cpp
    int getMin() {
        return minStack.top();
    }
};
```

The minimum of the entire stack was always
sitting at the top of the min tower.

No scanning. No searching. O(1).

---

### 🎺 The Trial of the Twin Towers

```cpp
int main() {
    MinStack ms;
    ms.push(-2);
    ms.push(0);
    ms.push(-3);
    cout << ms.getMin() << endl; // -3
    ms.pop();
    cout << ms.getMin() << endl; // -2
    cout << ms.top()    << endl; // 0
    return 0;
}
```

---

**Full trace:**

| Operation   | mainStack    | minStack     | getMin | top |
| ----------- | ------------ | ------------ | ------ | --- |
| push(-2)    | [-2]         | [-2]         | -2     | -2  |
| push(0)     | [-2, 0]      | [-2, -2]     | -2     | 0   |
| push(-3)    | [-2, 0, -3]  | [-2, -2, -3] | -3     | -3  |
| pop()       | [-2, 0]      | [-2, -2]     | -2     | 0   |
| getMin()    | --           | --           | **-2** | --  |
| top()       | --           | --           | --     | **0** |

At push(0): `min(0, -2) = -2` → minStack pushes `-2` (not `0`).
At push(-3): `min(-3, -2) = -3` → minStack pushes `-3`.
At pop(): both stacks lose their top. MinStack reveals `-2` beneath.

---

**Extended trace with duplicates:**

| Operation   | mainStack        | minStack         | getMin |
| ----------- | ---------------- | ---------------- | ------ |
| push(1)     | [1]              | [1]              | 1      |
| push(2)     | [1, 2]           | [1, 1]           | 1      |
| push(0)     | [1, 2, 0]        | [1, 1, 0]        | 0      |
| push(0)     | [1, 2, 0, 0]     | [1, 1, 0, 0]     | 0      |
| pop()       | [1, 2, 0]        | [1, 1, 0]        | 0      |
| pop()       | [1, 2]           | [1, 1]           | 1      |
| pop()       | [1]              | [1]              | 1      |

When `0` was pushed twice, the min stack recorded `0` twice.
When both were popped, the minimum correctly reverted to `1`.

---

## 🔍 Why Not Just Scan for the Min?

Scanning the stack for the minimum after every pop is O(N).
The problem demands O(1) for all operations.

The parallel min stack trades O(N) extra space
for O(1) `getMin()` -- a classic space-time tradeoff.

---

## 🔄 Alternative -- Single Stack with Pairs

Instead of two stacks, store `{value, currentMin}` pairs:

```cpp
stack<pair<int,int>> st;

void push(int val) {
    int curMin = st.empty() ? val : min(val, st.top().second);
    st.push({val, curMin});
}

int getMin() { return st.top().second; }
```

Same idea, one stack. Each entry carries its own minimum context.
Both approaches are O(1) time, O(N) space.

---

### 🧠 Memory of the Twin Tower Law

-   **Two stacks:** `mainStack` for values, `minStack` for minimum history
-   **Push:** push value to main; push `min(val, minStack.top())` to min
-   **Pop:** pop from both stacks simultaneously
-   **Top:** `mainStack.top()` -- standard peek
-   **GetMin:** `minStack.top()` -- O(1), always the current minimum
-   Both stacks always have the **same size**
-   The min stack preserves the minimum at every depth --
    popping reveals the previous minimum automatically
-   **Alternative:** single stack of `pair<int,int>` -- same complexity
-   **Time:** O(1) for all four operations
-   **Space:** O(N) -- two stacks (or one stack of pairs)
-   **Edge cases:**
    -   Duplicate minimums → min stack records them multiple times
    -   Single element → min is itself
    -   All elements equal → min stack is all the same value

Thus is remembered the saga of **Min Stack**,
where the Architect built two towers side by side --
one to remember the order of values,
one to remember the history of minimums --
always in sync, always the same height,
so that at any moment the minimum
could be read from the top of the second tower
in constant time --
and when the top was removed,
the previous minimum rose to the surface
as if it had been waiting there all along. 📉🏛️✨
