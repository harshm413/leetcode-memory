## 🔄🖼️ _The Spinning Grid: The Rotate Image Saga_

> \_"The Oracle was given an N × N matrix.
>
> She was commanded:
>
> **'Rotate the matrix 90 degrees CLOCKWISE. In-place.'**
>
> ```
> 1 2 3      7 4 1
> 4 5 6  →   8 5 2
> 7 8 9      9 6 3
> ```
>
> The Oracle discovered the same trick as Rotate Array:
>
> **Transpose + Reverse = 90° clockwise rotation.**
>
> Step 1: Transpose the matrix (swap rows and columns).
> Step 2: Reverse each row.
>
> Two simple operations. No extra matrix. In-place."\_

---

This is the saga of **Rotate Image (LeetCode 48)**.

Given an N × N matrix:
-   Rotate it **90 degrees clockwise**, in-place.

```
Input:  [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]

Input:  [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

---

## 💡 Approach 1 — Extra Space (Quick Mention)

```cpp
void rotate(vector<vector<int>>& matrix) {
    int n = matrix.size();
    vector<vector<int>> temp(n, vector<int>(n));
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            temp[j][n - 1 - i] = matrix[i][j];
    matrix = temp;
}
```

Each element at `(i, j)` moves to `(j, n-1-i)` after 90° clockwise.
O(N²) time, O(N²) space. Works but not in-place.

---

## 💡 Approach 2 — Transpose + Reverse (Optimal)

### 🧠 Why Transpose + Reverse = 90° Clockwise?

Let's trace what happens to element at position `(i, j)`:

**After transpose:** `(i, j)` → `(j, i)`. Rows become columns.
**After reversing each row:** `(j, i)` → `(j, n-1-i)`.

Combined: `(i, j)` → `(j, n-1-i)`. That's exactly 90° clockwise.

Visual proof with 3×3:

```
Original:       Transpose:       Reverse rows:
1  2  3         1  4  7          7  4  1
4  5  6    →    2  5  8    →     8  5  2
7  8  9         3  6  9          9  6  3
```

**Transpose** flips across the main diagonal (top-left to bottom-right).
**Reverse rows** mirrors each row left-to-right.
Together = 90° clockwise rotation. ✓

---

### 📜 The Scroll of the Spinning Grid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

### Step 1 — Transpose the matrix

```cpp
void rotate(vector<vector<int>>& matrix) {
    int n = matrix.size();

    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            swap(matrix[i][j], matrix[j][i]);
        }
    }
```

Swap `matrix[i][j]` with `matrix[j][i]` for all pairs above the diagonal.

Why `j = i + 1` and not `j = 0`? Because:
-   `j == i` is the diagonal — swapping with itself does nothing.
-   `j < i` would swap pairs we already swapped — undoing the work.

We only process the UPPER TRIANGLE. Each swap handles both sides.

> _"The Oracle folds the matrix along its diagonal.
> Every element above the diagonal swaps with its mirror below.
> Rows become columns. Columns become rows."_

---

### Step 2 — Reverse each row

```cpp
    for (int i = 0; i < n; i++) {
        int left = 0, right = n - 1;
        while (left < right) {
            swap(matrix[i][left], matrix[i][right]);
            left++;
            right--;
        }
    }
}
```

Standard two-pointer reversal on each row.
Left and right walk inward, swapping at each step.

> _"Each row is flipped like a mirror.
> The leftmost element swaps with the rightmost.
> They walk inward until they meet."_

---

### 🎺 The Trial of the Spinning Grid

```cpp
int main() {
    vector<vector<int>> m1 = {{1,2,3},{4,5,6},{7,8,9}};
    rotate(m1);
    // expected: [[7,4,1],[8,5,2],[9,6,3]]

    vector<vector<int>> m2 = {{5,1,9,11},{2,4,8,10},{13,3,6,7},{15,14,12,16}};
    rotate(m2);
    // expected: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]

    return 0;
}
```

---

**Full trace for [[1,2,3],[4,5,6],[7,8,9]]:**

```
Original:
  1  2  3
  4  5  6
  7  8  9

Step 1 — Transpose (swap across diagonal):
  swap(0,1)↔(1,0): 2↔4    swap(0,2)↔(2,0): 3↔7    swap(1,2)↔(2,1): 6↔8

  1  4  7
  2  5  8
  3  6  9

Step 2 — Reverse each row:
  Row 0: [1,4,7] → [7,4,1]
  Row 1: [2,5,8] → [8,5,2]
  Row 2: [3,6,9] → [9,6,3]

Result:
  7  4  1
  8  5  2
  9  6  3  ✓
```

---

**Trace for 4×4 matrix [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]:**

```
Original:
   5   1   9  11
   2   4   8  10
  13   3   6   7
  15  14  12  16

Step 1 — Transpose:
   5   2  13  15
   1   4   3  14
   9   8   6  12
  11  10   7  16

Step 2 — Reverse each row:
  [15, 13,  2,  5]
  [14,  3,  4,  1]
  [12,  6,  8,  9]
  [16,  7, 10, 11]  ✓
```

---

## 🔍 The Rotation Family — Different Combinations

| Rotation | Steps |
|----------|-------|
| **90° clockwise** | **Transpose → Reverse rows** |
| 90° counter-clockwise | Transpose → Reverse columns |
| 180° | Reverse rows → Reverse columns (or rotate 90° twice) |
| Horizontal flip | Reverse each row |
| Vertical flip | Reverse each column |
| Transpose | Swap `(i,j)` with `(j,i)` |

The transpose + reverse trick handles all rotation variants.
Just change WHICH axis you reverse.

---

## 🔍 Why Not Rotate Layer by Layer?

The "onion peel" approach rotates the outer ring, then the next ring, etc.
It works but is harder to code — four-way swaps, boundary tracking.

Transpose + reverse is:
-   Easier to code (two nested loops).
-   Easier to remember (two words: transpose, reverse).
-   Same O(N²) time, O(1) space.

---

## 🔍 Where Each Element Lands

```
90° CW:    (i, j) → (j, n-1-i)
90° CCW:   (i, j) → (n-1-j, i)
180°:      (i, j) → (n-1-i, n-1-j)
Transpose: (i, j) → (j, i)
```

For 90° CW: row becomes column, column becomes reversed row.
That's transpose `(j, i)` then reverse within row `(j, n-1-i)`.

---

### 🧠 Memory of the Spinning Grid Law

-   **90° clockwise = Transpose + Reverse each row**
-   **Transpose:** swap `matrix[i][j]` with `matrix[j][i]`, only upper triangle (`j > i`)
-   **Reverse rows:** two-pointer swap on each row
-   **Why it works:** transpose maps `(i,j)→(j,i)`, reverse maps `(j,i)→(j,n-1-i)` = 90° CW
-   **90° CCW:** transpose + reverse each COLUMN (or reverse rows then transpose)
-   **Time:** O(N²). **Space:** O(1).

Thus is remembered the saga of **Rotate Image**,
where the Oracle performed two elegant operations —
first folding the matrix along its diagonal,
then flipping each row like a mirror —
and the grid spun 90 degrees clockwise,
in-place, with nothing but swaps. 🔄🖼️✨
