## 👑♟️ _The Royal Placement: The N-Queens Saga_

> \_"On an `n × n` chessboard,
> `n` queens must be placed
> such that NO two queens attack each other.
>
> A queen attacks along its row, column,
> and both diagonals.
>
> The Oracle was commanded:
>
> **'Find ALL distinct solutions to the N-Queens puzzle.'**
>
> The Oracle placed queens **row by row**.
>
> In each row, she tried every column.
> Before placing, she checked:
> -   Is this column already taken?
> -   Is this left diagonal already taken?
> -   Is this right diagonal already taken?
>
> If safe → place the queen, move to the next row.
> If not → try the next column.
> If no column works → backtrack to the previous row.
>
> Three sets tracked the attacks:
> one for columns, one for left diagonals, one for right diagonals.
> (Rows were handled implicitly -- one queen per row.)
>
> The constraint-based backtracking
> pruned the vast majority of the search space."\_

---

This is the saga of **N-Queens**.

Place `n` queens on an `n × n` board such that
no two queens attack each other.

Return all distinct solutions (each as a board configuration).

```
Input:  n = 4
Output: [[".Q..","...Q","Q...","..Q."],
         ["..Q.","Q...","...Q",".Q.."]]

Input:  n = 1
Output: [["Q"]]
```

---

## 🧠 The Oracle's Core Insight -- Row by Row + Three Attack Sets

**Why row by row?**
Each row must have exactly one queen (n queens, n rows).
So we place one queen per row, choosing its column.

**How to check if a position is safe?**
A queen at `(row, col)` attacks:
-   Its **column**: `col`.
-   Its **left diagonal** (↘): all cells where `row - col` is the same.
-   Its **right diagonal** (↙): all cells where `row + col` is the same.

Three sets track which columns/diagonals are under attack:
-   `cols` -- columns with a queen.
-   `leftDiag` -- left diagonals with a queen (indexed by `row - col`).
-   `rightDiag` -- right diagonals with a queen (indexed by `row + col`).

**Placement is safe if** the column AND both diagonals are free.

```
backtrack(row):
  If row == n: all queens placed → add board to result.

  For col = 0 to n-1:
    If col not in cols AND (row-col) not in leftDiag AND (row+col) not in rightDiag:
      Place queen. Mark column and diagonals.
      backtrack(row + 1).
      Remove queen. Unmark.
```

```
Time:  O(n!) -- heavily pruned
Space: O(n²) -- the board + O(n) for the sets
```

---

### 📜 The Scroll of the Royal Placement

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_set>
using namespace std;
```

---

## ♟️ The Backtracking Ritual

```cpp
void backtrack(int n, int row, vector<string>& board,
               unordered_set<int>& cols,
               unordered_set<int>& leftDiag,
               unordered_set<int>& rightDiag,
               vector<vector<string>>& result) {
```

The Oracle carried:
-   `row` -- the current row to place a queen in.
-   `board` -- the current board state.
-   `cols` -- columns under attack.
-   `leftDiag` -- left diagonals under attack (key = `row - col`).
-   `rightDiag` -- right diagonals under attack (key = `row + col`).
-   `result` -- all valid board configurations.

---

### 🎯 Base Case -- All Queens Placed

```cpp
    if (row == n) {
        result.push_back(board);
        return;
    }
```

All `n` rows have a queen. The board is valid. Record it.

> _"Every row has its queen.
> No two queens threaten each other.
> This arrangement is one of the solutions."_

---

### 🔁 Try Every Column in This Row

```cpp
    for (int col = 0; col < n; col++) {
```

For the current row, try placing the queen in each column.

---

### 🛡️ Check If This Position Is Safe

```cpp
        if (cols.count(col) || leftDiag.count(row - col)
            || rightDiag.count(row + col)) {
            continue;
        }
```

**Column check:** is another queen already in this column?
**Left diagonal check:** is another queen on the same `row - col` diagonal?
**Right diagonal check:** is another queen on the same `row + col` diagonal?

If ANY of these is true → this position is under attack. Skip.

> _"The column is guarded. Or the diagonal is watched.
> This square is not safe.
> The queen cannot stand here."_

---

### 👑 Place the Queen

```cpp
        board[row][col] = 'Q';
        cols.insert(col);
        leftDiag.insert(row - col);
        rightDiag.insert(row + col);
```

Place the queen on the board.
Mark its column and both diagonals as under attack.

---

### 🔁 Recurse -- Place the Next Row's Queen

```cpp
        backtrack(n, row + 1, board, cols, leftDiag, rightDiag, result);
```

Move to the next row. Find a safe column there.

---

### 🔄 Backtrack -- Remove the Queen

```cpp
        board[row][col] = '.';
        cols.erase(col);
        leftDiag.erase(row - col);
        rightDiag.erase(row + col);
    }
}
```

Remove the queen. Unmark its attacks.
Try the next column in this row.

> _"The queen retreats.
> Her column and diagonals are freed.
> Another position will be tried."_

---

## 🔮 The Main Function

```cpp
vector<vector<string>> solveNQueens(int n) {
    vector<vector<string>> result;
    vector<string> board(n, string(n, '.'));
    unordered_set<int> cols, leftDiag, rightDiag;
    backtrack(n, 0, board, cols, leftDiag, rightDiag, result);
    return result;
}
```

Start with an empty board (all `.`). Place queens from row 0.

---

### 🎺 The Trial of the Royal Placement

```cpp
int main() {
    auto r1 = solveNQueens(4);
    for (auto& board : r1) {
        for (auto& row : board) cout << row << endl;
        cout << "---" << endl;
    }
    // expected:
    // .Q..
    // ...Q
    // Q...
    // ..Q.
    // ---
    // ..Q.
    // Q...
    // ...Q
    // .Q..

    return 0;
}
```

---

**Full trace for n = 4:**

```
Row 0: try col 0, 1, 2, 3.

Row 0, col 0: place Q at (0,0). cols={0}, leftDiag={0}, rightDiag={0}.
  Row 1: col 0 taken. col 1: leftDiag has 1-1=0 → attacked. col 2: safe!
  Row 1, col 2: place Q at (1,2). cols={0,2}, leftDiag={0,-1}, rightDiag={0,3}.
    Row 2: col 0 taken. col 1: rightDiag has 2+1=3 → attacked. col 2 taken. col 3: leftDiag has 2-3=-1 → attacked.
    ALL COLUMNS BLOCKED. Backtrack.
  Row 1, col 3: place Q at (1,3). cols={0,3}, leftDiag={0,-2}, rightDiag={0,4}.
    Row 2: col 0 taken. col 1: safe!
    Row 2, col 1: place Q at (2,1). cols={0,1,3}, leftDiag={0,-2,1}, rightDiag={0,4,3}.
      Row 3: col 0 taken. col 1 taken. col 2: leftDiag has 3-2=1 → attacked. col 3 taken.
      ALL BLOCKED. Backtrack.
    Row 2, col 2: leftDiag has 2-2=0 → attacked. Skip.
    Row 2, col 3: taken. Skip.
    ALL BLOCKED. Backtrack.
  Backtrack row 0, col 0.

Row 0, col 1: place Q at (0,1). cols={1}, leftDiag={-1}, rightDiag={1}.
  Row 1: col 0: rightDiag 1+0=1 → attacked. col 1 taken. col 2: leftDiag 1-2=-1 → attacked. col 3: safe!
  Row 1, col 3: place Q at (1,3). cols={1,3}, leftDiag={-1,-2}, rightDiag={1,4}.
    Row 2: col 0: safe!
    Row 2, col 0: place Q at (2,0). cols={0,1,3}, leftDiag={-1,-2,2}, rightDiag={1,4,2}.
      Row 3: col 0 taken. col 1 taken. col 2: safe!
      Row 3, col 2: place Q at (3,2). Check: cols has 2? No. leftDiag has 3-2=1? No. rightDiag has 3+2=5? No. SAFE!
        Row 4: row == n → ADD BOARD! ✓
```

**Solution 1:**
```
.Q..
...Q
Q...
..Q.
```

Continuing the search finds **Solution 2:**
```
..Q.
Q...
...Q
.Q..
```

**Total: 2 solutions for n=4.** ✓

---

## 🔍 Why `row - col` for Left Diagonal and `row + col` for Right Diagonal

```
Left diagonal (↘):
  (0,0), (1,1), (2,2), (3,3) → row-col = 0, 0, 0, 0. Same!
  (0,1), (1,2), (2,3)        → row-col = -1, -1, -1. Same!

Right diagonal (↙):
  (0,3), (1,2), (2,1), (3,0) → row+col = 3, 3, 3, 3. Same!
  (0,2), (1,1), (2,0)        → row+col = 2, 2, 2. Same!
```

Cells on the same left diagonal share the same `row - col`.
Cells on the same right diagonal share the same `row + col`.

These are the unique identifiers for each diagonal.

---

## 🔍 Why We Don't Need a `rows` Set

We place exactly one queen per row (row by row).
Row conflicts are impossible by construction.
Only column and diagonal conflicts need checking.

---

## 🔄 Alternative -- Boolean Arrays Instead of Sets

```cpp
vector<bool> cols(n, false);
vector<bool> leftDiag(2 * n - 1, false);
vector<bool> rightDiag(2 * n - 1, false);
```

For left diagonal: index = `row - col + (n - 1)` (shift to make non-negative).
For right diagonal: index = `row + col`.

Boolean arrays are faster than hash sets (O(1) with no hashing overhead).
Both approaches are correct.

---

## 🔍 The N-Queens Pattern

| Aspect               | N-Queens                           |
| -------------------- | ---------------------------------- |
| Placement strategy   | Row by row (one queen per row)     |
| Constraint check     | Column + two diagonals             |
| Tracking             | Three sets (or boolean arrays)     |
| Backtracking trigger | No safe column in current row      |
| Result               | All valid board configurations     |

---

### 🧠 Memory of the Royal Placement Law

-   **Place one queen per row** (row by row, row 0 to n-1)
-   **Three attack trackers:** `cols`, `leftDiag` (row-col), `rightDiag` (row+col)
-   **Safe if:** column free AND both diagonals free
-   **Place → mark attacks → recurse next row → unmark → try next column**
-   **Base case:** `row == n` → all queens placed, add board
-   **No row set needed** — one queen per row by construction
-   **Diagonal keys:** `row - col` (left ↘), `row + col` (right ↙)
-   **Time:** O(n!) heavily pruned
-   **Space:** O(n²) for board + O(n) for sets
-   **Edge cases:**
    -   n = 1 → [["Q"]]
    -   n = 2 or 3 → no solution (return [])
    -   n = 4 → 2 solutions
    -   n = 8 → 92 solutions

Thus is remembered the saga of **N-Queens**,
where the Oracle placed queens row by row --
at each row trying every column,
checking three attack lines (column, left diagonal, right diagonal) --
placing only where safe,
advancing to the next row,
and backtracking when no column was free --
until every valid arrangement of n non-attacking queens
was discovered on the n×n board. 👑♟️✨
