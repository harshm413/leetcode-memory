## 🔤🗺️ _The Hunt for the Hidden Word: The Word Search Saga_

> \_"In the Grid of Letters,
> every cell held a single character.
>
> The Oracle was given a word.
>
> She was commanded:
>
> **'Does this word exist in the grid?
> The word can be constructed from letters
> of sequentially adjacent cells (horizontally or vertically).
> The same cell may NOT be used more than once.'**
>
> The Oracle recognized this as **grid backtracking**:
>
> **For each cell that matches the first letter of the word,
> start a DFS. At each step:
> -   Does the current cell match the current character?
> -   If yes → mark visited, try all 4 neighbors for the next character.
> -   If no → backtrack.
> -   If the entire word is matched → return true.**
>
> The key difference from flood fill:
> we BACKTRACK (unmark visited) after exploring,
> because the same cell might be needed
> for a different path to the same word."\_

---

This is the saga of **Word Search**.

Given an `m × n` grid of characters and a string `word`:

-   Return `true` if `word` exists in the grid.
-   The word must be formed from adjacent cells (4 directions).
-   The same cell cannot be used twice in one path.

```
Input:  board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
Output: true

Input:  board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
Output: true

Input:  board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
Output: false
```

---

## 🧠 The Oracle's Core Insight -- DFS with Backtracking on Grid

For each cell `(r, c)` that matches `word[0]`:
start a DFS trying to match the rest of the word character by character.

```
dfs(r, c, index):
  If index == word.size(): return true. (entire word matched)
  If out of bounds OR cell != word[index] OR cell is visited: return false.

  Mark cell as visited.
  Try all 4 neighbors with index + 1.
  If any returns true → return true.
  Unmark cell (backtrack).
  Return false.
```

**Why backtrack (unmark)?**

Unlike Number of Islands where we permanently sink cells,
here the same cell might be part of a DIFFERENT valid path.
If we don't unmark, we'd block future attempts.

```
Time:  O(m × n × 4^L) where L = word length (worst case)
Space: O(L) -- recursion depth = word length
```

---

### 📜 The Scroll of the Hidden Word

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🔤 The DFS Backtracking Function

```cpp
bool dfs(vector<vector<char>>& board, string& word,
         int r, int c, int index) {
    int rows = board.size();
    int cols = board[0].size();
```

---

### 🎯 Base Case -- Entire Word Matched

```cpp
    if (index == (int)word.size()) return true;
```

Every character of the word has been matched.
The word exists in the grid. Return true.

---

### 🛑 Invalid Cell -- Out of Bounds, Wrong Character, or Visited

```cpp
    if (r < 0 || r >= rows || c < 0 || c >= cols) return false;
    if (board[r][c] != word[index]) return false;
```

Out of bounds → can't match. Wrong character → can't match.

The visited check is handled by temporarily changing the cell (see below).

---

### 🔒 Mark as Visited (Temporarily)

```cpp
    char temp = board[r][c];
    board[r][c] = '#';
```

Replace the cell with a special character `'#'`.
This prevents revisiting this cell in the current path.

> _"The cell is claimed for this path.
> No one else may use it until we release it."_

---

### 🧭 Try All 4 Neighbors

```cpp
    bool found = dfs(board, word, r - 1, c, index + 1)
              || dfs(board, word, r + 1, c, index + 1)
              || dfs(board, word, r, c - 1, index + 1)
              || dfs(board, word, r, c + 1, index + 1);
```

Try up, down, left, right. Match the NEXT character (`index + 1`).

Short-circuit `||` -- if any direction finds the word, stop immediately.

---

### 🔓 Backtrack -- Restore the Cell

```cpp
    board[r][c] = temp;
    return found;
}
```

**Restore the original character.**
This cell is now available for other paths.

This is the BACKTRACKING step -- the key difference from flood fill.
In flood fill, cells stay permanently marked.
Here, we unmark because the same cell might be needed elsewhere.

> _"The cell is released.
> The path didn't work (or it did and we're unwinding).
> Either way, the cell returns to its original state
> for future explorers."_

---

## 🗺️ The Main Function

```cpp
bool exist(vector<vector<char>>& board, string word) {
    int rows = board.size();
    int cols = board[0].size();

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (board[r][c] == word[0]) {
                if (dfs(board, word, r, c, 0)) {
                    return true;
                }
            }
        }
    }
    return false;
}
```

Try starting the DFS from every cell that matches the first character.
If any starting point finds the word → return true.
If none do → return false.

---

### 🎺 The Trial of the Hidden Word

```cpp
int main() {
    vector<vector<char>> board = {
        {'A','B','C','E'},
        {'S','F','C','S'},
        {'A','D','E','E'}
    };

    cout << exist(board, "ABCCED") << endl; // expected: 1 (true)
    cout << exist(board, "SEE") << endl;    // expected: 1 (true)
    cout << exist(board, "ABCB") << endl;   // expected: 0 (false)

    return 0;
}
```

---

**Trace for word = "ABCCED":**

```
Board:
  A B C E
  S F C S
  A D E E

Start at (0,0) = 'A' = word[0]. ✓
  (0,0)='A' → mark '#'. Try neighbors for 'B' (word[1]).
    (0,1)='B' ✓ → mark '#'. Try neighbors for 'C' (word[2]).
      (0,2)='C' ✓ → mark '#'. Try neighbors for 'C' (word[3]).
        (1,2)='C' ✓ → mark '#'. Try neighbors for 'E' (word[4]).
          (2,2)='E' ✓ → mark '#'. Try neighbors for 'D' (word[5]).
            (2,1)='D' ✓ → mark '#'. index+1 = 6 == word.size() → TRUE! ✓
```

**Path: (0,0)A → (0,1)B → (0,2)C → (1,2)C → (2,2)E → (2,1)D** ✓

---

**Trace for word = "ABCB":**

```
Start at (0,0)='A'. Match 'A' ✓.
  (0,1)='B'. Match 'B' ✓.
    (0,2)='C'. Match 'C' ✓.
      Need 'B' next. Neighbors: (0,3)='E' ✗, (1,2)='C' ✗, (0,1)='#' (visited!) ✗.
      ALL FAIL. Backtrack.
    Restore (0,2). Try other neighbors of (0,1) for 'C'...
    (1,1)='F' ✗. No more.
  Restore (0,1). No more neighbors of (0,0) for 'B'...
No valid path from (0,0).

No other starting cell matches 'A' that leads to "ABCB".
```

**Answer: false** ✓

The word "ABCB" requires revisiting (0,1) for the second 'B',
but it's already used in the path. Not allowed.

---

## 🔍 Why Backtrack (Unmark) and Not Permanent Mark?

| Flood Fill / Number of Islands    | Word Search                       |
| --------------------------------- | --------------------------------- |
| Mark permanently (sink to 0)      | Mark temporarily, then restore    |
| Each cell processed once globally | Same cell may be in different paths|
| No backtracking                   | Backtracking required             |
| Goal: visit all connected cells   | Goal: find ONE specific path      |

In Word Search, a cell that's part of a failed path
must be available for other attempts.
Permanent marking would incorrectly block valid paths.

---

## 🔍 Optimization -- Early Termination

The `||` short-circuit already helps:
```cpp
bool found = dfs(...) || dfs(...) || dfs(...) || dfs(...);
```
If the first direction finds the word, the other three are never tried.

Additional optimization: check if the board even contains
all characters of the word (with correct frequencies) before starting DFS.

---

### 🧠 Memory of the Hidden Word Law

-   **For each cell matching word[0]:** start DFS
-   **DFS checks:** bounds, character match, not visited
-   **Mark visited:** `board[r][c] = '#'` (temporary)
-   **Try 4 neighbors** with `index + 1` (next character)
-   **Backtrack:** restore `board[r][c] = temp` after exploring
-   **Base case:** `index == word.size()` → word found, return true
-   **Short-circuit `||`:** stop as soon as any direction succeeds
-   **Key difference from flood fill:** BACKTRACK (unmark), don't permanently mark
-   **Time:** O(m × n × 4^L)
-   **Space:** O(L) -- recursion depth = word length
-   **Edge cases:**
    -   Word longer than grid cells → false
    -   Single character word → just check if it exists
    -   Word requires same cell twice → false (not allowed)

Thus is remembered the saga of **Word Search**,
where the Oracle hunted for a hidden word in the grid --
starting from every cell matching the first letter,
DFS-ing through adjacent cells character by character,
marking each cell as claimed for the current path --
and when a path failed, releasing the cell
so other paths could try --
until the word was found
or every possibility was exhausted. 🔤🗺️✨
