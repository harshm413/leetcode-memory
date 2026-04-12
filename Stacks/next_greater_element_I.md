## 🔍🗺️ _The Map of Greater Successors: The Next Greater Element I Saga_

> \_"Two scrolls arrived at the Oracle's chamber.
>
> The first scroll, `nums1`, was a subset --
> a short list of chosen warriors.
>
> The second scroll, `nums2`, was the full army --
> all warriors standing in a line, each unique.
>
> The Oracle was commanded:
>
> **'For each warrior in nums1,
> find their position in nums2,
> then look to the RIGHT in nums2
> for the first warrior that is GREATER.
> If none exists -- return -1.'**
>
> The naive approach would search nums2
> for each element of nums1, then scan rightward.
> O(N × M) -- too slow.
>
> The Oracle devised a two-step ritual:
>
> **Step One -- Build a map of next greater elements
> for EVERY element in nums2 using a monotonic stack.
> Store the results in a hash map: value → next greater.**
>
> **Step Two -- For each element in nums1,
> simply look up the answer in the map.**
>
> One stack pass over nums2.
> One lookup per element of nums1.
> O(N + M) total."\_

---

This is the saga of **Next Greater Element I**.

Given two arrays `nums1` and `nums2` where:
-   `nums1` is a **subset** of `nums2`.
-   All elements in `nums2` are **unique**.

For each element in `nums1`, find the **next greater element**
to its right in `nums2`. If none exists, return `-1`.

```
Input:  nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: [-1,3,-1]

Input:  nums1 = [2,4], nums2 = [1,2,3,4]
Output: [3,-1]
```

---

## 🧠 The Oracle's Core Insight -- Precompute All Answers in nums2

Instead of searching for each nums1 element individually,
precompute the next greater element for EVERY element in nums2
and store it in a hash map.

**Step 1: Monotonic stack on nums2 (right to left)**

Walk nums2 from right to left.
Maintain a stack of values in decreasing order.
For each element, pop everything smaller --
the surviving top is the next greater element.

Store `map[nums2[i]] = next greater` (or -1 if stack empty).

**Step 2: Look up each nums1 element in the map**

```
Time:  O(N + M) -- N = nums2.size(), M = nums1.size()
Space: O(N) -- the map + stack
```

---

### 📜 The Scroll of the Two Scrolls

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <unordered_map>
using namespace std;
```

---

## ⚔️ Step One -- Build the Next Greater Map from nums2

```cpp
vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
    unordered_map<int, int> ngeMap;
    stack<int> st;
```

The Oracle prepared:
-   `ngeMap` -- maps each value in nums2 to its next greater element.
-   `st` -- a monotonic decreasing stack of values.

---

### 🔁 Walk nums2 Right to Left

```cpp
    for (int i = nums2.size() - 1; i >= 0; i--) {
```

Processing right to left ensures that when we handle element `i`,
the stack already contains elements to its right.

---

### 🧹 Pop Smaller or Equal Elements

```cpp
        while (!st.empty() && st.top() <= nums2[i]) {
            st.pop();
        }
```

Pop everything that is not strictly greater than the current element.
They can never be the "next greater" for anyone to the left --
the current element blocks them.

---

### 📝 Record the Answer

```cpp
        ngeMap[nums2[i]] = st.empty() ? -1 : st.top();
```

If the stack is not empty -- the top is the next greater element.
If empty -- no greater element exists to the right. Record -1.

---

### 📥 Push Current Element

```cpp
        st.push(nums2[i]);
    }
```

The current element joins the stack.
It might be the answer for elements further left.

---

## 🗺️ Step Two -- Look Up Each nums1 Element

```cpp
    vector<int> result;
    for (int x : nums1) {
        result.push_back(ngeMap[x]);
    }
    return result;
}
```

For each element in nums1, the answer was already computed.
One O(1) lookup per element.

---

### 🎺 The Trial of the Two Scrolls

```cpp
int main() {
    vector<int> n1a = {4, 1, 2}, n2a = {1, 3, 4, 2};
    auto r1 = nextGreaterElement(n1a, n2a);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: -1 3 -1

    vector<int> n1b = {2, 4}, n2b = {1, 2, 3, 4};
    auto r2 = nextGreaterElement(n1b, n2b);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 3 -1

    return 0;
}
```

---

**Full trace for nums2 = `[1, 3, 4, 2]`:**

Walking right to left:

| i | nums2[i] | Stack before | Pops          | NGE          | Stack after |
|---|----------|-------------|---------------|--------------|-------------|
| 3 | 2        | []          | --            | -1           | [2]         |
| 2 | 4        | [2]         | pop 2 (2<=4)  | -1           | [4]         |
| 1 | 3        | [4]         | --            | 4            | [3, 4]      |
| 0 | 1        | [3, 4]      | --            | 3            | [1, 3, 4]   |

**ngeMap:** `{2: -1, 4: -1, 3: 4, 1: 3}`

**Lookup for nums1 = `[4, 1, 2]`:**
-   4 → -1
-   1 → 3
-   2 → -1

**Answer: [-1, 3, -1]** ✓

---

**Full trace for nums2 = `[1, 2, 3, 4]`:**

Walking right to left:

| i | nums2[i] | Stack before | Pops              | NGE | Stack after |
|---|----------|-------------|-------------------|-----|-------------|
| 3 | 4        | []          | --                | -1  | [4]         |
| 2 | 3        | [4]         | --                | 4   | [3, 4]      |
| 1 | 2        | [3, 4]      | --                | 3   | [2, 3, 4]   |
| 0 | 1        | [2, 3, 4]   | --                | 2   | [1, 2, 3, 4]|

**ngeMap:** `{4: -1, 3: 4, 2: 3, 1: 2}`

**Lookup for nums1 = `[2, 4]`:**
-   2 → 3
-   4 → -1

**Answer: [3, -1]** ✓

Strictly increasing array -- each element's next greater is its immediate successor.
The stack never pops anything.

---

**Trace for nums2 = `[4, 3, 2, 1]` (strictly decreasing):**

| i | nums2[i] | Pops | NGE | Stack after      |
|---|----------|------|-----|------------------|
| 3 | 1        | --   | -1  | [1]              |
| 2 | 2        | 1    | -1  | [2]              |
| 1 | 3        | 2    | -1  | [3]              |
| 0 | 4        | 3    | -1  | [4]              |

**ngeMap:** `{1: -1, 2: -1, 3: -1, 4: -1}`

Every element pops the previous one. No greater element to the right for anyone.

---

**Trace for nums2 = `[2, 1, 3]`:**

| i | nums2[i] | Stack before | Pops     | NGE | Stack after |
|---|----------|-------------|----------|-----|-------------|
| 2 | 3        | []          | --       | -1  | [3]         |
| 1 | 1        | [3]         | --       | 3   | [1, 3]      |
| 0 | 2        | [1, 3]      | pop 1    | 3   | [2, 3]      |

**ngeMap:** `{3: -1, 1: 3, 2: 3}`

Both `1` and `2` have next greater = `3`.

---

## 🔍 Why Precompute Instead of Search Per Element?

If we searched nums2 for each nums1 element and then scanned right:
-   Finding the element: O(N) per search (or O(1) with a position map).
-   Scanning right for the next greater: O(N) worst case.
-   Total: O(M × N).

Precomputing with a monotonic stack: O(N) for the stack pass + O(M) for lookups = O(N + M).

The map turns repeated work into a one-time investment.

---

## 🔄 Left-to-Right Alternative

You can also walk nums2 left to right:

```cpp
for (int i = 0; i < nums2.size(); i++) {
    while (!st.empty() && st.top() < nums2[i]) {
        ngeMap[st.top()] = nums2[i];
        st.pop();
    }
    st.push(nums2[i]);
}
// Remaining stack elements have no next greater → -1
while (!st.empty()) {
    ngeMap[st.top()] = -1;
    st.pop();
}
```

Here the stack holds elements waiting for their answer.
When a greater element arrives, it resolves all smaller ones on the stack.
Same O(N) complexity, different direction.

---

### 🧠 Memory of the Greater Successor Map Law

-   **Precompute** next greater element for ALL of nums2 using monotonic stack
-   **Right-to-left:** pop while `st.top() <= nums2[i]`, NGE = st.top() or -1
-   **Store in hash map:** `ngeMap[value] = next greater element`
-   **Look up** each nums1 element in the map → O(1) per query
-   All elements in nums2 are **unique** → map keys are unambiguous
-   nums1 is a **subset** of nums2 → every lookup is guaranteed to exist
-   **Time:** O(N + M) -- N for stack pass, M for lookups
-   **Space:** O(N) -- map + stack
-   **Edge cases:**
    -   nums1 = nums2 → answer for every element
    -   Strictly increasing nums2 → each element's NGE is its successor
    -   Strictly decreasing nums2 → all answers are -1

Thus is remembered the saga of **Next Greater Element I**,
where the Oracle did not hunt for each warrior individually
but instead built a map of every warrior's greater successor --
walking nums2 once with a monotonic stack,
recording every answer in a hash map --
then answering each query from nums1
with a single glance at the map. 🔍🗺️✨
