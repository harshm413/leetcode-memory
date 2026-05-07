## 🔀🔤 _The Alternating Weave: The Merge Strings Alternately Saga_

> \_"The Oracle was given two strings.
>
> She was commanded:
>
> **'Merge them by alternating characters.
> Start with the first string, then the second,
> then the first, then the second...
> If one string is longer, append the remaining characters at the end.'**
>
> `"abc"` + `"pqr"` → `"apbqcr"`.
> `"ab"` + `"pqrs"` → `"apbqrs"`.
>
> The Oracle used two pointers — one on each string.
> Take one from the first, one from the second.
> Alternate. When one runs out, drain the other."\_

---

This is the saga of **Merge Strings Alternately (LeetCode 1768)**.

Given two strings `word1` and `word2`:
-   Merge them by alternating characters starting with `word1`.
-   If one is longer, append the remaining at the end.

```
Input:  word1 = "abc", word2 = "pqr"
Output: "apbqcr"

Input:  word1 = "ab", word2 = "pqrs"
Output: "apbqrs"

Input:  word1 = "abcd", word2 = "pq"
Output: "apbqcd"
```

---

## 🧠 The Approach — Two Pointers, Alternate

Two pointers: `i` on word1, `j` on word2.
At each step, take from word1 (if available), then from word2 (if available).
Continue until both are exhausted.

Alternatively: use a single index and check bounds.

---

### 📜 The Scroll of the Alternating Weave

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## 🔀 The Solution — Single Index Approach

```cpp
string mergeAlternately(string word1, string word2) {
    string result = "";
    int i = 0, j = 0;
```

---

### Alternate while both have characters

```cpp
    while (i < word1.size() && j < word2.size()) {
        result += word1[i++];
        result += word2[j++];
    }
```

Take one from word1, one from word2. Both advance.
This continues until one string is exhausted.

> _"One from the left, one from the right.
> They weave together, character by character,
> until one string runs dry."_

---

### Append remaining from word1

```cpp
    while (i < word1.size()) {
        result += word1[i++];
    }
```

If word1 is longer, its remaining characters go at the end.

---

### Append remaining from word2

```cpp
    while (j < word2.size()) {
        result += word2[j++];
    }
    return result;
}
```

If word2 is longer, its remaining characters go at the end.

Only ONE of these drain loops will execute (or neither if equal length).

---

### 🎺 The Trial of the Alternating Weave

```cpp
int main() {
    cout << mergeAlternately("abc", "pqr") << endl;   // expected: apbqcr
    cout << mergeAlternately("ab", "pqrs") << endl;   // expected: apbqrs
    cout << mergeAlternately("abcd", "pq") << endl;   // expected: apbqcd
    cout << mergeAlternately("", "xyz") << endl;       // expected: xyz
    cout << mergeAlternately("abc", "") << endl;       // expected: abc
    return 0;
}
```

---

**Trace for word1 = "ab", word2 = "pqrs":**

```
Alternate loop:
  i=0: result += 'a'. j=0: result += 'p'. → "ap"
  i=1: result += 'b'. j=1: result += 'q'. → "apbq"
  i=2: i >= word1.size(). Loop ends.

Drain word2:
  j=2: result += 'r'. → "apbqr"
  j=3: result += 's'. → "apbqrs"
```

**Result: "apbqrs"** ✓

---

**Trace for word1 = "abcd", word2 = "pq":**

```
Alternate loop:
  'a','p' → "ap"
  'b','q' → "apbq"
  j=2: j >= word2.size(). Loop ends.

Drain word1:
  'c' → "apbqc"
  'd' → "apbqcd"
```

**Result: "apbqcd"** ✓

---

**Trace for word1 = "abc", word2 = "pqr" (equal length):**

```
Alternate loop:
  'a','p' → "ap"
  'b','q' → "apbq"
  'c','r' → "apbqcr"
  Both exhausted. Loop ends.

No drain needed.
```

**Result: "apbqcr"** ✓

---

## 🔍 Alternative — Single Loop with Index

```cpp
string mergeAlternately(string word1, string word2) {
    string result = "";
    int n = max(word1.size(), word2.size());
    for (int i = 0; i < n; i++) {
        if (i < word1.size()) result += word1[i];
        if (i < word2.size()) result += word2[i];
    }
    return result;
}
```

Single loop up to the longer string's length.
At each index, add from word1 (if exists) then word2 (if exists).
Cleaner code, same result.

---

## 🔍 Edge Cases

**One string empty:** result = the other string entirely.
**Equal length:** perfect alternation, no drain needed.
**One much longer:** alternation for the short part, then drain the rest.
**Both empty:** result = "".

---

## 🔍 This vs Merge Sorted Arrays

| Aspect | Merge Sorted Arrays | Merge Alternately (this) |
|--------|--------------------|--------------------------| 
| Order | Sorted order (compare values) | Strict alternation (no comparison) |
| Logic | Take the SMALLER | Take from word1 THEN word2 |
| Drain | Same (append remaining) | Same (append remaining) |

Both use two pointers + drain. The merge logic differs.

---

### 🧠 Memory of the Alternating Weave Law

-   **Two pointers:** `i` on word1, `j` on word2
-   **Alternate:** take from word1, then word2, repeat
-   **Drain:** append remaining from whichever is longer
-   **Single-loop alternative:** iterate to `max(n, m)`, check bounds
-   **Time:** O(N + M). **Space:** O(N + M) for the result.

Thus is remembered the saga of **Merge Strings Alternately**,
where the Oracle wove two strings together —
one character from the left, one from the right,
alternating until one ran out —
then draining the remainder at the end —
a simple two-pointer dance
producing the interleaved result. 🔀🔤✨
