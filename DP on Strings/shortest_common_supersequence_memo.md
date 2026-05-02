## 🔤🧵🧠 _The Shortest String That Contains Both (Memoization): The Shortest Common Supersequence Saga_

> \_"The Oracle was given two strings: `s1` and `s2`.
>
> She was commanded:
>
> **'Find the shortest string that contains BOTH s1 and s2
> as subsequences.'**
>
> Not substrings -- subsequences.
> The result must contain every character of s1 in order,
> AND every character of s2 in order.
> Shared characters should be used ONCE, not duplicated.
>
> The Oracle thought about what the two strings SHARE:
>
> **The LCS.**
>
> The LCS characters appear in both strings.
> In the supersequence, they should appear ONCE (shared).
> The non-LCS characters from s1 must be included.
> The non-LCS characters from s2 must be included.
>
> **Length of SCS = len(s1) + len(s2) - LCS.**
>
> But the problem asked for the actual STRING, not just the length.
>
> To build the string, the Oracle needed to BACKTRACK
> through the LCS table --
> the same technique as Print LCS,
> but this time including ALL characters, not just the matches."\_

---

This is the saga of **Shortest Common Supersequence**.

Given two strings `s1` and `s2`:
-   Find the shortest string that has both `s1` and `s2` as subsequences.

```
Input:  s1 = "brute", s2 = "groot"
Output: "bgruoote" (length 8. Contains "brute" and "groot" as subsequences.)
        Actually let me verify: LCS = "rt" (length 2). SCS length = 5+5-2 = 8.
        One valid SCS: "bgruoote"

Input:  s1 = "abac", s2 = "cab"
Output: "cabac" (length 5. LCS = "ab" length 2. SCS = 4+3-2 = 5.)
```

---

## 🧠 Building the Intuition -- Why `m + n - LCS`?

The supersequence must contain ALL characters of s1 and ALL of s2.

If we just concatenated them: length = `m + n`.
But the LCS characters appear in BOTH strings.
Including them twice is wasteful.
Share them: length = `m + n - LCS`.

**Example: s1 = "abac", s2 = "cab"**

```
LCS = "ab" (length 2)

Naive concatenation: "abaccab" (length 7)
Sharing the LCS:     "cabac"  (length 5 = 4+3-2)
```

In "cabac":
-   s1 = "abac" → c**a**b**a**c → positions 1,2,3,4 → "abac" ✓
    Wait, let me re-check: "cabac" contains "abac"?
    c-**a**-**b**-**a**-**c** → "abac" at positions 1,2,3,4. ✓
-   s2 = "cab" → **c**-**a**-**b**-a-c → "cab" at positions 0,1,2. ✓

Both are subsequences. Length 5 = 4+3-2. ✓

---

## 🧠 Building the Actual String -- Backtracking the LCS Table

Finding the LENGTH is easy: `m + n - LCS`.

Finding the actual STRING requires backtracking through the LCS dp table.

The idea is similar to Print LCS, but instead of only collecting
matched characters, we collect ALL characters:

**At cell (i, j):**

**If characters match:**
Include this character ONCE (it's shared).
Move diagonally: `(i-1, j-1)`.

**If they don't match:**
We need to include the character that was "dropped" by the LCS.
Move toward the larger dp value.
-   If `dp[i-1][j] > dp[i][j-1]` → s1's character was dropped.
    Include `s1[i-1]` in the result. Move UP: `(i-1, j)`.
-   Else → s2's character was dropped.
    Include `s2[j-1]` in the result. Move LEFT: `(i, j-1)`.

**After the main loop:**
One string might have remaining characters.
Append them all.

**Reverse the result** (we built it backward).

---

### 📜 The Scroll of the Shortest Container

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🧵 Step 1 -- Build the LCS Table

```cpp
string shortestCommonSupersequence(string s1, string s2) {
    int m = s1.size();
    int n = s2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
```

Standard 1-indexed LCS table.

---

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

Standard LCS tabulation. Unchanged.

---

## 🧵 Step 2 -- Backtrack to Build the SCS

### Start at the Bottom-Right

```cpp
    string scs = "";
    int i = m, j = n;
```

Begin at `(m, n)`. Build the SCS in reverse.

---

### Walk Until Both Strings Are Exhausted

```cpp
    while (i > 0 && j > 0) {
```

---

### ✅ Characters Match -- Include ONCE, Go Diagonal

```cpp
        if (s1[i - 1] == s2[j - 1]) {
            scs += s1[i - 1];
            i--;
            j--;
        }
```

This character is in the LCS. It's shared.
Include it ONCE in the supersequence.
Move diagonally -- both strings consumed this character.

> _"The characters match.
> They are shared between both strings.
> Include once. Both strings step back."_

---

### ❌ Mismatch -- Include the Dropped Character

```cpp
        else if (dp[i - 1][j] > dp[i][j - 1]) {
            scs += s1[i - 1];
            i--;
        }
```

The LCS came from above (`dp[i-1][j]` was larger).
That means s1's character at position `i-1` was NOT in the LCS.
But it's still part of s1 -- so it MUST be in the supersequence.

Include `s1[i-1]`. Move up.

> _"This character belongs to s1 but not the LCS.
> It must still appear in the supersequence.
> Include it. Step up in s1."_

---

```cpp
        else {
            scs += s2[j - 1];
            j--;
        }
    }
```

The LCS came from the left (`dp[i][j-1]` was larger or equal).
s2's character was not in the LCS.
Include `s2[j-1]`. Move left.

> _"This character belongs to s2 but not the LCS.
> Include it. Step left in s2."_

---

### Append Remaining Characters

```cpp
    while (i > 0) {
        scs += s1[i - 1];
        i--;
    }
    while (j > 0) {
        scs += s2[j - 1];
        j--;
    }
```

One string might have characters left after the other is exhausted.
These characters are NOT in the LCS (the other string is empty).
They must all be included in the supersequence.

---

### Reverse the Result

```cpp
    reverse(scs.begin(), scs.end());
    return scs;
}
```

We built the string backward (walking from end to start).
Reverse to get the correct order.

---

### 🎺 The Trial of the Shortest Container

```cpp
int main() {
    cout << shortestCommonSupersequence("brute", "groot") << endl;
    // expected: one valid SCS of length 8

    cout << shortestCommonSupersequence("abac", "cab") << endl;
    // expected: "cabac" (length 5)

    return 0;
}
```

---

**Full backtracking trace for s1 = "abac", s2 = "cab":**

**LCS table:**

```
     ""  c  a  b
""    0  0  0  0
a     0  0  1  1
b     0  0  1  2
a     0  0  1  2
c     0  1  1  2
```

LCS = 2 ("ab").

**Backtracking from (4, 3):**

| (i,j) | s1[i-1] | s2[j-1] | Match? | dp comparison          | Action                | scs    |
| ------ | ------- | ------- | ------ | ---------------------- | --------------------- | ------ |
| (4,3)  | 'c'     | 'b'     | ✗      | dp[3][3]=2 vs dp[4][2]=1 | Up. Include 'c'.     | "c"    |
| (3,3)  | 'a'     | 'b'     | ✗      | dp[2][3]=2 vs dp[3][2]=1 | Up. Include 'a'.     | "ca"   |
| (2,3)  | 'b'     | 'b'     | ✓      | Match!                 | Include 'b'. Diagonal.| "cab"  |
| (1,2)  | 'a'     | 'a'     | ✓      | Match!                 | Include 'a'. Diagonal.| "caba" |
| (0,1)  | --      | --      | --     | i=0. Exit main loop.   |                       | "caba" |

**Remaining: j=1 → s2[0]='c'. Append 'c'.**

scs = "cabac".

**Reverse "cabac" → "cabac"** (palindrome-like, same reversed!)

**Answer: "cabac"** ✓

Verify:
-   s1 = "abac" in "cabac": c-**a**-**b**-**a**-**c** → "abac" ✓
-   s2 = "cab" in "cabac": **c**-**a**-**b**-a-c → "cab" ✓
-   Length = 5 = 4+3-2 ✓

---

**Trace for s1 = "brute", s2 = "groot":**

```
LCS("brute", "groot") = "rt" (length 2).
SCS length = 5 + 5 - 2 = 8.
```

Backtracking produces one valid SCS of length 8.
Multiple valid answers exist (different orderings of non-LCS characters).

---

## 🔍 Print LCS vs Build SCS -- The Difference

| Print LCS                         | Build SCS                         |
| --------------------------------- | --------------------------------- |
| Include ONLY matched characters   | Include ALL characters            |
| Skip mismatched characters        | Include the dropped character     |
| Result = LCS only                 | Result = full supersequence       |
| Length = LCS                      | Length = m + n - LCS              |

Same backtracking path. Different inclusion rule.

In Print LCS: match → include, mismatch → skip.
In Build SCS: match → include once, mismatch → include the dropped one.

---

## 🔍 Why This Problem Needs the Actual Table

Unlike "SCS length" (which is just `m + n - LCS`),
building the actual SCS string requires backtracking.
Backtracking needs the full dp table.

So this problem MUST use tabulation (or fill the memo table completely).
The 1D space optimization CANNOT be used here
because we need the full 2D table for backtracking.

---

### 🧠 Memory of the Shortest Container Law

-   **SCS length = m + n - LCS** (formula for length only)
-   **Building the string:** backtrack through the LCS dp table
-   **Match:** include character ONCE, go diagonal
-   **Mismatch:** include the dropped character, go toward larger dp value
-   **Remaining characters:** append after main loop
-   **Reverse** the result (built backward)
-   **Same backtracking as Print LCS** but include ALL characters, not just matches
-   **Full 2D table required** -- cannot use space optimization
-   **Time:** O(M × N) + O(M + N). **Space:** O(M × N).

Thus is remembered the saga of **Shortest Common Supersequence**,
where the Oracle backtracked through the LCS table --
including matched characters once (shared),
including mismatched characters always (unique to one string) --
building the shortest string that contained
both original strings as subsequences. 🔤🧵🧠✨
