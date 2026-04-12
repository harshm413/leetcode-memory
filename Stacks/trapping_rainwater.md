## 🌧️📚 _Filling Valleys Layer by Layer: The Trapping Rain Water (Stack) Saga_

> \_"The Oracle had solved the rain water problem
> column by column -- measuring the water above each pillar.
>
> But there was another way to see it:
>
> **Not column by column, but LAYER by LAYER.**
>
> When a taller bar appeared after a valley,
> it formed a pool with the bar beneath.
> The pool had a left wall, a bottom, and a right wall.
> Its water was `width × height` of that horizontal layer.
>
> A **monotonic decreasing stack** tracked the terrain.
> When a taller bar arrived and popped a shorter one,
> a horizontal water layer was computed
> between the new bar and the bar beneath the popped one.
>
> Layer by layer, valley by valley,
> the water accumulated --
> not from above each pillar,
> but from the shape of each pool."\_

---

This is the saga of **Trapping Rain Water (Monotonic Stack Approach)**.

Given an array `height` where `height[i]` is the elevation at position `i`:

-   Compute how much water can be trapped after raining.
-   Uses a **monotonic decreasing stack** to process water horizontally.

```
Input:  [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

Input:  [4,2,0,3,2,5]
Output: 9
```

---

## 🧠 The Oracle's Core Insight -- Horizontal Layers, Not Vertical Columns

The prefix/suffix and two-pointer approaches compute water
**vertically** -- how much water sits above each column.

The stack approach computes water **horizontally** --
each time a valley is bounded by two walls,
the rectangular water layer between them is computed.

The stack holds indices in **decreasing** order of height.
When a taller bar `i` arrives and pops a shorter bar `bottom`:

```
left wall  = new stack top (the bar beneath bottom)
right wall = current bar i
width      = i - left - 1
height     = min(height[left], height[i]) - height[bottom]
water     += width × height
```

This computes one horizontal slice of the pool.
Multiple pops at the same `i` compute multiple layers
stacked on top of each other.

```
Time:  O(N) -- each index pushed once, popped once
Space: O(N) -- the stack
```

---

### 📜 The Scroll of the Layered Pools

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Layer-by-Layer Ritual

```cpp
int trap(vector<int>& height) {
    stack<int> st;
    int water = 0;
```

The Oracle prepared a monotonic decreasing stack of indices
and a running total of trapped water.

---

## 🔁 Walk the Wall Bar by Bar

```cpp
    for (int i = 0; i < (int)height.size(); i++) {
```

Each bar was processed left to right.

---

### 💧 Pop Valleys and Compute Water Layers

```cpp
        while (!st.empty() && height[i] > height[st.top()]) {
            int bottom = st.top();
            st.pop();
```

The current bar was taller than the stack's top.
The top was a valley bottom -- pop it.

---

```cpp
            if (st.empty()) break;
```

If the stack is now empty, there's no left wall.
No pool can form. Break.

---

```cpp
            int left = st.top();
            int width = i - left - 1;
            int h = min(height[left], height[i]) - height[bottom];
            water += width * h;
        }
```

The left wall is the new stack top.
The right wall is the current bar `i`.

**Width** = distance between the two walls (exclusive).
**Height** = water level minus the valley floor.
The water level is `min(left wall, right wall)` --
water can't rise above the shorter wall.

`water += width × h` adds this horizontal layer.

> _"Each pop reveals a valley floor.
> The walls on either side define the pool.
> The water fills the gap between the floor
> and the shorter wall --
> one horizontal layer at a time."_

---

### 📥 Push the Current Bar

```cpp
        st.push(i);
    }
    return water;
}
```

After all pops, the current bar joined the stack.
The decreasing order was maintained.

---

### 🎺 The Trial of the Layered Pools

```cpp
int main() {
    vector<int> h1 = {0,1,0,2,1,0,1,3,2,1,2,1};
    cout << trap(h1) << endl; // expected: 6

    vector<int> h2 = {4,2,0,3,2,5};
    cout << trap(h2) << endl; // expected: 9

    return 0;
}
```

---

**Full trace for `[0,1,0,2,1,0,1,3,2,1,2,1]`:**

| i | h[i] | Stack before | Pops & water layers                                    | Water | Stack after |
|---|------|-------------|--------------------------------------------------------|-------|-------------|
| 0 | 0    | []          | --                                                     | 0     | [0]         |
| 1 | 1    | [0]         | pop 0: empty after → break                            | 0     | [1]         |
| 2 | 0    | [1]         | --                                                     | 0     | [1,2]       |
| 3 | 2    | [1,2]       | pop 2: left=1, w=3-1-1=1, h=min(1,2)-0=1, +1          | 1     | [1]         |
|   |      | [1]         | pop 1: empty → break                                  | 1     | [3]         |
| 4 | 1    | [3]         | --                                                     | 1     | [3,4]       |
| 5 | 0    | [3,4]       | --                                                     | 1     | [3,4,5]     |
| 6 | 1    | [3,4,5]     | pop 5: left=4, w=6-4-1=1, h=min(1,1)-0=1, +1          | 2     | [3,4]       |
|   |      | [3,4]       | h[6]=1 not > h[4]=1 → stop                            | 2     | [3,4,6]     |
| 7 | 3    | [3,4,6]     | pop 6: left=4, w=7-4-1=2, h=min(1,3)-1=0, +0          | 2     |             |
|   |      | [3,4]       | pop 4: left=3, w=7-3-1=3, h=min(2,3)-1=1, +3          | 5     | [3]         |
|   |      | [3]         | pop 3: empty → break                                  | 5     | [7]         |
| 8 | 2    | [7]         | --                                                     | 5     | [7,8]       |
| 9 | 1    | [7,8]       | --                                                     | 5     | [7,8,9]     |
|10 | 2    | [7,8,9]     | pop 9: left=8, w=10-8-1=1, h=min(2,2)-1=1, +1         | 6     | [7,8]       |
|   |      | [7,8]       | h[10]=2 not > h[8]=2 → stop                           | 6     | [7,8,10]    |
|11 | 1    | [7,8,10]    | --                                                     | 6     | [7,8,10,11] |

**Answer: 6** ✓

The water was computed in horizontal layers:
+1 at i=3, +1 at i=6, +3 at i=7 (a wide layer), +1 at i=10.

---

**Trace for `[4, 2, 0, 3, 2, 5]`:**

| i | h[i] | Pops & water layers                                         | Water | Stack after |
|---|------|-------------------------------------------------------------|-------|-------------|
| 0 | 4    | --                                                          | 0     | [0]         |
| 1 | 2    | --                                                          | 0     | [0,1]       |
| 2 | 0    | --                                                          | 0     | [0,1,2]     |
| 3 | 3    | pop 2: left=1, w=3-1-1=1, h=min(2,3)-0=2, +2               | 2     |             |
|   |      | pop 1: left=0, w=3-0-1=2, h=min(4,3)-2=1, +2               | 4     | [0,3]       |
| 4 | 2    | --                                                          | 4     | [0,3,4]     |
| 5 | 5    | pop 4: left=3, w=5-3-1=1, h=min(3,5)-2=1, +1               | 5     |             |
|   |      | pop 3: left=0, w=5-0-1=4, h=min(4,5)-3=1, +4               | 9     | [0]         |
|   |      | pop 0: empty → break                                       | 9     | [5]         |

**Answer: 9** ✓

Two major pools: one at i=3 (two layers) and one at i=5 (two layers).

---

## 🔍 Horizontal vs Vertical -- Same Answer, Different View

| Vertical (Prefix/Two Pointers)    | Horizontal (Stack)                |
| --------------------------------- | --------------------------------- |
| Water above each column           | Water in each pool layer          |
| `min(maxL, maxR) - h[i]` per col  | `width × h` per layer            |
| Intuitive for single columns      | Intuitive for pool shapes         |
| O(N) time, O(1) or O(N) space    | O(N) time, O(N) space            |

Both decompose the same total water differently.
The stack approach is useful when you need to think
about pools as bounded regions rather than individual columns.

---

### 🧠 Memory of the Layered Pool Law

-   **Monotonic decreasing stack** of indices
-   **When `height[i] > height[st.top()]`:** pop the top as `bottom`
    -   If stack empty → no left wall → break
    -   Left wall = new `st.top()`, right wall = `i`
    -   Width = `i - left - 1`
    -   Height = `min(height[left], height[i]) - height[bottom]`
    -   Water += width × height
-   **Push** current index after all pops
-   Computes water **layer by layer horizontally**
-   **Time:** O(N) -- each index pushed once, popped once
-   **Space:** O(N) -- the stack
-   **Edge cases:**
    -   Less than 3 bars → 0
    -   Strictly decreasing → stack grows, never pops (no water)
    -   Strictly increasing → each bar pops previous, but no left wall after pop

Thus is remembered the saga of **Trapping Rain Water (Stack)**,
where the Oracle did not measure water column by column
but instead filled valleys layer by layer --
popping each valley floor from the monotonic stack,
reading the left and right walls,
computing the horizontal water slice between them --
until every pool had been filled
and every layer had been counted. 🌧️📚✨
