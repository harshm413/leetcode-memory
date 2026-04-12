## 🏛️📐 _The Greatest Monument in the Skyline: The Largest Rectangle in Histogram Saga_

> \_"Along the Kingdom's skyline,
> bars of stone rose to different heights --
> a histogram carved against the horizon.
>
> The King commanded:
>
> **'Find the largest rectangle
> that can be formed within this histogram.
> The rectangle must be aligned with the bars --
> its height limited by the shortest bar it spans.'**
>
> The naive approach would check every pair of bars
> and find the minimum height between them.
> O(N²) -- too slow for a vast skyline.
>
> The Oracle devised a stack-based ritual:
>
> **For each bar, find how far LEFT it can extend
> (until a shorter bar blocks it)
> and how far RIGHT it can extend
> (until a shorter bar blocks it).
> The rectangle's width = right boundary - left boundary - 1.
> The rectangle's area = height × width.**
>
> Both boundaries could be found simultaneously
> using a **monotonic increasing stack**.
>
> When a shorter bar arrived,
> every taller bar on the stack had found its right boundary.
> The bar beneath the popped bar was its left boundary.
>
> One pass. One stack.
> The greatest monument revealed."\_

---

This is the saga of **Largest Rectangle in Histogram**.

Given an array `heights` where `heights[i]` is the height of bar `i`:

-   Find the area of the **largest rectangle** in the histogram.
-   Each bar has width 1.

```
Input:  [2,1,5,6,2,3]
Output: 10   (rectangle of height 5, spanning bars at index 2-3)

Input:  [2,4]
Output: 4
```

---

## 🧠 The Oracle's Core Insight -- Monotonic Increasing Stack

For each bar of height `h`, the largest rectangle using `h` as the height
extends left and right until a **shorter** bar is encountered.

```
width = rightBoundary - leftBoundary - 1
area  = h × width
```

A **monotonic increasing stack** (heights increase bottom to top)
finds both boundaries in a single pass:

```
Walk left to right.
For each bar i:
  While stack top's height >= heights[i]:
    Pop the top → this bar has found its RIGHT boundary (i).
    The new stack top is its LEFT boundary.
    Compute area.
  Push i.

After the array, pop remaining bars with right boundary = n.
```

When a bar is popped:
-   **Right boundary** = the current index `i` (the shorter bar that triggered the pop).
-   **Left boundary** = the new stack top after popping (the previous shorter bar).
-   **Width** = `i - stack.top() - 1` (or `i` if stack is empty).

```
Time:  O(N) -- each bar pushed once, popped once
Space: O(N) -- the stack
```

---

### 📜 The Scroll of the Skyline

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Monument-Finding Ritual

```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    stack<int> st;
    int maxArea = 0;
```

The Oracle prepared:
-   `st` -- a stack of **indices**, maintaining increasing heights.
-   `maxArea` -- the largest rectangle found so far.

---

## 🔁 Walk the Histogram Bar by Bar

```cpp
    for (int i = 0; i <= n; i++) {
        int currHeight = (i == n) ? 0 : heights[i];
```

The Oracle walked indices `0` to `n` -- one past the end.

At index `n`, a **virtual bar of height 0** was used.
This sentinel forced every remaining bar on the stack to be popped,
ensuring all rectangles were evaluated.

> _"The sentinel of height 0 at the end
> is shorter than everything.
> It flushes the entire stack,
> so no bar's rectangle goes uncounted."_

---

### 💥 Pop Taller Bars -- They Found Their Right Boundary

```cpp
        while (!st.empty() && heights[st.top()] >= currHeight) {
            int height = heights[st.top()];
            st.pop();
```

The current bar was shorter than (or equal to) the stack's top.
The top bar could not extend rightward past index `i`.

Pop it -- its right boundary is `i`.

---

### 📐 Compute the Rectangle

```cpp
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, height * width);
        }
```

**Width calculation:**

If the stack is empty after popping --
the popped bar was the shortest seen so far.
It can extend all the way from index `0` to `i-1`.
Width = `i`.

If the stack is not empty --
the new top is the **previous shorter bar** (left boundary).
Width = `i - stack.top() - 1`.

Area = `height × width`. Update `maxArea` if larger.

> _"The popped bar's rectangle stretches
> from just after the left boundary
> to just before the right boundary.
> The stack gives both boundaries for free."_

---

### 📥 Push the Current Bar

```cpp
        st.push(i);
    }
    return maxArea;
}
```

After all pops, the current bar was pushed.
The stack maintained its increasing order.

When the loop finished (including the sentinel at `i = n`),
every bar had been popped and its rectangle computed.

---

### 🎺 The Trial of the Skyline

```cpp
int main() {
    vector<int> h1 = {2, 1, 5, 6, 2, 3};
    cout << largestRectangleArea(h1) << endl; // expected: 10

    vector<int> h2 = {2, 4};
    cout << largestRectangleArea(h2) << endl; // expected: 4

    vector<int> h3 = {1};
    cout << largestRectangleArea(h3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `[2, 1, 5, 6, 2, 3]`:**

| i | currH | Stack before | Action                                          | Area computed      | maxArea | Stack after |
|---|-------|--------------|------------------------------------------------|--------------------|---------|-------------|
| 0 | 2     | []           | Push 0                                          | --                 | 0       | [0]         |
| 1 | 1     | [0]          | h[0]=2 >= 1 → pop 0, w=1, area=2×1=2           | 2                  | 2       | [1]         |
| 2 | 5     | [1]          | Push 2                                          | --                 | 2       | [1,2]       |
| 3 | 6     | [1,2]        | Push 3                                          | --                 | 2       | [1,2,3]     |
| 4 | 2     | [1,2,3]      | h[3]=6 >= 2 → pop 3, w=4-2-1=1, area=6×1=6     | 6                  | 6       |             |
|   |       | [1,2]        | h[2]=5 >= 2 → pop 2, w=4-1-1=2, area=5×2=10    | 10                 | **10**  | [1,4]       |
| 5 | 3     | [1,4]        | Push 5                                          | --                 | 10      | [1,4,5]     |
| 6 | 0     | [1,4,5]      | h[5]=3 >= 0 → pop 5, w=6-4-1=1, area=3×1=3     | 3                  | 10      |             |
|   |       | [1,4]        | h[4]=2 >= 0 → pop 4, w=6-1-1=4, area=2×4=8     | 8                  | 10      |             |
|   |       | [1]          | h[1]=1 >= 0 → pop 1, w=6, area=1×6=6            | 6                  | 10      | [6]         |

**Answer: 10** ✓

The rectangle of height 5 spanning indices 2-3 (width 2) gave area 10.

At i=4: bar 6 (index 3) was popped with width 1 → area 6.
Then bar 5 (index 2) was popped with width 2 (from index 2 to 3) → area 10.

---

**Trace for `[2, 4]`:**

| i | currH | Stack | Action                                    | Area | maxArea |
|---|-------|-------|------------------------------------------|------|---------|
| 0 | 2     | []    | Push 0                                    | --   | 0       |
| 1 | 4     | [0]   | Push 1                                    | --   | 0       |
| 2 | 0     | [0,1] | pop 1, w=2-0-1=1, area=4×1=4             | 4    | 4       |
|   |       | [0]   | pop 0, w=2, area=2×2=4                    | 4    | 4       |

**Answer: 4** ✓ — either the single bar of height 4, or both bars at height 2.

---

**Trace for `[3, 3, 3, 3]` (all equal):**

| i | currH | Action                                    | Area | maxArea |
|---|-------|------------------------------------------|------|---------|
| 0-3 | 3  | Push all: stack = [0,1,2,3]               | --   | 0       |
| 4 | 0     | pop 3, w=4-2-1=1, area=3                  | 3    | 3       |
|   |       | pop 2, w=4-1-1=2, area=6                  | 6    | 6       |
|   |       | pop 1, w=4-0-1=3, area=9                  | 9    | 9       |
|   |       | pop 0, w=4, area=12                       | 12   | **12**  |

**Answer: 12** ✓ — all 4 bars at height 3, width 4.

---

## 🔍 Why the Sentinel at Index n?

Without the virtual height-0 bar at the end,
bars remaining on the stack after the loop
would never be popped -- their rectangles uncounted.

The sentinel `currHeight = 0` at `i = n` is shorter than everything,
forcing every remaining bar to pop and compute its area.

This eliminates the need for a separate cleanup loop after the main pass.

---

### 🧠 Memory of the Greatest Monument Law

-   **Monotonic increasing stack** of indices (heights increase bottom→top)
-   **Sentinel:** virtual bar of height 0 at index `n` flushes the stack
-   **When popping bar at index `top`:**
    -   Right boundary = current index `i`
    -   Left boundary = new `stack.top()` (or -1 if empty → width = `i`)
    -   Width = `i - stack.top() - 1` (or `i` if stack empty)
    -   Area = `height × width`
-   **Push** current index after all pops
-   **Time:** O(N) -- each bar pushed once, popped once
-   **Space:** O(N) -- the stack
-   **Edge cases:**
    -   Single bar → area = its height
    -   All equal heights → area = height × n
    -   Strictly increasing → sentinel pops everything at the end
    -   Strictly decreasing → each bar popped immediately by the next

Thus is remembered the saga of **Largest Rectangle in Histogram**,
where the Oracle walked the skyline with a monotonic stack --
pushing bars that rose, popping bars that fell --
and at every pop, computing the rectangle
that the fallen bar could have formed,
bounded by the shorter bars on either side --
until the sentinel of zero flushed the final stones
and the greatest monument in the skyline
stood revealed. 🏛️📐✨
