## 🧩⚖️ _The Ninefold Trial: The Valid Sudoku Saga_

> \_"Within the Temple of Numbers lay a sacred board --
> nine rows, nine columns,
> and nine inner chambers carved in perfect 3x3 symmetry.
>
> Three ancient laws governed the temple:
>
> **Law One:** No digit may repeat within any row.
> **Law Two:** No digit may repeat within any column.
> **Law Three:** No digit may repeat within any 3x3 box.
>
> The Oracle was summoned not to solve the puzzle,
> but to **judge** it --
> to declare whether the digits already inscribed
> obeyed the three laws.
>
> Empty cells were marked with `'.'` --
> they carried no judgment, no violation.
> Only the filled digits needed inspection.
>
> The Oracle devised a single-pass ritual:
>
> **Walk every cell once.
> For each filled digit, check three sets --
> one for its row, one for its column, one for its box.
> If the digit already exists in any of the three --
> the law is broken. Return false.
> If not -- record it in all three sets and continue.**
>
> 9 row sets. 9 column sets. 9 box sets.
> One pass through 81 cells.
> The trial was swift and absolute."\_

---

This is the saga of **Valid Sudoku**.

You are given a 9x9 Sudoku board as a 2D array of characters.

Your task:

-   Determine if the board is **valid**.
-   Only the filled cells need to be validated according to the three rules.
-   The board does not need to be solvable -- only the current state must be valid.

```
A valid board:
  5 3 . | . 7 . | . . .
  6 . . | 1 9 5 | . . .
  . 9 8 | . . . | . 6 .
  ------+-------+------
  8 . . | . 6 . | . . 3
  4 . . | 8 . 3 | . . 1
  7 . . | . 2 . | . . 6
  ------+-------+------
  . 6 . | . . . | 2 8 .
  . . . | 4 1 9 | . . 5
  . . . | . 8 . | . 7 9
```

---

## 🧠 The Oracle's Core Insight -- Three Sets Per Group, One Pass

The board has 27 groups that must each contain no duplicate digits:
9 rows + 9 columns + 9 boxes.

For each filled cell at `(r, c)` with digit `ch`:

```
1. Check if ch already exists in rows[r]     -- row violation?
2. Check if ch already exists in cols[c]     -- column violation?
3. Check if ch already exists in boxes[box]  -- box violation?
```

If any check fails -- return `false`.
If all pass -- insert `ch` into all three sets and continue.

The key formula is the **box index**:

```
box = (r / 3) * 3 + (c / 3)
```

This maps every cell to one of 9 boxes (indexed 0 to 8):

```
Box layout:
  0 0 0 | 1 1 1 | 2 2 2
  0 0 0 | 1 1 1 | 2 2 2
  0 0 0 | 1 1 1 | 2 2 2
  ------+-------+------
  3 3 3 | 4 4 4 | 5 5 5
  3 3 3 | 4 4 4 | 5 5 5
  3 3 3 | 4 4 4 | 5 5 5
  ------+-------+------
  6 6 6 | 7 7 7 | 8 8 8
  6 6 6 | 7 7 7 | 8 8 8
  6 6 6 | 7 7 7 | 8 8 8
```

`r / 3` gives the box row (0, 1, or 2).
`c / 3` gives the box column (0, 1, or 2).
`(r/3) * 3 + (c/3)` flattens this into a single index 0-8.

---

### 📜 The Scroll of the Sacred Board

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## ⚖️ The Oracle's Threefold Judgment Ritual

```cpp
bool isValidSudoku(vector<vector<char>>& board) {
    vector<unordered_set<char>> rows(9), cols(9), boxes(9);
```

The Oracle prepared 27 sets:

-   `rows[0..8]` -- one set per row, tracking digits seen in that row.
-   `cols[0..8]` -- one set per column.
-   `boxes[0..8]` -- one set per 3x3 box.

All began empty -- no digits had been judged yet.

---

## 🧭 Walk the Entire Temple -- Cell by Cell

```cpp
    for (int r = 0; r < 9; r++) {
        for (int c = 0; c < 9; c++) {
            char ch = board[r][c];
            if (ch == '.') continue;
```

The Oracle walked every cell from top-left to bottom-right.

Empty cells (`'.'`) were skipped --
they carried no digit, no potential violation.

Only filled cells demanded judgment.

---

### 🔍 Check All Three Laws

```cpp
            int box = (r / 3) * 3 + (c / 3);
```

The box index was computed --
identifying which of the 9 sanctums this cell belonged to.

---

```cpp
            if (rows[r].count(ch) || cols[c].count(ch) || boxes[box].count(ch)) {
                return false;
            }
```

The Oracle checked all three laws in one condition:

**Has this digit already appeared in this row?**
**Has it appeared in this column?**
**Has it appeared in this box?**

If ANY of the three was true --
the law was broken. The board was invalid.
Return `false` immediately.

> _"A single violation is enough.
> The Oracle does not need to find all violations --
> just the first."_

---

### ✍️ Record the Digit in All Three Sets

```cpp
            rows[r].insert(ch);
            cols[c].insert(ch);
            boxes[box].insert(ch);
        }
    }
```

If the digit passed all three checks,
it was inscribed into its row set, column set, and box set.

Future cells in the same row, column, or box
would find it there if they tried to repeat it.

---

## 🕊️ The Final Verdict

```cpp
    return true;
}
```

If every filled cell passed the threefold judgment
without a single violation --
the board was valid.

---

### 🎺 The Trial of the Ninefold Board

```cpp
int main() {
    vector<vector<char>> board = {
        {'5','3','.','.','7','.','.','.','.'},
        {'6','.','.','1','9','5','.','.','.'},
        {'.','9','8','.','.','.','.','6','.'},
        {'8','.','.','.','6','.','.','.','3'},
        {'4','.','.','8','.','3','.','.','1'},
        {'7','.','.','.','2','.','.','.','6'},
        {'.','6','.','.','.','.','2','8','.'},
        {'.','.','.','4','1','9','.','.','5'},
        {'.','.','.','.','8','.','.','7','9'}
    };

    cout << (isValidSudoku(board) ? "true" : "false") << endl;
    // expected: true

    return 0;
}
```

---

**Partial trace for the first few filled cells:**

| Cell   | ch  | row | col | box = (r/3)*3+(c/3) | In row? | In col? | In box? | Action     |
| ------ | --- | --- | --- | ------------------- | ------- | ------- | ------- | ---------- |
| (0,0)  | '5' | 0   | 0   | 0                   | No      | No      | No      | Insert all |
| (0,1)  | '3' | 0   | 1   | 0                   | No      | No      | No      | Insert all |
| (0,4)  | '7' | 0   | 4   | 1                   | No      | No      | No      | Insert all |
| (1,0)  | '6' | 1   | 0   | 0                   | No      | No      | No      | Insert all |
| (1,3)  | '1' | 1   | 3   | 1                   | No      | No      | No      | Insert all |
| (1,4)  | '9' | 1   | 4   | 1                   | No      | No      | No      | Insert all |
| (1,5)  | '5' | 1   | 5   | 1                   | No      | No      | No      | Insert all |
| (2,1)  | '9' | 2   | 1   | 0                   | No      | No      | No      | Insert all |
| (2,2)  | '8' | 2   | 2   | 0                   | No      | No      | No      | Insert all |

No violations found at any cell. The Oracle continued through all 81 cells.

**Answer: true** ✓

---

**Example of an invalid board -- duplicate '8' in column 0:**

If we changed `board[0][0]` from `'5'` to `'8'`:

| Cell   | ch  | row | col | box | In row? | In col? | In box? | Action         |
| ------ | --- | --- | --- | --- | ------- | ------- | ------- | -------------- |
| (0,0)  | '8' | 0   | 0   | 0   | No      | No      | No      | Insert all     |
| ...    |     |     |     |     |         |         |         |                |
| (3,0)  | '8' | 3   | 0   | 3   | No      | **Yes** | No      | Return **false** |

Column 0 already had `'8'` from row 0. Violation detected.

---

## 🔍 Why `(r/3)*3 + (c/3)` Works

The 9x9 board is divided into nine 3x3 boxes.
Integer division `r/3` groups rows 0-2 into group 0, rows 3-5 into group 1, rows 6-8 into group 2.
Same for columns with `c/3`.

Multiplying the row group by 3 and adding the column group
gives a unique index 0-8 for each box:

```
(0,0)=0  (0,1)=1  (0,2)=2
(1,0)=3  (1,1)=4  (1,2)=5
(2,0)=6  (2,1)=7  (2,2)=8
```

This is the standard way to flatten a 2D grid index into 1D.

---

### 🧠 Memory of the Ninefold Law

-   **Three laws:** no duplicate digit in any row, column, or 3x3 box
-   **27 sets:** 9 for rows + 9 for columns + 9 for boxes
-   **Box index:** `(r / 3) * 3 + (c / 3)` -- maps each cell to its box (0-8)
-   **Single pass:** walk all 81 cells, skip `'.'`, check three sets, insert if clean
-   **Early exit:** return `false` the moment any violation is found
-   **Not solving** the puzzle -- only validating the current state
-   **Time:** O(81) = O(1) -- the board is always 9x9
-   **Space:** O(81) = O(1) -- at most 81 entries across all 27 sets

Thus is remembered the saga of **Valid Sudoku**,
where the Oracle walked the Temple of Ninefold Order
with 27 sets as her witnesses --
checking every filled digit against its row,
its column, and its sacred 3x3 box --
declaring the board valid only when
all three laws held in perfect harmony
across every cell of the temple. 🧩⚖️✨
