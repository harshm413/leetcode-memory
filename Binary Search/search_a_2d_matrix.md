## 🗺️🔎 _The Scroll of Flattened Realms: The Search a 2D Matrix Saga_

> \_"In the Library of Ordered Realms,
> rows of numbers were arranged with sacred discipline.
>
> Each row was sorted from left to right.
> The first number of every row
> was greater than the last number of the previous row.
>
> The Oracle was commanded:
>
> **'Search for the target in this 2D matrix.
> Return true if it exists, false otherwise.'**
>
> The naive approach would scan every cell -- O(m * n).
> A smarter approach might binary search each row -- O(m + log n).
>
> But the Oracle saw deeper.
>
> She realized that this matrix was not truly 2D.
> Because each row's first element was greater than
> the previous row's last element,
> the entire matrix was a **sorted 1D array in disguise** --
> merely wrapped across multiple rows.
>
> **Flatten the illusion.
> Treat the m x n matrix as a sorted array of length m * n.
> Map any 1D index to 2D coordinates:
> row = index / n, col = index % n.
> Then run a single binary search on [0, m*n - 1].**
>
> One binary search. O(log(m * n)) time.
> The grid was just a sorted line wearing a costume."\_

---

This is the saga of **Search a 2D Matrix**.

You are given:

-   An `m x n` integer matrix where:
    -   Each row is sorted in ascending order.
    -   The first element of each row is greater than the last element of the previous row.
-   An integer `target`.

Return `true` if `target` exists in the matrix, `false` otherwise.

```
Matrix:
  1   3   5   7
  10  11  16  20
  23  30  34  60

searchMatrix(target = 3)  --> true
searchMatrix(target = 13) --> false
```

---

## 🧠 The Oracle's Core Insight -- The Matrix Is a Sorted Array

Because each row starts where the previous row ended,
the matrix is one continuous sorted sequence:

```
[1, 3, 5, 7, 10, 11, 16, 20, 23, 30, 34, 60]
 ^-- row 0 --^  ^--- row 1 ---^  ^--- row 2 ---^
```

A 1D index `idx` in this flattened array maps to 2D coordinates:

```
row = idx / n     (which row)
col = idx % n     (which column within that row)
```

Where `n` is the number of columns.

This means we can run a standard binary search
on the range `[0, m*n - 1]` --
converting each `mid` index to `(row, col)` to read the value.

```
Time:  O(log(m * n)) -- single binary search over all elements
Space: O(1) -- only a few variables
```

---

### 📜 The Scroll of the Hidden Grid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Flattened Binary Search Ritual

```cpp
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    int m = matrix.size();
    int n = matrix[0].size();
    int left = 0;
    int right = m * n - 1;
```

The Oracle treated the matrix as a sorted array of length `m * n`.

Two sentinels were placed:
-   `left = 0` -- the first cell (top-left corner).
-   `right = m * n - 1` -- the last cell (bottom-right corner).

---

## 🍟 Halve Until Found or Exhausted

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle tested the middle of the current range.

---

### 🧭 Convert 1D Index to 2D Coordinates

```cpp
        int row = mid / n;
        int col = mid % n;
        int value = matrix[row][col];
```

The 1D index `mid` was translated back to grid coordinates:

-   `mid / n` gave the row (how many full rows fit before this index).
-   `mid % n` gave the column (the remainder within the current row).

The actual value at that position was read from the matrix.

> _"Index 5 in a matrix with 4 columns:
> row = 5 / 4 = 1, col = 5 % 4 = 1.
> That's matrix[1][1] = 11."_

---

### ✅ Target Found

```cpp
        if (value == target) {
            return true;
        }
```

The value at `mid` matched the target. Return `true`.

---

### ➡️ Target Is Larger -- Search Right

```cpp
        else if (value < target) {
            left = mid + 1;
        }
```

The target was greater than the middle value.
Eliminate the left half.

---

### ⬅️ Target Is Smaller -- Search Left

```cpp
        else {
            right = mid - 1;
        }
    }
```

The target was smaller than the middle value.
Eliminate the right half.

---

## ❌ Target Not Found

```cpp
    return false;
}
```

The sentinels crossed. The search space was empty.
The target did not exist in the matrix.

---

### 🎺 The Trial of the Hidden Scroll

```cpp
int main() {
    vector<vector<int>> matrix = {
        {1,  3,  5,  7},
        {10, 11, 16, 20},
        {23, 30, 34, 60}
    };

    cout << (searchMatrix(matrix, 3) ? "true" : "false") << endl;
    // expected: true

    cout << (searchMatrix(matrix, 13) ? "true" : "false") << endl;
    // expected: false

    return 0;
}
```

---

**Full trace for target = 3 (m=3, n=4, total=12):**

| Step | left | right | mid | row=mid/4 | col=mid%4 | value | Compare  | Action      |
| ---- | ---- | ----- | --- | --------- | --------- | ----- | -------- | ----------- |
| 1    | 0    | 11    | 5   | 1         | 1         | 11    | 11 > 3   | right = 4   |
| 2    | 0    | 4     | 2   | 0         | 2         | 5     | 5 > 3    | right = 1   |
| 3    | 0    | 1     | 0   | 0         | 0         | 1     | 1 < 3    | left = 1    |
| 4    | 1    | 1     | 1   | 0         | 1         | 3     | 3 == 3   | Return **true** |

**Answer: true** ✓

Four steps to find `3` in a 12-element matrix.
log2(12) ~ 3.6, so 4 steps is expected.

---

**Full trace for target = 13 (m=3, n=4, total=12):**

| Step | left | right | mid | row | col | value | Compare   | Action      |
| ---- | ---- | ----- | --- | --- | --- | ----- | --------- | ----------- |
| 1    | 0    | 11    | 5   | 1   | 1   | 11    | 11 < 13   | left = 6    |
| 2    | 6    | 11    | 8   | 2   | 0   | 23    | 23 > 13   | right = 7   |
| 3    | 6    | 7     | 6   | 1   | 2   | 16    | 16 > 13   | right = 5   |
| 4    | 6    | 5     | --  | --  | --  | --    | left>right| Exit loop   |

**Answer: false** ✓

The sentinels crossed. `13` does not exist in the matrix.
It would fall between `11` (index 5) and `16` (index 6) -- a gap.

---

## 🔍 The Index Mapping Visualized

For the 3x4 matrix:

```
1D index:  0   1   2   3   4   5   6   7   8   9  10  11
Value:     1   3   5   7  10  11  16  20  23  30  34  60
2D coord: 0,0 0,1 0,2 0,3 1,0 1,1 1,2 1,3 2,0 2,1 2,2 2,3
```

The mapping `row = idx/n, col = idx%n` perfectly reconstructs
the 2D position from any 1D index.

This works ONLY because the matrix has the special property
that each row's first element > previous row's last element.
Without this property, the matrix would not be a single sorted sequence.

---

### 🧠 Memory of the Flattened Realm Law

-   **Key insight:** the matrix is a sorted 1D array wrapped across rows
-   **1D to 2D mapping:** `row = mid / n`, `col = mid % n`
-   **Search range:** `[0, m * n - 1]`
-   **Standard binary search:** compare `matrix[row][col]` with target
    -   Equal --> return `true`
    -   Less --> `left = mid + 1`
    -   Greater --> `right = mid - 1`
-   **Not found:** loop exits when `left > right` --> return `false`
-   This mapping only works when each row starts greater than the previous row ends
-   **Time:** O(log(m * n)) -- equivalent to O(log m + log n)
-   **Space:** O(1) -- only a few variables

Thus is remembered the saga of **Search a 2D Matrix**,
where the Oracle looked beyond the illusion of rows and columns --
seeing the matrix for what it truly was:
a single sorted line wearing a 2D costume --
and with one binary search across the flattened realm,
found the target or proved its absence
in logarithmic time. 🗺️🔎✨
