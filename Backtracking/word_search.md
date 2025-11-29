## 🔍 _The Quest for the Hidden Word: The Word Search Saga_

> \*"Across the Gridlands lies a tapestry of letters —
> silent runes arranged in rows and columns.
> Yet some say a secret word sleeps within,
> woven not in straight scrolls,
> but letter by letter, stepping through neighbors.
>
> To awaken this hidden word,
> the Oracle must walk the board,
> tracing paths, marking her steps,
> and retreating whenever the trail grows false.
>
> For this is the Saga of Backtracking —
> the hunt for a word lost inside the very weave of the world."\*

---

In the Land of Letters, the Oracle faced a sacred board,
a grid where each cell held a character.
A word — ancient, whispered, fragile — was said to be hidden
somewhere inside,
formed by stepping **up, down, left, or right**,
never revisiting the same cell in a single path.

Her mission:

> _“Tell us if this word truly exists within the board.”_

To succeed, she used **backtracking**,
the art of stepping forward, checking fate,
and stepping back when destiny denied the path.

Thus began the saga of **Word Search**.

---

### 📜 The Scroll of Runes

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The letters lay in a grid,
silent and unmoving —
until the Oracle began her search.

---

## 🧭 The Oracle’s Path-Walking Ritual

_DFS + backtracking on each cell_

```cpp
bool dfs(vector<vector<char>>& board, int r, int c,
         string& word, int idx) {
```

The Oracle stepped onto cell `(r, c)`
to match character `word[idx]`.

---

### 🧱 Step 1 — Boundary & Character Check

```cpp
    if (idx == word.size()) return true;
    if (r < 0 || c < 0 || r >= board.size() || c >= board[0].size())
        return false;
    if (board[r][c] != word[idx]) return false;
```

If she matched all letters,
the word was found.

If she stepped outside the grid
or onto a mismatching letter,
the path ended.

---

### 🕯️ Step 2 — Mark the Rune as Visited

```cpp
    char temp = board[r][c];
    board[r][c] = '#';
```

She marked the rune with a symbol —
`'#'` — to show she had stepped here.
No revisiting in the same path.

---

### 🌬️ Step 3 — Explore All Directions

```cpp
    bool found = dfs(board, r+1, c, word, idx+1) ||
                 dfs(board, r-1, c, word, idx+1) ||
                 dfs(board, r, c+1, word, idx+1) ||
                 dfs(board, r, c-1, word, idx+1);
```

Like a whisper moving through the wind,
she explored four directions:

-   downward
-   upward
-   rightward
-   leftward

hoping the next letter lay ahead.

---

### 🌀 Step 4 — Backtrack

_restore the rune_

```cpp
    board[r][c] = temp;
    return found;
}
```

Whether success or failure,
the Oracle restored the rune
so other paths could use it.

Such is the sacred discipline of backtracking.

---

## 🔮 The Invocation of the Word Hunt

```cpp
bool exist(vector<vector<char>>& board, string word) {
    for (int r = 0; r < board.size(); r++) {
        for (int c = 0; c < board[0].size(); c++) {
            if (dfs(board, r, c, word, 0))
                return true;
        }
    }
    return false;
}
```

The Oracle began her search from **every cell**.
Any one of them might hold the first rune of destiny.

---

### 🎺 The Trial of the Gridlands

```cpp
int main() {
    vector<vector<char>> board = {
        {'A','B','C','E'},
        {'S','F','C','S'},
        {'A','D','E','E'}
    };

    cout << (exist(board, "ABCCED") ? "true" : "false") << endl; // true
    cout << (exist(board, "SEE") ? "true" : "false") << endl;    // true
    cout << (exist(board, "ABCB") ? "true" : "false") << endl;   // false

    return 0;
}
```

The Oracle walked the board,
and the runes whispered truth:

-   “ABCCED” was found.
-   “SEE” was found.
-   “ABCB” was impossible,
    for it would require revisiting a cell — a forbidden act.

---

### 🧠 Memory of the Rune Hunt

-   Explore every cell as a possible starting point.
-   DFS to follow each letter step by step.
-   Mark visited cells to avoid revisiting.
-   Backtrack after every attempt.
-   **Paths may twist**, but cannot reuse the same cell.
-   **Time:** O(N · M · 4^L), where L is word length.
-   **Space:** O(L) recursion depth.

Thus is remembered the saga of **Word Search**,
where the Oracle wanders the woven grid,
following letters like ancient footsteps,
until the hidden word reveals its destiny —
or fades back into silence. 🔍✨
