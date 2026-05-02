## 🔤🔢🧠 _Counting the Hidden Copies (Memoization): The Distinct Subsequences Saga_

> \_"The Oracle was given two strings: `s` and `t`.
>
> She was commanded:
>
> **'How many distinct subsequences of `s` equal `t`?'**
>
> In other words: how many ways can you DELETE characters from `s`
> to produce `t`? Each way = one distinct subsequence.
>
> This was NOT LCS.
> LCS asked: what's the longest shared subsequence?
> This asked: how many times does `t` HIDE inside `s`?
>
> The Oracle thought about each character of `s`:
>
> **If `s[i]` matches `t[j]`:**
> Two choices:
> -   USE this match: `s[i]` pairs with `t[j]`. Both advance.
> -   SKIP this match: `s[i]` is not used. Only `s` advances.
>     Maybe a later character in `s` also matches `t[j]`.
>
> **If `s[i]` does NOT match `t[j]`:**
> Only one choice:
> -   SKIP `s[i]`. It can't help match `t[j]`. Only `s` advances.
>
> Count ALL ways. Addition, not max.
>
> This was the **match: use or skip / mismatch: skip** pattern --
> a counting variant of the LCS structure."\_

---

This is the saga of **Distinct Subsequences (LeetCode 115)**.

Given strings `s` and `t`:
-   Count the number of distinct subsequences of `s` that equal `t`.

```
Input:  s = "rabbbit", t = "rabbit"
Output: 3

Input:  s = "babgbag", t = "bag"
Output: 5
```

---

## 🧠 Building the Intuition -- Why Two Choices on Match?

Consider `s = "aab"`, `t = "ab"`.

At `s[0] = 'a'` and `t[0] = 'a'`:
-   **USE this 'a':** pair s[0] with t[0]. Now match remaining "ab" in s[1..] with "b" in t[1..].
-   **SKIP this 'a':** don't use s[0]. Match "ab" in s[1..] with "ab" in t[0..].
    Maybe s[1] = 'a' can also match t[0].

Both choices are valid. Both lead to different subsequences.
We COUNT both.

If we only used the match (like LCS), we'd miss subsequences
that use a LATER occurrence of the same character.

**On mismatch:** no choice. `s[i]` can't match `t[j]`. Skip it.

---

## 🧠 The Recurrence

```
solve(i, j):
  If j < 0: return 1       ← t is fully matched! One valid subsequence found.
  If i < 0: return 0       ← s is exhausted but t isn't. Failed.

  If s[i] == t[j]:
    return solve(i-1, j-1)  +  solve(i-1, j)
           ← USE this match     ← SKIP this match (maybe a later one works)

  Else:
    return solve(i-1, j)
           ← SKIP s[i] (can't match t[j])
```

**Why `solve(i-1, j)` on skip (not `solve(i, j-1)`)?**

We're always advancing through `s` (deleting characters from `s`).
`t` only advances when a match is USED.
Skipping means `s` moves forward but `t` stays -- waiting for its match.

**Why return 1 when `j < 0`?**

All characters of `t` have been matched.
The characters we chose from `s` form one valid subsequence equal to `t`.
Count it: 1.

**Why return 0 when `i < 0` but `j >= 0`?**

`s` is exhausted but `t` still has unmatched characters.
Can't form `t` from nothing. Zero ways.

---

### 📜 The Scroll of the Hidden Copies

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔤 Setting Up the Cache

```cpp
int numDistinct(string s, string t) {
    int m = s.size();
    int n = t.size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

2D cache: `memo[i][j]` = number of ways to match `t[0..j]`
using characters from `s[0..i]`.

---

```cpp
    return solve(m - 1, n - 1, s, t, memo);
}
```

Start from the ends of both strings.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, string& s, string& t,
          vector<vector<int>>& memo) {
```

"How many subsequences of `s[0..i]` equal `t[0..j]`?"

---

### 🎯 t is fully matched!

```cpp
    if (j < 0) return 1;
```

Every character of `t` has been paired with a character in `s`.
One valid subsequence found. Count it.

Why check `j` before `i`?
Because even if `i < 0`, if `j < 0` too, we've succeeded.
`j < 0` means success regardless of `i`.

---

### 🛑 s is exhausted but t isn't

```cpp
    if (i < 0) return 0;
```

`s` has no more characters but `t` still needs matching.
Impossible. Zero ways.

---

### 📖 Cache check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### ✅ Characters Match -- USE or SKIP

```cpp
    if (s[i] == t[j]) {
        int use  = solve(i - 1, j - 1, s, t, memo);
        int skip = solve(i - 1, j, s, t, memo);
        memo[i][j] = use + skip;
    }
```

**USE this match:** pair `s[i]` with `t[j]`.
Both strings shrink. `solve(i-1, j-1)`.

**SKIP this match:** don't use `s[i]` for `t[j]`.
Only `s` shrinks. `t` stays, waiting for another match.
`solve(i-1, j)`.

**Add both:** both choices lead to different valid subsequences.
Total = sum.

Why would you SKIP a match?
Because a later character in `s` might also match `t[j]`,
leading to a DIFFERENT subsequence.
Skipping explores that possibility.

> _"The characters match. But the Oracle doesn't commit.
> She explores both futures:
> one where this match is used,
> one where it's saved for a later occurrence.
> Both futures are counted."_

---

### ❌ Characters Don't Match -- Only SKIP

```cpp
    else {
        memo[i][j] = solve(i - 1, j, s, t, memo);
    }
```

`s[i]` can't match `t[j]`. No choice. Skip `s[i]`.
`s` shrinks. `t` stays.

No "use" option exists. Only skip.

---

```cpp
    return memo[i][j];
}
```

---

### 🎺 The Trial of the Hidden Copies

```cpp
int main() {
    cout << numDistinct("rabbbit", "rabbit") << endl; // expected: 3
    cout << numDistinct("babgbag", "bag") << endl;    // expected: 5
    return 0;
}
```

---

**Trace for s = "babgbag", t = "bag":**

We need to find how many ways to pick "bag" from "babgbag".

The 5 subsequences:
1. **b**a**b**g**b**ag → wait, that's "bbb". Let me be careful.

s = b a b g b a g (indices 0-6)
t = b a g

Ways to pick 'b', 'a', 'g' in order:
1. s[0]='b', s[1]='a', s[3]='g' → "bag" ✓
2. s[0]='b', s[1]='a', s[6]='g' → "bag" ✓
3. s[0]='b', s[5]='a', s[6]='g' → "bag" ✓
4. s[2]='b', s[5]='a', s[6]='g' → "bag" ✓  (wait, s[2]='b', need 'a' after index 2: s[5]='a')
5. s[4]='b', s[5]='a', s[6]='g' → "bag" ✓

**Answer: 5** ✓

---

**Trace for s = "rabbbit", t = "rabbit":**

s has three 'b's. t needs two 'b's.
The three ways to choose 2 b's from 3: C(3,2) = 3.

**Answer: 3** ✓

---

## 🔍 How This Differs from LCS

| LCS                               | Distinct Subsequences             |
| ---------------------------------- | --------------------------------- |
| Find the LONGEST common subseq    | COUNT subsequences of s equal to t|
| Match: `1 + solve(i-1, j-1)`     | Match: `solve(i-1,j-1) + solve(i-1,j)` |
| Mismatch: `max(solve(i-1,j), solve(i,j-1))` | Mismatch: `solve(i-1, j)` |
| Operation: max (optimize)          | Operation: + (count)              |
| Both strings can be "dropped"      | Only s can be dropped (t is fixed)|

The key difference: **only `s` advances on skip. `t` never skips.**

In LCS, both strings can drop characters.
Here, `t` is the TARGET -- it must be matched exactly.
Only `s` provides characters. `t` just waits.

---

## 🔍 Why `s` Always Advances but `t` Doesn't

`t` is the pattern we're trying to match.
We can't skip characters in `t` -- every character must be matched.

`s` is the source. We choose which characters to use.
Skipping a character in `s` means "I don't use this one."

So `s` always shrinks (either used or skipped).
`t` only shrinks when a match is USED.

---

### 🧠 Memory of the Hidden Copies (Memoization) Law

-   **State:** `(i, j)` -- position in s and t
-   **Match:** `solve(i-1, j-1) + solve(i-1, j)` -- USE this match + SKIP it
-   **Mismatch:** `solve(i-1, j)` -- skip s[i] (can't match)
-   **Base:** `j < 0` → 1 (t fully matched). `i < 0` → 0 (s exhausted).
-   **Check `j < 0` BEFORE `i < 0`** -- success takes priority
-   **Only s advances on skip.** t never skips.
-   **Why skip a match?** A later character in s might also match.
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Distinct Subsequences (Memoization)**,
where the Oracle counted how many times `t` hid inside `s` --
at each matching character choosing to use it or save it,
at each mismatch moving past it --
counting every possible way to extract `t` from `s`
by deleting the right characters. 🔤🔢🧠✨
