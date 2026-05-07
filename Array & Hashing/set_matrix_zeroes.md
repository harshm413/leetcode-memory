## 🟰0️⃣ _The Zero Plague: The Set Matrix Zeroes Saga_

> \_"The Oracle was given an M × N matrix.
>
> She was commanded:
>
> **'If any cell is 0, set its ENTIRE row
> and ENTIRE column to 0. Do it in-place.'**
>
> The trap: if she sets zeros immediately,
> the new zeros trigger MORE zeros — a chain reaction.
> The original zeros and the new zeros become indistinguishable.
>
> She needed to MARK first, then SET.
>
> **Approach 1:** use two arrays — one for rows, one for columns.
> Mark which rows and columns contain a zero. Then set them.
> O(M+N) extra space.
>
> **Approach 2:** use the FIRST ROW and FIRST COLUMN
> of the matrix itself as the marker arrays.
> O(1) extra space. The elegant solution."\_

---

This is the saga of **Set Matrix Zeroes (LeetCode 73)**.

Given an M × N matrix:
-   If `matrix[i][j] == 0`, set entire row `i` and column `j` to 0.
-   Do it **in-place**.

```
Input:  [[1,1,1],[1,0,1],[1,1,1]]
Output: [[1,0,1],[0,0,0],[1,0,1]]

Input:  [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
Output: [[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```

---

## 🧠 The Problem — Why Not Set Immediately?

```
[[1, 1, 1],
 [1, 0, 1],
 [1, 1, 1]]
```

If we see `matrix[1][1] = 0` and immediately zero out row 1 and column 1:

```
[[1, 0, 1],
 [0, 0, 0],
 [1, 0, 1]]
```

That's correct here. But what if there were a 1 at `[0][1]` that we just
turned to 0? In the next scan, we'd think row 0 needs zeroing too.
Chain reaction. Wrong answer.

**Solution: separate the MARKING phase from the SETTING phase.**

---

## 💡 Approach 1 — O(M+N) Space (Quick Mention)

```cpp
void setZeroes(vector<vector<int>>& matrix) {
    int m = matrix.size(), n = matrix[0].size();
    vector<bool> zeroRow(m, false), zeroCol(n, false);

    for (int i = 0; i < m; i++)
        for (int j = 0; j < n; j++)
            if (matrix[i][j] == 0) {
                zeroRow[i] = true;
                zeroCol[j] = true;
            }

    for (int i = 0; i < m; i++)
        for (int j = 0; j < n; j++)
            if (zeroRow[i] || zeroCol[j])
                matrix[i][j] = 0;
}
```

**Pass 1:** scan the matrix. Mark which rows and columns have zeros.
**Pass 2:** set cells to 0 if their row OR column is marked.

O(M×N) time, O(M+N) space. Clean and simple.
But can we do O(1) space?

---

## 💡 Approach 2 — O(1) Space (The Optimal Solution)

### The Insight — Use the Matrix Itself as Markers

Instead of separate `zeroRow` and `zeroCol` arrays,
use the **first row** as `zeroCol` and the **first column** as `zeroRow`.

```
matrix[0][j] = 0  means "column j should be zeroed"
matrix[i][0] = 0  means "row i should be zeroed"
```

But there's a conflict: `matrix[0][0]` belongs to BOTH
the first row and the first column. It can't mark both.

**Fix:** use `matrix[0][0]` for the first ROW.
Use a separate variable `col0` for the first COLUMN.

---

### 📜 The Scroll of the Zero Plague

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

### Step 1 — Mark using first row and first column

```cpp
void setZeroes(vector<vector<int>>& matrix) {
    int m = matrix.size(), n = matrix[0].size();
    bool col0 = false;
```

`col0` tracks whether the first COLUMN should be zeroed.
`matrix[0][0]` will track whether the first ROW should be zeroed.

---

```cpp
    for (int i = 0; i < m; i++) {
        if (matrix[i][0] == 0) col0 = true;
```

If ANY cell in the first column is 0, the entire first column
must be zeroed later. `col0` remembers this.

We check this SEPARATELY because `matrix[i][0]` is being used
as a marker for row `i` — we can't let it also mark column 0.

---

```cpp
        for (int j = 1; j < n; j++) {
            if (matrix[i][j] == 0) {
                matrix[i][0] = 0;
                matrix[0][j] = 0;
            }
        }
    }
```

For every zero found in the matrix (excluding column 0):
-   `matrix[i][0] = 0` — mark row `i` for zeroing.
-   `matrix[0][j] = 0` — mark column `j` for zeroing.

The first row and first column now serve as marker arrays.

> _"The Oracle walks the matrix.
> Every zero she finds, she marks its row and column
> using the borders of the matrix itself.
> The borders become the scoreboard."_

Note: `j` starts at 1, not 0. Column 0 is handled by `col0`.

---

### Step 2 — Set zeros using the markers (inner cells)

```cpp
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    }
```

For every inner cell (not first row, not first column):
if its row marker OR column marker is 0 → set it to 0.

We skip the first row and first column here — they're markers.
Setting them now would corrupt the markers before we're done.

> _"The Oracle reads the scoreboard.
> Any cell whose row or column is marked — falls to zero.
> But the scoreboard itself is untouched. Not yet."_

---

### Step 3 — Handle the first row

```cpp
    if (matrix[0][0] == 0) {
        for (int j = 0; j < n; j++) {
            matrix[0][j] = 0;
        }
    }
```

If `matrix[0][0]` was marked (either originally 0 or marked during step 1),
zero out the entire first row.

---

### Step 4 — Handle the first column

```cpp
    if (col0) {
        for (int i = 0; i < m; i++) {
            matrix[i][0] = 0;
        }
    }
}
```

If `col0` is true, zero out the entire first column.

**Why handle first row and column LAST?**
Because they were used as markers. If we zeroed them in step 2,
we'd destroy the markers before all inner cells were processed.

> _"The scoreboard is cleared last.
> Only after every inner cell has been judged
> does the Oracle erase the borders themselves."_

---

### 🎺 The Trial of the Zero Plague

```cpp
int main() {
    vector<vector<int>> m1 = {{1,1,1},{1,0,1},{1,1,1}};
    setZeroes(m1);
    // expected: [[1,0,1],[0,0,0],[1,0,1]]

    vector<vector<int>> m2 = {{0,1,2,0},{3,4,5,2},{1,3,1,5}};
    setZeroes(m2);
    // expected: [[0,0,0,0],[0,4,5,0],[0,3,1,0]]

    return 0;
}
```

---

**Trace for matrix = [[1,1,1],[1,0,1],[1,1,1]]:**

**Step 1 — Mark:**
```
col0 = false (no zeros in column 0)
matrix[1][1] = 0 → mark matrix[1][0] = 0, matrix[0][1] = 0

After marking:
  [[1, 0, 1],
   [0, 0, 1],    ← matrix[1][0] marked
   [1, 1, 1]]
     ↑
  matrix[0][1] marked
```

**Step 2 — Set inner cells:**
```
(1,1): matrix[1][0]=0 → set 0. (1,2): matrix[1][0]=0 → set 0.
(2,1): matrix[0][1]=0 → set 0.

  [[1, 0, 1],
   [0, 0, 0],
   [1, 0, 1]]
```

**Step 3:** matrix[0][0] = 1, not 0. First row unchanged.
**Step 4:** col0 = false. First column unchanged.

**Result: [[1,0,1],[0,0,0],[1,0,1]]** ✓

---

**Trace for matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]:**

**Step 1 — Mark:**
```
i=0: matrix[0][0]=0 → col0=true.
     matrix[0][3]=0 → matrix[0][0]=0 (already), matrix[0][3]=0 (already).
i=1: no zeros.
i=2: no zeros.

After marking:
  col0 = true
  matrix[0][0] = 0 (marks row 0)
  matrix[0][3] = 0 (marks column 3)
```

**Step 2 — Set inner cells:**
```
Row 1: matrix[1][0]=3≠0. Check columns: matrix[0][3]=0 → matrix[1][3]=0.
Row 2: same → matrix[2][3]=0.
```

**Step 3:** matrix[0][0]=0 → zero entire first row: [0,0,0,0].
**Step 4:** col0=true → zero entire first column.

**Result: [[0,0,0,0],[0,4,5,0],[0,3,1,0]]** ✓

---

## 🔍 The Three Approaches Compared

| Approach | Time | Space | Complexity |
|----------|------|-------|------------|
| Brute force (copy matrix) | O(M×N) | O(M×N) | Simple |
| Two marker arrays | O(M×N) | O(M+N) | Clean |
| **First row/col as markers** | **O(M×N)** | **O(1)** | Trickiest |

The O(1) approach is optimal but requires careful ordering:
mark → set inner → set first row → set first column.

---

### 🧠 Memory of the Zero Plague Law

-   **Use first row as column markers, first column as row markers**
-   **`col0` variable** handles the first column separately (avoids conflict at `[0][0]`)
-   **Step 1:** scan matrix, mark borders. `j` starts at 1 (column 0 uses `col0`).
-   **Step 2:** set inner cells (skip first row/column — they're markers)
-   **Step 3:** if `matrix[0][0] == 0` → zero first row
-   **Step 4:** if `col0` → zero first column
-   **Order matters:** borders are cleared LAST to preserve markers
-   **Time:** O(M×N). **Space:** O(1).

Thus is remembered the saga of **Set Matrix Zeroes**,
where the Oracle used the matrix's own borders
as a scoreboard for the zero plague —
marking rows and columns for destruction,
then sweeping the inner cells,
and finally erasing the scoreboard itself —
all in-place, with nothing but one extra boolean. 🟰0️⃣✨
