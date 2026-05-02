## 🃏🔮📋 _The Shape-Shifting Pattern (Tabulation): The Wildcard Matching Saga_

> \_"The Oracle had matched wildcards with memoization.
> Now she built the answer bottom-up.
>
> **`dp[i][j]` = does `s[0..i-1]` match `p[0..j-1]`?**
>
> 1-indexed table. Fill row by row.
> Literal/`?`: inherit the diagonal.
> `*`: OR of left and above.
>
> The base cases were tricky --
> an empty string can match a pattern of all stars,
> but a non-empty string can never match an empty pattern."\_

---

This is the saga of **Wildcard Matching (Tabulation)**.

Same problem:
-   `?` matches any single character.
-   `*` matches any sequence of characters (including empty).
-   Return `true` if `p` matches the ENTIRE string `s`.

```
Input:  s = "aa", p = "*"        →  Output: true
Input:  s = "adceb", p = "*a*b"  →  Output: true
Input:  s = "acdcb", p = "a*c?b" →  Output: false
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][j] = does s[0..i-1] match p[0..j-1]?
```

**Base cases:**

```
dp[0][0] = true
```

Empty string matches empty pattern. Both have nothing. Perfect match.

```
dp[0][j] = true ONLY IF p[0..j-1] are ALL stars.
```

Empty string, non-empty pattern. The only way this works
is if every pattern character so far is `*`.
Stars can match empty. Anything else needs at least one character.

The moment we hit a non-star, `dp[0][j]` and everything after it is false.

```
dp[i][0] = false   for i > 0.
```

Non-empty string, empty pattern. Nothing in the pattern
to match the string characters. Always false.

**Transition:**

```
If p[j-1] == s[i-1] OR p[j-1] == '?':
  dp[i][j] = dp[i-1][j-1]          ← diagonal (both consumed)

If p[j-1] == '*':
  dp[i][j] = dp[i][j-1] || dp[i-1][j]
             ← match nothing  ← match one more

Else:
  dp[i][j] = false                  ← literal mismatch
```

---

## 🧠 Why `dp[i][j-1] || dp[i-1][j]` for Star?

**`dp[i][j-1]`** = the cell to the LEFT.
The star matches NOTHING. Pattern shrinks (j-1), string stays (i).
"Can `s[0..i-1]` match `p[0..j-2]`?" -- the pattern without this star.

**`dp[i-1][j]`** = the cell ABOVE.
The star matches one more character from `s`. String shrinks (i-1), pattern stays (j).
"Can `s[0..i-2]` match `p[0..j-1]`?" -- the string without its last character,
but the star is still there, ready to consume more.

If EITHER is true, the star succeeds.

> _"Left = the star rests (matches nothing).
> Above = the star consumes one more.
> If either world is a match, this world is too."_

---

### 📜 The Scroll of the Bottom-Up Shape-Shifting Pattern

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
bool isMatch(string s, string p) {
    int m = s.size();
    int n = p.size();
    vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));
```

`(m+1) × (n+1)` boolean table. 1-indexed.
Row `i` represents `s[0..i-1]`. Column `j` represents `p[0..j-1]`.
Everything starts as `false`.

---

### 🏁 Base Case -- Empty string, empty pattern

```cpp
    dp[0][0] = true;
```

Nothing matches nothing. The only cell that starts true.

---

### 🏁 Base Case -- Empty string, pattern of stars

```cpp
    for (int j = 1; j <= n; j++) {
        if (p[j - 1] == '*') {
            dp[0][j] = dp[0][j - 1];
        }
    }
```

For an empty string, `dp[0][j]` is true only if ALL of `p[0..j-1]` are stars.

How does this loop achieve that? It propagates `dp[0][j-1]` forward.
-   `dp[0][0] = true`. If `p[0]` is `*`, `dp[0][1] = dp[0][0] = true`.
-   If `p[1]` is `*`, `dp[0][2] = dp[0][1] = true`. And so on.
-   The moment `p[j-1]` is NOT a star, `dp[0][j]` stays `false` (default).
-   Once false, it can never become true again because `dp[0][j-1]` is false.

> _"Stars propagate truth along the first row.
> The first non-star breaks the chain forever."_

For pattern `"**a*"`:
`dp[0][1] = true` (star), `dp[0][2] = true` (star),
`dp[0][3] = false` ('a' breaks it), `dp[0][4] = false` (chain broken).

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
```

Row 0 (empty string) is handled. Column 0 (empty pattern) is all false
except `dp[0][0]`. Now fill the rest.

---

### ✅ Literal match or `?` -- diagonal

```cpp
            if (p[j - 1] == s[i - 1] || p[j - 1] == '?') {
                dp[i][j] = dp[i - 1][j - 1];
            }
```

`p[j-1]` is either the same character as `s[i-1]`,
or it's `?` which matches any single character.

Both characters are consumed. The answer depends entirely
on whether the prefixes without these characters matched.
That's the diagonal: `dp[i-1][j-1]`.

> _"One character from each side, resolved.
> The diagonal holds the history of everything before this pair."_

---

### 🌟 Star `*` -- left OR above

```cpp
            else if (p[j - 1] == '*') {
                dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
            }
```

**`dp[i][j-1]`** (LEFT): star matches nothing.
The star is skipped. Does `s[0..i-1]` match `p[0..j-2]`?

**`dp[i-1][j]`** (ABOVE): star matches one more character.
`s[i-1]` is consumed by the star. Does `s[0..i-2]` match `p[0..j-1]`?
The star stays in the pattern -- it might consume more.

The beauty of tabulation: the "above" cell was already computed.
If the star consumed characters earlier in this column,
that information is already baked into `dp[i-1][j]`.

> _"The star looks left: 'What if I match nothing?'
> The star looks above: 'What if I consume one more?'
> If either world succeeded, I succeed too."_

---

### ❌ Literal mismatch -- false

```cpp
            else {
                dp[i][j] = false;
            }
```

`p[j-1]` is a normal character that doesn't match `s[i-1]`.
No wildcard to save us. This cell is false.

(This is technically redundant since the default is false,
but it makes the logic explicit and readable.)

---

```cpp
        }
    }
    return dp[m][n];
}
```

The bottom-right cell: does the full string match the full pattern?

---

### 🎺 The Trial of the Bottom-Up Shape-Shifting Pattern

```cpp
int main() {
    cout << boolalpha;
    cout << isMatch("aa", "a") << endl;        // expected: false
    cout << isMatch("aa", "*") << endl;         // expected: true
    cout << isMatch("cb", "?a") << endl;        // expected: false
    cout << isMatch("adceb", "*a*b") << endl;   // expected: true
    cout << isMatch("acdcb", "a*c?b") << endl;  // expected: false
    return 0;
}
```

---

**Full table for s = "adceb", p = "\*a\*b":**

```
       ""   *     a     *     b
  ""    T    T     F     F     F
  a     F    T     T     T     F
  d     F    T     F     T     F
  c     F    T     F     T     F
  e     F    T     F     T     F
  b     F    T     F     T     T
```

**Base cases:**
-   `dp[0][0] = T` (empty matches empty).
-   `dp[0][1] = T` (pattern `"*"` matches empty).
-   `dp[0][2] = F` (pattern `"*a"` -- 'a' breaks the star chain).

**Key cells explained:**

| (i,j) | s[i-1] | p[j-1] | Rule     | Calculation                        | dp[i][j] |
| ------ | ------ | ------ | -------- | ---------------------------------- | --------- |
| (1,1)  | a      | *      | star     | left=T \|\| above=F               | **T**     |
| (1,2)  | a      | a      | match    | diag dp[0][1]=T                    | **T**     |
| (1,3)  | a      | *      | star     | left=T \|\| above=T               | **T**     |
| (2,1)  | d      | *      | star     | left=F \|\| above=T               | **T**     |
| (2,3)  | d      | *      | star     | left=F \|\| above=T               | **T**     |
| (5,4)  | b      | b      | match    | diag dp[4][3]=T                    | **T**     |

**Answer: dp[5][4] = true** ✓

The first `*` (column 1) is true for every row -- it can match
any prefix of `s`. The 'a' at column 2 only matches row 1.
The second `*` (column 3) propagates truth downward.
The 'b' at column 4 matches only at row 5.

---

**Full table for s = "acdcb", p = "a\*c?b":**

```
       ""   a     *     c     ?     b
  ""    T    F     F     F     F     F
  a     F    T     T     F     F     F
  c     F    F     T     T     F     F
  d     F    F     T     F     T     F
  c     F    F     T     T     F     F
  b     F    F     T     F     T     F
```

**Answer: dp[5][5] = false** ✓

The `?` at column 4 needs a match at column 3 diagonally.
At row 5 (b), `dp[5][4]` checks diagonal `dp[4][3] = T` → `dp[5][4] = T`.
But then 'b' at column 5 checks diagonal `dp[4][4] = F` → `dp[5][5] = F`.

The pattern `"a*c?b"` requires the last three characters to be
"c(any)b". The string ends with "dcb" -- 'd' matches `?`, 'c' matches 'c',
but the alignment doesn't work out for the full pattern.

---

**Full table for s = "aa", p = "\*":**

```
       ""   *
  ""    T    T
  a     F    T
  a     F    T
```

The star column is all true -- `*` matches any prefix.
**Answer: dp[2][1] = true** ✓

---

## 🔧 Space Optimization -- Two Rows

Each row depends only on the previous row.

```cpp
bool isMatch(string s, string p) {
    int m = s.size(), n = p.size();
    vector<bool> prev(n + 1, false);
    vector<bool> curr(n + 1, false);
```

---

### Base case -- first row

```cpp
    prev[0] = true;
    for (int j = 1; j <= n; j++) {
        if (p[j - 1] == '*') prev[j] = prev[j - 1];
    }
```

`prev` is the first row: empty string vs pattern prefixes.

---

### Fill row by row

```cpp
    for (int i = 1; i <= m; i++) {
        curr[0] = false;
```

Empty pattern never matches a non-empty string.

---

```cpp
        for (int j = 1; j <= n; j++) {
            if (p[j - 1] == s[i - 1] || p[j - 1] == '?') {
                curr[j] = prev[j - 1];
            } else if (p[j - 1] == '*') {
                curr[j] = curr[j - 1] || prev[j];
            } else {
                curr[j] = false;
            }
        }
```

`prev[j-1]` = diagonal. `prev[j]` = above. `curr[j-1]` = left.

Match/`?`: diagonal. Star: left OR above. Mismatch: false.

---

```cpp
        prev = curr;
    }
    return prev[n];
}
```

```
Time:  O(M × N)
Space: O(N)
```

Present the 2D table first. Mention this as a follow-up.

---

## 🔍 How the Star Column Works -- A Visual Insight

Look at any `*` column in the table. Once a `true` appears,
every cell below it in that column is also `true`.

Why? Because `dp[i][j] = dp[i][j-1] || dp[i-1][j]`.
If `dp[i-1][j]` is true (above is true), then `dp[i][j]` is true.
And then `dp[i+1][j]` sees `dp[i][j]` as its "above" -- also true.

Truth cascades downward through star columns.
This is the star consuming more and more characters.

> _"Once a star finds a match, it never loses it.
> Every additional character is just one more the star can consume."_

---

## 🔍 Wildcard Matching vs Edit Distance -- Table Structure

| Aspect              | Edit Distance                    | Wildcard Matching               |
| ------------------- | -------------------------------- | ------------------------------- |
| Cell type           | `int` (cost)                     | `bool` (match/no match)         |
| Match               | diagonal (no cost)               | diagonal (inherit)              |
| Mismatch            | 1 + min(left, above, diag)       | false                           |
| Special (`*`)       | N/A                              | left OR above                   |
| Base row            | `0, 1, 2, 3, ...`              | `T, T/F depending on stars`     |
| Base column         | `0, 1, 2, 3, ...`              | `T, F, F, F, ...`              |

Both use the same table structure and similar neighbor lookups.
The difference: Edit Distance always has a path (minimum cost),
while Wildcard Matching is boolean (match or no match).

---

### 🧠 Memory of the Shape-Shifting Pattern (Tabulation) Law

-   **State:** `dp[i][j]` = does `s[0..i-1]` match `p[0..j-1]`?
-   **Base:** `dp[0][0] = true`. `dp[0][j] = true` only if `p[0..j-1]` all stars.
    `dp[i>0][0] = false`.
-   **Literal/`?`:** `dp[i][j] = dp[i-1][j-1]` (diagonal)
-   **`*`:** `dp[i][j] = dp[i][j-1] || dp[i-1][j]` (left OR above)
-   **Mismatch:** `dp[i][j] = false`
-   **Star columns:** truth cascades downward once established
-   **Answer:** `dp[m][n]`
-   **Space optimization:** two rows → O(N)
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Wildcard Matching (Tabulation)**,
where the Oracle filled the matching table row by row --
literals and question marks looked to the diagonal,
stars looked left and above, seeking any path to truth --
truth cascading down star columns like water,
consuming characters one by one --
until the bottom-right cell declared
whether the shape-shifting pattern
had embraced the entire string. 🃏🔮📋✨
