## 🔥📜 _The Scroll of Chosen Warriors: The Next Greater Element I Saga_

> \*"In the Grand Line of Numbers stood many warriors —
> strong and weak alike.
>
> But the Oracle was not asked about all of them.
>
> Instead, she was handed a smaller scroll —
> a list of **chosen warriors** —
> and commanded:
>
> **‘For each chosen warrior,
> reveal the next greater warrior
> that appears to his right
> in the greater line.’**
>
> The Oracle knew she must first understand
> the destiny of **all warriors**,
> before answering only for the chosen few."\*

---

This is the epic saga of **Next Greater Element I**.

You are given:

-   `nums1` → subset of elements (chosen warriors)
-   `nums2` → full array (greater line)

For each element in `nums1`:

-   Find the **next greater element** in `nums2`
-   If none exists → return `-1`

---

## 🧠 The Oracle’s Core Insight — Solve Once, Answer Many

The Oracle realized:

-   Instead of searching for each element in `nums1`,
-   Compute the **next greater element for all of `nums2`**
-   Store results in a map
-   Then answer each query instantly

Thus she used:

-   A **monotonic decreasing stack**
-   A **hash map** from value → next greater value

---

## ⚔️ The Oracle’s Prophecy Ritual

```cpp
vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
    unordered_map<int, int> nextGreater;
    stack<int> st;
```

The stack would resolve all destinies in `nums2`.

---

### 🌊 Walk Through the Greater Line

```cpp
    for (int num : nums2) {
```

Each warrior stepped forward.

---

### 🔥 Resolve Waiting Warriors

```cpp
        while (!st.empty() && num > st.top()) {
            nextGreater[st.top()] = num;
            st.pop();
        }
```

If the current warrior was stronger,
he fulfilled the prophecy
for all weaker warriors waiting.

---

### 🏗️ Add Current Warrior

```cpp
        st.push(num);
    }
```

Unresolved warriors remained waiting.

---

### 📜 Assign -1 to Unfulfilled Warriors

```cpp
    while (!st.empty()) {
        nextGreater[st.top()] = -1;
        st.pop();
    }
```

Some warriors would never find a stronger one.

---

### 🎯 Answer for the Chosen Scroll

```cpp
    vector<int> result;
    for (int num : nums1) {
        result.push_back(nextGreater[num]);
    }
    return result;
}
```

The Oracle now answered only for the chosen few.

---

### 🎺 The Trial of the Two Scrolls

```cpp
int main() {
    vector<int> nums1 = {4,1,2};
    vector<int> nums2 = {1,3,4,2};

    vector<int> ans = nextGreaterElement(nums1, nums2);

    for (int x : ans) cout << x << " ";
    cout << endl;
    // expected: -1 3 -1
    return 0;
}
```

The Oracle declared:

-   4 → -1
-   1 → 3
-   2 → -1

Destiny fulfilled.

---

### 🧠 Memory of the Chosen Prophecy Law

-   Use monotonic decreasing stack on `nums2`
-   Build map: value → next greater
-   Default unresolved values → -1
-   Lookup answers for `nums1`
-   **Time:** O(n + m)
-   **Space:** O(n)

Thus is remembered the saga of
**Next Greater Element I**,
where the Oracle solves the fate of all warriors once,
then calmly reveals the destiny
of the chosen few —
proving that wisdom lies
in solving the greater problem first. 🔥✨
