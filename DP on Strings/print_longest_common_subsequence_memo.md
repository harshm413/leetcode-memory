## 🔤🖨️🧠 _Recovering the Shared Thread (Memoization): The Print LCS Saga_

> \_"The Oracle had found the LCS length with memoization.
> Now the King demanded the actual string.
>
> **Can the memoized table be used for backtracking?**
>
> Yes. The memo table, once fully filled,
> contains the same information as the tabulation table.
> The backtracking logic is identical.
>
> But there's a subtlety:
>
> **Memoization fills cells on-demand (only what's needed).
> Backtracking might visit cells that were never filled.**
>
> The fix: either ensure all cells are filled
> (by calling `solve(m-1, n-1)` which triggers everything),
> or convert to tabulation for the printing step.
>
> The Oracle chose to fill the memo table via recursion,
> then backtrack through it --
> same logic, same result."\_

---

This is the saga of **Print Longest Common Subsequence (Memoization)**.

Given two strings: find and **print** the actual LCS.

```
Input:  text1 = "abcde", text2 = "ace"  →  Output: "ace"
Input:  text1 = "brute", text2 = "groot" →  Output: "rt"
```

---

## 🧠 The Strategy -- Memoize, Then Backtrack

```
Step 1: Run LCS memoization → fills the memo table.
Step 2: Backtrack through the memo table → recover the string.
```

The memo table after `solve(m-1, n-1)` is called
contains `memo[i][j]` = LCS length of `text1[0..i]` and `text2[0..j]`.

Backtracking reads these values to trace the path.

**Important:** the memo uses 0-indexed `(i, j)` where
`memo[i][j]` = LCS of `text1[0..i]` and `text2[0..j]`.
This is slightly different from the 1-indexed tabulation table.
The backtracking adjusts accordingly.

---

### 📜 The Scroll of the Memoized Recovery

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🔤 Step 1 -- Fill the Memo Table

### The LCS Memoization (Same as Before)

```cpp
int solve(int i, int j, string& text1, string& text2,
          vector<vector<int>>& memo) {
    if (i < 0 || j < 0) return 0;
    if (memo[i][j] != -1) return memo[i][j];

    if (text1[i] == text2[j]) {
        memo[i][j] = 1 + solve(i - 1, j - 1, text1, text2, memo);
    } else {
        memo[i][j] = max(solve(i - 1, j, text1, text2, memo),
                         solve(i, j - 1, text1, text2, memo));
    }
    return memo[i][j];
}
```

Standard LCS memoization. After calling `solve(m-1, n-1)`,
the memo table is filled for all reachable states.

---

## 🖨️ Step 2 -- Backtrack Through the Memo Table

```cpp
string printLCS(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

---

### Fill the table

```cpp
    solve(m - 1, n - 1, text1, text2, memo);
```

This triggers the recursion and fills all needed cells.

---

### Backtrack from (m-1, n-1)

```cpp
    string lcs = "";
    int i = m - 1, j = n - 1;
```

Start at the bottom-right of the memo table.

---

### Walk backward

```cpp
    while (i >= 0 && j >= 0) {
```

Continue while both indices are valid.

---

### ✅ Characters match -- include and go diagonal

```cpp
        if (text1[i] == text2[j]) {
            lcs += text1[i];
            i--;
            j--;
        }
```

This character is part of the LCS.
Add it. Move diagonally (both strings shrink).

---

### ❌ Mismatch -- follow the larger neighbor

```cpp
        else {
            int up = (i - 1 >= 0) ? memo[i - 1][j] : 0;
            int left = (j - 1 >= 0) ? memo[i][j - 1] : 0;

            if (up > left) {
                i--;
            } else {
                j--;
            }
        }
    }
```

The characters don't match.
Check the neighbor above (`memo[i-1][j]`) and left (`memo[i][j-1]`).

**Boundary check:** if `i-1 < 0` or `j-1 < 0`, treat that neighbor as 0.
This handles the edges of the 0-indexed table.

Move toward the larger value -- that's where the LCS came from.

> _"The 0-indexed memo needs boundary guards
> that the 1-indexed tabulation table doesn't.
> Row 0 and column 0 are real data here, not padding."_

---

### Reverse the result

```cpp
    reverse(lcs.begin(), lcs.end());
    return lcs;
}
```

Characters were collected backward. Reverse to get the correct order.

---

### 🎺 The Trial

```cpp
int main() {
    cout << printLCS("abcde", "ace") << endl;   // expected: "ace"
    cout << printLCS("brute", "groot") << endl; // expected: "rt"
    cout << printLCS("abc", "abc") << endl;     // expected: "abc"
    return 0;
}
```

---

**Backtracking trace for text1 = "abcde", text2 = "ace":**

**Memo table (0-indexed):**

```
     a  c  e
a  [ 1, 1, 1]
b  [ 1, 1, 1]
c  [ 1, 2, 2]
d  [ 1, 2, 2]
e  [ 1, 2, 3]
```

| (i, j) | text1[i] | text2[j] | Match? | Action              | lcs    |
| ------- | -------- | -------- | ------ | ------------------- | ------ |
| (4, 2)  | 'e'      | 'e'      | ✓      | Include. Go (3,1).  | "e"    |
| (3, 1)  | 'd'      | 'c'      | ✗      | up=memo[2][1]=2, left=memo[3][0]=1. Up. | "e" |
| (2, 1)  | 'c'      | 'c'      | ✓      | Include. Go (1,0).  | "ec"   |
| (1, 0)  | 'b'      | 'a'      | ✗      | up=memo[0][0]=1, left=0 (j-1<0). Up. | "ec" |
| (0, 0)  | 'a'      | 'a'      | ✓      | Include. Go (-1,-1).| "eca"  |

**Reverse "eca" → "ace"** ✓

---

## 🔍 Memo vs Tab for Printing -- The Difference

| Aspect              | Memo (0-indexed)                | Tab (1-indexed)                |
| ------------------- | ------------------------------- | ------------------------------ |
| Table indices       | `memo[i][j]` = LCS of `[0..i]` and `[0..j]` | `dp[i][j]` = LCS of `[0..i-1]` and `[0..j-1]` |
| Base case           | `i < 0 \|\| j < 0` → 0 (implicit) | Row 0 and col 0 = 0 (explicit) |
| Backtrack start     | `(m-1, n-1)`                    | `(m, n)`                       |
| Backtrack stop      | `i < 0 \|\| j < 0`             | `i == 0 \|\| j == 0`          |
| Boundary guards     | Need `i-1 >= 0` checks         | No guards needed (row/col 0 = 0) |
| Character access    | `text1[i]`, `text2[j]`         | `text1[i-1]`, `text2[j-1]`    |

**The tabulation version is cleaner for backtracking**
because the 1-indexed padding eliminates boundary checks.

**The memoization version works** but needs extra guards
for the 0-indexed edges.

---

### 🧠 Memory of the Memoized Recovery Law

-   **Step 1:** fill memo table via `solve(m-1, n-1)`
-   **Step 2:** backtrack from `(m-1, n-1)` to `(-1, -1)`
-   **Match:** include character, go diagonal `(i-1, j-1)`
-   **Mismatch:** check `memo[i-1][j]` vs `memo[i][j-1]`, go toward larger
-   **Boundary guards:** `i-1 >= 0` and `j-1 >= 0` checks needed (0-indexed)
-   **Collect in reverse** → reverse at the end
-   **Tab version is cleaner** for backtracking (1-indexed padding)
-   **Time:** O(M × N) + O(M + N). **Space:** O(M × N).

Thus is remembered the saga of **Print LCS (Memoization)**,
where the Oracle filled the memo table via recursion,
then walked it backward --
matching characters traced the diagonal,
mismatches followed the larger neighbor --
until the shared thread was spelled out
from the 0-indexed depths of the memo table. 🔤🖨️🧠✨
