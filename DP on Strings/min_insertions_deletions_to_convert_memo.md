## 🔤✂️➕🧠 _The Cost of Transformation (Memoization): The Min Insertions/Deletions to Convert Saga_

> \_"The Oracle was given two strings: `s1` and `s2`.
>
> She was commanded:
>
> **'What is the minimum number of insertions and deletions
> needed to convert s1 into s2?'**
>
> She could DELETE characters from s1.
> She could INSERT characters into s1.
> She wanted the fewest total operations.
>
> The Oracle thought about what STAYS the same
> during the transformation:
>
> **The Longest Common Subsequence.**
>
> The LCS characters are already in both strings.
> They don't need to be touched.
> Everything else must be deleted from s1
> or inserted from s2.
>
> **Deletions = characters in s1 that are NOT in the LCS.**
> These exist in s1 but not in s2. Remove them.
>
> **Insertions = characters in s2 that are NOT in the LCS.**
> These exist in s2 but not in s1. Add them.
>
> `deletions = len(s1) - LCS`
> `insertions = len(s2) - LCS`
> `total operations = deletions + insertions`
>
> One LCS computation. Two subtractions. Done."\_

---

This is the saga of **Minimum Insertions/Deletions to Convert String A to B (Memoization)**.

Given two strings `s1` and `s2`:
-   Find the minimum number of **deletions from s1** and **insertions into s1**
    to transform s1 into s2.

```
Input:  s1 = "heap", s2 = "pea"
Output: deletions = 2, insertions = 1, total = 3
(delete 'h' and 'p' from "heap" → "ea", insert 'p' before 'e' → "pea")
Actually: LCS("heap", "pea") = "ea" (length 2).
deletions = 4 - 2 = 2. insertions = 3 - 2 = 1. total = 3. ✓

Input:  s1 = "abc", s2 = "abc"
Output: 0  (already identical, LCS = 3, deletions = 0, insertions = 0)

Input:  s1 = "abc", s2 = "def"
Output: 6  (delete all 3 from s1, insert all 3 from s2. LCS = 0.)
```

---

## 🧠 Building the Intuition -- What Stays, What Goes, What Comes

Think of the transformation as three groups of characters:

```
Characters in BOTH s1 and s2 (the LCS):
  → Keep them. No operation needed.

Characters in s1 but NOT in s2:
  → DELETE them from s1. They don't belong in s2.

Characters in s2 but NOT in s1:
  → INSERT them into s1. They're needed in s2.
```

**Example: s1 = "heap", s2 = "pea"**

```
LCS = "ea" (length 2)

s1 = "heap":  h, e, a, p
               ↑        ↑
               delete    delete    (h and p are not in LCS)
               
s2 = "pea":   p, e, a
               ↑
               insert              (p is not in LCS... wait, 'p' IS in s1)
```

Hmm, let me reconsider. LCS of "heap" and "pea":

```
     ""  p  e  a
""    0  0  0  0
h     0  0  0  0
e     0  0  1  1
a     0  0  1  2
p     0  1  1  2
```

LCS = 2 ("ea").

Characters in s1 not in LCS: 'h' and 'p' → 2 deletions.
Characters in s2 not in LCS: 'p' → 1 insertion.

Total = 2 + 1 = 3. ✓

---

## 🧠 The Formulas

```
LCS = LCS(s1, s2)

deletions  = len(s1) - LCS    (remove s1's non-LCS characters)
insertions = len(s2) - LCS    (add s2's non-LCS characters)
total      = deletions + insertions
           = len(s1) + len(s2) - 2 * LCS
```

**Why `len(s1) - LCS` deletions?**

s1 has `len(s1)` characters. `LCS` of them are shared with s2.
The remaining `len(s1) - LCS` are unique to s1 → must be deleted.

**Why `len(s2) - LCS` insertions?**

s2 has `len(s2)` characters. `LCS` of them are already in s1.
The remaining `len(s2) - LCS` are unique to s2 → must be inserted.

**The total simplifies to `m + n - 2 * LCS`.**

---

### 📜 The Scroll of the Transformation Cost

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔤 The LCS Memoization (Unchanged)

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

## ✂️➕ The Wrapper -- Two Subtractions

```cpp
int minOperations(string s1, string s2) {
    int m = s1.size();
    int n = s2.size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

---

### Compute LCS

```cpp
    int lcs = solve(m - 1, n - 1, s1, s2, memo);
```

The shared characters. These stay untouched.

---

### Compute Deletions and Insertions

```cpp
    int deletions = m - lcs;
    int insertions = n - lcs;
    return deletions + insertions;
}
```

**`m - lcs`** = characters in s1 that must be removed.
**`n - lcs`** = characters in s2 that must be added.
**Total** = `m + n - 2 * lcs`.

> _"The LCS is the bridge between the two strings.
> Everything in s1 that's not on the bridge must be torn down.
> Everything in s2 that's not on the bridge must be built.
> The total cost = demolition + construction."_

---

### 🎺 The Trial of the Transformation Cost

```cpp
int main() {
    cout << minOperations("heap", "pea") << endl;   // expected: 3
    cout << minOperations("abc", "abc") << endl;    // expected: 0
    cout << minOperations("abc", "def") << endl;    // expected: 6
    cout << minOperations("abcd", "anc") << endl;   // expected: 3
    return 0;
}
```

---

**Trace for s1 = "heap", s2 = "pea":**

```
LCS("heap", "pea") = 2 ("ea")
deletions = 4 - 2 = 2  (delete 'h' and 'p' from s1)
insertions = 3 - 2 = 1  (insert 'p' into result)
total = 2 + 1 = 3
```

**Answer: 3** ✓

---

**Trace for s1 = "abc", s2 = "abc":**

```
LCS = 3 (identical strings)
deletions = 3 - 3 = 0
insertions = 3 - 3 = 0
total = 0
```

**Answer: 0** ✓ (no operations needed)

---

**Trace for s1 = "abc", s2 = "def":**

```
LCS = 0 (no common characters)
deletions = 3 - 0 = 3  (delete all of s1)
insertions = 3 - 0 = 3  (insert all of s2)
total = 6
```

**Answer: 6** ✓

---

**Trace for s1 = "abcd", s2 = "anc":**

```
LCS("abcd", "anc") = 2 ("ac")
deletions = 4 - 2 = 2  (delete 'b' and 'd')
insertions = 3 - 2 = 1  (insert 'n')
total = 3
```

**Answer: 3** ✓

---

## 🔍 Connection to Edit Distance

Edit Distance (LeetCode 72) allows three operations:
insert, delete, and REPLACE.

This problem allows only insert and delete (no replace).

Without replace, converting one character to another
costs 2 operations (delete old + insert new).
With replace, it costs 1.

```
Min insertions + deletions = m + n - 2 * LCS
Edit distance (with replace) ≤ m + n - 2 * LCS
```

Edit distance is always ≤ this answer
because replace is cheaper than delete + insert.

---

## 🔍 The LCS Reduction Family -- Now Complete

| Problem                          | Formula                            |
| -------------------------------- | ---------------------------------- |
| LCS length                       | `LCS(s1, s2)`                     |
| Shortest Common Supersequence    | `m + n - LCS`                     |
| Min insertions + deletions (this)| `m + n - 2 * LCS`                |
| Longest Palindromic Subsequence  | `LCS(s, reverse(s))`             |
| Min insertions for palindrome    | `n - LCS(s, reverse(s))`         |

All built on one algorithm: LCS.

---

### 🧠 Memory of the Transformation Cost (Memoization) Law

-   **`deletions = len(s1) - LCS`** (remove s1's non-shared characters)
-   **`insertions = len(s2) - LCS`** (add s2's non-shared characters)
-   **`total = m + n - 2 * LCS`**
-   **LCS = the bridge** between the two strings. Everything else is cost.
-   **No new DP** -- just LCS + two subtractions
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Min Insertions/Deletions to Convert (Memoization)**,
where the Oracle found the bridge between two strings --
the LCS -- and counted the cost of transformation:
tear down what's not on the bridge from s1,
build what's missing from s2 --
the total cost = `m + n - 2 × LCS`. 🔤✂️➕🧠✨
