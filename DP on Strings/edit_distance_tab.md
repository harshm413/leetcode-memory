## 🔤🔧📋 _The Cost of Transformation (Tabulation): The Edit Distance Saga_

> \_"The Oracle had found the minimum edit distance with memoization.
> Now she built the answer bottom-up.
>
> **`dp[i][j]` = minimum operations to convert
> `word1[0..i-1]` into `word2[0..j-1]`.**
>
> 1-indexed table. Fill row by row.
> Match: inherit the diagonal (no cost).
> Mismatch: 1 + min of three neighbors (insert, delete, replace).
>
> The first row and first column were not zeros --
> they were COSTS.
> Converting an empty string to a non-empty one
> requires insertions.
> Converting a non-empty string to an empty one
> requires deletions."\_

---

This is the saga of **Edit Distance (Tabulation)**.

Same problem:
-   Given two strings `word1` and `word2`.
-   Find the minimum number of operations (insert, delete, replace)
    to convert `word1` into `word2`.

```
Input:  word1 = "horse", word2 = "ros"
Output: 3   (horse → rorse → rose → ros)

Input:  word1 = "intention", word2 = "execution"
Output: 5
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][j] = minimum operations to convert word1[0..i-1] into word2[0..j-1].
```

**Base cases:**

```
dp[0][j] = j   for all j.
```

Why? `word1` is empty (i=0). To create `word2[0..j-1]` from nothing,
we must INSERT every character of word2. That's `j` insertions.

`dp[0][0] = 0` (empty to empty: zero operations).
`dp[0][1] = 1` (empty to "r": insert 'r').
`dp[0][2] = 2` (empty to "ro": insert 'r', insert 'o').
`dp[0][3] = 3` (empty to "ros": three insertions).

```
dp[i][0] = i   for all i.
```

Why? `word2` is empty (j=0). To convert `word1[0..i-1]` into nothing,
we must DELETE every character of word1. That's `i` deletions.

`dp[1][0] = 1` (delete 'h').
`dp[2][0] = 2` (delete 'h', delete 'o').
And so on.

**This is different from LCS where the base cases were all zeros.**
In LCS, an empty string has LCS length 0 with anything.
In Edit Distance, an empty string requires WORK to become non-empty.

**Transition:**

```
If word1[i-1] == word2[j-1]:     ← characters match
  dp[i][j] = dp[i-1][j-1]        ← diagonal, no cost

Else:                              ← characters don't match
  dp[i][j] = 1 + min(
    dp[i][j-1],                   ← INSERT  (left)
    dp[i-1][j],                   ← DELETE  (above)
    dp[i-1][j-1]                  ← REPLACE (diagonal)
  )
```

---

## 🧠 Why These Three Neighbors?

When characters don't match, we have three operations.
Each corresponds to a specific cell in the table:

**INSERT** → `dp[i][j-1]` (the cell to the LEFT).
We insert `word2[j-1]` into word1. Now word2's last character is matched.
word2 shrinks by one (j-1), word1 stays the same (i).
So we look at the cost of converting `word1[0..i-1]` to `word2[0..j-2]`,
then add 1 for the insertion.

**DELETE** → `dp[i-1][j]` (the cell ABOVE).
We delete `word1[i-1]`. word1 shrinks by one (i-1), word2 stays (j).
So we look at the cost of converting `word1[0..i-2]` to `word2[0..j-1]`,
then add 1 for the deletion.

**REPLACE** → `dp[i-1][j-1]` (the DIAGONAL cell).
We replace `word1[i-1]` with `word2[j-1]`. Now they match.
Both shrink by one (i-1, j-1).
So we look at the cost of converting `word1[0..i-2]` to `word2[0..j-2]`,
then add 1 for the replacement.

> _"Left = insert. Above = delete. Diagonal = replace.
> Three neighbors, three operations, three futures.
> Pick the cheapest."_

---

### 📜 The Scroll of the Bottom-Up Transformation

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
int minDistance(string word1, string word2) {
    int m = word1.size();
    int n = word2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
```

`(m+1) × (n+1)` table. 1-indexed.
Row `i` represents `word1[0..i-1]`. Column `j` represents `word2[0..j-1]`.
Row 0 = empty word1. Column 0 = empty word2.

---

### 🏁 Base Case -- Empty word1 (First Row)

```cpp
    for (int j = 0; j <= n; j++) {
        dp[0][j] = j;
    }
```

word1 is empty. To build `word2[0..j-1]` from nothing,
we need exactly `j` insertions.

`dp[0][0] = 0`: empty to empty, no work.
`dp[0][1] = 1`: insert one character.
`dp[0][2] = 2`: insert two characters.
...and so on.

> _"Starting from nothing, every character of word2
> must be conjured into existence. One insertion each."_

---

### 🏁 Base Case -- Empty word2 (First Column)

```cpp
    for (int i = 0; i <= m; i++) {
        dp[i][0] = i;
    }
```

word2 is empty. To reduce `word1[0..i-1]` to nothing,
we need exactly `i` deletions.

`dp[1][0] = 1`: delete one character.
`dp[2][0] = 2`: delete two characters.
...and so on.

> _"To reach emptiness, every character of word1
> must be erased. One deletion each."_

---

### 🔁 Fill Row by Row

```cpp
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
```

Starting from `(1,1)`, fill every cell.
Row 0 and column 0 are already filled (base cases).

---

### ✅ Characters Match -- Free Pass

```cpp
            if (word1[i - 1] == word2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            }
```

`word1[i-1]` and `word2[j-1]` are the same character.
No operation needed for this pair. Zero cost.

The answer is simply whatever it cost to convert
the prefixes WITHOUT these characters: `dp[i-1][j-1]`.

The diagonal cell. Both strings one shorter.
The matching characters are a gift -- they cost nothing.

> _"The characters already agree.
> No insert, no delete, no replace.
> Inherit the diagonal's cost and move on."_

---

### ❌ Characters Don't Match -- Three Operations

```cpp
            else {
                int insert_op  = dp[i][j - 1];
```

**INSERT:** look LEFT.
We insert `word2[j-1]` into word1. word2 shrinks (j-1), word1 stays (i).
The cost before this insertion was `dp[i][j-1]`.

Why left? After inserting, word2's last character is handled.
We still need to convert `word1[0..i-1]` to `word2[0..j-2]`.
That's the cell one column to the left.

---

```cpp
                int delete_op  = dp[i - 1][j];
```

**DELETE:** look ABOVE.
We delete `word1[i-1]`. word1 shrinks (i-1), word2 stays (j).
The cost before this deletion was `dp[i-1][j]`.

Why above? After deleting, word1's last character is gone.
We still need to convert `word1[0..i-2]` to `word2[0..j-1]`.
That's the cell one row above.

---

```cpp
                int replace_op = dp[i - 1][j - 1];
```

**REPLACE:** look DIAGONAL.
We replace `word1[i-1]` with `word2[j-1]`. Both consumed.
The cost before this replacement was `dp[i-1][j-1]`.

Why diagonal? After replacing, both characters are handled.
We need to convert `word1[0..i-2]` to `word2[0..j-2]`.
That's the cell diagonally above-left.

---

### ⚖️ Take the Cheapest + 1

```cpp
                dp[i][j] = 1 + min({insert_op, delete_op, replace_op});
            }
```

Each operation costs exactly 1.
We add 1 to whichever neighbor was cheapest.

Three futures. Three costs. The Oracle always picks the minimum.

---

```cpp
        }
    }
    return dp[m][n];
}
```

The bottom-right cell holds the answer:
minimum operations to convert the full `word1` into the full `word2`.

---

### 🎺 The Trial of the Bottom-Up Transformation

```cpp
int main() {
    cout << minDistance("horse", "ros") << endl;         // expected: 3
    cout << minDistance("intention", "execution") << endl; // expected: 5
    cout << minDistance("", "abc") << endl;               // expected: 3
    cout << minDistance("abc", "") << endl;               // expected: 3
    return 0;
}
```

---

**Full table for word1 = "horse", word2 = "ros":**

```
       ""  r  o  s
  ""    0  1  2  3
  h     1  1  2  3
  o     2  2  1  2
  r     3  2  2  2
  s     4  3  3  2
  e     5  4  4  3
```

**Base cases visible:**
-   First row: `0, 1, 2, 3` → inserting 0, 1, 2, 3 characters.
-   First column: `0, 1, 2, 3, 4, 5` → deleting 0, 1, 2, 3, 4, 5 characters.

**Key cells explained:**

| (i,j) | w1[i-1] | w2[j-1] | Match? | Calculation                              | dp[i][j] |
| ------ | ------- | ------- | ------ | ---------------------------------------- | --------- |
| (1,1)  | h       | r       | ✗      | 1+min(dp[1][0], dp[0][1], dp[0][0]) = 1+min(1,1,0) | **1** |
| (2,2)  | o       | o       | ✓      | dp[1][1] = 1                             | **1** |
| (3,1)  | r       | r       | ✓      | dp[2][0] = 2                             | **2** |
| (4,3)  | s       | s       | ✓      | dp[3][2] = 2                             | **2** |
| (5,3)  | e       | s       | ✗      | 1+min(dp[5][2], dp[4][3], dp[4][2]) = 1+min(4,2,3) | **3** |

**Answer: dp[5][3] = 3** ✓

The optimal path: horse → rorse (replace h→r) → rose (delete r) → ros (delete e).

---

**Full table for word1 = "intention", word2 = "execution":**

```
          ""  e  x  e  c  u  t  i  o  n
  ""       0  1  2  3  4  5  6  7  8  9
  i        1  1  2  3  4  5  6  6  7  8
  n        2  2  2  3  4  5  6  7  7  7
  t        3  3  3  3  4  5  5  6  7  8
  e        4  3  4  3  4  5  6  6  7  8
  n        5  4  4  4  4  5  6  7  7  7
  t        6  5  5  5  5  5  5  6  7  8
  i        7  6  6  6  6  6  6  5  6  7
  o        8  7  7  7  7  7  7  6  5  6
  n        9  8  8  8  8  8  8  7  6  5
```

**Answer: dp[9][9] = 5** ✓

---

## 🔧 Space Optimization -- Two Rows

Each row depends only on the previous row.
We can use two 1D arrays: `prev` and `curr`.

```cpp
int minDistance(string word1, string word2) {
    int m = word1.size(), n = word2.size();
    vector<int> prev(n + 1), curr(n + 1);
```

---

### Base case -- first row

```cpp
    for (int j = 0; j <= n; j++) {
        prev[j] = j;
    }
```

`prev` starts as the first row: `0, 1, 2, ..., n`.
This represents converting empty word1 to word2 prefixes.

---

### Fill row by row

```cpp
    for (int i = 1; i <= m; i++) {
        curr[0] = i;
```

Each row's first cell = `i` (deleting `i` characters from word1).
This is the first-column base case, rebuilt for each row.

---

```cpp
        for (int j = 1; j <= n; j++) {
            if (word1[i - 1] == word2[j - 1]) {
                curr[j] = prev[j - 1];
            } else {
                curr[j] = 1 + min({curr[j - 1], prev[j], prev[j - 1]});
            }
        }
```

`prev[j-1]` = diagonal (dp[i-1][j-1]).
`prev[j]` = above (dp[i-1][j]).
`curr[j-1]` = left (dp[i][j-1], already computed this row).

Match: inherit diagonal. Mismatch: 1 + min of three.

---

```cpp
        prev = curr;
    }
    return prev[n];
}
```

After processing all rows, `prev[n]` holds the answer.

```
Time:  O(M × N)
Space: O(N)
```

Present the 2D table first. Mention this as a follow-up.

---

### 🧠 Memory of the Transformation (Tabulation) Law

-   **State:** `dp[i][j]` = min ops to convert `word1[0..i-1]` to `word2[0..j-1]`
-   **Base:** `dp[0][j] = j` (insert j chars). `dp[i][0] = i` (delete i chars).
-   **Match:** `dp[i][j] = dp[i-1][j-1]` (diagonal, no cost)
-   **Mismatch:** `dp[i][j] = 1 + min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1])`
    -   Left = insert. Above = delete. Diagonal = replace.
-   **Answer:** `dp[m][n]`
-   **Space optimization:** two rows (`prev` and `curr`) → O(N)
-   **Time:** O(M × N). **Space:** O(M × N), optimizable to O(N).
-   **Backtrack** through the table to find the actual operations.

Thus is remembered the saga of **Edit Distance (Tabulation)**,
where the Oracle filled the transformation table row by row --
base cases were not zeros but costs,
matching characters inherited the diagonal for free,
mismatching characters paid 1 and chose the cheapest
of three neighbors -- insert, delete, or replace --
until the bottom-right cell revealed
the minimum price of turning one word into another. 🔤🔧📋✨
