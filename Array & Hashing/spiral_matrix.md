## 🌀📜 _The Spiral Walk: The Spiral Matrix Saga_

> \_"The Oracle was given an M × N matrix.
>
> She was commanded:
>
> **'Walk the matrix in a spiral — clockwise, from the outside in.
> Return all elements in the order you visit them.'**
>
> ```
> 1  2  3
> 4  5  6   →  [1, 2, 3, 6, 9, 8, 7, 4, 5]
> 7  8  9
> ```
>
> The Oracle defined four boundaries:
>
> **`top`** — the topmost unvisited row.
> **`bottom`** — the bottommost unvisited row.
> **`left`** — the leftmost unvisited column.
> **`right`** — the rightmost unvisited column.
>
> She walked four directions in a cycle:
> RIGHT across the top → DOWN the right side →
> LEFT across the bottom → UP the left side.
>
> After each direction, she shrank the boundary inward.
> When the boundaries crossed — the spiral was complete."\_

---

This is the saga of **Spiral Matrix (LeetCode 54)**.

Given an M × N matrix:
-   Return all elements in **spiral order** (clockwise, outside-in).

```
Input:  [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]

Input:  [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]

Input:  [[1,2,3,4]]
Output: [1,2,3,4]  (single row)

Input:  [[1],[2],[3]]
Output: [1,2,3]    (single column)
```

---

## 🧠 The Four Boundaries

```
        left            right
         ↓                ↓
top →  [ 1   2   3   4 ]
       [ 5   6   7   8 ]
bottom→[ 9  10  11  12 ]
```

The boundaries define the "unvisited rectangle."
After each pass along one edge, that edge is consumed
and the boundary shrinks inward.

---

## 🧠 The Four Directions — One Cycle

Each cycle of the spiral has four legs:

**Leg 1 — RIGHT:** walk `top` row from `left` to `right`. Then `top++`.
**Leg 2 — DOWN:** walk `right` column from `top` to `bottom`. Then `right--`.
**Leg 3 — LEFT:** walk `bottom` row from `right` to `left`. Then `bottom--`.
**Leg 4 — UP:** walk `left` column from `bottom` to `top`. Then `left++`.

After one full cycle, the outer ring is consumed.
The boundaries have shrunk by 1 on all sides.
Repeat until boundaries cross.

---

### 📜 The Scroll of the Spiral Walk

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🌀 The Spiral Traversal

### Initialize boundaries

```cpp
vector<int> spiralOrder(vector<vector<int>>& matrix) {
    vector<int> result;
    int m = matrix.size(), n = matrix[0].size();
    int top = 0, bottom = m - 1;
    int left = 0, right = n - 1;
```

Four boundaries define the unvisited rectangle.
Initially: the entire matrix.

---

### Spiral loop

```cpp
    while (top <= bottom && left <= right) {
```

Continue while the boundaries haven't crossed.
When `top > bottom` or `left > right`, every cell has been visited.

---

### Leg 1 — RIGHT across the top row

```cpp
        for (int j = left; j <= right; j++) {
            result.push_back(matrix[top][j]);
        }
        top++;
```

Walk the `top` row from `left` to `right`.
Every element in this row is added to the result.
Then shrink: `top++` — this row is consumed.

> _"The Oracle walks east along the top edge.
> Every stone is collected. The top boundary drops down."_

---

### Leg 2 — DOWN the right column

```cpp
        for (int i = top; i <= bottom; i++) {
            result.push_back(matrix[i][right]);
        }
        right--;
```

Walk the `right` column from `top` to `bottom`.
Note: `top` was already incremented, so we don't revisit the corner.
Then shrink: `right--` — this column is consumed.

> _"The Oracle turns south along the right edge.
> The right boundary moves left."_

---

### Leg 3 — LEFT across the bottom row (if still valid)

```cpp
        if (top <= bottom) {
            for (int j = right; j >= left; j--) {
                result.push_back(matrix[bottom][j]);
            }
            bottom--;
        }
```

Walk the `bottom` row from `right` to `left`.

**Why the `if (top <= bottom)` check?**
After legs 1 and 2, `top` was incremented. If the matrix had only
one row remaining, `top > bottom` now. There's no bottom row to walk.
Without this check, we'd revisit the top row going backward.

> _"The Oracle turns west — but only if a bottom row still exists.
> A single-row matrix has no 'bottom' to walk back on."_

---

### Leg 4 — UP the left column (if still valid)

```cpp
        if (left <= right) {
            for (int i = bottom; i >= top; i--) {
                result.push_back(matrix[i][left]);
            }
            left++;
        }
    }
```

Walk the `left` column from `bottom` to `top`.

**Why the `if (left <= right)` check?**
After legs 2 and (possibly) 3, `right` was decremented.
If only one column remained, `left > right` now.
Without this check, we'd revisit the right column going upward.

> _"The Oracle turns north — but only if a left column still exists.
> A single-column matrix has no 'left side' to walk up."_

---

```cpp
    return result;
}
```

---

### 🎺 The Trial of the Spiral Walk

```cpp
int main() {
    vector<vector<int>> m1 = {{1,2,3},{4,5,6},{7,8,9}};
    auto r1 = spiralOrder(m1);
    for (int x : r1) cout << x << " ";
    cout << endl; // expected: 1 2 3 6 9 8 7 4 5

    vector<vector<int>> m2 = {{1,2,3,4},{5,6,7,8},{9,10,11,12}};
    auto r2 = spiralOrder(m2);
    for (int x : r2) cout << x << " ";
    cout << endl; // expected: 1 2 3 4 8 12 11 10 9 5 6 7

    vector<vector<int>> m3 = {{1,2,3,4}};
    auto r3 = spiralOrder(m3);
    for (int x : r3) cout << x << " ";
    cout << endl; // expected: 1 2 3 4

    return 0;
}
```

---

**Full trace for [[1,2,3],[4,5,6],[7,8,9]]:**

```
Initial: top=0, bottom=2, left=0, right=2

Cycle 1:
  RIGHT (top=0): [1, 2, 3]         → top=1
  DOWN  (right=2): [6, 9]          → right=1
  LEFT  (bottom=2): [8, 7]         → bottom=1
  UP    (left=0): [4]              → left=1

  Boundaries: top=1, bottom=1, left=1, right=1

Cycle 2:
  RIGHT (top=1): [5]               → top=2
  DOWN  (right=1): (top=2 > bottom=1, skip)
  (top > bottom → while loop ends)

Result: [1, 2, 3, 6, 9, 8, 7, 4, 5] ✓
```

---

**Trace for [[1,2,3,4],[5,6,7,8],[9,10,11,12]]:**

```
Initial: top=0, bottom=2, left=0, right=3

Cycle 1:
  RIGHT: [1, 2, 3, 4]              → top=1
  DOWN:  [8, 12]                    → right=2
  LEFT:  [11, 10, 9]               → bottom=1
  UP:    [5]                        → left=1

  Boundaries: top=1, bottom=1, left=1, right=2

Cycle 2:
  RIGHT: [6, 7]                    → top=2
  (top=2 > bottom=1 → loop ends)

Result: [1, 2, 3, 4, 8, 12, 11, 10, 9, 5, 6, 7] ✓
```

---

**Trace for [[1,2,3,4]] (single row):**

```
top=0, bottom=0, left=0, right=3

Cycle 1:
  RIGHT: [1, 2, 3, 4]              → top=1
  (top=1 > bottom=0 → loop ends)

Result: [1, 2, 3, 4] ✓
```

The `if (top <= bottom)` check prevents a phantom bottom-row walk.

---

**Trace for [[1],[2],[3]] (single column):**

```
top=0, bottom=2, left=0, right=0

Cycle 1:
  RIGHT: [1]                       → top=1
  DOWN:  [2, 3]                    → right=-1
  (left=0 > right=-1 → skip LEFT and UP)

Result: [1, 2, 3] ✓
```

---

## 🔍 Why the Boundary Checks on Legs 3 and 4?

Without them, single-row or single-column matrices break:

```
Single row [[1,2,3]]:
  RIGHT: [1,2,3], top=1.
  DOWN: nothing (top=1 > bottom=0).
  LEFT without check: would walk bottom=0 row AGAIN → [3,2,1]. WRONG.
  With check: top > bottom → skip. Correct.
```

Legs 1 and 2 don't need checks because the `while` loop condition
already guarantees `top <= bottom` and `left <= right` at the start.
But after legs 1 and 2 modify the boundaries, legs 3 and 4 must recheck.

---

### 🧠 Memory of the Spiral Walk Law

-   **Four boundaries:** `top`, `bottom`, `left`, `right`
-   **Four legs per cycle:** RIGHT → DOWN → LEFT → UP
-   **After each leg:** shrink the corresponding boundary inward
-   **Boundary checks on legs 3 and 4:** prevent revisiting in single-row/column cases
-   **Loop ends when:** `top > bottom` OR `left > right`
-   **Time:** O(M × N) — every cell visited once. **Space:** O(1) extra (result excluded).

Thus is remembered the saga of **Spiral Matrix**,
where the Oracle walked the matrix in an ever-tightening spiral —
east along the top, south down the right,
west along the bottom, north up the left —
shrinking the boundaries after each pass,
checking for collapse before doubling back —
until every cell had been visited
in one elegant clockwise journey. 🌀📜✨
