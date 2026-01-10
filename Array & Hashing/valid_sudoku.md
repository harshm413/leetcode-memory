## 🧩⚖️ _The Ninefold Trial: The Valid Sudoku Saga_

> \*"Within the Temple of Numbers lay a sacred board —
> nine rows, nine columns,
> and nine inner chambers carved in perfect symmetry.
>
> Each chamber demanded a single law:
> **no number may repeat**
> within its row,
> within its column,
> or within its 3×3 sanctum.
>
> The Oracle was summoned not to solve the puzzle,
> but to **judge** it —
> to declare whether the arrangement already inscribed
> obeyed the ancient rules.
>
> And so began the Trial of Validity."\*

---

The Oracle received a **partially filled Sudoku board**.
Empty cells were marked with `'.'`,
and only the filled digits needed judgment.

Her task was simple in words,
but strict in execution:

> **Verify that no rule is broken.**

She did **not** need to complete the board —
only ensure that the present numbers lived in harmony.

Thus began the saga of **Valid Sudoku**.

---

### 📜 The Scroll of the Sacred Board

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

The tools of judgment were prepared.

---

## ⚖️ The Oracle’s Threefold Judgment Ritual

The Oracle enforced **three independent laws**:

1. No duplicates in any **row**
2. No duplicates in any **column**
3. No duplicates in any **3×3 box**

She used **sets** to remember what had already appeared.

---

### 🧱 Law 1 — Judgment of Rows

```cpp
bool isValidSudoku(vector<vector<char>>& board) {
    vector<unordered_set<char>> rows(9), cols(9), boxes(9);
```

She prepared:

-   9 sets for rows
-   9 sets for columns
-   9 sets for boxes

Each would remember numbers already seen.

---

### 🧭 Walk the Entire Temple

```cpp
    for (int r = 0; r < 9; r++) {
        for (int c = 0; c < 9; c++) {
            char ch = board[r][c];
            if (ch == '.') continue;
```

The Oracle walked cell by cell.
Empty chambers were ignored —
they carried no judgment yet.

---

### ⚠️ Law 1 — Row Violation

```cpp
            if (rows[r].count(ch)) return false;
            rows[r].insert(ch);
```

If a number already existed in this row,
the law was broken.

---

### ⚠️ Law 2 — Column Violation

```cpp
            if (cols[c].count(ch)) return false;
            cols[c].insert(ch);
```

If a number repeated in a column,
judgment failed.

---

### ⚠️ Law 3 — Box Violation

_Identify which 3×3 sanctum this cell belongs to_

```cpp
            int box = (r / 3) * 3 + (c / 3);
            if (boxes[box].count(ch)) return false;
            boxes[box].insert(ch);
        }
    }
```

Each 3×3 box was indexed from 0 to 8.
If a number appeared twice inside the same sanctum,
the puzzle was invalid.

---

### 🕊️ Final Verdict

```cpp
    return true;
}
```

If no law was broken,
the board stood valid before the Oracle.

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

    cout << isValidSudoku(board) << endl; // expected: true
    return 0;
}
```

The Oracle examined every row,
every column,
every sacred box —
and found no violation.

The board passed the trial.

---

### 🧠 Memory of the Ninefold Law

-   Ignore empty cells (`.`)
-   Use sets to track seen digits
-   Check three conditions for every filled cell:

    -   Row
    -   Column
    -   3×3 Box

-   Box index formula:
    `(row / 3) * 3 + (col / 3)`
-   **Time:** O(81) → constant
-   **Space:** O(81)

Thus is remembered the saga of **Valid Sudoku**,
where the Oracle walks the Temple of Ninefold Order,
judging not solutions but **harmony**,
and declaring whether the numbers
live together in lawful balance. 🧩✨
