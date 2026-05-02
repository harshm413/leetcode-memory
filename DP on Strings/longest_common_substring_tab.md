## 🔤📏 _The Unbroken Shared Thread: The Longest Common Substring Saga_

> \_"The Oracle had mastered the Longest Common SUBSEQUENCE --
> characters picked in order but not necessarily contiguous.
>
> Now the King demanded something stricter:
>
> **'Find the longest SUBSTRING common to both strings.
> Not a subsequence -- a SUBSTRING.
> The characters must be CONTIGUOUS.
> No gaps. No skipping.'**
>
> `"abc"` is a substring of `"xabcy"` (contiguous).
> `"ace"` is NOT a substring of `"abcde"` (gaps between a, c, e).
>
> The Oracle saw this as a variation of LCS
> with one critical change:
>
> **In LCS, a mismatch meant 'try dropping one character.'
> In Longest Common Substring, a mismatch means 'RESET to zero.'**
>
> Because the characters must be contiguous,
> the moment they stop matching, the streak is broken.
> There's no 'skip and continue' -- the chain snaps.
>
> **Match → extend the streak (diagonal + 1).**
> **Mismatch → streak is broken (reset to 0).**
>
> Track the maximum streak seen anywhere in the table.
> That's the answer."\_

---

This is the saga of **Longest Common Substring**.

Given two strings `text1` and `text2`:
-   Find the length of their **longest common substring**.
-   A substring is a contiguous sequence of characters.

```
Input:  text1 = "abcde", text2 = "abfce"
Output: 2   (common substrings: "ab" length 2)

Input:  text1 = "abc", text2 = "abc"
Output: 3   (the entire string)

Input:  text1 = "abc", text2 = "def"
Output: 0   (no common substring)
```

---

## 🧠 Building the Intuition -- Subsequence vs Substring

In LCS (subsequence):

```
Match:    dp[i][j] = 1 + dp[i-1][j-1]              ← extend
Mismatch: dp[i][j] = max(dp[i-1][j], dp[i][j-1])   ← try dropping one
```

In Longest Common Substring:

```
Match:    dp[i][j] = 1 + dp[i-1][j-1]   ← extend the streak
Mismatch: dp[i][j] = 0                   ← streak broken, reset
```

**The match case is IDENTICAL.**
**The mismatch case is completely different.**

In LCS, a mismatch means "maybe the LCS continues elsewhere."
In substring, a mismatch means "the contiguous run is over. Start fresh."

**Why reset to 0?**

A substring must be contiguous. If `text1[i] != text2[j]`,
there's no contiguous common substring ENDING at both `i` and `j`.
The streak at this position is 0.

**The answer is NOT at `dp[m][n]`.**

In LCS, the answer was always at the bottom-right corner.
In substring, the longest streak could END anywhere in the table.
The answer = the MAXIMUM value in the entire table.

---

## 🧠 What `dp[i][j]` Means Here

```
dp[i][j] = length of the longest common substring
           ENDING at text1[i-1] and text2[j-1].
```

Not "up to" -- ENDING AT. This is the key difference.

In LCS, `dp[i][j]` meant "best LCS of prefixes up to i and j."
Here, `dp[i][j]` means "how long is the matching streak
that ends RIGHT HERE at these two characters?"

If the characters match → the streak extends from the diagonal.
If they don't → the streak at this position is 0.

---

### 📜 The Scroll of the Unbroken Thread

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 📏 The Tabulation Ritual

### Create the Table

```cpp
int longestCommonSubstring(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
```

1-indexed table. Row 0 and column 0 are all zeros
(empty string has no common substring with anything).

---

### Track the Maximum Streak

```cpp
    int maxLen = 0;
```

The answer isn't at a fixed cell.
It's the maximum value found ANYWHERE in the table.

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
```

---

### ✅ Characters Match -- Extend the Streak

```cpp
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
                maxLen = max(maxLen, dp[i][j]);
            }
```

The characters at this position match.
The contiguous streak extends from the diagonal.

`dp[i-1][j-1]` = the streak ending at the PREVIOUS characters
in both strings. Adding this match extends it by 1.

Update `maxLen` if this streak is the longest seen so far.

Why update maxLen HERE and not at the end?
Because the longest streak could end at ANY cell.
We track the max as we go.

> _"The characters match.
> The unbroken thread grows by one.
> If this is the longest thread seen so far -- remember it."_

---

### ❌ Characters Don't Match -- Streak Broken

```cpp
            // else: dp[i][j] remains 0 (initialized)
```

No code needed. The cell was initialized to 0.
A mismatch means no contiguous common substring ends here.

In LCS, we'd write `max(dp[i-1][j], dp[i][j-1])`.
Here, we write NOTHING. The streak is dead. Zero.

> _"The characters don't match.
> The thread snaps.
> No 'try the other direction.'
> No 'maybe it continues elsewhere.'
> It's over. Zero."_

---

```cpp
        }
    }
    return maxLen;
}
```

The answer = the maximum streak found anywhere in the table.

---

### 🎺 The Trial of the Unbroken Thread

```cpp
int main() {
    cout << longestCommonSubstring("abcde", "abfce") << endl; // expected: 2
    cout << longestCommonSubstring("abc", "abc") << endl;     // expected: 3
    cout << longestCommonSubstring("abc", "def") << endl;     // expected: 0
    cout << longestCommonSubstring("abcdxyz", "xyzabcd") << endl; // expected: 4
    return 0;
}
```

---

**Full table for text1 = "abcde", text2 = "abfce":**

```
     ""  a  b  f  c  e
""    0  0  0  0  0  0
a     0  1  0  0  0  0
b     0  0  2  0  0  0
c     0  0  0  0  1  0
d     0  0  0  0  0  0
e     0  0  0  0  0  1
```

| (i,j) | text1[i-1] | text2[j-1] | Match? | dp[i][j]              | maxLen |
| ------ | ---------- | ---------- | ------ | ---------------------- | ------ |
| (1,1)  | a          | a          | ✓      | 1 + dp[0][0] = 1      | 1      |
| (1,2)  | a          | b          | ✗      | 0                      | 1      |
| (2,1)  | b          | a          | ✗      | 0                      | 1      |
| (2,2)  | b          | b          | ✓      | 1 + dp[1][1] = 1+1 = **2** | **2** |
| (2,3)  | b          | f          | ✗      | 0                      | 2      |
| (3,4)  | c          | c          | ✓      | 1 + dp[2][3] = 1+0 = 1 | 2     |
| (5,5)  | e          | e          | ✓      | 1 + dp[4][4] = 1+0 = 1 | 2     |

**Answer: maxLen = 2** ✓

The longest common substring is "ab" (positions 1-2 in both strings).
The streak of 2 was at `dp[2][2]`: 'b' matched, extending the 'a' match from `dp[1][1]`.

Notice: 'c' matched at (3,4) but `dp[2][3] = 0` (previous characters didn't match).
So the streak reset to 1. The 'c' match was isolated, not extending "ab".

---

**Table for text1 = "abcdxyz", text2 = "xyzabcd":**

```
     ""  x  y  z  a  b  c  d
""    0  0  0  0  0  0  0  0
a     0  0  0  0  1  0  0  0
b     0  0  0  0  0  2  0  0
c     0  0  0  0  0  0  3  0
d     0  0  0  0  0  0  0  4
x     0  1  0  0  0  0  0  0
y     0  0  2  0  0  0  0  0
z     0  0  0  3  0  0  0  0
```

Two streaks:
-   "abcd" starting at (1,4): streak grows 1→2→3→4. **maxLen = 4.**
-   "xyz" starting at (5,1): streak grows 1→2→3.

**Answer: 4** ✓ (common substring "abcd")

---

**Table for text1 = "abc", text2 = "abc":**

```
     ""  a  b  c
""    0  0  0  0
a     0  1  0  0
b     0  0  2  0
c     0  0  0  3
```

Perfect diagonal streak. **Answer: 3** ✓

---

## 🔧 Space Optimization -- Two Rows

Each row depends only on the previous row (the diagonal `dp[i-1][j-1]`).

```cpp
int longestCommonSubstring(string text1, string text2) {
    int m = text1.size(), n = text2.size();
    vector<int> prev(n + 1, 0), curr(n + 1, 0);
    int maxLen = 0;

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1[i - 1] == text2[j - 1]) {
                curr[j] = 1 + prev[j - 1];
                maxLen = max(maxLen, curr[j]);
            } else {
                curr[j] = 0;
            }
        }
        prev = curr;
        fill(curr.begin(), curr.end(), 0);
    }
    return maxLen;
}
```

`prev[j-1]` = `dp[i-1][j-1]` (diagonal from previous row).
`curr[j] = 0` on mismatch (must explicitly reset, unlike the 2D version
where initialization handled it).

Present the 2D table first. Mention this as a follow-up.

```
Time:  O(M × N)
Space: O(N)
```

---

## 🔍 LCS vs Longest Common Substring -- Side by Side

| Aspect              | LCS (Subsequence)                 | Longest Common Substring          |
| ------------------- | --------------------------------- | --------------------------------- |
| Characters          | Can skip (non-contiguous)         | Must be contiguous                |
| Match               | `1 + dp[i-1][j-1]`              | `1 + dp[i-1][j-1]` (same!)      |
| Mismatch            | `max(dp[i-1][j], dp[i][j-1])`   | **0** (reset)                    |
| `dp[i][j]` means    | Best LCS of prefixes 0..i, 0..j  | Streak ENDING at i, j            |
| Answer location     | `dp[m][n]` (bottom-right)        | `max` over entire table          |
| Memoization         | Works naturally                   | Less natural (reset doesn't recurse well) |

**The match case is identical. The mismatch case is the entire difference.**

---

## 🔍 Why Memoization Is Less Natural Here

In LCS, the mismatch case recurses: `max(solve(i-1,j), solve(i,j-1))`.
That's a natural recursive call.

In substring, the mismatch case is just `0`. No recursion.
The "answer" isn't at a fixed endpoint -- it's the max across ALL cells.

Memoization works best when the answer is at a specific state.
Here, the answer is a global maximum. Tabulation handles this more naturally
(just track `maxLen` as you fill).

That's why this problem is typically presented as tabulation only.

---

### 🧠 Memory of the Unbroken Thread Law

-   **`dp[i][j]`** = length of common substring ENDING at `text1[i-1]` and `text2[j-1]`
-   **Match:** `dp[i][j] = 1 + dp[i-1][j-1]` (extend streak)
-   **Mismatch:** `dp[i][j] = 0` (streak broken, reset)
-   **Answer:** `max` over the entire table (not at a fixed cell)
-   **Same match case as LCS.** Different mismatch case.
-   **Space optimization:** two rows, but must explicitly reset `curr[j] = 0` on mismatch
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Longest Common Substring**,
where the Oracle filled the table character by character --
matching characters extended the unbroken streak,
mismatching characters shattered it to zero --
and the longest streak found anywhere in the table
was the longest contiguous thread
shared by both strings. 🔤📏✨
