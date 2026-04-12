## 🔥⚔️ _The Prophecy of the Greater Warrior: The Next Greater Element Saga_

> \_"In the Line of Numbers,
> each warrior stood facing right --
> wondering if someone stronger
> would one day appear beyond them.
>
> The Oracle was commanded:
>
> **'For every warrior,
> reveal the next greater warrior to the right.
> If none exists -- mark their fate as -1.'**
>
> The foolish would scan the horizon
> from every position, one by one.
> O(N²) -- a waste of the Oracle's time.
>
> The Oracle knew a deeper truth:
>
> **When a stronger warrior arrives,
> he fulfills the prophecy
> for MANY waiting souls at once.**
>
> She maintained a stack of warriors still waiting.
> When a greater element appeared,
> it resolved every weaker warrior on the stack
> in one sweep.
>
> This was the **Monotonic Decreasing Stack** --
> the foundational pattern behind
> Daily Temperatures, Stock Span,
> Largest Rectangle in Histogram,
> and countless other stack problems.
>
> One pass. One stack.
> Every prophecy fulfilled or forever marked -1."\_

---

This is the saga of **Next Greater Element** --
the foundational template for monotonic stack problems.

Given an array `nums`:

-   For each element, find the **next greater element to its right**.
-   If no greater element exists → return `-1`.

```
Input:  [2, 1, 2, 4, 3]
Output: [4, 2, 4, -1, -1]

Input:  [1, 3, 2, 4]
Output: [3, 4, 4, -1]

Input:  [5, 4, 3, 2, 1]
Output: [-1, -1, -1, -1, -1]
```

---

## 🧠 The Oracle's Core Insight -- Resolve When Greater Appears

The key observation:

> **When a greater element arrives at index `i`,
> it is the answer for ALL elements on the stack
> that are smaller than it.**

The stack holds indices of elements still waiting for their answer.
Elements are kept in **decreasing** order of value (bottom to top).

When `nums[i]` arrives:

```
While stack is not empty AND nums[i] > nums[stack.top()]:
  result[stack.top()] = nums[i]   (prophecy fulfilled)
  Pop the top                      (no longer waiting)

Push i onto the stack              (now waiting for its own answer)
```

Elements left on the stack at the end never found a greater element.
Their answer stays `-1` (the default).

```
Time:  O(N) -- each index pushed once, popped at most once
Space: O(N) -- the stack + result array
```

---

### 📜 The Scroll of the Waiting Line

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Monotonic Stack Ritual (Left to Right)

```cpp
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;
```

`result` initialized to `-1` -- the default for elements with no greater successor.
`st` holds indices of elements still waiting.

---

## 🔁 Walk the Line Left to Right

```cpp
    for (int i = 0; i < n; i++) {
```

Each warrior stepped into view.

---

### 🔥 Resolve All Waiting Warriors

```cpp
        while (!st.empty() && nums[i] > nums[st.top()]) {
            result[st.top()] = nums[i];
            st.pop();
        }
```

The current element was greater than the stack's top.
The top had been waiting -- now its prophecy was fulfilled.

`result[st.top()] = nums[i]` -- the answer is recorded.
Pop it -- its wait is over.

The while loop continued -- the current element might resolve
multiple waiting warriors in one sweep.

> _"A single strong warrior arriving
> can fulfill the prophecy of many weaker ones at once.
> The stack unwinds until it meets someone stronger --
> or empties entirely."_

---

### 📥 Current Warrior Joins the Waiting Stack

```cpp
        st.push(i);
    }
    return result;
}
```

After resolving all it could,
the current element was pushed onto the stack.
It now waited for its own greater successor.

Elements remaining on the stack at the end
never found anyone greater. Their answer stays `-1`.

---

### 🎺 The Trial of the Waiting Line

```cpp
int main() {
    vector<int> nums1 = {2, 1, 2, 4, 3};
    auto r1 = nextGreaterElement(nums1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 4 2 4 -1 -1

    vector<int> nums2 = {1, 3, 2, 4};
    auto r2 = nextGreaterElement(nums2);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 3 4 4 -1

    vector<int> nums3 = {5, 4, 3, 2, 1};
    auto r3 = nextGreaterElement(nums3);
    for (int x : r3) cout << x << " "; cout << endl;
    // expected: -1 -1 -1 -1 -1

    return 0;
}
```

---

**Full trace for `[2, 1, 2, 4, 3]`:**

| i | nums[i] | Stack before (indices) | Pops & resolutions                    | Stack after |
|---|---------|----------------------|---------------------------------------|-------------|
| 0 | 2       | []                   | --                                    | [0]         |
| 1 | 1       | [0]                  | 1 < 2 → no pop                       | [0, 1]      |
| 2 | 2       | [0, 1]               | 2 > nums[1]=1 → result[1]=2, pop 1   | [0]         |
|   |         | [0]                  | 2 == nums[0]=2 → not >, stop         | [0, 2]      |
| 3 | 4       | [0, 2]               | 4 > nums[2]=2 → result[2]=4, pop 2   |             |
|   |         | [0]                  | 4 > nums[0]=2 → result[0]=4, pop 0   | [3]         |
| 4 | 3       | [3]                  | 3 < nums[3]=4 → no pop               | [3, 4]      |

Remaining stack: [3, 4] → result[3]=-1, result[4]=-1 (default).

**Answer: [4, 2, 4, -1, -1]** ✓

At i=2: element `2` resolved index 1 (value `1`).
At i=3: element `4` resolved both index 2 (value `2`) and index 0 (value `2`).
One arrival, two prophecies fulfilled.

---

**Full trace for `[1, 3, 2, 4]`:**

| i | nums[i] | Stack before | Pops & resolutions                    | Stack after |
|---|---------|-------------|---------------------------------------|-------------|
| 0 | 1       | []          | --                                    | [0]         |
| 1 | 3       | [0]         | 3 > 1 → result[0]=3, pop 0           | [1]         |
| 2 | 2       | [1]         | 2 < 3 → no pop                       | [1, 2]      |
| 3 | 4       | [1, 2]      | 4 > 2 → result[2]=4, pop 2           |             |
|   |         | [1]         | 4 > 3 → result[1]=4, pop 1           | [3]         |

**Answer: [3, 4, 4, -1]** ✓

---

**Trace for `[5, 4, 3, 2, 1]` (strictly decreasing):**

| i | nums[i] | Pops | Stack after          |
|---|---------|------|----------------------|
| 0 | 5       | --   | [0]                  |
| 1 | 4       | --   | [0, 1]               |
| 2 | 3       | --   | [0, 1, 2]            |
| 3 | 2       | --   | [0, 1, 2, 3]         |
| 4 | 1       | --   | [0, 1, 2, 3, 4]      |

No pops ever happen. Every element is smaller than the previous.
The stack grows monotonically. All answers stay `-1`.

**Answer: [-1, -1, -1, -1, -1]** ✓

---

**Trace for `[1, 2, 3, 4, 5]` (strictly increasing):**

| i | nums[i] | Pops                          | Stack after |
|---|---------|-------------------------------|-------------|
| 0 | 1       | --                            | [0]         |
| 1 | 2       | pop 0 → result[0]=2           | [1]         |
| 2 | 3       | pop 1 → result[1]=3           | [2]         |
| 3 | 4       | pop 2 → result[2]=4           | [3]         |
| 4 | 5       | pop 3 → result[3]=5           | [4]         |

**Answer: [2, 3, 4, 5, -1]** ✓

Every element is immediately resolved by its successor.
The stack always has exactly one element.

---

## 🔄 Alternative -- Right to Left Approach

Instead of resolving waiting elements when a greater one arrives,
you can walk right to left and **read** the answer from the stack:

```cpp
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && st.top() <= nums[i]) {
            st.pop();
        }
        result[i] = st.empty() ? -1 : st.top();
        st.push(nums[i]);
    }
    return result;
}
```

Here the stack stores **values** (not indices).
At each position, pop everything smaller --
the surviving top IS the next greater element.

Both approaches are O(N). Left-to-right resolves on arrival.
Right-to-left reads the answer directly.

---

## 🌐 The NGE Family -- Where This Pattern Appears

| Problem                          | Variation                          |
| -------------------------------- | ---------------------------------- |
| Next Greater Element I           | NGE with subset lookup (hash map)  |
| Next Greater Element II          | Circular array (2N iteration)      |
| Daily Temperatures               | NGE but return distance, not value |
| Online Stock Span                | Previous Greater (streaming)       |
| Largest Rectangle in Histogram   | Previous/Next Smaller Element      |
| Sum of Subarray Minimums         | PSE + NSE with contribution count  |

The monotonic stack is the Swiss army knife of these problems.
Master this template and the rest are variations.

---

### 🧠 Memory of the Greater Prophecy Law

-   **Monotonic decreasing stack** of indices (values decrease bottom→top)
-   **Left-to-right:** when `nums[i] > nums[st.top()]` → resolve top, pop, repeat
-   **Right-to-left:** pop smaller, read surviving top as the answer
-   **Default:** `-1` for elements with no greater successor
-   Each index pushed once, popped at most once → **amortized O(1)** per element
-   **Time:** O(N)
-   **Space:** O(N)
-   **Edge cases:**
    -   Strictly increasing → each resolved by immediate successor
    -   Strictly decreasing → no pops, all answers -1
    -   All equal → no element is strictly greater, all -1
-   **Foundation** for Daily Temperatures, Stock Span, Histogram, and more

Thus is remembered the saga of **Next Greater Element**,
where the Oracle walked the line of warriors once --
maintaining a stack of those still waiting for a stronger successor --
and when a greater warrior arrived,
he fulfilled the prophecy of every weaker soul beneath him
in one powerful sweep --
until the line was fully walked
and only the unanswered remained,
their fate sealed as -1. 🔥⚔️✨
