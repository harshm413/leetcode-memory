## 🔤🖨️ _Recovering the Shared Thread: The Print Longest Common Subsequence Saga_

> \_"The Oracle had found the LENGTH of the LCS.
> But the King demanded more:
>
> **'Don't just tell me how long it is.
> SHOW me the actual subsequence.
> Print the characters.'**
>
> Finding the length was the DP.
> Printing the actual LCS was the **backtracking**.
>
> The Oracle realized:
>
> **The dp table already contains all the information
> needed to reconstruct the LCS.
> She just needed to walk it backward --
> from the bottom-right corner to the top-left --
> following the decisions that were made.**
>
> At each cell, the dp table told her:
> -   Did the characters match here? → include this character, go diagonal.
> -   Did we come from above? → text1's character was dropped, go up.
> -   Did we come from the left? → text2's character was dropped, go left.
>
> The path through the table traced the LCS."\_

---

This is the saga of **Print Longest Common Subsequence**.

Given two strings `text1` and `text2`:
-   Find and **print** the actual longest common subsequence.

```
Input:  text1 = "abcde", text2 = "ace"
Output: "ace"

Input:  text1 = "abc", text2 = "abc"
Output: "abc"

Input:  text1 = "brute", text2 = "groot"
Output: "rt"
```

---

## 🧠 The Two-Step Process

**Step 1 -- Build the LCS dp table.**
Standard LCS tabulation. Already mastered.

**Step 2 -- Backtrack through the table to recover the actual string.**
Start at `dp[m][n]`. Walk toward `dp[0][0]`.
At each cell, the table tells you what happened.

---

## 🧠 The Backtracking Logic

Stand at cell `(i, j)` in the dp table.

**If `text1[i-1] == text2[j-1]` (characters match):**

This character was part of the LCS.
Include it in the result.
Move diagonally: `(i-1, j-1)`.

Why diagonal? Because the match came from `1 + dp[i-1][j-1]`.
The diagonal cell is where the LCS was before this match.

**If `text1[i-1] != text2[j-1]` (characters don't match):**

The dp value came from `max(dp[i-1][j], dp[i][j-1])`.
Move toward the LARGER value:
-   If `dp[i-1][j] > dp[i][j-1]` → go UP: `(i-1, j)`.
-   Else → go LEFT: `(i, j-1)`.

Why follow the larger? Because that's where the LCS came from.
The dp table recorded the max. We retrace that max.

**Stop when `i == 0` or `j == 0`.**
One string is exhausted. No more characters to find.

**The characters are collected in REVERSE order**
(we walk backward). Reverse at the end.

---

### 📜 The Scroll of the Recovered Thread

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🖨️ Step 1 -- Build the LCS Table (Same as Before)

```cpp
string printLCS(string text1, string text2) {
    int m = text1.size();
    int n = text2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
```

Standard 1-indexed LCS table.

---

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
```

Identical to LCS tabulation. Nothing new.
After this, `dp[m][n]` = length of LCS.
But we need the table itself for backtracking.

---

## 🖨️ Step 2 -- Backtrack to Recover the LCS

### Start at the Bottom-Right

```cpp
    string lcs = "";
    int i = m, j = n;
```

Begin at `(m, n)` -- the cell that holds the full LCS length.
`lcs` will accumulate the characters (in reverse).

---

### Walk Until One String Is Exhausted

```cpp
    while (i > 0 && j > 0) {
```

Keep walking as long as both strings have characters left.
When either `i` or `j` reaches 0, we're done.

---

### ✅ Characters Match -- Include and Go Diagonal

```cpp
        if (text1[i - 1] == text2[j - 1]) {
            lcs += text1[i - 1];
            i--;
            j--;
        }
```

The characters at this position matched.
This character is part of the LCS. Add it to `lcs`.

Move diagonally -- both strings shrink by 1.

Why diagonal? Because the match was computed as `1 + dp[i-1][j-1]`.
The "before this match" state is at `(i-1, j-1)`.

> _"The characters match.
> They are part of the shared thread.
> Record this character.
> Step diagonally -- both strings gave up one character."_

---

### ❌ Mismatch -- Follow the Larger Value

```cpp
        else if (dp[i - 1][j] > dp[i][j - 1]) {
            i--;
        }
```

The characters didn't match.
The dp value came from `max(dp[i-1][j], dp[i][j-1])`.

If `dp[i-1][j]` was larger → the LCS came from dropping text1's character.
Move UP.

> _"Text1's character was not needed.
> The LCS was better without it.
> Step up -- text1 shrinks, text2 stays."_

---

```cpp
        else {
            j--;
        }
    }
```

If `dp[i][j-1]` was larger (or equal) → the LCS came from dropping text2's character.
Move LEFT.

> _"Text2's character was not needed.
> Step left -- text2 shrinks, text1 stays."_

---

### Reverse the Result

```cpp
    reverse(lcs.begin(), lcs.end());
    return lcs;
}
```

We walked backward (from end to start),
so the characters were collected in reverse order.
Reverse to get the correct order.

---

### 🎺 The Trial of the Recovered Thread

```cpp
int main() {
    cout << printLCS("abcde", "ace") << endl;   // expected: "ace"
    cout << printLCS("abc", "abc") << endl;     // expected: "abc"
    cout << printLCS("brute", "groot") << endl; // expected: "rt"
    return 0;
}
```

---

**Full backtracking trace for text1 = "abcde", text2 = "ace":**

**The dp table:**

```
     ""  a  c  e
""    0  0  0  0
a     0  1  1  1
b     0  1  1  1
c     0  1  2  2
d     0  1  2  2
e     0  1  2  3
```

**Backtracking from (5, 3):**

| (i, j) | text1[i-1] | text2[j-1] | Match? | Action                    | lcs so far |
| ------- | ---------- | ---------- | ------ | ------------------------- | ---------- |
| (5, 3)  | 'e'        | 'e'        | ✓      | Include 'e'. Go (4, 2).  | "e"        |
| (4, 2)  | 'd'        | 'c'        | ✗      | dp[3][2]=2 vs dp[4][1]=1. Up. Go (3, 2). | "e" |
| (3, 2)  | 'c'        | 'c'        | ✓      | Include 'c'. Go (2, 1).  | "ec"       |
| (2, 1)  | 'b'        | 'a'        | ✗      | dp[1][1]=1 vs dp[2][0]=0. Up. Go (1, 1). | "ec" |
| (1, 1)  | 'a'        | 'a'        | ✓      | Include 'a'. Go (0, 0).  | "eca"      |
| (0, 0)  | --         | --         | --     | i==0. Stop.               | "eca"      |

**Reverse "eca" → "ace"**

**Answer: "ace"** ✓

---

**Backtracking trace for text1 = "brute", text2 = "groot":**

**The dp table:**

```
     ""  g  r  o  o  t
""    0  0  0  0  0  0
b     0  0  0  0  0  0
r     0  0  1  1  1  1
u     0  0  1  1  1  1
t     0  0  1  1  1  2
e     0  0  1  1  1  2
```

**Backtracking from (5, 5):**

| (i, j) | text1[i-1] | text2[j-1] | Match? | Action                    | lcs so far |
| ------- | ---------- | ---------- | ------ | ------------------------- | ---------- |
| (5, 5)  | 'e'        | 't'        | ✗      | dp[4][5]=2 vs dp[5][4]=1. Up. | "" |
| (4, 5)  | 't'        | 't'        | ✓      | Include 't'. Go (3, 4).  | "t"        |
| (3, 4)  | 'u'        | 'o'        | ✗      | dp[2][4]=1 vs dp[3][3]=1. Equal → left. | "t" |
| (3, 3)  | 'u'        | 'o'        | ✗      | dp[2][3]=1 vs dp[3][2]=1. Equal → left. | "t" |
| (3, 2)  | 'u'        | 'r'        | ✗      | dp[2][2]=1 vs dp[3][1]=0. Up. | "t" |
| (2, 2)  | 'r'        | 'r'        | ✓      | Include 'r'. Go (1, 1).  | "tr"       |
| (1, 1)  | 'b'        | 'g'        | ✗      | dp[0][1]=0 vs dp[1][0]=0. Equal → left. | "tr" |
| (1, 0)  | --         | --         | --     | j==0. Stop.               | "tr"       |

**Reverse "tr" → "rt"**

**Answer: "rt"** ✓

---

## 🔍 Why Backtracking Works

The dp table records the OPTIMAL decisions at every cell.
Backtracking simply REPLAYS those decisions in reverse.

At each cell:
-   Match → the optimal decision was to include this character.
-   Mismatch → the optimal decision was to drop one character.
    The larger neighbor tells us WHICH one was dropped.

We're not solving anything new.
We're just reading the decisions the DP already made.

---

## 🔍 What If dp[i-1][j] == dp[i][j-1] on Mismatch?

When both neighbors are equal, EITHER direction is valid.
Both lead to a valid LCS of the same length.

Our code goes LEFT (`j--`) when they're equal.
Going UP (`i--`) would also be correct -- just a different LCS.

Multiple valid LCS strings may exist.
This algorithm returns ONE of them.

---

## 🔍 Why Collect in Reverse and Then Reverse?

We walk from `(m, n)` to `(0, 0)` -- backward.
Characters are encountered from the END of the LCS to the START.

Appending to a string and reversing at the end is O(n).
Prepending at each step would be O(n²) (string shifting).

Reverse at the end is the efficient approach.

---

### 🧠 Memory of the Recovered Thread Law

-   **Step 1:** Build the standard LCS dp table (tabulation)
-   **Step 2:** Backtrack from `(m, n)` to `(0, 0)`
    -   Match → include character, go diagonal `(i-1, j-1)`
    -   Mismatch → go toward larger neighbor (up or left)
-   **Collect characters in reverse** → reverse at the end
-   **Equal neighbors on mismatch** → either direction is valid (different valid LCS)
-   **Time:** O(M × N) for table + O(M + N) for backtracking
-   **Space:** O(M × N) for the table

Thus is remembered the saga of **Print Longest Common Subsequence**,
where the Oracle did not just measure the shared thread
but walked the dp table backward --
following matches diagonally,
following mismatches toward the larger neighbor --
collecting each character of the LCS in reverse
until the full shared thread was spelled out. 🔤🖨️✨
