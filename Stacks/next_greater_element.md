## 🔥⚔️ _The Prophecy of the Greater Warrior: The Next Greater Element Saga_

> \*"In the Line of Numbers,
> each warrior stood facing right —
> wondering if someone stronger
> would one day appear.
>
> The Oracle was commanded:
>
> \*\*‘For every warrior,
> reveal the next greater warrior to the right.
>
> If none exists,
> mark their fate as -1.’\*\*
>
> The foolish would scan the horizon again and again.
>
> But the Oracle knew:
> when a stronger warrior arrives,
> he fulfills the prophecy
> for many waiting souls at once."\*

---

This is the epic saga of **Next Greater Element**.

You are given an array `nums`.

Your task:

-   For each element, find the **next greater element to its right**
-   If none exists → return `-1`

---

## 🧠 The Oracle’s Core Insight — Resolve When Greater Appears

The Oracle realized:

-   If the current element is greater than the one before,
    it resolves the prophecy for that previous element.
-   Keep a stack of indices
-   The stack must maintain a **monotonic decreasing order**

When a greater element appears:

-   Pop smaller elements
-   Assign their answer

---

## ⚔️ The Oracle’s Monotonic Stack Ritual

_Wait for the stronger one_

```cpp
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;
```

The stack stores indices
of elements still waiting for a greater one.

---

### 🌊 Walk Through the Line

```cpp
    for (int i = 0; i < n; i++) {
```

Each warrior stepped into view.

---

### 🔥 Resolve Waiting Warriors

```cpp
        while (!st.empty() && nums[i] > nums[st.top()]) {
            result[st.top()] = nums[i];
            st.pop();
        }
```

If the current warrior was stronger:

-   He fulfilled the prophecy
-   The weaker warrior was resolved

---

### 🏗️ Add Current Warrior to Waiting Stack

```cpp
        st.push(i);
    }
    return result;
}
```

If no stronger warrior had appeared yet,
he remained waiting.

---

### 🎺 The Trial of the Horizon

```cpp
int main() {
    vector<int> nums = {2, 1, 2, 4, 3};
    vector<int> ans = nextGreaterElement(nums);

    for (int x : ans) cout << x << " ";
    cout << endl;
    // expected: 4 2 4 -1 -1
    return 0;
}
```

The Oracle revealed:

-   2 → 4
-   1 → 2
-   2 → 4
-   4 → -1
-   3 → -1

Some prophecies were fulfilled.
Some were destined never to be.

---

### 🧠 Memory of the Greater Law

-   Use a monotonic decreasing stack
-   Store indices
-   When current > stack top → resolve
-   Default answer is -1
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Next Greater Element**,
where the Oracle walks the line once,
allowing stronger warriors to fulfill waiting destinies —
and proving that sometimes,
one arrival
can resolve many futures at once. 🔥✨
