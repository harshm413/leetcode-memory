## 🧮🏛️ _The Marble Ledger of the Grid: The Range Sum Query 2D Immutable Saga_

> \_"In the City of Stone Matrices,
> a vast marble floor lay tiled with numbers —
> rows upon rows, columns upon columns.
>
> Travelers came daily with the same question:
>
> **'What is the sum of all numbers
> inside this rectangular chamber?'**
>
> The naive servant would walk the chamber
> every single time — counting tile by tile.
> For a single query, that was O(m x n).
> For thousands of queries, the marble wore thin.
>
> But the Oracle knew a better way.
>
> She engraved a **Prefix Ledger** into stone —
> a sacred map where each tile remembered
> the total weight of everything above and to its left.
>
> With this ledger, any rectangle's sum
> could be revealed in O(1) time —
> using four lookups and the ancient law
> of **Inclusion-Exclusion**.
>
> **Building the ledger:**
> `pre[r][c] = matrix[r-1][c-1] + pre[r-1][c] + pre[r][c-1] - pre[r-1][c-1]`
>
> **Querying a rectangle (row1,col1) to (row2,col2):**
> `pre[row2+1][col2+1] - pre[row1][col2+1] - pre[row2+1][col1] + pre[row1][col1]`
>
> Start with the big rectangle.
> Subtract the strip above. Subtract the strip to the left.
> Add back the corner that was subtracted twice.
>
> One engraving. Infinite queries. O(1) each."\_

---

This is the saga of **Range Sum Query 2D - Immutable**.

You are given a 2D matrix `matrix` and many queries,
each asking for the sum of elements inside a rectangle
defined by its top-left corner `(row1, col1)`
and bottom-right corner `(row2, col2)`.

The matrix **never changes** after construction.

```
Matrix:
  3  0  1  4  2
  5  6  3  2  1
  1  2  0  1  5
  4  1  0  1  7
  1  0  3  0  5

sumRegion(2, 1, 4, 3) = 8
sumRegion(1, 1, 2, 2) = 11
sumRegion(1, 2, 2, 4) = 12
```

---

## 🧠 The Oracle's Core Insight — 2D Prefix Sum + Inclusion-Exclusion

In 1D, a prefix sum lets you compute any subarray sum in O(1):
`sum(i..j) = prefix[j+1] - prefix[i]`.

In 2D, the same idea extends to rectangles.

Define `pre[r][c]` as the sum of all elements
in the rectangle from `(0,0)` to `(r-1, c-1)`:

```
pre[r][c] = sum of matrix[0..r-1][0..c-1]
```

The prefix matrix is built one row and one column larger (padded with zeros)
so boundary checks are never needed.

**Building `pre[r][c]`:**

```
pre[r][c] = matrix[r-1][c-1]     <- the current cell
          + pre[r-1][c]           <- everything above
          + pre[r][c-1]           <- everything to the left
          - pre[r-1][c-1]         <- subtract the overlap (counted twice)
```

**Querying rectangle `(row1,col1)` to `(row2,col2)`:**

```
sum = pre[row2+1][col2+1]        <- big rectangle from (0,0) to (row2,col2)
    - pre[row1][col2+1]          <- strip above the rectangle
    - pre[row2+1][col1]          <- strip left of the rectangle
    + pre[row1][col1]            <- corner subtracted twice, add back once
```

This is the **Inclusion-Exclusion Principle** applied to 2D areas.

---

### 📜 The Scroll of the Stone Ledger

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏗️ The Oracle Engraves the Prefix Ledger

```cpp
class NumMatrix {
    vector<vector<int>> pre;

public:
    NumMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        pre = vector<vector<int>>(m + 1, vector<int>(n + 1, 0));
```

The Oracle laid down a frame of zeros —
`(m+1)` rows and `(n+1)` columns —
so that `pre[0][...]` and `pre[...][0]` were all zero.

This padding eliminated all boundary checks.
Row `r` and column `c` in `pre` corresponded to
row `r-1` and column `c-1` in the original matrix.

---

```cpp
        for (int r = 1; r <= m; r++) {
            for (int c = 1; c <= n; c++) {
                pre[r][c] = matrix[r - 1][c - 1]
                          + pre[r - 1][c]
                          + pre[r][c - 1]
                          - pre[r - 1][c - 1];
            }
        }
    }
```

Each cell was engraved with the sum of the entire rectangle
from `(0,0)` to `(r-1, c-1)`:

-   `matrix[r-1][c-1]` — the current cell's own value.
-   `pre[r-1][c]` — the sum of everything directly above.
-   `pre[r][c-1]` — the sum of everything directly to the left.
-   `- pre[r-1][c-1]` — the top-left corner was counted in both
    the above and the left, so subtract it once.

After this O(m x n) construction,
the ledger was complete. Every query would be O(1).

---

## 🪟 The Oracle's Instant Query — Inclusion-Exclusion

```cpp
    int sumRegion(int row1, int col1, int row2, int col2) {
        return pre[row2 + 1][col2 + 1]
             - pre[row1][col2 + 1]
             - pre[row2 + 1][col1]
             + pre[row1][col1];
    }
};
```

To reveal the sum inside any rectangle:

```
  +------------------+
  |  pre[row1][col1]  |  (add back - subtracted twice)
  +--------+---------+
  |        | TARGET  |
  +--------+---------+
```

-   `pre[row2+1][col2+1]` — the full rectangle from origin to bottom-right.
-   `- pre[row1][col2+1]` — remove the strip above the target.
-   `- pre[row2+1][col1]` — remove the strip left of the target.
-   `+ pre[row1][col1]` — the top-left corner was removed twice, add it back.

Four lookups. Three subtractions. One addition. O(1).

---

### 🎺 The Trial of the Marble City

```cpp
int main() {
    vector<vector<int>> matrix = {
        {3, 0, 1, 4, 2},
        {5, 6, 3, 2, 1},
        {1, 2, 0, 1, 5},
        {4, 1, 0, 1, 7},
        {1, 0, 3, 0, 5}
    };

    NumMatrix obj(matrix);
    cout << obj.sumRegion(2, 1, 4, 3) << endl; // expected: 8
    cout << obj.sumRegion(1, 1, 2, 2) << endl; // expected: 11
    cout << obj.sumRegion(1, 2, 2, 4) << endl; // expected: 12

    return 0;
}
```

---

**Prefix matrix `pre` for the given matrix:**

```
pre (6x6, 0-indexed):
  0   0   0   0   0   0
  0   3   3   4   8  10
  0   8  14  18  24  27
  0   9  17  21  28  36
  0  13  22  26  34  49
  0  14  23  30  38  58
```

**Query: sumRegion(2, 1, 4, 3):**

```
pre[5][4] - pre[2][4] - pre[5][1] + pre[2][1]
= 38 - 24 - 14 + 8
= 8
```

Manual check: rows 2-4, cols 1-3:
`2 + 0 + 1 + 1 + 0 + 1 + 0 + 3 + 0 = 8` ✓

**Query: sumRegion(1, 1, 2, 2):**

```
pre[3][3] - pre[1][3] - pre[3][1] + pre[1][1]
= 21 - 4 - 9 + 3
= 11
```

Manual check: rows 1-2, cols 1-2:
`6 + 3 + 2 + 0 = 11` ✓

**Query: sumRegion(1, 2, 2, 4):**

```
pre[3][5] - pre[1][5] - pre[3][2] + pre[1][2]
= 36 - 10 - 17 + 3
= 12
```

Manual check: rows 1-2, cols 2-4:
`3 + 2 + 1 + 0 + 1 + 5 = 12` ✓

---

### 🧠 Memory of the Marble Ledger Law

-   **2D Prefix Sum:** `pre[r][c]` = sum of all elements from `(0,0)` to `(r-1,c-1)`
-   **Padding:** `pre` is `(m+1) x (n+1)` with row 0 and col 0 all zeros — eliminates boundary checks
-   **Build formula:** `pre[r][c] = matrix[r-1][c-1] + pre[r-1][c] + pre[r][c-1] - pre[r-1][c-1]`
-   **Query formula:** `pre[r2+1][c2+1] - pre[r1][c2+1] - pre[r2+1][c1] + pre[r1][c1]`
-   This is the **Inclusion-Exclusion Principle** — subtract overlapping strips, add back the doubly-subtracted corner
-   **Preprocessing:** O(m x n) time and space
-   **Each query:** O(1) — four lookups and three arithmetic operations
-   **Immutable** — the matrix never changes after construction, so the prefix ledger stays valid forever

Thus is remembered the saga of **Range Sum Query 2D - Immutable**,
where the Oracle engraved the weight of every rectangle
into a prefix ledger of stone —
so that when travelers asked
for the sum inside any chamber,
she answered in a single breath
of four lookups and three operations —
never walking the marble again,
never counting tile by tile,
for the ledger remembered everything. 🧮🏛️✨
