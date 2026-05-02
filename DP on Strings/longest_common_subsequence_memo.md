## 🔤🔗🧠 _The Shared Thread (Memoization): The Longest Common Subsequence Saga_

> \_"Two strings stood before the Oracle:
> `text1` and `text2`.
>
> A **subsequence** was a sequence of characters
> picked from a string in order --
> but not necessarily contiguous.
> Characters could be skipped, but the order was preserved.
>
> For example, from `"abcde"`:
> `"ace"` is a subsequence (skip b, d).
> `"aec"` is NOT (order violated).
>
> A **common subsequence** appeared in BOTH strings.
>
> The Oracle was commanded:
>
> **'Find the length of the LONGEST common subsequence.'**
>
> The Oracle compared the strings character by character,
> starting from the ends:
>
> **If the last characters MATCH:**
> This character is part of the LCS.
> Count it (+ 1) and solve the rest (both strings shrink by 1).
>
> **If they DON'T match:**
> At least one of the last characters is NOT in the LCS.
> Try dropping the last character of text1.
> Try dropping the last character of text2.
> Take the better result.
>
> This was the **match or mismatch** pattern --
> the foundation of all string DP problems."\_

---

This is the saga of **Longest Common Subsequence (Memoization)**.

Given two strings `text1` and `text2`:
-   Find the length of their **longest common subsequence** (LCS).

```
Input:  text1 = "abcde", text2 = "ace"
Output: 3   (LCS = "ace")

Input:  text1 = "abc", text2 = "abc"
Output: 3   (LCS = "abc" -- identical strings)

Input:  text1 = "abc", text2 = "def"
Output: 0   (no common characters)
```

---

## 🧠 Building the Intuition -- Compare from the End

Stand at the last character of both strings.
Two characters face each other: `text1[i]` and `text2[j]`.

**Case 1 -- They MATCH:**

Both strings end with the same character.
This character MUST be part of the LCS
(it's common and it's at the end -- including it is always optimal).

```
LCS includes this character.
1 + solve(i-1, j-1)
```

Both strings shrink by 1. We've "consumed" the matching character.

**Why is including it always optimal?**
If both strings end with 'e', any LCS that doesn't include this 'e'
could be extended by adding 'e' at the end -- making it longer.
So including it is never worse.

**Case 2 -- They DON'T match:**

At least one of them is NOT in the LCS.
We don't know which one to drop.
So we try BOTH:

```
Drop text1's last character: solve(i-1, j)
Drop text2's last character: solve(i, j-1)
Take the max.
```

**Why max?** Because we want the LONGEST subsequence.
One of the two drops leads to a longer LCS. We pick it.

**Why not drop both?**
Dropping both (`solve(i-1, j-1)`) is always ≤ dropping just one.
If we drop text1's last, text2's last is still available.
Dropping both loses a character unnecessarily.
So `max(solve(i-1, j), solve(i, j-1))` always covers `solve(i-1, j-1)`.

---

## 🧠 The Recurrence

```
solve(i, j):
  If text1[i] == text2[j]:
    return 1 + solve(i-1, j-1)          ← match: count it, shrink both
  Else:
    return max(solve(i-1, j), solve(i, j-1))  ← mismatch: try dropping each
```

**Base case:**

```
If i < 0 OR j < 0: return 0
```

One string is exhausted. No more characters to compare.
LCS length from here = 0.

**State:** `(i, j)` -- position in text1 and text2.

---

### 📜 The Scroll of the Shared Thread

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔤 Setting Up the Cache

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

2D cache: `memo[i][j]` = LCS length of `text1[0..i]` and `text2[0..j]`.
`-1` = not yet computed.

---

```cpp
    return solve(m - 1, n - 1, text1, text2, memo);
}
```

Start from the last characters of both strings.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, string& text1, string& text2,
          vector<vector<int>>& memo) {
```

"What is the LCS length of `text1[0..i]` and `text2[0..j]`?"

---

### 🛑 One string exhausted

```cpp
    if (i < 0 || j < 0) return 0;
```

If either string has no characters left --
there can be no common subsequence. Length = 0.

---

### 📖 Cache check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### ✅ Characters MATCH

```cpp
    if (text1[i] == text2[j]) {
        memo[i][j] = 1 + solve(i - 1, j - 1, text1, text2, memo);
    }
```

The last characters are the same.
This character is part of the LCS.

**`1`** -- count this matching character.
**`solve(i-1, j-1)`** -- find the LCS of the remaining prefixes.

Both strings shrink by 1 because both "used" this character.

> _"The characters meet and recognize each other.
> They join the shared thread.
> Both strings step back one position,
> having found common ground."_

---

### ❌ Characters DON'T match

```cpp
    else {
        int dropText1 = solve(i - 1, j, text1, text2, memo);
        int dropText2 = solve(i, j - 1, text1, text2, memo);
        memo[i][j] = max(dropText1, dropText2);
    }
```

The last characters are different.
At least one of them is NOT in the LCS.

**`solve(i-1, j)`** -- drop text1's last character.
Maybe the LCS doesn't need it.

**`solve(i, j-1)`** -- drop text2's last character.
Maybe the LCS doesn't need it.

**`max`** -- whichever drop leads to a longer LCS.

> _"The characters don't match.
> One of them must leave.
> The Oracle tries removing each one
> and keeps the future that gives the longer thread."_

---

```cpp
    return memo[i][j];
}
```

---

### 🎺 The Trial of the Shared Thread

```cpp
int main() {
    cout << longestCommonSubsequence("abcde", "ace") << endl; // expected: 3
    cout << longestCommonSubsequence("abc", "abc") << endl;   // expected: 3
    cout << longestCommonSubsequence("abc", "def") << endl;   // expected: 0
    return 0;
}
```

---

**Full trace for text1 = "abcde", text2 = "ace":**

```
solve(4, 2): text1[4]='e', text2[2]='e' → MATCH!
  1 + solve(3, 1): text1[3]='d', text2[1]='c' → MISMATCH
    dropText1: solve(2, 1): text1[2]='c', text2[1]='c' → MATCH!
      1 + solve(1, 0): text1[1]='b', text2[0]='a' → MISMATCH
        dropText1: solve(0, 0): text1[0]='a', text2[0]='a' → MATCH!
          1 + solve(-1, -1) → 0
        solve(0, 0) = 1
        dropText2: solve(1, -1) → 0
      memo[1][0] = max(1, 0) = 1
    solve(2, 1) = 1 + 1 = 2
    dropText2: solve(3, 0): text1[3]='d', text2[0]='a' → MISMATCH
      dropText1: solve(2, 0): text1[2]='c', text2[0]='a' → MISMATCH
        dropText1: solve(1, 0) → 1 (cached!)
        dropText2: solve(2, -1) → 0
      memo[2][0] = 1
      dropText2: solve(3, -1) → 0
    memo[3][0] = 1
  memo[3][1] = max(2, 1) = 2
memo[4][2] = 1 + 2 = 3
```

**Answer: 3** ✓

LCS = "ace". Characters matched at positions:
-   'e' at (4, 2)
-   'c' at (2, 1)
-   'a' at (0, 0)

---

**Trace for text1 = "abc", text2 = "abc":**

Every character matches in order.
```
solve(2,2): 'c'='c' → 1 + solve(1,1)
solve(1,1): 'b'='b' → 1 + solve(0,0)
solve(0,0): 'a'='a' → 1 + solve(-1,-1) = 1
```

**Answer: 1 + 1 + 1 = 3** ✓

---

**Trace for text1 = "abc", text2 = "def":**

No characters match. Every comparison is a mismatch.
Every path eventually reaches a base case of 0.

**Answer: 0** ✓

---

## 🔍 Why This Is the Foundation of String DP

Almost every string DP problem is built on LCS:

| Problem                          | Relation to LCS                    |
| -------------------------------- | ---------------------------------- |
| Longest Common Subsequence       | Direct                             |
| Shortest Common Supersequence    | `m + n - LCS`                     |
| Edit Distance                    | Similar recurrence (match/mismatch)|
| Longest Palindromic Subsequence  | LCS of string and its reverse      |
| Minimum Insertions for Palindrome| `n - LPS` (which uses LCS)        |

Master LCS and the entire string DP family opens up.

---

## 🔍 The Match/Mismatch Pattern

```
At positions (i, j):
  If characters match:
    1 + solve(i-1, j-1)           ← consume both
  If they don't match:
    max(solve(i-1, j), solve(i, j-1))  ← drop one, keep the other
```

This pattern appears in LCS, Edit Distance, and many others.
The "match" case always consumes both characters.
The "mismatch" case tries dropping each one.

---

### 🧠 Memory of the Shared Thread (Memoization) Law

-   **State:** `(i, j)` -- position in text1 and text2
-   **Match:** `1 + solve(i-1, j-1)` -- count it, shrink both
-   **Mismatch:** `max(solve(i-1, j), solve(i, j-1))` -- drop one, keep better
-   **Base:** `i < 0 || j < 0` → 0 (one string exhausted)
-   **This is the foundation** of all string DP problems
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Longest Common Subsequence (Memoization)**,
where the Oracle compared two strings from the end --
matching characters joined the shared thread,
mismatched characters were tried both ways --
until the longest thread common to both strings
was measured and known. 🔤🔗🧠✨
