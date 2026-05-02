## 🃏🔮🧠 _The Shape-Shifting Pattern (Memoization): The Wildcard Matching Saga_

> \_"The Oracle was given a string `s` and a pattern `p`.
>
> The pattern contained two special symbols:
> -   **`?`** -- matches exactly ONE character (any character).
> -   **`*`** -- matches ANY SEQUENCE of characters (including empty).
>
> She was commanded:
>
> **'Does the pattern match the ENTIRE string?'**
>
> The Oracle compared from the end:
>
> **If `p[j]` is a normal character:**
> It must match `s[i]` exactly. Both move back.
>
> **If `p[j]` is `?`:**
> It matches whatever `s[i]` is. Both move back.
> `?` is generous -- it accepts anyone, but exactly one.
>
> **If `p[j]` is `*`:**
> The star is the shape-shifter. Two futures:
> -   The star matches NOTHING (empty sequence). Pattern moves back, string stays.
> -   The star matches ONE MORE character from `s`. String moves back, pattern stays.
>
> The star doesn't move back when consuming a character
> because it might need to consume MORE.
> It only moves back when it chooses to match nothing."\_

---

This is the saga of **Wildcard Matching (LeetCode 44)**.

Given a string `s` and a pattern `p`:
-   `?` matches any single character.
-   `*` matches any sequence of characters (including empty).
-   Return `true` if `p` matches the ENTIRE string `s`.

```
Input:  s = "aa", p = "a"     →  Output: false
Input:  s = "aa", p = "*"     →  Output: true
Input:  s = "cb", p = "?a"    →  Output: false
Input:  s = "adceb", p = "*a*b"  →  Output: true
```

---

## 🧠 Building the Intuition -- The Three Cases

Stand at the end: `s[i]` and `p[j]`.

**Case 1 -- `p[j]` is a normal character:**

It must match `s[i]` exactly. If `s[i] == p[j]`, both consumed.
If they differ, the pattern fails. Return false.

This is the simplest case -- literal matching.

**Case 2 -- `p[j]` is `?`:**

`?` matches ANY single character. It doesn't care what `s[i]` is.
Both consumed: `solve(i-1, j-1)`.

Think of `?` as a wildcard that says "I'll take one, whatever it is."

**Case 3 -- `p[j]` is `*`:**

This is where the real thinking happens.

`*` can match an empty sequence, or one character, or two, or a hundred.
How do we handle "any number of characters" recursively?

**Two choices:**

**Choice A -- The star matches NOTHING (empty sequence):**
The star is done. It consumed zero characters from `s`.
Pattern moves back: `solve(i, j-1)`.
String stays at `i` because nothing was consumed.

**Choice B -- The star matches ONE MORE character from `s`:**
The star consumes `s[i]`. String moves back: `i-1`.
But the star STAYS at `j` -- it doesn't move back.
Why? Because it might need to consume even more characters.
`solve(i-1, j)`.

> _"The star is greedy but patient.
> When it consumes a character, it stays put --
> ready to consume another, or to finally rest."_

If EITHER choice leads to a match, the answer is true.
`solve(i, j-1) || solve(i-1, j)`.

---

## 🧠 The Base Cases -- Where Recursion Stops

**Both exhausted: `i < 0` AND `j < 0`:**
String is empty, pattern is empty. Perfect match. Return true.

**String exhausted but pattern remains: `i < 0`, `j >= 0`:**
The string is gone. The only way the remaining pattern can match
is if EVERY remaining character in `p` is `*`.
Stars can match empty. Normal characters and `?` cannot.

So we check: are `p[0..j]` ALL stars?
If yes → true. If any non-star exists → false.

**Pattern exhausted but string remains: `j < 0`, `i >= 0`:**
The pattern is gone but the string still has characters.
Nothing left in the pattern to match them. Return false.

---

## 🧠 The Recurrence

```
solve(i, j):
  If i < 0 AND j < 0: return true
  If i < 0: return allStars(p, 0..j)
  If j < 0: return false

  If p[j] == s[i] OR p[j] == '?':
    return solve(i-1, j-1)

  If p[j] == '*':
    return solve(i, j-1) || solve(i-1, j)
    ← match nothing    ← match one more char

  Else:
    return false   ← literal mismatch
```

---

### 📜 The Scroll of the Shape-Shifting Pattern

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🃏 Setting Up the Cache

```cpp
bool isMatch(string s, string p) {
    int m = s.size();
    int n = p.size();
    vector<vector<int>> memo(m, vector<int>(n, -1));
```

2D cache: `memo[i][j]` stores whether `s[0..i]` matches `p[0..j]`.
`-1` = not computed. `0` = false. `1` = true.

We use `int` instead of `bool` because we need three states:
unvisited (-1), false (0), true (1).

---

```cpp
    return solve(m - 1, n - 1, s, p, memo);
}
```

Start from the ends of both strings.

---

## 🔮 The Recursive Matching

```cpp
bool solve(int i, int j, string& s, string& p,
           vector<vector<int>>& memo) {
```

"Does `s[0..i]` match `p[0..j]`?"

---

### 🛑 Both exhausted -- perfect match

```cpp
    if (i < 0 && j < 0) return true;
```

String empty, pattern empty. Everything was matched.
Nothing left on either side. Success.

---

### 🛑 String exhausted, pattern remains

```cpp
    if (i < 0) {
        for (int k = 0; k <= j; k++) {
            if (p[k] != '*') return false;
        }
        return true;
    }
```

The string is fully consumed. But the pattern still has characters.

The ONLY way this can still be a match is if every remaining
pattern character is `*`. Stars can match empty sequences.
But if even one `?` or literal character remains,
it needs at least one string character -- which we don't have.

> _"The string is gone. The pattern still speaks.
> If every remaining voice is a star -- they all whisper 'nothing' and it works.
> If any voice is not a star -- it demands a character that doesn't exist."_

---

### 🛑 Pattern exhausted, string remains

```cpp
    if (j < 0) return false;
```

The pattern is gone but the string still has characters.
No pattern left to match them. Failure.

---

### 📖 Cache check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### ✅ Literal match or `?` -- both consumed

```cpp
    if (p[j] == s[i] || p[j] == '?') {
        memo[i][j] = solve(i - 1, j - 1, s, p, memo);
    }
```

Two sub-cases collapsed into one:

If `p[j]` is a normal character and equals `s[i]` -- exact match.
If `p[j]` is `?` -- it matches any single character.

Either way, both characters are consumed. Move both back.

> _"The character and the pattern agree -- either by equality
> or by the question mark's unconditional acceptance.
> One pair resolved. Move to the next."_

---

### 🌟 Star `*` -- the shape-shifter

```cpp
    else if (p[j] == '*') {
        bool matchNothing = solve(i, j - 1, s, p, memo);
```

**Choice A: the star matches NOTHING.**

The star decides to be empty. It consumed zero characters.
Pattern moves back (j-1), string stays (i).

Why does this work? The star says "I'll match the empty sequence."
So it's done. The rest of the pattern must handle the rest of the string.

---

```cpp
        bool matchOne = solve(i - 1, j, s, p, memo);
```

**Choice B: the star matches ONE MORE character.**

The star consumes `s[i]`. String moves back (i-1).
But the star STAYS (j) -- it doesn't move back.

Why doesn't the star move back? Because `*` matches a SEQUENCE.
After consuming one character, it might need to consume more.
It stays in position, ready to consume again on the next call.

This is the key insight: the star "eats" characters one at a time
through recursion. Each recursive call either eats one more
or decides to stop (choice A).

> _"The star consumes one character and stays.
> 'I might need more,' it says.
> Only when it chooses to match nothing does it finally step back."_

---

```cpp
        memo[i][j] = matchNothing || matchOne;
    }
```

If EITHER future leads to a match, the answer is true.
The star succeeds if matching nothing works OR matching more works.

---

### ❌ Literal mismatch -- failure

```cpp
    else {
        memo[i][j] = false;
    }
```

`p[j]` is a normal character but doesn't equal `s[i]`.
No wildcard to save us. This path fails.

---

```cpp
    return memo[i][j];
}
```

---

### 🎺 The Trial of the Shape-Shifting Pattern

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

**Trace for s = "adceb", p = "\*a\*b":**

```
solve(4, 3): 'b' vs 'b' → match. solve(3, 2).
solve(3, 2): 'e' vs '*' → star.
  matchNothing: solve(3, 1) → 'e' vs 'a' → mismatch → false.
  matchOne:     solve(2, 2) → 'c' vs '*' → star.
    matchNothing: solve(2, 1) → 'c' vs 'a' → mismatch → false.
    matchOne:     solve(1, 2) → 'd' vs '*' → star.
      matchNothing: solve(1, 1) → 'd' vs 'a' → mismatch → false.
      matchOne:     solve(0, 2) → 'a' vs '*' → star.
        matchNothing: solve(0, 1) → 'a' vs 'a' → match. solve(-1, 0).
          solve(-1, 0): i<0. Check p[0]='*'. All stars? Yes → true!
        matchOne: (not needed, already true)
      → true
    → true
  → true
→ true
```

The first `*` consumed "adc" (three characters, one at a time).
The second `*` consumed "e" (one character).
**Answer: true** ✓

---

**Trace for s = "cb", p = "?a":**

```
solve(1, 1): 'b' vs 'a' → mismatch → false.
```

**Answer: false** ✓

---

**Trace for s = "aa", p = "\*":**

```
solve(1, 0): 'a' vs '*' → star.
  matchNothing: solve(1, -1) → j<0, i>=0 → false.
  matchOne:     solve(0, 0) → 'a' vs '*' → star.
    matchNothing: solve(0, -1) → j<0, i>=0 → false.
    matchOne:     solve(-1, 0) → i<0. p[0]='*'. All stars → true!
  → true
→ true
```

The `*` consumed both 'a's. **Answer: true** ✓

---

## 🔍 Wildcard `*` vs Edit Distance -- The Structural Parallel

| Aspect              | Edit Distance (mismatch)         | Wildcard `*`                    |
| ------------------- | -------------------------------- | ------------------------------- |
| Choices             | insert, delete, replace          | match nothing, match one more   |
| Index movement      | `(i,j-1)`, `(i-1,j)`, `(i-1,j-1)` | `(i,j-1)`, `(i-1,j)`       |
| Combining           | `min` of three                   | `OR` of two                     |
| Goal                | minimize cost                    | find any valid match            |

The star's two choices mirror insert and delete from Edit Distance:
-   `solve(i, j-1)` = like "insert" (pattern shrinks, string stays)
-   `solve(i-1, j)` = like "delete" (string shrinks, pattern stays)

But here we OR (any path works) instead of MIN (cheapest path).

---

## 🔍 Wildcard `*` vs Regular Expression `*`

**Wildcard `*`** (this problem): matches any sequence independently.
It's a standalone symbol.

**Regex `*`** (LeetCode 10): means "zero or more of the PREVIOUS character."
`a*` means zero or more 'a's. `.*` means zero or more of any character.

Completely different problems despite the same symbol.
Don't confuse them.

---

### 🧠 Memory of the Shape-Shifting Pattern (Memoization) Law

-   **State:** `(i, j)` -- position in string and pattern
-   **Literal/`?` match:** `solve(i-1, j-1)` -- both consumed
-   **`*` (star):** `solve(i, j-1) || solve(i-1, j)`
    -   `(i, j-1)` = star matches nothing (pattern moves back)
    -   `(i-1, j)` = star matches one more (string moves back, star stays)
-   **Literal mismatch:** false
-   **Base cases:**
    -   Both empty → true
    -   String empty, pattern remains → true only if all remaining are `*`
    -   Pattern empty, string remains → false
-   **Time:** O(M × N). **Space:** O(M × N).

Thus is remembered the saga of **Wildcard Matching (Memoization)**,
where the Oracle faced three kinds of pattern characters --
literals demanded exact equality,
question marks accepted anyone,
and stars shape-shifted to match nothing or everything --
consuming characters one at a time through recursion,
staying in place until they chose to rest,
until the entire string was matched
or declared unmatchable. 🃏🔮🧠✨
