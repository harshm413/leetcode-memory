## 🪞🔤🧠 _The Mirror Within (Memoization): The Longest Palindromic Subsequence Saga_

> \_"The Oracle was given a single string.
>
> She was commanded:
>
> **'Find the longest subsequence within this string
> that reads the same forwards and backwards --
> a palindrome.'**
>
> A subsequence can skip characters but must preserve order.
> A palindrome reads the same in both directions.
>
> The Oracle did NOT invent a new algorithm.
>
> She made one brilliant observation:
>
> **The longest palindromic subsequence of a string `s`
> is the LCS of `s` and its REVERSE.**
>
> Why? A palindrome reads the same forwards and backwards.
> So the palindromic characters appear in the same order
> in both `s` and `reverse(s)`.
> The longest such shared sequence = LCS of `s` and `reverse(s)`.
>
> **One observation. Zero new code.
> The entire problem reduced to LCS.**"\_

---

This is the saga of **Longest Palindromic Subsequence (Memoization)**.

Given a string `s`:
-   Find the length of the **longest palindromic subsequence**.

```
Input:  s = "bbbab"
Output: 4   (LPS = "bbbb")

Input:  s = "cbbd"
Output: 2   (LPS = "bb")

Input:  s = "a"
Output: 1

Input:  s = "abcba"
Output: 5   (the entire string is a palindrome)
```

---

## 🧠 The Brilliant Reduction -- LPS = LCS(s, reverse(s))

Consider `s = "bbbab"`.
`reverse(s) = "babbб"` → `"babbb"`.

The LCS of `"bbbab"` and `"babbb"`:
-   Common subsequence `"bbbb"` (length 4).
-   That's a palindrome (reads same both ways).

**Why does this work?**

A palindromic subsequence reads the same forwards and backwards.
If we pick characters from `s` that form a palindrome,
those SAME characters appear in the SAME order in `reverse(s)`
(because a palindrome is its own reverse).

So the palindromic subsequence is a common subsequence
of `s` and `reverse(s)`.

The LONGEST palindromic subsequence = the LONGEST common subsequence
of `s` and `reverse(s)`.

**No new DP needed. Just call LCS.**

---

## 🧠 Why This Is Elegant

Instead of designing a new recurrence for palindromes
(which would involve comparing characters from both ends,
tracking two pointers, etc.),
we reduce to a problem we've already solved.

```
LPS(s) = LCS(s, reverse(s))
```

One line of insight. The rest is copy-paste from LCS.

---

### 📜 The Scroll of the Mirror Within

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🪞 The LCS Memoization (Same as Before)

```cpp
int solve(int i, int j, string& s1, string& s2,
          vector<vector<int>>& memo) {
    if (i < 0 || j < 0) return 0;
    if (memo[i][j] != -1) return memo[i][j];

    if (s1[i] == s2[j]) {
        memo[i][j] = 1 + solve(i - 1, j - 1, s1, s2, memo);
    } else {
        memo[i][j] = max(solve(i - 1, j, s1, s2, memo),
                         solve(i, j - 1, s1, s2, memo));
    }
    return memo[i][j];
}
```

Standard LCS memoization. Unchanged. Not a single line different.

---

## 🪞 The Wrapper -- Reverse and Call LCS

```cpp
int longestPalindromeSubseq(string s) {
    string rev = s;
    reverse(rev.begin(), rev.end());
```

Create the reverse of `s`.

---

```cpp
    int n = s.size();
    vector<vector<int>> memo(n, vector<int>(n, -1));
    return solve(n - 1, n - 1, s, rev, memo);
}
```

Call LCS on `s` and `reverse(s)`.
The result = length of the longest palindromic subsequence.

That's it. The entire "new" code is two lines:
reverse the string, call LCS.

---

### 🎺 The Trial of the Mirror Within

```cpp
int main() {
    cout << longestPalindromeSubseq("bbbab") << endl; // expected: 4
    cout << longestPalindromeSubseq("cbbd") << endl;  // expected: 2
    cout << longestPalindromeSubseq("a") << endl;     // expected: 1
    cout << longestPalindromeSubseq("abcba") << endl; // expected: 5
    return 0;
}
```

---

**Trace for s = "bbbab":**

```
s   = "bbbab"
rev = "babbb"
```

LCS of "bbbab" and "babbb":

```
     ""  b  a  b  b  b
""    0  0  0  0  0  0
b     0  1  1  1  1  1
b     0  1  1  2  2  2
b     0  1  1  2  3  3
a     0  1  2  2  3  3
b     0  1  2  3  3  4
```

**LCS = 4** → LPS = 4 ✓

The palindromic subsequence is "bbbb" --
four b's picked from "bbbab".

---

**Trace for s = "cbbd":**

```
s   = "cbbd"
rev = "dbbc"
```

LCS of "cbbd" and "dbbc":

```
     ""  d  b  b  c
""    0  0  0  0  0
c     0  0  0  0  1
b     0  0  1  1  1
b     0  0  1  2  2
d     0  1  1  2  2
```

**LCS = 2** → LPS = 2 ✓

The palindromic subsequence is "bb".

---

**Trace for s = "abcba":**

```
s   = "abcba"
rev = "abcba"   ← the string IS a palindrome, so reverse = itself!
```

LCS of "abcba" and "abcba" = 5 (the entire string).

**LPS = 5** ✓

When the string is already a palindrome,
`s == reverse(s)`, and LCS = the full length.

---

**Trace for s = "a":**

```
s = "a", rev = "a". LCS = 1.
```

**LPS = 1** ✓ (single character is always a palindrome)

---

## 🔍 Why LCS(s, reverse(s)) = LPS(s) -- The Proof

**Claim:** every palindromic subsequence of `s`
is a common subsequence of `s` and `reverse(s)`.

**Proof:**
Let P be a palindromic subsequence of `s`.
P appears in `s` (by definition of subsequence).
P is a palindrome → P = reverse(P).
reverse(P) appears in reverse(s) (reversing preserves subsequences).
So P appears in both `s` and `reverse(s)`.
P is a common subsequence. ✓

**Claim:** the longest common subsequence of `s` and `reverse(s)`
is a palindrome.

**Proof:**
Let L be the LCS of `s` and `reverse(s)`.
L appears in `s` in some positions.
L appears in `reverse(s)` in some positions.
By the symmetry of LCS, reverse(L) is also a common subsequence
of the same length. Since L is the LONGEST, L = reverse(L).
So L is a palindrome. ✓

Therefore: LPS(s) = LCS(s, reverse(s)). ✓

---

## 🔍 The String DP Reduction Chain

```
Longest Palindromic Subsequence
  ↓ "LPS(s) = LCS(s, reverse(s))"
Longest Common Subsequence
  ↓ "match/mismatch recurrence"
Standard 2D string DP
```

One reduction. One existing algorithm. Problem solved.

---

## 🔍 Related Problems Using This Reduction

| Problem                          | Reduction                          |
| -------------------------------- | ---------------------------------- |
| Longest Palindromic Subsequence  | LCS(s, reverse(s))                |
| Min Insertions for Palindrome    | n - LPS(s) = n - LCS(s, rev(s))  |
| Min Deletions for Palindrome     | n - LPS(s) (same as insertions)   |

All reduce to LCS. Master LCS, solve them all.

---

### 🧠 Memory of the Mirror Within (Memoization) Law

-   **LPS(s) = LCS(s, reverse(s))** -- the entire reduction
-   **No new DP needed** -- just reverse the string and call LCS
-   **Why it works:** palindromic subsequences appear in both s and reverse(s)
-   **When s is already a palindrome:** LPS = n (full length)
-   **Time:** O(N²). **Space:** O(N²).

Thus is remembered the saga of **Longest Palindromic Subsequence (Memoization)**,
where the Oracle did not invent a new algorithm
but instead held the string up to a mirror --
reversing it, then finding the longest thread
shared between the string and its reflection --
knowing that the shared thread
was the longest palindrome hiding within. 🪞🔤🧠✨
