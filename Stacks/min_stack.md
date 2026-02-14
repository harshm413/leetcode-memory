## 🗝️🏔️ _The Vault of Twin Truths: The Min Stack Saga_

> \*"Deep within the Vault of Operations,
> values were pushed and pulled at will.
>
> The Oracle was given a dangerous command:
>
> **‘Design a stack that behaves like any other —
> but at any moment,
> you must reveal the smallest value
> without disturbing the vault.’**
>
> Searching the vault each time was forbidden.
>
> The Oracle needed a structure
> that remembered not just the present,
> but also the **minimum truth of the past**.
>
> Thus she forged a stack with a hidden companion —
> a twin shadow that always knew the smallest secret."\*

---

This is the saga of **Min Stack**.

You must design a stack that supports:

-   `push(val)`
-   `pop()`
-   `top()`
-   `getMin()`

All operations must run in **O(1) time**.

---

## 🧠 The Oracle’s Core Insight — Two Truths per Step

For every value pushed, the Oracle stored:

-   the **value itself**
-   the **minimum value so far**

Thus, no matter how deep the vault grew,
the minimum was always available at the top.

---

## ⚔️ The Oracle’s Twin-Stack Ritual

_One stack for values, one for truth_

```cpp
class MinStack {
    stack<int> st;
    stack<int> minSt;
```

Two stacks were maintained:

-   `st` → actual values
-   `minSt` → minimum at each depth

---

### 🧾 Push a New Value

```cpp
public:
    MinStack() {}

    void push(int val) {
        st.push(val);
        if (minSt.empty()) {
            minSt.push(val);
        } else {
            minSt.push(min(val, minSt.top()));
        }
    }
```

Every push recorded:

-   the value
-   the smallest value seen so far

---

### ❌ Remove the Top Value

```cpp
    void pop() {
        st.pop();
        minSt.pop();
    }
```

Both stacks moved in perfect sync.

---

### 👀 Peek the Top

```cpp
    int top() {
        return st.top();
    }
```

The Oracle revealed the most recent value.

---

### 🏔️ Reveal the Minimum

```cpp
    int getMin() {
        return minSt.top();
    }
};
```

The smallest value was always waiting —
no searching required.

---

### 🎺 The Trial of the Vault

```cpp
int main() {
    MinStack ms;
    ms.push(-2);
    ms.push(0);
    ms.push(-3);

    cout << ms.getMin() << endl; // -3
    ms.pop();
    cout << ms.top() << endl;    // 0
    cout << ms.getMin() << endl; // -2
    return 0;
}
```

The Oracle proved:

-   Minimum tracked correctly at every step
-   Operations stayed constant time

---

### 🧠 Memory of the Twin-Truth Law

-   Maintain two stacks
-   Each level stores the minimum so far
-   Pop both stacks together
-   All operations in **O(1)**
-   **Space:** O(n)

Thus is remembered the saga of **Min Stack**,
where the Oracle builds a vault of values
with a shadow of truth beneath —
ensuring that no matter how deep the stack grows,
the smallest secret
is always within immediate reach. 🗝️✨
