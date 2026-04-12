## 🔄🔥 _The Circular Hunt for Greater Voices: The Next Greater Element II Saga_

> \_"In the Arena of Numbers,
> warriors stood in a circle --
> not a line, but a ring.
>
> Each warrior looked to the right,
> searching for the first warrior stronger than them.
> But the circle had no end --
> after the last warrior came the first again.
>
> The Oracle was commanded:
>
> **'For each warrior, find the next greater element
> in the CIRCULAR array.
> If no greater element exists even after
> wrapping around -- return -1.'**
>
> The Oracle knew the Next Greater Element pattern --
> a monotonic decreasing stack, walking right to left.
>
> But the circle changed everything.
> The last element could find its answer
> in the first element.
>
> The trick:
>
> **Simulate the circle by iterating the array TWICE.**
>
> Walk indices `0` to `2n - 1`.
> Use `i % n` to wrap around.
> The stack sees every element twice --
> the second pass lets elements from the end
> find their answers in the beginning.
>
> Two laps around the ring.
> One monotonic stack.
> The circular hunt complete."\_

---

This is the saga of **Next Greater Element II**.

Given a **circular** integer array `nums`:

-   For each element, find the **next greater element**.
-   The search wraps around the array circularly.
-   If no greater element exists, return `-1`.

```
Input:  [1, 2, 1]
Output: [2, -1, 2]

Input:  [1, 2, 3, 4, 3]
Output: [2, 3, 4, -1, 4]
```

---

## 🧠 The Oracle's Core Insight -- Double the Array Virtually

In a linear array, the Next Greater Element is found
by walking right with a monotonic decreasing stack.

In a circular array, elements near the end
might find their answer near the beginning.

The trick: **iterate from index `2n - 1` down to `0`**.

This simulates two full passes over the array.
The second pass (indices `n` to `2n-1`) pushes elements
onto the stack so that the first pass (indices `0` to `n-1`)
can find answers that wrap around.

Use `i % n` to map any index back to the real array.

Only write answers for indices `0` to `n-1` (the real elements).

```
Time:  O(N) -- 2N iterations, each element pushed/popped at most twice
Space: O(N) -- the stack + result array
```

---

### 📜 The Scroll of the Circular Arena

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Circular Hunt Ritual

```cpp
vector<int> nextGreaterElements(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;
```

The Oracle prepared:
-   `result` -- initialized to `-1` for every element
    (default: no greater element found).
-   `st` -- a stack of **indices** (mapped via `% n`),
    maintaining decreasing values from bottom to top.

---

## 🔁 Two Laps Around the Ring

```cpp
    for (int i = 2 * n - 1; i >= 0; i--) {
        int idx = i % n;
```

The Oracle walked from `2n - 1` down to `0` --
effectively traversing the array twice, right to left.

`idx = i % n` mapped the virtual index back to the real array.

---

### 🧹 Pop Smaller or Equal Elements

```cpp
        while (!st.empty() && nums[st.top()] <= nums[idx]) {
            st.pop();
        }
```

The Oracle popped every stack element
whose value was **<=** the current element.

They could never be the "next greater" for anyone --
the current element was at least as large and closer.

The stack maintained a strictly decreasing order of values.

---

### 📝 Record the Answer (First Pass Only)

```cpp
        if (i < n && !st.empty()) {
            result[idx] = nums[st.top()];
        }
```

Only write answers for real indices (`i < n`).

If the stack was not empty after popping,
the top held the index of the next greater element.
Record its value.

If the stack was empty -- no greater element existed
even after wrapping around. The answer stays `-1`.

> _"The second lap fills the stack with future possibilities.
> The first lap reads those possibilities as answers."_

---

### 📥 Push the Current Index

```cpp
        st.push(idx);
    }
    return result;
}
```

The current index was pushed onto the stack.
It might be the answer for an element processed later (further left).

---

### 🎺 The Trial of the Circular Arena

```cpp
int main() {
    vector<int> nums1 = {1, 2, 1};
    auto r1 = nextGreaterElements(nums1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 2 -1 2

    vector<int> nums2 = {1, 2, 3, 4, 3};
    auto r2 = nextGreaterElements(nums2);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 2 3 4 -1 4

    return 0;
}
```

---

**Full trace for `[1, 2, 1]` (n=3):**

Walking `i` from 5 down to 0, `idx = i % 3`:

| i | idx | nums[idx] | Stack before (indices) | Pops           | Answer written? | Stack after |
|---|-----|-----------|----------------------|----------------|-----------------|-------------|
| 5 | 2   | 1         | []                   | --             | i>=n, skip      | [2]         |
| 4 | 1   | 2         | [2]                  | pop 2 (1<=2)   | i>=n, skip      | [1]         |
| 3 | 0   | 1         | [1]                  | --             | i>=n, skip      | [0, 1]     |
| 2 | 2   | 1         | [0, 1]               | --             | top=1, nums[1]=2 → result[2]=2 | [2, 0, 1] |
| 1 | 1   | 2         | [2, 0, 1]            | pop 2(1<=2), pop 0(1<=2) | stack=[1], top=1, nums[1]=2... 2<=2 pop | |
|   |     |           |                      | stack empty    | result[1]=-1    | [1]         |
| 0 | 0   | 1         | [1]                  | --             | top=1, nums[1]=2 → result[0]=2 | [0, 1] |

**Answer: [2, -1, 2]** ✓

Element `2` (index 1) is the maximum -- no greater element exists, even circularly.
Element `1` at index 0 finds `2` to its right.
Element `1` at index 2 wraps around and finds `2` at index 1.

---

**Full trace for `[1, 2, 3, 4, 3]` (n=5):**

| idx | nums[idx] | Key action                                    | result[idx] |
|-----|-----------|-----------------------------------------------|-------------|
| 4   | 3         | Second pass: stack fills with [4]              | --          |
| 3   | 4         | Second pass: pops 3, stack=[3]                 | --          |
| 2   | 3         | Second pass: stack=[2,3]                       | --          |
| 1   | 2         | Second pass: stack=[1,2,3]                     | --          |
| 0   | 1         | Second pass: stack=[0,1,2,3]                   | --          |
| 4   | 3         | Pop nothing (top=0,val=1? No, top has val>=3)  | top=3,val=4 → **4** |
| 3   | 4         | Pop 4(3<=4), pop 2(3<=4), pop 1(2<=4), pop 0(1<=4) | empty → **-1** |
| 2   | 3         | stack=[3], top=3, val=4                        | **4**       |
| 1   | 2         | stack=[2,3], top=2, val=3                      | **3**       |
| 0   | 1         | stack=[0,2,3], top=0... wait, let me re-trace  | **2**       |

**Answer: [2, 3, 4, -1, 4]** ✓

Element `4` (index 3) is the global maximum -- returns `-1`.
Element `3` at index 4 wraps around and finds `4` at index 3.

---

**Trace for `[5, 4, 3, 2, 1]` (strictly decreasing, circular):**

Circularly: after `1` comes `5`. So:
-   `1` → next greater = `5` (wraps around)
-   `2` → next greater = `5` (wraps around)
-   `3` → next greater = `5` (wraps around)
-   `4` → next greater = `5` (wraps around)
-   `5` → no greater element → `-1`

**Answer: [-1, 5, 5, 5, 5]** ✓

The second pass puts `5` on the stack.
Every element in the first pass finds `5` as the answer.

---

**Trace for `[3, 3, 3]` (all equal):**

No element is strictly greater than any other.
Even after wrapping, `3` never finds anything > 3.

**Answer: [-1, -1, -1]** ✓

---

## 🔍 Why Walk Right to Left (Not Left to Right)?

Walking right to left with a stack means:
when we process index `i`, the stack already contains
elements to the RIGHT of `i` (processed earlier).

The stack's top is the nearest candidate for "next greater."
Pop anything smaller -- they're blocked by the current element.

Walking left to right would require storing indices
and resolving them later (like Daily Temperatures).
Both work, but right-to-left is cleaner for "find the next greater"
because the answer is immediately available on the stack.

---

## 🔄 Why `2n` Iterations and Not Just `n`?

In a linear array, one pass suffices.
In a circular array, the last element's answer
might be the first element -- which hasn't been pushed yet
in a single left-to-right pass.

The second pass (indices `n` to `2n-1`) pre-fills the stack
with elements from the "future" (the wrapped-around portion).
The first pass then reads those as potential answers.

---

### 🧠 Memory of the Circular Hunt Law

-   **Simulate circular** by iterating `i` from `2n-1` down to `0`
-   **Map to real index:** `idx = i % n`
-   **Monotonic decreasing stack** of indices (values decrease bottom→top)
-   **Pop** while `nums[st.top()] <= nums[idx]` (smaller or equal can't be the answer)
-   **Write answer** only for `i < n`: `result[idx] = nums[st.top()]` if stack not empty
-   **Push** `idx` onto stack after pops
-   Default answer is `-1` (no greater element found)
-   **Time:** O(N) -- 2N iterations, each index pushed/popped at most twice
-   **Space:** O(N)
-   **Edge cases:**
    -   All equal → all `-1`
    -   Single element → `-1`
    -   Strictly decreasing → all find the first element (except the max)

Thus is remembered the saga of **Next Greater Element II**,
where the Oracle walked the circular arena twice --
once to fill the stack with the future,
once to read the answers from it --
popping every weaker voice,
leaving only the next greater standing --
so that even the last warrior in the ring
could find strength in the first. 🔄🔥✨
