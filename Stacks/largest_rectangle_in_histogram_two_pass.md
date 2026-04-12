## 🏛️📐 _The Greatest Monument (The Two-Pass Way): The Largest Rectangle in Histogram [Easy] Saga_

> \_"Before the Oracle mastered the single-pass ritual,
> she learned a simpler, more intuitive approach --
> one that broke the problem into two familiar pieces.
>
> For each bar of height `h`,
> the largest rectangle using `h` as the limiting height
> stretches left and right until a **shorter** bar blocks it.
>
> The Oracle needed two things for every bar:
>
> **The Previous Smaller Element (PSE)** --
> the nearest bar to the LEFT that is shorter.
> This is the left boundary.
>
> **The Next Smaller Element (NSE)** --
> the nearest bar to the RIGHT that is shorter.
> This is the right boundary.
>
> Both could be found in O(N) using monotonic stacks --
> one pass left-to-right for NSE,
> one pass right-to-left for PSE
> (or both left-to-right with different logic).
>
> Once both arrays were built:
>
> **width[i] = NSE[i] - PSE[i] - 1**
> **area[i] = heights[i] × width[i]**
> **answer = max of all areas**
>
> Two passes to build boundaries.
> One pass to compute areas.
> The greatest monument found."\_

---

This is the saga of **Largest Rectangle in Histogram [Easy Approach]**.

Same problem -- find the largest rectangle in a histogram.
But solved with the **Previous Smaller + Next Smaller** two-array method.

```
Input:  [2, 1, 5, 6, 2, 3]
Output: 10

Input:  [2, 4]
Output: 4
```

---

## 🧠 The Oracle's Core Insight -- PSE and NSE Define the Rectangle

For bar `i` with height `h = heights[i]`:

-   **PSE[i]** = index of the nearest bar to the LEFT with height < h.
    If none exists, PSE[i] = -1 (imaginary boundary before index 0).

-   **NSE[i]** = index of the nearest bar to the RIGHT with height < h.
    If none exists, NSE[i] = n (imaginary boundary after index n-1).

The rectangle using bar `i` as the height spans from
`PSE[i] + 1` to `NSE[i] - 1`.

```
width = NSE[i] - PSE[i] - 1
area  = heights[i] × width
```

Both PSE and NSE arrays are computed using **monotonic increasing stacks**
in O(N) each.

```
Time:  O(N) -- three linear passes
Space: O(N) -- two arrays + stack
```

---

### 📜 The Scroll of the Two Boundaries

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⬅️ Pass One -- Previous Smaller Element (PSE)

_Walk left to right, monotonic increasing stack_

```cpp
vector<int> findPSE(vector<int>& heights) {
    int n = heights.size();
    vector<int> pse(n, -1);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
            st.pop();
        }
        pse[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }
    return pse;
}
```

For each bar `i`, the Oracle popped all stack entries
with height **>=** the current bar.

After popping, the stack's top was the nearest shorter bar to the left.
If the stack was empty -- no shorter bar existed, so PSE = -1.

The stack maintained increasing heights from bottom to top.

> _"Pop everything that is not shorter than me.
> Whatever survives on top is my left boundary --
> the nearest bar that would block my rectangle from extending left."_

---

## ➡️ Pass Two -- Next Smaller Element (NSE)

_Walk right to left, monotonic increasing stack_

```cpp
vector<int> findNSE(vector<int>& heights) {
    int n = heights.size();
    vector<int> nse(n, n);
    stack<int> st;

    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
            st.pop();
        }
        nse[i] = st.empty() ? n : st.top();
        st.push(i);
    }
    return nse;
}
```

Mirror image of PSE -- walk from right to left.

For each bar `i`, pop all stack entries with height >= current.
The surviving top is the nearest shorter bar to the right.
If stack empty -- no shorter bar exists, NSE = n.

> _"Pop everything that is not shorter than me.
> Whatever survives on top is my right boundary --
> the nearest bar that would block my rectangle from extending right."_

---

## 📐 Pass Three -- Compute All Areas

```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    vector<int> pse = findPSE(heights);
    vector<int> nse = findNSE(heights);
    int maxArea = 0;

    for (int i = 0; i < n; i++) {
        int width = nse[i] - pse[i] - 1;
        int area = heights[i] * width;
        maxArea = max(maxArea, area);
    }
    return maxArea;
}
```

For each bar, the width was the gap between its two boundaries.
The area was height × width.
The maximum across all bars was the answer.

---

### 🎺 The Trial of the Skyline

```cpp
int main() {
    vector<int> h1 = {2, 1, 5, 6, 2, 3};
    cout << largestRectangleArea(h1) << endl; // expected: 10

    vector<int> h2 = {2, 4};
    cout << largestRectangleArea(h2) << endl; // expected: 4

    vector<int> h3 = {3, 3, 3, 3};
    cout << largestRectangleArea(h3) << endl; // expected: 12

    return 0;
}
```

---

**Full trace for `[2, 1, 5, 6, 2, 3]`:**

**PSE (Previous Smaller Element):**

| i | heights[i] | Stack before | Pops              | PSE[i]  | Stack after |
|---|-----------|--------------|-------------------|---------|-------------|
| 0 | 2         | []           | --                | -1      | [0]         |
| 1 | 1         | [0]          | pop 0 (2>=1)      | -1      | [1]         |
| 2 | 5         | [1]          | --                | 1       | [1,2]       |
| 3 | 6         | [1,2]        | --                | 2       | [1,2,3]     |
| 4 | 2         | [1,2,3]      | pop 3,2 (6>=2,5>=2)| 1     | [1,4]       |
| 5 | 3         | [1,4]        | --                | 4       | [1,4,5]     |

**PSE = [-1, -1, 1, 2, 1, 4]**

**NSE (Next Smaller Element):**

| i | heights[i] | Stack before | Pops              | NSE[i]  | Stack after |
|---|-----------|--------------|-------------------|---------|-------------|
| 5 | 3         | []           | --                | 6       | [5]         |
| 4 | 2         | [5]          | pop 5 (3>=2)      | 6       | [4]         |
| 3 | 6         | [4]          | --                | 4       | [4,3]       |
| 2 | 5         | [4,3]        | pop 3 (6>=5)      | 4       | [4,2]       |
| 1 | 1         | [4,2]        | pop 2,4 (5>=1,2>=1)| 6     | [1]         |
| 0 | 2         | [1]          | --                | 1       | [1,0]       |

**NSE = [1, 6, 4, 4, 6, 6]**

**Area computation:**

| i | h | PSE[i] | NSE[i] | Width = NSE-PSE-1 | Area = h×w | maxArea |
|---|---|--------|--------|--------------------|------------|---------|
| 0 | 2 | -1     | 1      | 1-(-1)-1 = 1      | 2×1 = 2    | 2       |
| 1 | 1 | -1     | 6      | 6-(-1)-1 = 6      | 1×6 = 6    | 6       |
| 2 | 5 | 1      | 4      | 4-1-1 = 2         | 5×2 = **10** | **10** |
| 3 | 6 | 2      | 4      | 4-2-1 = 1         | 6×1 = 6    | 10      |
| 4 | 2 | 1      | 6      | 6-1-1 = 4         | 2×4 = 8    | 10      |
| 5 | 3 | 4      | 6      | 6-4-1 = 1         | 3×1 = 3    | 10      |

**Answer: 10** ✓

Bar at index 2 (height 5) extends from index 2 to 3 (width 2) → area 10.

---

**Trace for `[3, 3, 3, 3]` (all equal):**

**PSE:** For `>=` comparison, each bar pops the previous equal one.
PSE = [-1, -1, -1, -1] (using strict `<` for PSE, all equal bars have no strictly smaller predecessor... wait)

Actually with `>=` in the pop condition:

| i | Stack before | Pops         | PSE[i] |
|---|-------------|--------------|--------|
| 0 | []          | --           | -1     |
| 1 | [0]         | pop 0 (3>=3) | -1     |
| 2 | [1]         | pop 1 (3>=3) | -1     |
| 3 | [2]         | pop 2 (3>=3) | -1     |

PSE = [-1, -1, -1, -1]

NSE = [4, 4, 4, 4] (all get `n` since no strictly smaller exists)

| i | h | Width = 4-(-1)-1 = 4 | Area = 3×4 = 12 |
|---|---|----------------------|------------------|

**Answer: 12** ✓ — full rectangle, height 3, width 4.

---

## 🔍 Why `>=` in the Pop Condition?

We pop when `heights[st.top()] >= heights[i]`, not just `>`.

This handles **equal heights** correctly.
If two adjacent bars have the same height,
one of them should see the other as NOT a boundary --
otherwise the width would be too narrow.

Using `>=` ensures that among equal bars,
the leftmost one gets the widest rectangle
(its PSE is further left, its NSE is further right).

---

## 🔄 Comparison with the Single-Pass Approach

| Two-Pass (PSE + NSE)              | Single-Pass (Sentinel)           |
| --------------------------------- | -------------------------------- |
| Build PSE array, build NSE array  | Pop and compute in one loop      |
| Three separate loops               | One loop with sentinel at end    |
| Easier to understand and debug    | More compact, slightly trickier  |
| Same O(N) time and O(N) space     | Same O(N) time and O(N) space    |

Both are equally valid. The two-pass approach is often easier
to explain in interviews because PSE and NSE are standalone concepts
that can be reused in many other problems.

---

### 🧠 Memory of the Two-Boundary Law

-   **PSE[i]** = index of nearest shorter bar to the LEFT (-1 if none)
-   **NSE[i]** = index of nearest shorter bar to the RIGHT (n if none)
-   Both computed with **monotonic increasing stacks** in O(N)
-   **Pop condition:** `heights[st.top()] >= heights[i]` (handles equal heights)
-   **Width:** `NSE[i] - PSE[i] - 1`
-   **Area:** `heights[i] × width`
-   **Answer:** max of all areas
-   PSE and NSE are reusable patterns -- appear in many stack problems
-   **Time:** O(N) -- three linear passes
-   **Space:** O(N) -- two arrays + stack

Thus is remembered the saga of
**Largest Rectangle in Histogram [Easy Approach]**,
where the Oracle split the problem into two familiar quests --
finding the Previous Smaller Element for every bar,
finding the Next Smaller Element for every bar --
each solved with a monotonic increasing stack --
then computing every bar's rectangle
as height times the gap between its two boundaries --
and crowning the largest among them all. 🏛️📐✨
