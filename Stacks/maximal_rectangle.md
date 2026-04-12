## 🏗️📐 _The Hidden Monument in the Grid: The Maximal Rectangle Saga_

> \_"The Kingdom's skyline had been conquered --
> the Oracle had found the largest rectangle in a histogram.
>
> But now a harder challenge arrived:
>
> **A 2D grid of '0's and '1's.
> Find the largest rectangle containing only '1's.**
>
> The Oracle stared at the grid.
> Then she saw the hidden truth:
>
> **Every row of the grid is the base of a histogram.**
>
> If she built a histogram of heights for each row --
> where the height at each column was the count
> of consecutive '1's stacked above (including the current row) --
> then the problem reduced to
> 'Largest Rectangle in Histogram'
> applied to each row.
>
> Row by row, she built the histogram.
> Row by row, she solved it with the monotonic stack.
> The maximum across all rows was the answer.
>
> A 2D problem reduced to N applications
> of a 1D problem already mastered."\_

---

This is the saga of **Maximal Rectangle**.

Given a `rows × cols` binary matrix filled with `'0'` and `'1'`:

-   Find the area of the largest rectangle containing only `'1'`s.

```
Input:
  ["1","0","1","0","0"]
  ["1","0","1","1","1"]
  ["1","1","1","1","1"]
  ["1","0","0","1","0"]

Output: 6
```

The 6-area rectangle spans rows 1-2, columns 2-4.

---

## 🧠 The Oracle's Core Insight -- Row-by-Row Histogram

Build a `heights[]` array where `heights[j]` =
number of consecutive `'1'`s ending at the current row in column `j`.

For each row, update the heights:

```
If matrix[i][j] == '1' → heights[j] += 1
If matrix[i][j] == '0' → heights[j] = 0   (the streak breaks)
```

Then apply **Largest Rectangle in Histogram** on `heights[]`.

**Example for the given matrix:**

```
Row 0: heights = [1, 0, 1, 0, 0]  → max rect in histogram
Row 1: heights = [2, 0, 2, 1, 1]  → max rect in histogram
Row 2: heights = [3, 1, 3, 2, 2]  → max rect in histogram
Row 3: heights = [4, 0, 0, 3, 0]  → max rect in histogram
```

The answer is the maximum across all rows.

```
Time:  O(rows × cols) -- each row: O(cols) to update heights + O(cols) for histogram
Space: O(cols) -- the heights array + stack
```

---

### 📜 The Scroll of the Hidden Grid

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## 🏛️ The Histogram Solver -- Already Mastered

```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    stack<int> st;
    int maxArea = 0;

    for (int i = 0; i <= n; i++) {
        int currHeight = (i == n) ? 0 : heights[i];

        while (!st.empty() && heights[st.top()] >= currHeight) {
            int height = heights[st.top()];
            st.pop();
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, height * width);
        }
        st.push(i);
    }
    return maxArea;
}
```

The same monotonic increasing stack ritual
from the Largest Rectangle in Histogram saga.

Sentinel at index `n` (height 0) flushes the stack.
Each bar is pushed once, popped once. O(cols).

---

## 🏗️ The Oracle's Grid-to-Histogram Ritual

```cpp
int maximalRectangle(vector<vector<char>>& matrix) {
    if (matrix.empty()) return 0;

    int rows = matrix.size();
    int cols = matrix[0].size();
    vector<int> heights(cols, 0);
    int maxArea = 0;
```

The Oracle prepared:
-   `heights` -- the histogram for the current row, initialized to all 0.
-   `maxArea` -- the largest rectangle found across all rows.

---

## 🔁 Process Each Row as a Histogram Base

```cpp
    for (int i = 0; i < rows; i++) {
```

Each row became the base of a new histogram.

---

### 📊 Update the Heights

```cpp
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] == '1') {
                heights[j] += 1;
            } else {
                heights[j] = 0;
            }
        }
```

For each column `j`:

**If `'1'`** -- the streak of consecutive 1s continues.
Increment the height.

**If `'0'`** -- the streak is broken.
Reset the height to 0.

> _"A '0' is a crack in the foundation.
> No matter how tall the column was above,
> the crack resets everything to ground level."_

---

### 🏛️ Solve the Histogram for This Row

```cpp
        maxArea = max(maxArea, largestRectangleArea(heights));
    }
    return maxArea;
}
```

The updated `heights` array was fed into the histogram solver.
The result was compared against the global maximum.

After all rows, `maxArea` held the answer.

---

### 🎺 The Trial of the Hidden Grid

```cpp
int main() {
    vector<vector<char>> matrix = {
        {'1','0','1','0','0'},
        {'1','0','1','1','1'},
        {'1','1','1','1','1'},
        {'1','0','0','1','0'}
    };
    cout << maximalRectangle(matrix) << endl; // expected: 6
    return 0;
}
```

---

**Full trace:**

```
Matrix:
  1 0 1 0 0
  1 0 1 1 1
  1 1 1 1 1
  1 0 0 1 0
```

**Row 0:** heights = `[1, 0, 1, 0, 0]`

Histogram: bars of height 1, 0, 1, 0, 0.
Largest rectangle = 1 (single bar of height 1).

---

**Row 1:** heights = `[2, 0, 2, 1, 1]`

Histogram: bars of height 2, 0, 2, 1, 1.
Largest rectangle = 3 (height 1 × width 3, spanning columns 2-4).

---

**Row 2:** heights = `[3, 1, 3, 2, 2]`

Histogram: bars of height 3, 1, 3, 2, 2.

| i | currH | Stack pops and areas                          | maxArea |
|---|-------|-----------------------------------------------|---------|
| 0 | 3     | Push 0                                        | --      |
| 1 | 1     | pop 0: h=3, w=1, area=3                       | 3       |
| 2 | 3     | Push 2                                        | 3       |
| 3 | 2     | pop 2: h=3, w=3-1-1=1, area=3                 | 3       |
| 4 | 2     | Push 4                                        | 3       |
| 5 | 0     | pop 4: h=2, w=5-3-1=1, area=2                 | 3       |
|   |       | pop 3: h=2, w=5-1-1=3, area=**6**             | **6**   |
|   |       | pop 1: h=1, w=5, area=5                       | 6       |

Largest rectangle at row 2 = **6** (height 2 × width 3, columns 2-4).

---

**Row 3:** heights = `[4, 0, 0, 3, 0]`

Histogram: bars of height 4, 0, 0, 3, 0.
Largest rectangle = 4 (single bar of height 4, column 0).

---

**Global maximum across all rows: 6** ✓

The rectangle of height 2, width 3 at row 2 (columns 2-4)
corresponds to the 2×3 block of '1's in rows 1-2, columns 2-4.

---

**Trace for a simple grid `[["1","1"],["1","1"]]`:**

Row 0: heights = [1, 1] → histogram max = 2 (1×2)
Row 1: heights = [2, 2] → histogram max = 4 (2×2)

**Answer: 4** ✓ -- the entire 2×2 grid.

---

**Trace for `[["0"]]`:**

Row 0: heights = [0] → histogram max = 0.

**Answer: 0** ✓ -- no '1's at all.

---

## 🔍 Why This Reduction Works

Every rectangle of '1's in the grid has a **bottom row**.
When we process that bottom row, the heights array captures
exactly how tall the columns of '1's are above it.

The histogram solver finds the widest rectangle
at every possible height within those columns.

By processing every row as a potential bottom,
we guarantee that every possible rectangle is considered.

---

## 🔄 Connection to the Histogram Problem

| Maximal Rectangle (2D)            | Largest Rectangle in Histogram (1D) |
| --------------------------------- | ----------------------------------- |
| Binary grid of '0' and '1'       | Array of bar heights                |
| Build heights row by row          | Heights given directly              |
| Apply histogram solver per row    | Solve once                          |
| O(rows × cols) total              | O(cols) per call                    |

The 2D problem is literally N calls to the 1D problem.
Master the histogram, and the grid falls for free.

---

### 🧠 Memory of the Hidden Monument Law

-   **Reduce 2D to 1D:** each row is the base of a histogram
-   **Heights update:** `'1'` → `heights[j]++`, `'0'` → `heights[j] = 0`
-   **Apply Largest Rectangle in Histogram** on each row's heights
-   **Answer:** maximum across all rows
-   The histogram solver uses a **monotonic increasing stack** with sentinel
-   **Time:** O(rows × cols) -- O(cols) per row for heights + O(cols) for histogram
-   **Space:** O(cols) -- heights array + stack
-   **Edge cases:**
    -   Empty matrix → 0
    -   All '0's → 0
    -   All '1's → rows × cols
    -   Single row → reduces to a single histogram call
    -   Single column → longest consecutive '1's streak

Thus is remembered the saga of **Maximal Rectangle**,
where the Oracle looked at a grid of ones and zeros
and saw not a 2D puzzle but a series of histograms --
building the heights row by row,
resetting at every zero,
and solving each histogram with the monotonic stack --
until the largest hidden monument of ones
was unearthed from the grid. 🏗️📐✨
