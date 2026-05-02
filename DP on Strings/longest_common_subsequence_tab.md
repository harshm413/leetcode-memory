## 🔤🔗📋 _The Shared Thread (Tabulation): The Longest Common Subsequence Saga_

> \_"The Oracle had found the LCS with memoization.
> Now she built the answer bottom-up.
>
> **A 2D table where `dp[i][j]` = LCS length
> of `text1[0..i-1]` and `text2[0..j-1]`.**
>
> The table was filled row by row.
> Matching characters added 1 to the diagonal.
> Mismatching characters took the max of above and left.
>
> When the table was complete,
> the bottom-right cell held the answer."\_

---

This is the saga of **Longest Common Subsequence (Tabulation)**.

Same problem:
-   Two strings. Find the length of their longest common subsequence.

```
Input:  text1 = "abcde", text2 = "ace"  →  Output: 3
Input:  text1 = "abc", text2 = "def"    →  Output: 0
```

---

## 🧠 The Bottom-Up Formulation

We use **1-indexed** dp to avoid messy boundary checks:

```
dp[i][j] = LCS length of text1[0..i-1] and text2[0..j-1]
```

`dp[0][j] = 0` for all j (empty text1 → LCS = 0).
`dp[i][0] = 0` for all i (empty text2 → LCS = 0).

**Why 1-indexed?**

In the memoization, we used `if (i < 0 || j < 0) return 0`.
In tabulation, negative indices don't exist.
By making the table `(m+1) × (n+1)` and using row 0 / col 0
as the "empty string" base case, we avoid boundary issues.

`dp[i][j]` corresponds to `text1[i-1]` and `text2[j-1]`.

**Transition:**

```
If text1[i-1] == text2[j-1]:     ← characters match
  dp[i][j] = 1 + dp[i-1][j-1]   ← diagonal + 1

Else:                             ← characters don't match
  dp[i][j] = max(dp[i-1][j], dp[i][j-1])  ← max of above and left
```

---

### 📜 The Scroll of the Bottom-Up Shared Thread

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the Table

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
```

`(m+1) × (n+1)` table. Row 0 and column 0 are all zeros
(empty string has LCS 0 with anything).

The zeros are already set by the initialization.
No explicit base case filling needed.

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
```

`i` ranges from 1 to m. `j` ranges from 1 to n.
`i = 0` and `j = 0` are the base cases (already 0).

---

### ✅ Characters Match -- Diagonal + 1

```cpp
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            }
```

`text1[i-1]` and `text2[j-1]` are the actual characters
(shifted by 1 because dp is 1-indexed).

They match → this character extends the LCS.
The LCS of the prefixes WITHOUT these characters is `dp[i-1][j-1]`.
Adding this match: `1 + dp[i-1][j-1]`.

**`dp[i-1][j-1]`** is the DIAGONAL cell -- both strings one shorter.

> _"The characters recognize each other.
> They extend the thread by 1.
> The answer comes from the diagonal --
> the world where neither of these characters existed yet."_

---

### ❌ Characters Don't Match -- Max of Above and Left

```cpp
            else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
```

They don't match. At least one is not in the LCS.

**`dp[i-1][j]`** = LCS without text1's current character (drop text1's end).
**`dp[i][j-1]`** = LCS without text2's current character (drop text2's end).

Take the max -- whichever drop preserves the longer LCS.

> _"The characters don't match.
> Look above (drop text1's character)
> and look left (drop text2's character).
> The larger value wins."_

---

```cpp
        }
    }
    return dp[m][n];
}
```

The bottom-right cell holds the LCS of the full strings.

---

### 🎺 The Trial of the Bottom-Up Shared Thread

```cpp
int main() {
    cout << longestCommonSubsequence("abcde", "ace") << endl; // expected: 3
    cout << longestCommonSubsequence("abc", "abc") << endl;   // expected: 3
    cout << longestCommonSubsequence("abc", "def") << endl;   // expected: 0
    return 0;
}
```

---

**Full table for text1 = "abcde", text2 = "ace":**

```
     ""  a  c  e
""    0  0  0  0
a     0  1  1  1
b     0  1  1  1
c     0  1  2  2
d     0  1  2  2
e     0  1  2  3
```

| (i,j) | text1[i-1] | text2[j-1] | Match? | dp[i][j]                    |
| ------ | ---------- | ---------- | ------ | --------------------------- |
| (1,1)  | a          | a          | ✓      | 1 + dp[0][0] = 1           |
| (1,2)  | a          | c          | ✗      | max(dp[0][2], dp[1][1]) = max(0,1) = 1 |
| (1,3)  | a          | e          | ✗      | max(0, 1) = 1              |
| (2,1)  | b          | a          | ✗      | max(1, 0) = 1              |
| (2,2)  | b          | c          | ✗      | max(1, 1) = 1              |
| (3,2)  | c          | c          | ✓      | 1 + dp[2][1] = 1+1 = **2** |
| (5,3)  | e          | e          | ✓      | 1 + dp[4][2] = 1+2 = **3** |

**Answer: dp[5][3] = 3** ✓

The LCS "ace" was built:
-   'a' matched at (1,1) → dp = 1.
-   'c' matched at (3,2) → dp = 2.
-   'e' matched at (5,3) → dp = 3.

---

**Table for text1 = "abc", text2 = "abc":**

```
     ""  a  b  c
""    0  0  0  0
a     0  1  1  1
b     0  1  2  2
c     0  1  2  3
```

Every diagonal is a match. **Answer: 3** ✓

---

**Table for text1 = "abc", text2 = "def":**

```
     ""  d  e  f
""    0  0  0  0
a     0  0  0  0
b     0  0  0  0
c     0  0  0  0
```

No matches anywhere. **Answer: 0** ✓

---

## 🔧 Space Optimization -- Two Rows

Each row depends only on the previous row.
We can use two 1D arrays: `prev` and `curr`.

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = text1.size(), n = text2.size();
    vector<int> prev(n + 1, 0);
    vector<int> curr(n + 1, 0);

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1[i - 1] == text2[j - 1]) {
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

`prev[j]` = `dp[i-1][j]` (above).
`prev[j-1]` = `dp[i-1][j-1]` (diagonal).
`curr[j-1]` = `dp[i][j-1]` (left, already updated this row).

```
Time:  O(M × N)
Space: O(N)
```

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 The String DP Family -- Built on LCS

| Problem                          | How it uses LCS                    |
| -------------------------------- | ---------------------------------- |
| LCS (this)                       | Direct                             |
| Shortest Common Supersequence    | Length = `m + n - LCS`            |
| Longest Palindromic Subsequence  | LCS of `s` and `reverse(s)`       |
| Min Insertions for Palindrome    | `n - LPS`                         |
| Edit Distance                    | Same match/mismatch structure      |
| Longest Common Substring         | LCS but reset on mismatch          |

Master LCS and the entire family follows.

---

## 🔍 Printing the Actual LCS (Bonus)

To recover the actual subsequence (not just the length),
backtrack through the dp table:

```cpp
string lcs = "";
int i = m, j = n;
while (i > 0 && j > 0) {
    if (text1[i-1] == text2[j-1]) {
        lcs = text1[i-1] + lcs;
        i--; j--;
    } else if (dp[i-1][j] > dp[i][j-1]) {
        i--;
    } else {
        j--;
    }
}
```

Match → include character, go diagonal.
Mismatch → go toward the larger value (up or left).

For "abcde" and "ace": backtrack gives "ace". ✓

---

### 🧠 Memory of the Shared Thread (Tabulation) Law

-   **1-indexed table:** `dp[i][j]` = LCS of `text1[0..i-1]` and `text2[0..j-1]`
-   **Base:** row 0 and col 0 = all zeros (empty string)
-   **Match:** `dp[i][j] = 1 + dp[i-1][j-1]` (diagonal + 1)
-   **Mismatch:** `dp[i][j] = max(dp[i-1][j], dp[i][j-1])` (above or left)
-   **Answer:** `dp[m][n]`
-   **Space optimization:** two rows (`prev` and `curr`) → O(N)
-   **Backtracking:** follow matches diagonally to recover the actual LCS
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Longest Common Subsequence (Tabulation)**,
where the Oracle filled a table character by character --
matching characters extended the diagonal by 1,
mismatching characters took the max of above and left --
until the bottom-right cell revealed
the length of the longest thread
shared by both strings. 🔤🔗📋✨
