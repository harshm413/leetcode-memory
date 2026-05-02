## 🔤✂️➕📋 _The Cost of Transformation (Tabulation): The Min Insertions/Deletions to Convert Saga_

> \_"The Oracle had computed the transformation cost with memoization.
> Now she built the LCS table bottom-up
> and applied the formula."\_

---

This is the saga of **Min Insertions/Deletions to Convert String A to B (Tabulation)**.

Same formula: `total = m + n - 2 * LCS(s1, s2)`.

```
Input:  s1 = "heap", s2 = "pea"   →  Output: 3
Input:  s1 = "abcd", s2 = "anc"   →  Output: 3
```

---

### 📜 The Scroll of the Bottom-Up Transformation

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation

### Build the LCS Table

```cpp
int minOperations(string s1, string s2) {
    int m = s1.size();
    int n = s2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
```

Standard 1-indexed LCS table.

---

### Fill It

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1[i - 1] == s2[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
```

Standard LCS. Unchanged.

---

### Apply the Formula

```cpp
    int lcs = dp[m][n];
    int deletions = m - lcs;
    int insertions = n - lcs;
    return deletions + insertions;
}
```

`m - lcs` characters deleted from s1.
`n - lcs` characters inserted from s2.
Total = `m + n - 2 * lcs`.

---

### 🎺 The Trial

```cpp
int main() {
    cout << minOperations("heap", "pea") << endl;  // expected: 3
    cout << minOperations("abc", "abc") << endl;   // expected: 0
    cout << minOperations("abc", "def") << endl;   // expected: 6
    cout << minOperations("abcd", "anc") << endl;  // expected: 3
    return 0;
}
```

---

**Table for s1 = "abcd", s2 = "anc":**

```
     ""  a  n  c
""    0  0  0  0
a     0  1  1  1
b     0  1  1  1
c     0  1  1  2
d     0  1  1  2
```

LCS = dp[4][3] = 2 ("ac").

```
deletions = 4 - 2 = 2  (delete 'b' and 'd')
insertions = 3 - 2 = 1  (insert 'n')
total = 3
```

**Answer: 3** ✓

---

**Table for s1 = "heap", s2 = "pea":**

```
     ""  p  e  a
""    0  0  0  0
h     0  0  0  0
e     0  0  1  1
a     0  0  1  2
p     0  1  1  2
```

LCS = 2 ("ea").

```
deletions = 4 - 2 = 2
insertions = 3 - 2 = 1
total = 3
```

**Answer: 3** ✓

---

## 🔧 Space Optimization -- Two Rows

```cpp
int minOperations(string s1, string s2) {
    int m = s1.size(), n = s2.size();
    vector<int> prev(n + 1, 0), curr(n + 1, 0);

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1[i - 1] == s2[j - 1]) {
                curr[j] = 1 + prev[j - 1];
            } else {
                curr[j] = max(prev[j], curr[j - 1]);
            }
        }
        prev = curr;
    }
    int lcs = prev[n];
    return m + n - 2 * lcs;
}
```

Two rows. O(N) space. Same answer.

---

## 🔍 Returning Deletions and Insertions Separately

Some problems ask for the counts separately:

```cpp
pair<int, int> minOperations(string s1, string s2) {
    // ... build LCS table ...
    int lcs = dp[m][n];
    return {m - lcs, n - lcs};  // {deletions, insertions}
}
```

Or just return the total: `m + n - 2 * lcs`.

---

## 🔍 Visual Intuition

```
s1 = "abcd"     s2 = "anc"

LCS = "ac" (the shared bridge)

s1:  a  b  c  d
     ✓  ✗  ✓  ✗     → delete b, d (2 deletions)

s2:  a  n  c
     ✓  ✗  ✓         → insert n (1 insertion)

Total: 2 + 1 = 3 operations.
```

The LCS characters (✓) stay. Everything else (✗) is an operation.

---

### 🧠 Memory of the Transformation Cost (Tabulation) Law

-   **Build LCS table** of s1 and s2 (standard tabulation)
-   **`deletions = m - LCS`**, **`insertions = n - LCS`**
-   **`total = m + n - 2 * LCS`**
-   **Space optimization:** two rows → O(N)
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).

Thus is remembered the saga of **Min Insertions/Deletions to Convert (Tabulation)**,
where the Oracle built the LCS table,
found the bridge between two strings,
and counted the cost:
delete what's not on the bridge from s1,
insert what's missing from s2 --
`m + n - 2 × LCS` = the price of transformation. 🔤✂️➕📋✨
