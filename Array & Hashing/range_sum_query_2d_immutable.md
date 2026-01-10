## 🧮🏛️ _The Marble Ledger of the Grid: The Range Sum Query 2D (Immutable) Saga_

> \*"In the City of Stone Matrices,
> a vast marble floor lay tiled with numbers —
> rows upon rows, columns upon columns.
>
> Travelers came daily with the same question:
>
> **‘What is the sum of numbers
> inside this rectangular chamber?’**
>
> The Oracle knew that walking the chamber
> every single time would wear the marble thin.
>
> Instead, she engraved a **prefix ledger** into stone —
> a sacred map where each tile remembered
> the total weight of everything above and to its left.
>
> Thus, any rectangle’s sum
> could be revealed instantly,
> without retracing a single step."\*

---

This is the saga of **Range Sum Query 2D – Immutable**.

You are given:

-   a 2D matrix `matrix`
-   many queries asking for the sum of elements
    inside a rectangle `(row1, col1)` to `(row2, col2)`

The matrix **never changes**.
So the Oracle prepares once,
and answers forever in constant time.

---

## 🗿 The Sacred Insight: 2D Prefix Sum

The Oracle builds a **prefix sum matrix** `pre`
where:

```
pre[r][c] =
sum of all elements
from (0,0) to (r-1,c-1)
```

With this, any rectangle can be computed using **inclusion–exclusion**.

---

### 📜 The Scroll of the Stone Ledger

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🧱 The Oracle’s Construction of the Marble Ledger

```cpp
class NumMatrix {
    vector<vector<int>> pre;
```

The ledger `pre` would be **one row and one column larger**,
so boundaries never caused trouble.

---

### 🏗️ Engraving the Prefix Sums

```cpp
public:
    NumMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        pre = vector<vector<int>>(m + 1, vector<int>(n + 1, 0));
```

The Oracle laid down an empty frame of zeros,
guarding the edges of the grid.

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

Each cell was engraved with:

-   its own value
-   plus everything above
-   plus everything to the left
-   minus what was counted twice

The marble remembered **all history**.

---

## 🪟 The Oracle’s Instant Chamber Query

```cpp
    int sumRegion(int row1, int col1, int row2, int col2) {
        return pre[row2 + 1][col2 + 1]
             - pre[row1][col2 + 1]
             - pre[row2 + 1][col1]
             + pre[row1][col1];
    }
};
```

To reveal the sum inside a rectangle:

-   Start with the large prefix
-   Subtract the areas above and left
-   Add back the overlapped corner

No walking.
No recalculation.
Only pure arithmetic truth.

---

## 🎺 The Trial of the Marble City

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
    return 0;
}
```

The Oracle answered instantly —
as if the marble itself whispered the truth.

---

## 🧠 Memory of the Marble Ledger

-   Precompute a **2D prefix sum**
-   Extra row and column simplify boundaries
-   Rectangle sum via inclusion–exclusion
-   Query time: **O(1)**
-   Preprocessing time: **O(m × n)**
-   Space: **O(m × n)**

Thus is remembered the saga of
**Range Sum Query 2D – Immutable**,
where the Oracle engraves history into stone,
and every rectangular truth
is revealed in a single breath of arithmetic. 🧮🏛️✨
