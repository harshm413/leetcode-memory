## 📖✂️ _The Dictionary of Fragments: The Word Break Saga_

> \_"The Oracle was given a string `s`
> and a dictionary of words.
>
> She was commanded:
>
> **'Can the string be segmented into a sequence
> of one or more dictionary words?'**
>
> The string `"leetcode"` with dictionary `["leet", "code"]`
> could be split as `"leet" + "code"` → YES.
>
> But `"catsandog"` with `["cats", "dog", "sand", "and", "cat"]`
> could NOT be fully segmented → NO.
>
> The Oracle thought recursively:
>
> **Stand at position `i` in the string.
> Try every possible prefix starting at `i`.
> If that prefix is a dictionary word,
> jump to the end of that prefix
> and ask: 'Can the REST of the string be segmented?'**
>
> If ANY prefix leads to a successful segmentation
> of the remainder → return true.
> If NO prefix works → return false.
>
> This was a classic DP problem --
> overlapping subproblems (same position reached multiple ways)
> and optimal substructure (if the rest can be segmented, the whole can).
>
> The Oracle chose **tabulation** --
> building the answer from left to right,
> position by position."\_

---

This is the saga of **Word Break**.

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

## 🧠 The Oracle's Core Insight -- DP on Positions

Define `dp[i]` = can the substring `s[0..i-1]` (first `i` characters)
be segmented into dictionary words?

**Base case:** `dp[0] = true` -- the empty string is trivially segmented.

**Transition:** for each position `i` (1 to n):
try every possible last word ending at position `i`.

```
dp[i] = true IF there exists some j (0 ≤ j < i) such that:
  1. dp[j] == true   (the prefix s[0..j-1] can be segmented)
  2. s[j..i-1] is in the dictionary  (the substring from j to i is a valid word)
```

In other words: split the string at position `j`.
If the left part is valid (dp[j]) AND the right part is a dictionary word →
the whole thing up to `i` is valid.

**Answer:** `dp[n]` (can the entire string be segmented?)

```
Time:  O(n² × L) where L = average word length (for substring comparison)
       or O(n²) with a hash set
Space: O(n)
```

---

### 📜 The Scroll of the Dictionary Fragments

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## 📖 The Tabulation Ritual

### Build the Dictionary Set

```cpp
bool wordBreak(string s, vector<string>& wordDict) {
    unordered_set<string> dict(wordDict.begin(), wordDict.end());
    int n = s.size();
```

Load all dictionary words into a hash set for O(1) lookup.

---

### Create the DP Array

```cpp
    vector<bool> dp(n + 1, false);
    dp[0] = true;
```

`dp[i]` = can `s[0..i-1]` be segmented?
`dp[0] = true` -- the empty prefix is always valid.

Size `n + 1` because we need indices 0 through n.

> _"The empty string needs no words.
> It is the foundation on which all segmentations are built."_

---

### 🔁 Fill Position by Position

```cpp
    for (int i = 1; i <= n; i++) {
```

For each position `i` (end of a potential segmentation):

---

### 🔁 Try Every Split Point

```cpp
        for (int j = 0; j < i; j++) {
```

Try splitting at every position `j` before `i`.
`j` is where the last word starts.

---

### ✅ Check Both Conditions

```cpp
            if (dp[j] && dict.count(s.substr(j, i - j))) {
                dp[i] = true;
                break;
            }
        }
    }
```

**Condition 1:** `dp[j] == true` -- everything before position `j` is valid.

**Condition 2:** `s.substr(j, i - j)` is in the dictionary --
the substring from index `j` to index `i-1` (length `i - j`) is a valid word.

If BOTH are true → `dp[i] = true`. Break early (no need to try more splits).

> _"If I can reach position j with valid words,
> AND the substring from j to i is itself a word --
> then I can reach position i.
> The chain extends."_

---

### 🏁 The Answer

```cpp
    return dp[n];
}
```

`dp[n]` = can the entire string `s[0..n-1]` be segmented?

---

### 🎺 The Trial of the Dictionary Fragments

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
n = 8. dp = [T, F, F, F, F, F, F, F, F]
dict = {"leet", "code"}
```

| i | j tried | dp[j]? | substr(j, i-j) | In dict? | dp[i] |
|---|---------|--------|----------------|----------|-------|
| 1 | j=0     | T      | "l"            | No       | F     |
| 2 | j=0     | T      | "le"           | No       | F     |
| 3 | j=0     | T      | "lee"          | No       | F     |
| 4 | j=0     | T      | "leet"         | **Yes!** | **T** |
| 5 | j=0..4  | --     | no match       | --       | F     |
| 6 | j=0..5  | --     | no match       | --       | F     |
| 7 | j=0..6  | --     | no match       | --       | F     |
| 8 | j=4     | T      | "code"         | **Yes!** | **T** |

**dp = [T, F, F, F, T, F, F, F, T]**

`dp[8] = true`. **Answer: true** ✓

At i=4: dp[0]=true AND "leet" in dict → dp[4]=true.
At i=8: dp[4]=true AND "code" in dict → dp[8]=true.

Segmentation: "leet" + "code".

---

**Full trace for s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]:**

```
n = 9. dict = {"cats", "dog", "sand", "and", "cat"}
```

| i | Key checks                                    | dp[i] |
|---|-----------------------------------------------|-------|
| 3 | dp[0]=T, "cat" in dict → **T**               | T     |
| 4 | dp[0]=T, "cats" in dict → **T**              | T     |
| 6 | dp[3]=T, "san" not in dict. dp[4]=T, "an" not in dict. | F |
| 7 | dp[3]=T, "sand" in dict → **T**. Also dp[4]=T, "and" in dict → T. | T |
| 8 | dp[7]=T, "o" not. dp[4]=T, "ando" not. dp[3]=T, "sando" not. | F |
| 9 | dp[7]=T, "og" not. dp[4]=T, "andog" not. dp[3]=T, "sandog" not. | F |

**dp[9] = false.** **Answer: false** ✓

The problem: after "cats" or "cat"+"sand", the remaining "og" or "dog"
doesn't align properly. "andog" is not a word. No valid segmentation exists.

---

**Trace for s = "applepenapple", wordDict = ["apple", "pen"]:**

```
dp[5] = true  (dp[0]=T, "apple" in dict)
dp[8] = true  (dp[5]=T, "pen" in dict)
dp[13] = true (dp[8]=T, "apple" in dict)
```

**Answer: true** ✓ — "apple" + "pen" + "apple".

---

## 🌐 The Word Break Family

| Problem              | What it asks                        |
| -------------------- | ----------------------------------- |
| Word Break (this)    | Can the string be segmented? (T/F)  |
| Word Break II        | Return ALL valid segmentations      |
| Concatenated Words   | Find words that are concatenations  |

Word Break II uses backtracking to collect all paths.
This problem only needs existence -- much simpler.

---

### 🧠 Memory of the Dictionary Fragments Law

-   **State:** `dp[i]` = can `s[0..i-1]` be segmented into dictionary words?
-   **Base:** `dp[0] = true` (empty string is valid)
-   **Transition:** `dp[i] = true` if ∃ `j` where `dp[j]=true` AND `s[j..i-1]` ∈ dict
-   **Answer:** `dp[n]`
-   **Dictionary in hash set** for O(1) lookup
-   **Break early** once a valid split is found
-   **Optimization:** limit `j` range to `max(0, i - maxWordLen)`
-   **Memoization alternative:** `solve(start)` = can `s[start..n-1]` be segmented?
-   **Time:** O(n² × L) or O(n²) with hash set
-   **Space:** O(n) for dp array + O(total dict chars) for set
-   **Edge cases:**
    -   Empty string → true
    -   Single character in dict → check if s is that character
    -   Same word used multiple times → allowed (unbounded)
    -   No valid segmentation → false

Thus is remembered the saga of **Word Break**,
where the Oracle stood at each position in the string
and asked: "Can I reach here with valid words?" --
trying every possible last word ending at this position,
checking if the prefix before it was already valid --
building the answer left to right,
position by position,
until the final position revealed
whether the entire string could be shattered
into fragments from the dictionary. 📖✂️✨
