## 🪞✂️🧠 _The Missing Mirror Pieces (Memoization): The Min Insertions to Make Palindrome Saga_

> \_"The Oracle was given a string.
>
> She was commanded:
>
> **'What is the minimum number of characters
> you must INSERT to make this string a palindrome?'**
>
> The Oracle did not panic.
> She had already found the Longest Palindromic Subsequence.
>
> She made one observation:
>
> **The LPS is the longest part of the string
> that is ALREADY a palindrome (as a subsequence).
> The remaining characters are NOT part of any palindrome.
> Each of those characters needs ONE insertion
> to create its mirror counterpart.**
>
> `minInsertions = n - LPS(s)`
>
> That's it. One subtraction.
> The entire problem reduced to a formula
> on top of a problem already solved."\_

---

This is the saga of **Minimum Insertion Steps to Make a String Palindrome (Memoization)**.

Given a string `s`:
-   Find the minimum number of insertions to make it a palindrome.

```
Input:  s = "zzazz"
Output: 0   (already a palindrome)

Input:  s = "mbadm"
Output: 2   (insert 'd' and 'b' → "mbdadm"... wait, "mbadm" → "mbdabdm"? 
             Let me think: LPS = "mam" length 3? No... LPS = "madam"? No, 
             s = "mbadm". LPS = "badm"? No. LPS = "mam" (m, a, m) = 3.
             n - LPS = 5 - 3 = 2.)
Output: 2 ✓

Input:  s = "leetcode"
Output: 5   (LPS = "ece" length 3. n - LPS = 8 - 3 = 5.)

Input:  s = "a"
Output: 0
```

---

## 🧠 The Reduction -- Why `n - LPS`?

The LPS is the longest subsequence that's already a palindrome.
These characters are already in the right positions
(relative to each other) to form a palindrome.

The remaining `n - LPS` characters are NOT part of this palindrome.
Each one needs a mirror character inserted somewhere
to make the full string a palindrome.

**Example: s = "abcaa"**

LPS = "aca" or "aaa" (length 3).
Remaining characters: 2 (the 'b' and one extra).
Insert 2 characters to mirror them.
Result: "aacbcaa" (one possible palindrome).

**Why exactly `n - LPS` insertions?**

The LPS characters form the palindromic "skeleton."
Every non-LPS character breaks the palindrome.
Each needs exactly one insertion to create its mirror.
No more, no less.

**Why not deletions?**

Minimum DELETIONS to make a palindrome is ALSO `n - LPS`.
Deleting the non-palindromic characters leaves the LPS (a palindrome).
Inserting mirrors for them also makes a palindrome.
Both cost `n - LPS`. Same number.

---

## 🧠 The Full Reduction Chain

```
Min Insertions to Make Palindrome
  = n - LPS(s)
  = n - LCS(s, reverse(s))
```

Three levels. One formula. One existing algorithm.

---

### 📜 The Scroll of the Missing Mirror Pieces

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🪞 The LCS Memoization (Unchanged)

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

Standard LCS. Not a line changed.

---

## ✂️ The Wrapper -- One Formula

```cpp
int minInsertions(string s) {
    int n = s.size();
    string rev = s;
    reverse(rev.begin(), rev.end());
```

Reverse the string to prepare for LCS.

---

### Compute LPS via LCS

```cpp
    vector<vector<int>> memo(n, vector<int>(n, -1));
    int lps = solve(n - 1, n - 1, s, rev, memo);
```

LPS = LCS(s, reverse(s)). Already proven.

---

### The Answer -- One Subtraction

```cpp
    return n - lps;
}
```

The characters NOT in the LPS need to be mirrored.
Each needs one insertion. Total = `n - LPS`.

> _"The palindrome skeleton is already there --
> it's the LPS.
> The missing pieces are `n - LPS` characters
> that need their mirrors inserted."_

---

### 🎺 The Trial of the Missing Mirrors

```cpp
int main() {
    cout << minInsertions("zzazz") << endl;    // expected: 0
    cout << minInsertions("mbadm") << endl;    // expected: 2
    cout << minInsertions("leetcode") << endl; // expected: 5
    cout << minInsertions("a") << endl;        // expected: 0
    return 0;
}
```

---

**Trace for s = "zzazz":**

```
s = "zzazz", rev = "zzazz"   ← already a palindrome!
LCS("zzazz", "zzazz") = 5 = n.
minInsertions = 5 - 5 = 0.
```

**Answer: 0** ✓ (already a palindrome, nothing to insert)

---

**Trace for s = "mbadm":**

```
s = "mbadm", rev = "dabm"... wait.
s = "mbadm", rev = "mdabm".

LCS("mbadm", "mdabm"):
```

Let me build the table:

```
     ""  m  d  a  b  m
""    0  0  0  0  0  0
m     0  1  1  1  1  1
b     0  1  1  1  2  2
a     0  1  1  2  2  2
d     0  1  2  2  2  2
m     0  1  2  2  2  3
```

LCS = 3. LPS = 3 (subsequence "mam" or "mdm"... actually "mam": m at pos 0, a at pos 2, m at pos 4).

`minInsertions = 5 - 3 = 2`.

**Answer: 2** ✓

Insert 2 characters to make it a palindrome.
One possible result: "mbdadm"... hmm, that's 6 chars, 1 insertion.
Actually: "mbadm" → "mbdadbm" (insert 'd' and 'b')? That's 7 chars, 2 insertions.
Or: "mbadabm" (insert 'a' and 'b')? Let me just trust the math: n - LPS = 5 - 3 = 2. ✓

---

**Trace for s = "leetcode":**

```
s = "leetcode", rev = "edocteel"
n = 8.
```

LCS of "leetcode" and "edocteel":

The LPS includes "ete" or "ece" (length 3).

`minInsertions = 8 - 3 = 5`.

**Answer: 5** ✓

---

## 🔍 Insertions = Deletions for Palindrome

| Operation  | Formula     | What it does                        |
| ---------- | ----------- | ----------------------------------- |
| Insertions | `n - LPS`   | Add mirror characters for non-LPS   |
| Deletions  | `n - LPS`   | Remove non-LPS characters           |

Both give the same number!

Inserting mirrors and deleting non-palindromic characters
are two sides of the same coin.
Both leave a palindrome. Both cost `n - LPS`.

---

## 🔍 The Complete Reduction Family

| Problem                          | Formula                            |
| -------------------------------- | ---------------------------------- |
| Longest Palindromic Subsequence  | `LCS(s, reverse(s))`             |
| Min Insertions for Palindrome    | `n - LCS(s, reverse(s))`         |
| Min Deletions for Palindrome     | `n - LCS(s, reverse(s))`         |
| Shortest Common Supersequence    | `m + n - LCS(s1, s2)`            |

All built on LCS. One algorithm powers them all.

---

### 🧠 Memory of the Missing Mirror Pieces (Memoization) Law

-   **`minInsertions = n - LPS(s) = n - LCS(s, reverse(s))`**
-   **LPS** = the palindromic skeleton already present
-   **`n - LPS`** = characters that need mirrors inserted
-   **Same as min deletions** for palindrome (same formula)
-   **No new DP** -- just LCS + one subtraction
-   **Time:** O(N²). **Space:** O(N²).

Thus is remembered the saga of **Min Insertions to Make Palindrome (Memoization)**,
where the Oracle found the palindromic skeleton within the string --
the LPS -- and counted the characters left outside it.
Each outsider needed one mirror.
The count of mirrors = `n - LPS` = the minimum insertions. 🪞✂️🧠✨
