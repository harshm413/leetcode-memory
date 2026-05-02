## 🔤🔢📋 _Counting the Hidden Copies (Tabulation): The Distinct Subsequences Saga_

> \_"The Oracle had counted distinct subsequences with memoization.
> Now she built the answer bottom-up.
>
> **`dp[i][j]` = number of subsequences of `s[0..i-1]`
> that equal `t[0..j-1]`.**
>
> 1-indexed table. Fill row by row.
> Match: use + skip. Mismatch: skip only."\_

---

This is the saga of **Distinct Subsequences (Tabulation)**.

Same problem:
-   Count subsequences of `s` that equal `t`.

```
Input:  s = "rabbbit", t = "rabbit"  →  Output: 3
Input:  s = "babgbag", t = "bag"     →  Output: 5
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][j] = number of subsequences of s[0..i-1] that equal t[0..j-1].
```

**Base cases:**

```
dp[i][0] = 1 for all i.
```

Why? `t` is empty (j=0). There's exactly ONE subsequence of any string
that equals the empty string: the empty subsequence. Always 1.

```
dp[0][j] = 0 for j > 0.
```

Why? `s` is empty (i=0) but `t` is not. Can't form `t` from nothing. Zero ways.

**`dp[0][0] = 1`:** both empty. One way (empty matches empty).

**Transition:**

```
If s[i-1] == t[j-1]:
  dp[i][j] = dp[i-1][j-1] + dp[i-1][j]
             ← USE match      ← SKIP match

Else:
  dp[i][j] = dp[i-1][j]
             ← SKIP s[i-1]
```

---

### 📜 The Scroll of the Bottom-Up Hidden Copies

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
int numDistinct(string s, string t) {
    int m = s.size();
    int n = t.size();
    vector<vector<unsigned long long>> dp(m + 1, vector<unsigned long long>(n + 1, 0));
```

Using `unsigned long long` because the count can be very large.
Some test cases overflow `int`.

---

### 🏁 Base Case -- Empty t

```cpp
    for (int i = 0; i <= m; i++) {
        dp[i][0] = 1;
    }
```

Empty `t` can be formed from any prefix of `s` in exactly one way:
use no characters (the empty subsequence).

This is the column `j = 0`. All 1s.

---

### 🏁 Base Case -- Empty s (Already Handled)

Row `i = 0` is already all zeros (from initialization),
except `dp[0][0] = 1` (set above).

Empty `s` can't form any non-empty `t`. Zero ways.

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
```

---

### ✅ Characters Match -- USE + SKIP

```cpp
            if (s[i - 1] == t[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
            }
```

**`dp[i-1][j-1]`** = USE this match.
Pair `s[i-1]` with `t[j-1]`. Both consumed.
How many ways were there before this pair? That many.

**`dp[i-1][j]`** = SKIP this match.
Don't use `s[i-1]`. `t[j-1]` still needs matching.
How many ways without `s[i-1]`? That many.

**Sum** = total ways with and without using this character.

---

### ❌ Mismatch -- SKIP Only

```cpp
            else {
                dp[i][j] = dp[i - 1][j];
            }
```

`s[i-1]` can't match `t[j-1]`. Skip it.
The count is the same as without `s[i-1]`.

---

```cpp
        }
    }
    return dp[m][n];
}
```

---

### 🎺 The Trial of the Bottom-Up Hidden Copies

```cpp
int main() {
    cout << numDistinct("rabbbit", "rabbit") << endl; // expected: 3
    cout << numDistinct("babgbag", "bag") << endl;    // expected: 5
    return 0;
}
```

---

**Full table for s = "babgbag", t = "bag":**

```
     ""  b  a  g
""    1  0  0  0
b     1  1  0  0
a     1  1  1  0
b     1  2  1  0
g     1  2  1  1
b     1  3  1  1
a     1  3  4  1
g     1  3  4  5
```

**Key cells explained:**

| (i,j) | s[i-1] | t[j-1] | Match? | Calculation                    | dp[i][j] |
| ------ | ------ | ------ | ------ | ------------------------------ | --------- |
| (1,1)  | b      | b      | ✓      | dp[0][0] + dp[0][1] = 1+0     | 1         |
| (3,1)  | b      | b      | ✓      | dp[2][0] + dp[2][1] = 1+1     | **2**     |
| (5,1)  | b      | b      | ✓      | dp[4][0] + dp[4][1] = 1+2     | **3**     |
| (6,2)  | a      | a      | ✓      | dp[5][1] + dp[5][2] = 3+1     | **4**     |
| (7,3)  | g      | g      | ✓      | dp[6][2] + dp[6][3] = 4+1     | **5**     |

**Answer: dp[7][3] = 5** ✓

The three 'b's in s give 3 ways to start.
Combined with the 'a' and 'g' choices, total = 5.

---

**Table for s = "rabbbit", t = "rabbit":**

```
      ""  r  a  b  b  i  t
""     1  0  0  0  0  0  0
r      1  1  0  0  0  0  0
a      1  1  1  0  0  0  0
b      1  1  1  1  0  0  0
b      1  1  1  2  1  0  0
b      1  1  1  3  3  0  0
i      1  1  1  3  3  3  0
t      1  1  1  3  3  3  3
```

**Answer: dp[7][6] = 3** ✓

The three 'b's give C(3,2) = 3 ways to choose two 'b's for "rabbit".

---

## 🔧 Space Optimization -- Single Row, Right to Left

Each row depends only on the previous row.
We can use a 1D array, iterating RIGHT TO LEFT.

```cpp
int numDistinct(string s, string t) {
    int m = s.size(), n = t.size();
    vector<unsigned long long> dp(n + 1, 0);
    dp[0] = 1;

    for (int i = 1; i <= m; i++) {
        for (int j = n; j >= 1; j--) {
            if (s[i - 1] == t[j - 1]) {
                dp[j] = dp[j - 1] + dp[j];
            }
            // else: dp[j] stays the same (= dp[i-1][j])
        }
    }
    return dp[n];
}
```

**Right to left** because `dp[j-1]` must be the value from the PREVIOUS row
(not yet overwritten in this iteration).

On match: `dp[j] = dp[j-1] + dp[j]`.
-   `dp[j-1]` = USE (previous row, previous column = diagonal).
-   `dp[j]` before update = SKIP (previous row, same column = above).

On mismatch: `dp[j]` stays unchanged (= previous row's value). No code needed.

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 How This Differs from Other String DP Problems

| Problem              | Match case                        | Mismatch case          |
| -------------------- | --------------------------------- | ---------------------- |
| LCS                  | `1 + dp[i-1][j-1]`              | `max(dp[i-1][j], dp[i][j-1])` |
| Distinct Subsequences| `dp[i-1][j-1] + dp[i-1][j]`     | `dp[i-1][j]`          |
| Edit Distance        | `dp[i-1][j-1]`                   | `1 + min(three options)` |

Distinct Subsequences is unique:
-   Match has TWO terms (use + skip).
-   Mismatch only advances `s` (never `t`).
-   It COUNTS (addition) rather than optimizes (max/min).

---

### 🧠 Memory of the Hidden Copies (Tabulation) Law

-   **State:** `dp[i][j]` = subsequences of `s[0..i-1]` equaling `t[0..j-1]`
-   **Base:** `dp[i][0] = 1` (empty t). `dp[0][j>0] = 0` (empty s).
-   **Match:** `dp[i][j] = dp[i-1][j-1] + dp[i-1][j]` (use + skip)
-   **Mismatch:** `dp[i][j] = dp[i-1][j]` (skip only)
-   **Use `unsigned long long`** -- counts can overflow int
-   **Space optimization:** 1D, right to left
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Distinct Subsequences (Tabulation)**,
where the Oracle filled the counting table row by row --
matching characters offered two futures (use or skip),
mismatching characters offered only one (skip) --
until the bottom-right cell revealed
how many times `t` hid inside `s`. 🔤🔢📋✨
