## 🔄🔥 _The Circle of Endless Horizon: The Next Greater Element II Saga_

> \*"In the Realm of Numbers,
> warriors stood not in a straight line —
> but in a perfect circle.
>
> After the last warrior,
> the first stood again.
>
> The Oracle was commanded:
>
> **‘For each warrior,
> reveal the next greater warrior —
> even if he must look beyond the end
> and wrap around the circle.’**
>
> None could escape this circular fate.
>
> Some would find strength ahead.
> Others would circle the world
> and still find none."\*

---

This is the epic saga of **Next Greater Element II**.

You are given a circular array `nums`.

Your task:

-   For each element, find the **next greater element**
-   The search continues circularly
-   If none exists → return `-1`

---

## 🧠 The Oracle’s Core Insight — Walk the Circle Twice

The Oracle realized:

-   Circular means: after index `n-1`,
    continue at index `0`
-   Instead of physically rotating,
    simply iterate **2 × n times**
-   Use a **monotonic decreasing stack**

But:

-   Only push indices during the **first pass**
-   Use the second pass only to resolve pending warriors

---

## ⚔️ The Oracle’s Circular Prophecy Ritual

```cpp
vector<int> nextGreaterElements(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;
```

The stack stores indices
still waiting for a stronger warrior.

---

### 🌍 Walk the Circle Twice

```cpp
    for (int i = 0; i < 2 * n; i++) {
        int curr = nums[i % n];
```

`i % n` wraps around the circle.

---

### 🔥 Resolve Waiting Warriors

```cpp
        while (!st.empty() && nums[st.top()] < curr) {
            result[st.top()] = curr;
            st.pop();
        }
```

If current warrior is stronger,
he fulfills the prophecy
for waiting ones.

---

### 🏗️ Add Warriors Only in First Pass

```cpp
        if (i < n) {
            st.push(i);
        }
    }
    return result;
}
```

Only original indices are pushed.
Second pass resolves remaining ones.

---

### 🎺 The Trial of the Circular Line

```cpp
int main() {
    vector<int> nums = {1, 2, 1};
    vector<int> ans = nextGreaterElements(nums);

    for (int x : ans) cout << x << " ";
    cout << endl;
    // expected: 2 -1 2
    return 0;
}
```

The Oracle revealed:

-   1 → 2
-   2 → -1
-   1 → 2 (found after wrapping around)

The circle had spoken.

---

### 🧠 Memory of the Endless Horizon Law

-   Circular array → iterate 2 × n
-   Use monotonic decreasing stack
-   Push indices only during first pass
-   Default answer = -1
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Next Greater Element II**,
where the Oracle walks the circle twice,
allowing warriors to search beyond the end —
and proving that even in a looping world,
each destiny is resolved
with a single disciplined traversal. 🔄✨
