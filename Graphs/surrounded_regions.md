## 🏰⚔️ _The Siege of the Inner Lands: The Surrounded Regions Saga_

> \_"In the Grid Kingdom,
> two factions existed:
> `'X'` -- the conquerors.
> `'O'` -- the unconquered.
>
> The King commanded:
>
> **'Capture all `O` regions that are COMPLETELY surrounded by `X`.
> Flip them to `X`.
> But any `O` connected to the BORDER of the grid
> must NOT be captured -- it has an escape route.'**
>
> The Oracle studied the grid.
>
> The naive approach: for each `O` region,
> check if it touches the border.
> If not -- capture it. If yes -- leave it.
> Complex and error-prone.
>
> The Oracle flipped the problem:
>
> **Instead of finding which `O`s to CAPTURE,
> find which `O`s to PROTECT.**
>
> **Step 1 -- Mark the safe ones.**
> DFS from every `O` on the BORDER.
> Every `O` reachable from the border is SAFE --
> it cannot be captured.
> Mark these with a temporary symbol `'S'`.
>
> **Step 2 -- Capture the rest.**
> Walk the entire grid:
> -   `'O'` → flip to `'X'` (surrounded, captured).
> -   `'S'` → flip back to `'O'` (safe, restored).
> -   `'X'` → leave as is.
>
> Protect first. Then capture everything unprotected.
> Two passes. Clean and simple."\_

---

This is the saga of **Surrounded Regions**.

Given an `m × n` board of `'X'` and `'O'`:

-   Capture all `'O'` regions **completely surrounded** by `'X'`.
-   An `'O'` on the **border** (or connected to a border `'O'`) is NOT captured.
-   Flip captured `'O'`s to `'X'`. Leave the rest.

```
Input:
  X X X X
  X O O X
  X X O X
  X O X X

Output:
  X X X X
  X X X X
  X X X X
  X O X X
```

The inner `O`s at (1,1), (1,2), (2,2) are surrounded → captured.
The border `O` at (3,1) is NOT surrounded → stays.

---

## 🧠 The Oracle's Core Insight -- Protect the Border, Capture the Rest

**Why flip the problem?**

Finding surrounded regions directly is hard --
you'd need to check if an entire connected component
touches the border, which requires full traversal per component.

Finding SAFE regions is easy --
just DFS from every border `'O'`.
Everything reachable from the border is safe.
Everything else is surrounded.

```
Step 1: DFS from border 'O's → mark reachable as 'S' (safe)
Step 2: Walk grid:
  'O' → 'X'  (was not reached from border → surrounded → capture)
  'S' → 'O'  (was reached from border → safe → restore)
  'X' → 'X'  (unchanged)
```

```
Time:  O(M × N) -- DFS + one grid scan
Space: O(M × N) -- recursion stack worst case
```

---

### 📜 The Scroll of the Siege

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🛡️ The DFS -- Mark Safe Regions from the Border

```cpp
void dfs(vector<vector<char>>& board, int r, int c) {
```

The Oracle entered a border-connected `'O'`
and began marking it and all its connected `'O'`s as safe.

---

### 🛑 Base Case -- Out of Bounds

```cpp
    int rows = board.size();
    int cols = board[0].size();

    if (r < 0 || r >= rows || c < 0 || c >= cols) {
        return;
    }
```

Walked off the grid. Stop.

---

### 🛑 Base Case -- Not an Unvisited 'O'

```cpp
    if (board[r][c] != 'O') {
        return;
    }
```

This cell is either:
-   `'X'` -- a wall. Can't pass through.
-   `'S'` -- already marked safe. Don't revisit.

Only unvisited `'O'`s proceed.

---

### 🛡️ Mark This Cell as Safe

```cpp
    board[r][c] = 'S';
```

The `'O'` was temporarily changed to `'S'` --
meaning "this cell is SAFE, connected to the border."

This also served as the **visited marker** --
`'S'` is not `'O'`, so the DFS won't revisit it.

> _"The shield is placed.
> This cell is protected from capture.
> It will be restored to 'O' in the final pass."_

---

### 🧭 Spread to All Four Neighbors

```cpp
    dfs(board, r - 1, c);
    dfs(board, r + 1, c);
    dfs(board, r, c - 1);
    dfs(board, r, c + 1);
}
```

The protection spread in all four directions.
Every connected `'O'` reachable from this border cell
was marked safe.

> _"If you are connected to the border --
> even through a chain of 'O's --
> you are safe. The shield spreads to all your kin."_

---

## 🏰 The Main Ritual -- Protect, Then Capture

```cpp
void solve(vector<vector<char>>& board) {
    int rows = board.size();
    if (rows == 0) return;
    int cols = board[0].size();
```

The Oracle measured the battlefield.

---

## 🛡️ Step One -- DFS from Every Border 'O'

### Top and Bottom Edges

```cpp
    for (int c = 0; c < cols; c++) {
        if (board[0][c] == 'O') {
            dfs(board, 0, c);
        }
        if (board[rows - 1][c] == 'O') {
            dfs(board, rows - 1, c);
        }
    }
```

Walk the **top row** and **bottom row**.
Every `'O'` found on these edges triggered a DFS --
marking it and all connected `'O'`s as `'S'` (safe).

---

### Left and Right Edges

```cpp
    for (int r = 0; r < rows; r++) {
        if (board[r][0] == 'O') {
            dfs(board, r, 0);
        }
        if (board[r][cols - 1] == 'O') {
            dfs(board, r, cols - 1);
        }
    }
```

Walk the **left column** and **right column**.
Same logic -- DFS from every border `'O'`.

After this step, every `'O'` connected to the border
was marked `'S'`. Every remaining `'O'` was surrounded.

> _"The border has been walked.
> Every 'O' with an escape route has been shielded.
> What remains unshielded is doomed."_

---

## ⚔️ Step Two -- Walk the Grid, Capture and Restore

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
```

The Oracle walked every cell one final time.

---

### ⚔️ Unshielded 'O' → Capture

```cpp
            if (board[r][c] == 'O') {
                board[r][c] = 'X';
            }
```

This `'O'` was never reached from the border.
It was completely surrounded. **Captured.**

> _"You were not shielded.
> No path to the border existed.
> You are surrounded. You fall."_

---

### 🛡️ Shielded 'S' → Restore

```cpp
            else if (board[r][c] == 'S') {
                board[r][c] = 'O';
            }
        }
    }
}
```

This cell was marked safe during Step One.
Restore it back to `'O'`.

> _"You were shielded.
> The border was your lifeline.
> You are restored to your rightful state."_

`'X'` cells were untouched -- they were never in question.

---

### 🎺 The Trial of the Siege

```cpp
int main() {
    vector<vector<char>> board = {
        {'X','X','X','X'},
        {'X','O','O','X'},
        {'X','X','O','X'},
        {'X','O','X','X'}
    };

    solve(board);

    for (auto& row : board) {
        for (char c : row) cout << c << " ";
        cout << endl;
    }
    // expected:
    // X X X X
    // X X X X
    // X X X X
    // X O X X
    return 0;
}
```

---

**Full trace:**

```
Initial board:
  X X X X
  X O O X
  X X O X
  X O X X
```

**Step 1 -- DFS from border 'O's:**

Scan borders:
-   Top row: all `X`. No DFS.
-   Bottom row: (3,1) is `'O'` → DFS!
-   Left column: all `X`. No DFS.
-   Right column: all `X`. No DFS.

DFS from (3,1):
-   (3,1) = `'O'` → mark `'S'`.
-   Neighbors: (2,1)=`X`, (3,0)=`X`, (3,2)=`X`. All walls.
-   No further spreading.

```
After Step 1:
  X X X X
  X O O X
  X X O X
  X S X X
```

Only (3,1) was shielded. The inner `O`s at (1,1), (1,2), (2,2)
were NOT reached from any border -- they remain `'O'`.

**Step 2 -- Capture and Restore:**

| Cell  | Value | Action          |
| ----- | ----- | --------------- |
| (1,1) | `O`   | → `X` (capture) |
| (1,2) | `O`   | → `X` (capture) |
| (2,2) | `O`   | → `X` (capture) |
| (3,1) | `S`   | → `O` (restore) |

```
Final board:
  X X X X
  X X X X
  X X X X
  X O X X
```

**Answer matches expected output** ✓

---

**Trace for a grid where all 'O's touch the border:**

```
  O X O
  X O X
  O X O
```

Border `O`s: (0,0), (0,2), (2,0), (2,2).
DFS from each. (1,1) is `'O'` but surrounded by `X` --
not reachable from any border `O`.

After Step 1: corners are `'S'`, center is still `'O'`.
Step 2: center `'O'` → `'X'`. Corners `'S'` → `'O'`.

```
  O X O
  X X X
  O X O
```

---

**Trace for all 'O' grid:**

```
  O O O
  O O O
  O O O
```

Every cell is `'O'`. Every border cell triggers DFS.
DFS reaches every cell → all marked `'S'`.
Step 2: all `'S'` → `'O'`. Nothing captured.

**Board unchanged** ✓ -- no cell is surrounded.

---

**Trace for `[['X']]`:**

No `'O'`s at all. Nothing to do.

---

## 🔍 Why Not Check Each Region Directly?

**Direct approach:**
For each `'O'` region, BFS/DFS to check if it touches the border.
If yes → leave it. If no → capture it.

Problem: you'd need to track which cells belong to which region,
and for each region, check border connectivity.
Multiple passes, complex bookkeeping.

**Border-first approach:**
One DFS pass from borders marks all safe cells.
One grid scan captures the rest.
Two passes, no region tracking, no bookkeeping.

Much simpler. Same complexity.

---

## 🔄 The Three-State Trick

The temporary `'S'` state is the key to avoiding a separate visited array:

```
'O' → unvisited, potentially surrounded
'S' → visited, safe (connected to border)
'X' → wall (never changes during Step 1)
```

After Step 2, only `'X'` and `'O'` remain -- `'S'` is gone.
The board is clean. No extra memory needed.

---

### 🧠 Memory of the Siege Law

-   **Flip the problem:** find what to PROTECT, not what to capture
-   **Step 1 -- DFS from border 'O's:**
    -   Walk all 4 edges of the grid
    -   DFS from every border `'O'` → mark connected `'O'`s as `'S'`
-   **Step 2 -- Walk the grid:**
    -   `'O'` → `'X'` (surrounded, captured)
    -   `'S'` → `'O'` (safe, restored)
    -   `'X'` → unchanged
-   **Three-state trick:** `'O'`, `'S'`, `'X'` -- no visited array needed
-   **DFS guard clauses:** out of bounds OR not `'O'` → return
-   **Time:** O(M × N)
-   **Space:** O(M × N) -- recursion stack (or O(1) extra with BFS)
-   **Edge cases:**
    -   All `'O'` → nothing captured (all border-connected)
    -   All `'X'` → nothing to do
    -   Single row/column → all cells are border cells
    -   No border `'O'`s → all `'O'`s are surrounded

Thus is remembered the saga of **Surrounded Regions**,
where the Oracle did not hunt for surrounded lands
but instead shielded the free ones --
DFS-ing from every border `'O'`,
marking every connected cell as safe --
then walking the grid one final time,
capturing every unshielded `'O'`
and restoring every shielded `'S'` --
until only the truly surrounded had fallen
and the border-connected stood free. 🏰⚔️✨
