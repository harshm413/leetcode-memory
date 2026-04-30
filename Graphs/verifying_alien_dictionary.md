## 👽📖 _The Foreign Alphabet: The Verifying an Alien Dictionary Saga_

> \_"In a distant galaxy,
> an alien civilization used the same 26 letters --
> but in a completely different order.
>
> Their alphabet was not `a, b, c, d, ...`
> but some strange permutation like `h, l, a, b, c, ...`
>
> The Oracle was given:
> -   A list of words claimed to be sorted
>     in this alien alphabetical order.
> -   The alien alphabet order as a string.
>
> She was commanded:
>
> **'Verify whether the words are truly sorted
> according to this alien dictionary order.'**
>
> The Oracle devised a two-step ritual:
>
> **Step One -- Build a rank map.**
> Map each letter to its position in the alien order.
> `order[0]` has rank 0, `order[1]` has rank 1, etc.
>
> **Step Two -- Compare adjacent words.**
> For every consecutive pair of words,
> check if the first word comes before the second
> in the alien order.
>
> If every pair is in order -- the list is sorted.
> If any pair is out of order -- it is not.
>
> The comparison of two words followed
> the same logic as dictionary comparison --
> character by character, first difference decides --
> but using alien ranks instead of English ranks."\_

---

This is the saga of **Verifying an Alien Dictionary**.

Given:
-   A list of `words`.
-   A string `order` representing the alien alphabet.

Return `true` if the words are sorted in the alien dictionary order.

```
Input:  words = ["hello","leetcode"], order = "hlabcdefgijkmnopqrstuvwxyz"
Output: true   ('h' comes before 'l' in alien order)

Input:  words = ["word","world","row"], order = "worldabcefghijkmnpqstuvxyz"
Output: false  ("world" > "word" because 'l' > 'd' in alien order... 
                 actually "word" vs "world": first 4 chars match, 
                 "word" is shorter → "word" < "world" ✓
                 but "world" vs "row": 'w' vs 'r', alien rank of 'w'=0, 'r'=3 → 'w' < 'r' ✗)

Input:  words = ["apple","app"], order = "abcdefghijklmnopqrstuvwxyz"
Output: false  ("app" is a prefix of "apple", but "apple" comes first → wrong)
```

---

## 🧠 The Oracle's Core Insight -- Rank Map + Pairwise Comparison

**Step 1: Build the rank map.**

The alien order string tells us the priority of each letter.
`order = "hlabcde..."` means `h` has rank 0, `l` has rank 1, `a` has rank 2, etc.

Store this in an array: `rank[ch] = position in alien order`.

**Step 2: Compare every adjacent pair.**

If `words[i]` and `words[i+1]` are in order for every `i`,
the entire list is sorted.

Two words are compared character by character:
-   First position where they differ → the one with the smaller alien rank comes first.
-   If all compared characters are equal and one word is shorter →
    the shorter word must come first (like "app" before "apple").

```
Time:  O(total characters across all words)
Space: O(1) -- rank array of size 26 (constant)
```

---

### 📜 The Scroll of the Foreign Alphabet

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🗺️ Step One -- Build the Rank Map

```cpp
bool isAlienSorted(vector<string>& words, string order) {
    int rank[26];
    for (int i = 0; i < 26; i++) {
        rank[order[i] - 'a'] = i;
    }
```

The Oracle mapped every letter to its alien rank.

`order[i] - 'a'` converted the character to an index 0-25.
`rank[that index] = i` stored its position in the alien alphabet.

Now `rank['h' - 'a']` might be 0 (if `h` is the first alien letter),
and `rank['a' - 'a']` might be 15 (if `a` is the 15th).

> _"In the alien world, 'a' is not first.
> The rank map translates every letter
> from its English identity to its alien priority."_

---

## 🔁 Step Two -- Compare Every Adjacent Pair

```cpp
    for (int i = 0; i < (int)words.size() - 1; i++) {
        if (!isInOrder(words[i], words[i + 1], rank)) {
            return false;
        }
    }
    return true;
}
```

The Oracle walked through every consecutive pair.

If ANY pair was out of order → the list was not sorted. Return `false`.

If all pairs passed → the list was sorted. Return `true`.

> _"A sorted list is one where every adjacent pair is in order.
> If even one pair is wrong, the whole list fails."_

---

## 🔍 The Comparison -- Are Two Words in Alien Order?

```cpp
bool isInOrder(string& a, string& b, int rank[]) {
    int len = min(a.size(), b.size());
```

The Oracle compared the two words character by character,
up to the length of the shorter word.

---

### 🔤 Character-by-Character Comparison

```cpp
    for (int i = 0; i < len; i++) {
        if (rank[a[i] - 'a'] < rank[b[i] - 'a']) {
            return true;
        }
        if (rank[a[i] - 'a'] > rank[b[i] - 'a']) {
            return false;
        }
    }
```

At each position `i`, the Oracle compared the alien ranks
of `a[i]` and `b[i]`.

**If `a[i]` has a smaller rank** → `a` comes first. In order. Return `true`.

**If `a[i]` has a larger rank** → `a` comes after `b`. Out of order. Return `false`.

**If equal** → move to the next character. This position doesn't decide.

> _"The first difference decides everything.
> If 'h' has rank 0 and 'l' has rank 1,
> then 'hello' comes before 'leetcode' --
> no matter what the remaining characters are."_

---

### 📏 Prefix Rule -- Shorter Word Must Come First

```cpp
    return a.size() <= b.size();
}
```

If all compared characters were equal
(one word is a prefix of the other):

**If `a` is shorter or equal** → `a` comes first. In order. Return `true`.
**If `a` is longer** → `a` should come after `b`. Out of order. Return `false`.

This is the **prefix rule**: `"app"` comes before `"apple"`,
but `"apple"` does NOT come before `"app"`.

> _"A word that is a prefix of another
> must always come first.
> 'app' before 'apple' -- never the reverse."_

---

### 🎺 The Trial of the Foreign Alphabet

```cpp
int main() {
    vector<string> w1 = {"hello", "leetcode"};
    string o1 = "hlabcdefgijkmnopqrstuvwxyz";
    cout << isAlienSorted(w1, o1) << endl; // expected: 1 (true)

    vector<string> w2 = {"word", "world", "row"};
    string o2 = "worldabcefghijkmnpqstuvxyz";
    cout << isAlienSorted(w2, o2) << endl; // expected: 0 (false)

    vector<string> w3 = {"apple", "app"};
    string o3 = "abcdefghijklmnopqrstuvwxyz";
    cout << isAlienSorted(w3, o3) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full trace for words = ["hello", "leetcode"], order = "hlabcdefgijkmnopqrstuvwxyz":**

**Rank map (partial):**
`h=0, l=1, a=2, b=3, c=4, d=5, e=6, ...`

**Compare "hello" vs "leetcode":**

| Position | a[i] | b[i] | rank[a[i]] | rank[b[i]] | Result       |
| -------- | ---- | ---- | ---------- | ---------- | ------------ |
| 0        | 'h'  | 'l'  | 0          | 1          | 0 < 1 → **in order** |

First character decides. `h` (rank 0) < `l` (rank 1). Return `true`.

**Answer: true** ✓

---

**Full trace for words = ["word", "world", "row"], order = "worldabcefghijkmnpqstuvxyz":**

**Rank map (partial):**
`w=0, o=1, r=2, l=3, d=4, a=5, b=6, ...`

**Compare "word" vs "world":**

| Position | a[i] | b[i] | rank[a[i]] | rank[b[i]] | Result |
| -------- | ---- | ---- | ---------- | ---------- | ------ |
| 0        | 'w'  | 'w'  | 0          | 0          | Equal  |
| 1        | 'o'  | 'o'  | 1          | 1          | Equal  |
| 2        | 'r'  | 'r'  | 2          | 2          | Equal  |
| 3        | 'd'  | 'l'  | 4          | 3          | 4 > 3 → **out of order** |

`d` (rank 4) > `l` (rank 3) in alien order. "word" > "world". Return `false`.

**Answer: false** ✓

We didn't even need to check "world" vs "row" -- the first violation was enough.

---

**Full trace for words = ["apple", "app"], order = "abcdefghijklmnopqrstuvwxyz":**

**Compare "apple" vs "app":**

| Position | a[i] | b[i] | rank[a[i]] | rank[b[i]] | Result |
| -------- | ---- | ---- | ---------- | ---------- | ------ |
| 0        | 'a'  | 'a'  | 0          | 0          | Equal  |
| 1        | 'p'  | 'p'  | 15         | 15         | Equal  |
| 2        | 'p'  | 'p'  | 15         | 15         | Equal  |

All 3 compared characters equal. Check lengths:
`a.size() = 5 > b.size() = 3` → `a` is longer → **out of order**.

**Answer: false** ✓

"apple" is longer than "app" but comes first -- violates the prefix rule.

---

**Trace for words = ["app", "apple"]:**

Same comparison, but `a.size() = 3 <= b.size() = 5` → **in order**.

**Answer: true** ✓ -- shorter prefix comes first, as it should.

---

## 🔍 Why Compare Only Adjacent Pairs?

If `words[0] <= words[1]` and `words[1] <= words[2]`,
then `words[0] <= words[2]` by transitivity.

So checking every adjacent pair is sufficient
to verify the entire list is sorted.
No need to compare every pair -- that would be O(N²).

---

## 🔄 Connection to Graph Problems

This problem is the **reverse** of the harder problem
**Alien Dictionary** (LeetCode 269):

| Verifying Alien Dictionary       | Alien Dictionary (Hard)           |
| -------------------------------- | --------------------------------- |
| Given the order, verify sorting  | Given sorted words, FIND the order|
| Simple comparison                | Topological sort on a graph       |
| O(total chars)                   | O(total chars) but much harder    |

In the hard version, you BUILD a directed graph
from the word comparisons and topologically sort it
to discover the alien order.

This easy version is the foundation --
understanding how alien comparison works
is essential before tackling the graph version.

---

### 🧠 Memory of the Foreign Alphabet Law

-   **Rank map:** `rank[order[i] - 'a'] = i` -- maps each letter to its alien priority
-   **Compare adjacent pairs:** if any pair is out of order → return false
-   **Word comparison:** character by character using alien ranks
    -   First difference decides (smaller rank = comes first)
    -   All equal + one is prefix → shorter must come first
-   **Prefix rule:** `a.size() <= b.size()` when all compared chars are equal
-   Only adjacent pairs needed (transitivity handles the rest)
-   **Time:** O(total characters across all words)
-   **Space:** O(1) -- rank array of fixed size 26
-   **Edge cases:**
    -   Single word → always sorted (true)
    -   All words identical → sorted (true)
    -   Prefix violation ("apple" before "app") → false

Thus is remembered the saga of **Verifying an Alien Dictionary**,
where the Oracle built a rank map from the alien alphabet,
then walked every adjacent pair of words --
comparing character by character using alien priorities,
letting the first difference decide the order,
and enforcing the prefix rule when one word
was the beginning of another --
until every pair was verified
or a single violation was found. 👽📖✨
