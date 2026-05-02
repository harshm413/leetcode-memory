## 🔤🔧🧠 _The Cost of Transformation (Memoization): The Edit Distance Saga_

> \_"The Oracle was given two strings: `word1` and `word2`.
>
> She was commanded:
>
> **'What is the minimum number of operations
> to convert word1 into word2?'**
>
> Three operations were allowed:
> -   **Insert** a character.
> -   **Delete** a character.
> -   **Replace** a character with another.
>
> Each operation cost 1.
>
> The Oracle compared the strings from the end,
> character by character:
>
> **If the last characters MATCH:**
> No operation needed for these characters.
> They're already the same. Move both strings back.
> Cost = 0 + solve the rest.
>
> **If they DON'T match:**
> Three choices. Try all three. Take the cheapest.
> -   **Insert:** add word2's character to word1. word2 shrinks.
> -   **Delete:** remove word1's character. word1 shrinks.
> -   **Replace:** change word1's character to word2's. Both shrink.
> Cost = 1 + min of all three.
>
> This was the most complete string DP problem --
> three operations, three recursive calls on mismatch,
> and the match case from LCS."\_

---

This is the saga of **Edit Distance (LeetCode 72)**.

Given two strings `word1` and `word2`:
-   Find the minimum number of operations (insert, delete, replace)
    to convert `word1` into `word2`.

```
Input:  word1 = "horse", word2 = "ros"
Output: 3   (horse → rorse → rose → ros)

Input:  word1 = "intention", word2 = "execution"
Output: 5
```

---

## 🧠 Building the Intuition -- Three Operations, Three Futures

Stand at the last characters: `word1[i]` and `word2[j]`.

**Case 1 -- They MATCH:**

No operation needed. These characters are already aligned.
Move both back: `solve(i-1, j-1)`.
Cost = 0 (no operation for this pair).

This is identical to the LCS match case.

**Case 2 -- They DON'T match:**

We must perform ONE operation. Which one?

**INSERT:** add `word2[j]` to the end of word1.
Now word1's end matches word2's end.
word2 shrinks (j matched), word1 stays (i still needs processing).
`1 + solve(i, j-1)`

Why `solve(i, j-1)`? We inserted word2[j] into word1,
so word2[j] is now matched. word2 moves back.
But word1[i] is still there -- it wasn't touched.

**DELETE:** remove `word1[i]`.
word1 shrinks (i removed), word2 stays (j still needs matching).
`1 + solve(i-1, j)`

Why `solve(i-1, j)`? We deleted word1[i].
It's gone. word1 moves back.
But word2[j] still needs to be matched.

**REPLACE:** change `word1[i]` to `word2[j]`.
Now they match. Both shrink.
`1 + solve(i-1, j-1)`

Why `solve(i-1, j-1)`? After replacing, word1[i] == word2[j].
Both are consumed. Both move back.

**Take the minimum of all three.**

---

## 🧠 The Recurrence

```
solve(i, j):
  If i < 0: return j + 1     ← word1 exhausted. Insert remaining j+1 chars of word2.
  If j < 0: return i + 1     ← word2 exhausted. Delete remaining i+1 chars of word1.

  If word1[i] == word2[j]:
    return solve(i-1, j-1)                    ← match: no cost

  Else:
    insert  = 1 + solve(i, j-1)              ← insert word2[j]
    delete  = 1 + solve(i-1, j)              ← delete word1[i]
    replace = 1 + solve(i-1, j-1)            ← replace word1[i] with word2[j]
    return min({insert, delete, replace})
```

**Base cases explained:**

**`i < 0` (word1 exhausted):**
word1 is empty. word2 still has `j + 1` characters.
We must INSERT all of them. Cost = `j + 1`.

**`j < 0` (word2 exhausted):**
word2 is empty. word1 still has `i + 1` characters.
We must DELETE all of them. Cost = `i + 1`.

---

### 📜 The Scroll of the Three Operations

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔤 Setting Up the Cache

```cpp
int minDistance(string word1, string word2) {
    int m = word1.size();
    int n = word2.size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

2D cache: `memo[i][j]` = min operations to convert
`word1[0..i]` to `word2[0..j]`.

---

```cpp
    return solve(m - 1, n - 1, word1, word2, memo);
}
```

Start from the ends of both strings.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, string& word1, string& word2,
          vector<vector<int>>& memo) {
```

"What's the minimum operations to convert `word1[0..i]` to `word2[0..j]`?"

---

### 🛑 word1 exhausted -- insert the rest of word2

```cpp
    if (i < 0) return j + 1;
```

word1 is empty. word2 has `j + 1` characters left.
Insert all of them. Cost = `j + 1`.

Why `j + 1` and not `j`? Because `j` is 0-indexed.
`j = 0` means 1 character left. `j = 2` means 3 characters.

---

### 🛑 word2 exhausted -- delete the rest of word1

```cpp
    if (j < 0) return i + 1;
```

word2 is empty. word1 has `i + 1` characters left.
Delete all of them. Cost = `i + 1`.

---

### 📖 Cache check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### ✅ Characters MATCH -- no operation needed

```cpp
    if (word1[i] == word2[j]) {
        memo[i][j] = solve(i - 1, j - 1, word1, word2, memo);
    }
```

The characters are already the same.
No operation. Zero cost for this pair.
Solve the remaining prefixes.

This is the "free" case. Matches are gifts.

> _"The characters already agree.
> No work needed. Move on to the next pair."_

---

### ❌ Characters DON'T match -- three operations

```cpp
    else {
        int insert  = 1 + solve(i, j - 1, word1, word2, memo);
```

**INSERT** word2[j] into word1.
word2[j] is now matched → `j - 1`.
word1[i] is untouched → `i` stays.
Cost: 1 operation.

> _"I add the character word2 needs.
> word2 is satisfied and steps back.
> But word1 still has its character -- unresolved."_

---

```cpp
        int del     = 1 + solve(i - 1, j, word1, word2, memo);
```

**DELETE** word1[i].
word1[i] is removed → `i - 1`.
word2[j] still needs matching → `j` stays.
Cost: 1 operation.

> _"I remove word1's character.
> It's gone. word1 steps back.
> But word2 still waits for its match."_

---

```cpp
        int replace = 1 + solve(i - 1, j - 1, word1, word2, memo);
```

**REPLACE** word1[i] with word2[j].
Now they match → both consumed → `i - 1, j - 1`.
Cost: 1 operation.

> _"I change word1's character to match word2's.
> Now they agree. Both step back."_

---

### ⚖️ Take the cheapest

```cpp
        memo[i][j] = min({insert, del, replace});
    }
    return memo[i][j];
}
```

Three futures. Three costs. Pick the cheapest.

---

### 🎺 The Trial of the Three Operations

```cpp
int main() {
    cout << minDistance("horse", "ros") << endl;         // expected: 3
    cout << minDistance("intention", "execution") << endl; // expected: 5
    return 0;
}
```

---

**Trace for word1 = "horse", word2 = "ros":**

```
solve(4, 2): 'e' vs 's' → mismatch
  insert:  1 + solve(4, 1)  → ...
  delete:  1 + solve(3, 2)  → ...
  replace: 1 + solve(3, 1)  → ...
```

The recursion explores all paths. The optimal:

```
horse → rorse  (replace h with r)     cost 1
rorse → rose   (delete r at index 2)  cost 1
rose  → ros    (delete e)             cost 1
Total: 3
```

**Answer: 3** ✓

---

**Trace for word1 = "intention", word2 = "execution":**

```
intention → exention   (replace i with e)     cost 1
exention  → exection   (replace n with c)     cost 1
exection  → execution  (insert u)             cost 1
... (more steps)
Total: 5
```

**Answer: 5** ✓

---

## 🔍 The Three Operations -- Visual Summary

```
word1 = "abc", word2 = "adc"

At position (1, 1): 'b' vs 'd' → mismatch

INSERT 'd':  word1 becomes "adbc", word2 matched 'd' → solve("adbc"[..], "ac"[..])
             Effectively: solve(i, j-1)

DELETE 'b':  word1 becomes "ac" → solve("ac"[..], "adc"[..])
             Effectively: solve(i-1, j)

REPLACE 'b' with 'd': word1 becomes "adc" → both matched → solve("a"[..], "a"[..])
             Effectively: solve(i-1, j-1)
```

---

## 🔍 How Edit Distance Relates to Other String DP

| Problem              | Match                    | Mismatch                          |
| -------------------- | ------------------------ | --------------------------------- |
| LCS                  | `1 + dp[i-1][j-1]`     | `max(dp[i-1][j], dp[i][j-1])`   |
| Distinct Subseq      | `dp[i-1][j-1] + dp[i-1][j]` | `dp[i-1][j]`              |
| **Edit Distance**    | **`dp[i-1][j-1]`**     | **`1 + min(three options)`**     |

Edit Distance is the most general:
three operations on mismatch (insert, delete, replace).
LCS has two options. Distinct Subsequences has one.

---

## 🔍 Connection to Min Insertions/Deletions

Min insertions + deletions (no replace) = `m + n - 2 * LCS`.
Edit distance (with replace) ≤ `m + n - 2 * LCS`.

Replace is cheaper than delete + insert (1 operation vs 2).
So edit distance is always ≤ the insert/delete-only cost.

---

### 🧠 Memory of the Three Operations (Memoization) Law

-   **State:** `(i, j)` -- position in word1 and word2
-   **Match:** `solve(i-1, j-1)` -- no cost, both consumed
-   **Mismatch -- three operations:**
    -   Insert: `1 + solve(i, j-1)` -- word2 shrinks, word1 stays
    -   Delete: `1 + solve(i-1, j)` -- word1 shrinks, word2 stays
    -   Replace: `1 + solve(i-1, j-1)` -- both shrink
    -   Take `min` of all three
-   **Base:** `i < 0` → `j+1` (insert rest). `j < 0` → `i+1` (delete rest).
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Edit Distance (Memoization)**,
where the Oracle faced three choices at every mismatch --
insert, delete, or replace --
each leading to a different future,
each costing one operation --
and she always chose the cheapest path,
transforming one word into another
with the fewest possible changes. 🔤🔧🧠✨
