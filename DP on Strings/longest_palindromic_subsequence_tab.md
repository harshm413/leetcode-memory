## 🪞🔤📋 _The Mirror Within (Tabulation): The Longest Palindromic Subsequence Saga_

> \_"The Oracle had reduced LPS to LCS(s, reverse(s))
> and solved it with memoization.
>
> Now she built the answer bottom-up --
> the standard LCS tabulation
> applied to `s` and its reverse."\_

---

This is the saga of **Longest Palindromic Subsequence (Tabulation)**.

Same reduction:
-   `LPS(s) = LCS(s, reverse(s))`
-   Build the LCS table. Read the answer.

```
Input:  s = "bbbab"  →  Output: 4
Input:  s = "cbbd"   →  Output: 2
Input:  s = "abcba"  →  Output: 5
```

---

## 📋 The Tabulation -- LCS of s and reverse(s)

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

### Reverse the String

```cpp
int longestPalindromeSubseq(string s) {
    string rev = s;
    reverse(rev.begin(), rev.end());
```

Create the reverse. This is the "mirror."

---

### Build the LCS Table

```cpp
    int n = s.size();
    vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
```

1-indexed LCS table. `(n+1) × (n+1)`.
Row 0 and column 0 = all zeros (empty string base case).

---

### Fill the Table -- Standard LCS

```cpp
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (s[i - 1] == rev[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
```

Match → diagonal + 1.
Mismatch → max of above and left.

Identical to LCS tabulation. Not a single line changed.

---

### The Answer

```cpp
    return dp[n][n];
}
```

LCS of `s` and `reverse(s)` = LPS of `s`.

---

### 🎺 The Trial

```cpp
int main() {
    cout << longestPalindromeSubseq("bbbab") << endl; // expected: 4
    cout << longestPalindromeSubseq("cbbd") << endl;  // expected: 2
    cout << longestPalindromeSubseq("abcba") << endl; // expected: 5
    return 0;
}
```

---

**Full table for s = "bbbab", rev = "babbb":**

```
     ""  b  a  b  b  b
""    0  0  0  0  0  0
b     0  1  1  1  1  1
b     0  1  1  2  2  2
b     0  1  1  2  3  3
a     0  1  2  2  3  3
b     0  1  2  3  3  4
```

| Key cells | Characters | Match? | dp value |
| --------- | ---------- | ------ | -------- |
| (1,1)     | b, b       | ✓      | 1        |
| (2,3)     | b, b       | ✓      | 1+1 = 2  |
| (3,4)     | b, b       | ✓      | 1+2 = 3  |
| (4,2)     | a, a       | ✓      | 1+1 = 2  |
| (5,5)     | b, b       | ✓      | 1+3 = **4** |

**Answer: dp[5][5] = 4** ✓

LPS = "bbbb" -- four b's from "bbbab".

---

**Table for s = "cbbd", rev = "dbbc":**

```
     ""  d  b  b  c
""    0  0  0  0  0
c     0  0  0  0  1
b     0  0  1  1  1
b     0  0  1  2  2
d     0  1  1  2  2
```

**Answer: dp[4][4] = 2** ✓ (LPS = "bb")

---

**Table for s = "abcba", rev = "abcba":**

```
     ""  a  b  c  b  a
""    0  0  0  0  0  0
a     0  1  1  1  1  1
b     0  1  2  2  2  2
c     0  1  2  3  3  3
b     0  1  2  3  4  4
a     0  1  2  3  4  5
```

**Answer: dp[5][5] = 5** ✓

The string is already a palindrome → LPS = full length.
`s == reverse(s)` → LCS = n.

---

## 🔧 Space Optimization -- Two Rows

Same as LCS space optimization:

```cpp
int longestPalindromeSubseq(string s) {
    string rev = s;
    reverse(rev.begin(), rev.end());
    int n = s.size();

    vector<int> prev(n + 1, 0), curr(n + 1, 0);

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (s[i - 1] == rev[j - 1]) {
                curr[j] = 1 + prev[j - 1];
            } else {
                curr[j] = max(prev[j], curr[j - 1]);
            }
        }
        prev = curr;
    }
    return prev[n];
}
```

`prev[j-1]` = diagonal (previous row, previous column).
`prev[j]` = above (previous row, same column).
`curr[j-1]` = left (current row, previous column).

```
Time:  O(N²)
Space: O(N)
```

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 The LPS Family -- All Reduce to LCS

| Problem                          | Formula                            |
| -------------------------------- | ---------------------------------- |
| Longest Palindromic Subsequence  | `LCS(s, reverse(s))`             |
| Min Insertions for Palindrome    | `n - LCS(s, reverse(s))`         |
| Min Deletions for Palindrome     | `n - LCS(s, reverse(s))`         |

**Min insertions = min deletions = `n - LPS`.**

Why? The LPS is the longest part that's already a palindrome.
The remaining `n - LPS` characters need to be either
inserted (to mirror them) or deleted (to remove them).

---

## 🔍 Why Not Solve LPS Directly (Without LCS)?

You CAN solve LPS with its own recurrence:

```
solve(left, right):
  If s[left] == s[right]:
    2 + solve(left+1, right-1)     ← both ends match, shrink inward
  Else:
    max(solve(left+1, right), solve(left, right-1))  ← drop one end
```

This works. But it's a NEW recurrence to memorize.
The LCS reduction reuses an algorithm you already know.

Both are O(N²). The LCS approach is more elegant
and connects to the broader string DP family.

---

### 🧠 Memory of the Mirror Within (Tabulation) Law

-   **LPS(s) = LCS(s, reverse(s))** -- the reduction
-   **Standard LCS tabulation** on `s` and `reverse(s)`
-   **Answer:** `dp[n][n]`
-   **Space optimization:** two rows → O(N)
-   **Min insertions/deletions for palindrome:** `n - LPS`
-   **Direct recurrence exists** but LCS reduction is cleaner
-   **Time:** O(N²). **Space:** O(N²), optimizable to O(N).

Thus is remembered the saga of **Longest Palindromic Subsequence (Tabulation)**,
where the Oracle reversed the string,
built the LCS table between the string and its mirror,
and read the answer from the bottom-right corner --
the longest palindrome hiding within,
revealed by the reflection. 🪞🔤📋✨
