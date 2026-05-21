## 📖🧠 _The Dictionary of Fragments (Memoization): The Word Break Saga_

> \_"The Oracle was given a string `s`
> and a dictionary of words.
>
> She was commanded:
>
> **'Can the string be segmented into a sequence
> of one or more dictionary words?'**
>
> The Oracle thought recursively:
>
> **Stand at position `start` in the string.
> Try every possible prefix starting at `start`.
> If that prefix is a dictionary word,
> jump to the end of that prefix
> and ask: 'Can the REST of the string be segmented?'**
>
> If ANY prefix leads to a successful segmentation
> of the remainder → return true.
> If NO prefix works → return false.
>
> Without memoization, the same position would be
> revisited many times -- exponential blowup.
> With memoization, each position is solved once.
>
> Top-down. Recursive. Cached."\_

---

This is the saga of **Word Break (Memoization)**.

Given a string `s` and a list of words `wordDict`:

-   Return `true` if `s` can be segmented into a space-separated
    sequence of one or more dictionary words.
-   The same word may be used multiple times.

```
Input:  s = "leetcode", wordDict = ["leet","code"]
Output: true   ("leet" + "code")

Input:  s = "applepenapple", wordDict = ["apple","pen"]
Output: true   ("apple" + "pen" + "apple")

Input:  s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

---

## 🧠 The Recurrence

`solve(start)` = can the substring `s[start..n-1]` be segmented?

At position `start`, try every possible word ending:

```
solve(start):
  If start == n: return true  (entire string consumed)

  For end = start+1 to n:
    word = s[start..end-1]
    If word is in dictionary AND solve(end) == true:
      return true

  return false  (no prefix worked)
```

**Base case:** `start == n` → the entire string has been consumed.
All characters are covered by valid words. Return true.

**Recursive case:** try every prefix `s[start..end-1]`.
If it's a dictionary word AND the rest can be segmented → true.

**Memoization:** `memo[start]` caches the answer for each starting position.
Without it, the same position is revisited exponentially.

---

### 📜 The Scroll of the Recursive Fragments

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## 📖 Setting Up the Cache

```cpp
bool wordBreak(string s, vector<string>& wordDict) {
    unordered_set<string> dict(wordDict.begin(), wordDict.end());
    int n = s.size();
    vector<int> memo(n, -1);
```

`dict` = hash set for O(1) word lookup.
`memo[i]` = can `s[i..n-1]` be segmented? (`-1` = not computed, `0` = false, `1` = true).

---

```cpp
    return solve(0, s, dict, memo);
}
```

Start from position 0 -- can the entire string be segmented?

---

## 🔮 The Recursive Thinking

```cpp
bool solve(int start, string& s, unordered_set<string>& dict,
           vector<int>& memo) {
```

"Starting from position `start`, can the rest of the string be segmented?"

---

### 🎯 Base Case -- Entire String Consumed

```cpp
    if (start == (int)s.size()) return true;
```

We've reached the end. Every character has been covered
by a valid dictionary word. Success.

> _"The string is fully consumed.
> Every fragment was a real word.
> The segmentation is complete."_

---

### 📖 Cache Check

```cpp
    if (memo[start] != -1) return memo[start];
```

If this position was already solved → return the cached answer.

---

### 🔁 Try Every Possible Word Starting Here

```cpp
    for (int end = start + 1; end <= (int)s.size(); end++) {
        string word = s.substr(start, end - start);
```

Try every substring starting at `start` with increasing length.
`s.substr(start, end - start)` = the substring from index `start` to `end-1`.

---

### ✅ If It's a Word AND the Rest Works → True

```cpp
        if (dict.count(word) && solve(end, s, dict, memo)) {
            return memo[start] = true;
        }
    }
```

**Two conditions:**
1. `dict.count(word)` -- this prefix is a valid dictionary word.
2. `solve(end, ...)` -- the remaining string (from `end` onward) can also be segmented.

If BOTH are true → the string from `start` onward can be segmented.
Cache and return true.

> _"If this prefix is a real word,
> and everything after it can also be broken into words --
> then the whole thing works.
> One valid split is all we need."_

---

### ❌ No Prefix Worked → False

```cpp
    return memo[start] = false;
}
```

Every possible prefix was tried. None led to a valid segmentation.
Cache false and return.

> _"No word in the dictionary starts here
> and leads to a valid segmentation of the rest.
> This position is a dead end."_

---

### 🎺 The Trial of the Recursive Fragments

```cpp
int main() {
    vector<string> d1 = {"leet", "code"};
    cout << wordBreak("leetcode", d1) << endl; // expected: 1 (true)

    vector<string> d2 = {"apple", "pen"};
    cout << wordBreak("applepenapple", d2) << endl; // expected: 1 (true)

    vector<string> d3 = {"cats", "dog", "sand", "and", "cat"};
    cout << wordBreak("catsandog", d3) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full trace for s = "leetcode", wordDict = ["leet", "code"]:**

```
solve(0): "leetcode"
  end=1: "l" not in dict.
  end=2: "le" not in dict.
  end=3: "lee" not in dict.
  end=4: "leet" IN DICT! → solve(4)?
    solve(4): "code"
      end=5: "c" not in dict.
      end=6: "co" not in dict.
      end=7: "cod" not in dict.
      end=8: "code" IN DICT! → solve(8)?
        solve(8): start == n → return true.
      return true.
    memo[4] = true. Return true.
  return true.
memo[0] = true. Return true.
```

**Answer: true** ✓

The recursion found: "leet" (positions 0-3) + "code" (positions 4-7).

---

**Full trace for s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]:**

```
solve(0): "catsandog"
  end=3: "cat" IN DICT! → solve(3)?
    solve(3): "sandog"
      end=4: "s" no. end=5: "sa" no. end=6: "san" no.
      end=7: "sand" IN DICT! → solve(7)?
        solve(7): "og"
          end=8: "o" no. end=9: "og" no.
        memo[7] = false.
      end=8: "sando" no. end=9: "sandog" no.
    memo[3] = false. Return false.
  end=4: "cats" IN DICT! → solve(4)?
    solve(4): "andog"
      end=5: "a" no. end=6: "an" no. end=7: "and" IN DICT! → solve(7)?
        solve(7): memo[7] = false (already computed!). Return false.
      end=8: "ando" no. end=9: "andog" no.
    memo[4] = false. Return false.
  end=5..9: no more dict words starting at 0.
memo[0] = false. Return false.
```

**Answer: false** ✓

Notice `solve(7)` was computed once and reused -- that's memoization saving work.

---

**Trace for s = "applepenapple":**

```
solve(0): "apple" IN DICT → solve(5)?
  solve(5): "pen" IN DICT → solve(8)?
    solve(8): "apple" IN DICT → solve(13)?
      solve(13): start == n → true.
    memo[8] = true.
  memo[5] = true.
memo[0] = true.
```

**Answer: true** ✓ — "apple" + "pen" + "apple".

---

## 🔍 Why Memoization Is Needed

Without memoization, the recursion tree explodes:

```
s = "aaaaaab", dict = ["a", "aa", "aaa"]

solve(0) tries "a" → solve(1)
                tries "aa" → solve(2)
                tries "aaa" → solve(3)
solve(1) tries "a" → solve(2)  ← RECOMPUTED
                tries "aa" → solve(3)  ← RECOMPUTED
...
```

Position 2 is reached from positions 0 and 1.
Position 3 is reached from 0, 1, and 2.
Without caching → exponential. With caching → O(n²).

---

## 🔍 Optimization -- Limit Word Length

```cpp
int maxLen = 0;
for (auto& w : wordDict) maxLen = max(maxLen, (int)w.size());

for (int end = start + 1; end <= min((int)s.size(), start + maxLen); end++) {
```

No dictionary word is longer than `maxLen`.
Don't try substrings longer than that -- they can never match.

---

### 🧠 Memory of the Recursive Fragments (Memoization) Law

-   **State:** `solve(start)` = can `s[start..n-1]` be segmented?
-   **Base:** `start == n` → true (string fully consumed)
-   **Transition:** try every prefix `s[start..end-1]`
    -   If prefix ∈ dict AND `solve(end)` == true → return true
-   **If no prefix works** → return false
-   **Memoization:** `memo[start]` caches each position's answer
-   **Dictionary in hash set** for O(1) lookup
-   **Optimization:** limit `end` to `start + maxWordLen`
-   **Time:** O(n² × L) where L = substring creation cost
-   **Space:** O(n) for memo + O(total dict chars) for set

Thus is remembered the saga of **Word Break (Memoization)**,
where the Oracle stood at each position in the string
and tried every possible prefix as a dictionary word --
if the prefix was valid and the rest could be segmented,
the answer was true --
caching every position's fate in her notebook
so that no position was ever reconsidered --
until the starting position revealed
whether the entire string could be shattered
into fragments from the dictionary. 📖🧠✨
