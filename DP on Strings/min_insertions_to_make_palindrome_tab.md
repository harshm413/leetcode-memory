## 🪞✂️📋 _The Missing Mirror Pieces (Tabulation): The Min Insertions to Make Palindrome Saga_

> \_"The Oracle had computed min insertions with memoization.
> Now she built the LCS table bottom-up
> and subtracted from n.
>
> Same formula. Iterative execution."\_

---

This is the saga of **Minimum Insertion Steps to Make a String Palindrome (Tabulation)**.

Same formula: `minInsertions = n - LCS(s, reverse(s))`.

```
Input:  s = "zzazz"     →  Output: 0
Input:  s = "mbadm"     →  Output: 2
Input:  s = "leetcode"  →  Output: 5
```

---

### 📜 The Scroll of the Bottom-Up Missing Mirrors

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 📋 The Tabulation

### Reverse and Build LCS Table

```cpp
int minInsertions(string s) {
    int n = s.size();
    string rev = s;
    reverse(rev.begin(), rev.end());
```

---

```cpp
    vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
```

Standard 1-indexed LCS table.

---

### Fill the LCS Table

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

Standard LCS tabulation. Unchanged.

---

### The Answer -- One Subtraction

```cpp
    return n - dp[n][n];
}
```

`dp[n][n]` = LCS(s, reverse(s)) = LPS(s).
`n - LPS` = minimum insertions.

---

### 🎺 The Trial

```cpp
int main() {
    cout << minInsertions("zzazz") << endl;    // expected: 0
    cout << minInsertions("mbadm") << endl;    // expected: 2
    cout << minInsertions("leetcode") << endl; // expected: 5
    return 0;
}
```

---

**Table for s = "mbadm", rev = "mdabm":**

```
     ""  m  d  a  b  m
""    0  0  0  0  0  0
m     0  1  1  1  1  1
b     0  1  1  1  2  2
a     0  1  1  2  2  2
d     0  1  2  2  2  2
m     0  1  2  2  2  3
```

LPS = dp[5][5] = 3.
`minInsertions = 5 - 3 = 2`.

**Answer: 2** ✓

---

**Table for s = "zzazz", rev = "zzazz":**

```
     ""  z  z  a  z  z
""    0  0  0  0  0  0
z     0  1  1  1  1  1
z     0  1  2  2  2  2
a     0  1  2  3  3  3
z     0  1  2  3  4  4
z     0  1  2  3  4  5
```

LPS = 5 = n. `minInsertions = 5 - 5 = 0`.

**Answer: 0** ✓ (already a palindrome)

---

## 🔧 Space Optimization -- Two Rows

```cpp
int minInsertions(string s) {
    int n = s.size();
    string rev = s;
    reverse(rev.begin(), rev.end());

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
    return n - prev[n];
}
```

Two rows. O(N) space. Same answer.

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 Why `n - LPS` Works -- Visual Intuition

```
s = "mbadm"
LPS = "mam" (length 3)

The palindromic skeleton:  m _ a _ m
The gaps:                    b     d

Insert 'd' before 'b' and 'b' after 'd':
  m b d a d b m  ← palindrome! (7 chars, 2 inserted)

Or insert differently:
  m b a d a b m  ← also palindrome! (7 chars, 2 inserted)
```

The LPS characters are already in palindromic order.
The non-LPS characters (`b` and `d`) each need one mirror.
2 insertions. Exactly `n - LPS = 5 - 3 = 2`.

---

## 🔍 Min Insertions = Min Deletions

Both equal `n - LPS`.

**Insertions:** add mirrors for the `n - LPS` non-palindromic characters.
**Deletions:** remove the `n - LPS` non-palindromic characters, leaving the LPS.

Same cost. Different operations. Same formula.

If a problem asks for min deletions instead of min insertions --
the answer is identical: `n - LCS(s, reverse(s))`.

---

### 🧠 Memory of the Missing Mirror Pieces (Tabulation) Law

-   **`minInsertions = n - dp[n][n]`** where dp = LCS table of s and reverse(s)
-   **Standard LCS tabulation** -- no changes
-   **Space optimization:** two rows → O(N)
-   **Same as min deletions** for palindrome
-   **Visual:** LPS = skeleton, `n - LPS` = gaps needing mirrors
-   **Time:** O(N²). **Space:** O(N²), optimizable to O(N).

Thus is remembered the saga of **Min Insertions to Make Palindrome (Tabulation)**,
where the Oracle built the LCS table
between the string and its reverse,
read the LPS from the bottom-right corner,
and subtracted from n --
the gap between the string's length
and its palindromic skeleton
was the number of mirrors needed. 🪞✂️📋✨
