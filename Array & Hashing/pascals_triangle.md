## 🔺🔢 _The Triangle of Sums: The Pascal's Triangle Saga_

> \_"The Oracle was given a number `numRows`.
>
> She was commanded:
>
> **'Build Pascal's Triangle with that many rows.'**
>
> ```
> Row 0:       1
> Row 1:      1 1
> Row 2:     1 2 1
> Row 3:    1 3 3 1
> Row 4:   1 4 6 4 1
> ```
>
> The rules were simple:
>
> **Every row starts and ends with 1.**
> **Every inner element = sum of the two elements above it.**
>
> `triangle[i][j] = triangle[i-1][j-1] + triangle[i-1][j]`
>
> The Oracle built it row by row,
> each row depending only on the previous one."\_

---

This is the saga of **Pascal's Triangle (LeetCode 118)**.

Given `numRows`:
-   Return the first `numRows` rows of Pascal's Triangle.

```
Input:  numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]

Input:  numRows = 1
Output: [[1]]
```

---

## 🧠 The Three Key Observations

**1. Row `i` has exactly `i + 1` elements.**
Row 0 has 1 element. Row 1 has 2. Row 4 has 5.

**2. First and last elements of every row are 1.**
The edges of the triangle are always 1.

**3. Every inner element = sum of two parents.**
`triangle[i][j] = triangle[i-1][j-1] + triangle[i-1][j]`

The element at position `j` in row `i` is the sum of
the element directly above-left (`j-1`) and directly above (`j`)
from the previous row.

> _"Each number is born from two parents above it.
> The edges have no two parents — they're always 1.
> The inner numbers carry the sum of their lineage."_

---

### 📜 The Scroll of the Triangle of Sums

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔺 Building the Triangle

### Initialize the result

```cpp
vector<vector<int>> generate(int numRows) {
    vector<vector<int>> triangle;
```

---

### Build row by row

```cpp
    for (int i = 0; i < numRows; i++) {
        vector<int> row(i + 1, 1);
```

Row `i` has `i + 1` elements, all initialized to 1.
The first and last are already correct (they stay as 1).
Only the inner elements need updating.

---

### Fill inner elements from the previous row

```cpp
        for (int j = 1; j < i; j++) {
            row[j] = triangle[i - 1][j - 1] + triangle[i - 1][j];
        }
```

`j` goes from 1 to `i - 1` — skipping the first and last (they're 1).

`triangle[i-1][j-1]` = the parent above-left.
`triangle[i-1][j]` = the parent directly above.
Their sum = the current element.

For row 0 and row 1, the inner loop doesn't execute
(no inner elements). They're just `[1]` and `[1, 1]`.

> _"Each inner element looks up at its two parents
> and takes their combined strength."_

---

### Add the row to the triangle

```cpp
        triangle.push_back(row);
    }
    return triangle;
}
```

---

### 🎺 The Trial of the Triangle of Sums

```cpp
int main() {
    auto t = generate(5);
    for (auto& row : t) {
        for (int x : row) cout << x << " ";
        cout << endl;
    }
    // expected:
    // 1
    // 1 1
    // 1 2 1
    // 1 3 3 1
    // 1 4 6 4 1

    return 0;
}
```

---

**Full trace for numRows = 5:**

| Row i | Size | Init | Inner computation | Result |
|-------|------|------|-------------------|--------|
| 0 | 1 | [1] | (no inner elements) | **[1]** |
| 1 | 2 | [1, 1] | (no inner: j=1 to 0, skip) | **[1, 1]** |
| 2 | 3 | [1, 1, 1] | j=1: row[1] = tri[1][0]+tri[1][1] = 1+1 = 2 | **[1, 2, 1]** |
| 3 | 4 | [1, 1, 1, 1] | j=1: 1+2=3, j=2: 2+1=3 | **[1, 3, 3, 1]** |
| 4 | 5 | [1, 1, 1, 1, 1] | j=1: 1+3=4, j=2: 3+3=6, j=3: 3+1=4 | **[1, 4, 6, 4, 1]** |

```
     1
    1 1
   1 2 1
  1 3 3 1
 1 4 6 4 1
```

---

## 🔍 The Three Variants of Pascal's Triangle Problems

**Variant 1 — Given row and column, find the element.**
`C(row, col) = row! / (col! × (row-col)!)`
Or compute iteratively: `C(n, r) = C(n, r-1) × (n-r+1) / r`

```cpp
long long nCr(int n, int r) {
    long long res = 1;
    for (int i = 0; i < r; i++) {
        res = res * (n - i) / (i + 1);
    }
    return res;
}
```

O(r) time. No need to build the whole triangle.

**Variant 2 — Print the entire nth row.**
Use the nCr trick: each element = previous × (row-col) / (col+1).

```cpp
vector<int> getRow(int rowIndex) {
    vector<int> row(1, 1);
    long long val = 1;
    for (int j = 1; j <= rowIndex; j++) {
        val = val * (rowIndex - j + 1) / j;
        row.push_back(val);
    }
    return row;
}
```

O(N) time, O(N) space. Each element derived from the previous.

**Variant 3 — Build the entire triangle (this problem).**
Row by row, each row from the previous. O(N²) time.

---

## 🔍 Pascal's Triangle and Combinations

Element at row `n`, column `r` = `C(n, r)` = "n choose r".

```
Row 4: 1  4  6  4  1
       C(4,0) C(4,1) C(4,2) C(4,3) C(4,4)
```

This is why Pascal's Triangle appears in combinatorics,
binomial expansion, and probability.

`(a + b)^n` = sum of `C(n, r) × a^(n-r) × b^r` for r = 0 to n.
The coefficients are exactly row `n` of Pascal's Triangle.

---

## 🔍 Properties Worth Knowing

**Symmetry:** `C(n, r) = C(n, n-r)`. Each row is a palindrome.
**Sum of row n:** `2^n`. (Row 4: 1+4+6+4+1 = 16 = 2⁴.)
**Each element:** sum of two parents above.
**Edges:** always 1. `C(n, 0) = C(n, n) = 1`.

---

### 🧠 Memory of the Triangle of Sums Law

-   **Row `i`** has `i + 1` elements, starts and ends with 1
-   **Inner elements:** `triangle[i][j] = triangle[i-1][j-1] + triangle[i-1][j]`
-   **Build row by row:** each row depends only on the previous
-   **Three variants:** single element (nCr), single row (iterative nCr), full triangle (this)
-   **Connection to combinations:** element at `(n, r)` = `C(n, r)`
-   **Time:** O(N²). **Space:** O(N²) for the triangle.

Thus is remembered the saga of **Pascal's Triangle**,
where the Oracle built the triangle row by row —
edges always 1, inner elements born from two parents above —
each number the sum of its lineage,
each row one element longer than the last,
until the full triangle stood revealed —
a monument to addition, symmetry, and combinations. 🔺🔢✨
