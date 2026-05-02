## 🔤⛓️ _The Word Chain: The Longest String Chain Saga_

> \_"The Oracle was given a list of words.
>
> A word `a` is a **predecessor** of word `b` if you can insert
> exactly ONE letter anywhere in `a` to make it equal to `b`.
>
> `"abc"` → insert 'd' → `"abdc"`. Predecessor ✓.
> `"abc"` → `"abcde"`. Two letters added. Not a predecessor ✗.
>
> She was commanded:
>
> **'Find the longest chain of words where each word
> is a predecessor of the next.'**
>
> The Oracle recognized the pattern:
>
> **Sort by word length. Then it's LIS.**
>
> A word can only be a predecessor of a LONGER word.
> After sorting by length, predecessors always come before
> their successors. The LIS condition becomes:
> 'Is word j a predecessor of word i?'
>
> Sort by length. Run LIS-style DP.
> Replace `nums[j] < nums[i]` with `isPredecessor(words[j], words[i])`.
> Done."\_

---

This is the saga of **Longest String Chain (LeetCode 1048)**.

Given a list of words:
-   Word `a` is a predecessor of `b` if inserting exactly one letter
    into `a` (at any position) makes it equal to `b`.
-   Find the length of the **longest chain** of predecessors.

```
Input:  words = ["a","b","ba","bca","bda","bdca"]
Output: 4   (chain: "a" → "ba" → "bda" → "bdca")

Input:  words = ["xbc","pcxbcf","xb","cxbc","pcxbc"]
Output: 5   (chain: "xb" → "xbc" → "cxbc" → "pcxbc" → "pcxbcf")

Input:  words = ["abcd","dbqca"]
Output: 1   (no valid predecessor pair)
```

---

## 🧠 The Predecessor Check -- The Core Helper

How to check if `short` is a predecessor of `long`?

**Condition 1:** `long.size() == short.size() + 1`.
Exactly one character was inserted. Length differs by exactly 1.

**Condition 2:** All characters of `short` appear in `long` in order,
with exactly one extra character in `long`.

Use two pointers: walk both strings. Allow exactly one mismatch
(where `long` has an extra character that `short` doesn't).

```
short = "bda", long = "bdca"

b == b ✓ (both advance)
d == d ✓ (both advance)
c ≠ a   → skip c in long (one mismatch allowed)
a == a ✓ (both advance)

One skip used. All of short matched. Predecessor ✓.
```

---

## 🧠 Why Sort by Length?

A predecessor is always SHORTER than its successor (by exactly 1).
After sorting by length, every valid predecessor comes BEFORE
its successor in the array.

This means the LIS-style "check all j < i" naturally finds
all valid predecessors — they're guaranteed to appear earlier.

Without sorting, a longer word might appear before a shorter one,
and we'd miss valid chains.

---

### 📜 The Scroll of the Word Chain

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;
```

---

## ⛓️ The Predecessor Check

```cpp
bool isPredecessor(string& shorter, string& longer) {
    if (longer.size() != shorter.size() + 1) return false;
```

Length must differ by exactly 1. If not, can't be a predecessor.

---

```cpp
    int i = 0, j = 0;
    int skips = 0;
```

Two pointers: `i` walks `shorter`, `j` walks `longer`.
`skips` counts how many extra characters `longer` has.

---

```cpp
    while (i < shorter.size() && j < longer.size()) {
        if (shorter[i] == longer[j]) {
            i++;
            j++;
        } else {
            j++;
            skips++;
            if (skips > 1) return false;
        }
    }
    return true;
}
```

**Match:** both pointers advance. Characters align.
**Mismatch:** only `longer` advances. This is the "inserted" character.
If more than one skip — more than one insertion — not a predecessor.

After the loop, if all of `shorter` was matched, it's a predecessor.
The remaining characters in `longer` (at most 1) are the insertion.

> _"Walk both words side by side.
> When they disagree, the longer word skips ahead once.
> If they disagree again — too many insertions. Not a predecessor."_

---

## ⛓️ Sort by Length

```cpp
int longestStrChain(vector<string>& words) {
    sort(words.begin(), words.end(), [](string& a, string& b) {
        return a.size() < b.size();
    });
```

Sort by word length (shortest first).
This ensures predecessors appear before their successors.

Words of the same length are never predecessors of each other
(they'd need to differ by exactly 1 in length).
So their relative order within the same length doesn't matter.

---

## ⛓️ LIS-Style DP

### Initialize

```cpp
    int n = words.size();
    vector<int> dp(n, 1);
```

Every word alone is a chain of length 1.

---

### Fill dp — check all predecessors

```cpp
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
```

For each word `i`, check all earlier words `j`.

---

### If j is a predecessor of i AND gives a longer chain

```cpp
            if (isPredecessor(words[j], words[i]) && dp[j] + 1 > dp[i]) {
                dp[i] = dp[j] + 1;
            }
```

`isPredecessor` replaces the `nums[j] < nums[i]` from standard LIS.
Same structure. Different condition.

> _"Word i asks every earlier word:
> 'Are you my predecessor — one letter shorter, one insertion away?
> If so, I extend your chain by one.'"_

---

```cpp
        }
    }
    return *max_element(dp.begin(), dp.end());
}
```

The longest chain could end at any word. Take the max.

---

### 🎺 The Trial of the Word Chain

```cpp
int main() {
    vector<string> w1 = {"a", "b", "ba", "bca", "bda", "bdca"};
    cout << longestStrChain(w1) << endl; // expected: 4

    vector<string> w2 = {"xbc", "pcxbcf", "xb", "cxbc", "pcxbc"};
    cout << longestStrChain(w2) << endl; // expected: 5

    vector<string> w3 = {"abcd", "dbqca"};
    cout << longestStrChain(w3) << endl; // expected: 1

    return 0;
}
```

---

**Trace for words = ["a","b","ba","bca","bda","bdca"]:**

After sorting by length: `["a", "b", "ba", "bca", "bda", "bdca"]`
(already sorted — lengths: 1, 1, 2, 3, 3, 4)

| i | word | Check j < i | dp[i] | Chain |
|---|------|-------------|-------|-------|
| 0 | "a"  | —           | 1     | [a] |
| 1 | "b"  | j=0: isPred("a","b")? len 1→1, diff≠1 ✗ | 1 | [b] |
| 2 | "ba" | j=0: isPred("a","ba")? "a"→"ba" insert 'b' ✓ dp=2. j=1: isPred("b","ba")? "b"→"ba" insert 'a' ✓ dp=2 (tie) | **2** | [a, ba] |
| 3 | "bca"| j=0: isPred("a","bca")? len 1→3, diff≠1 ✗. j=1: same ✗. j=2: isPred("ba","bca")? "ba"→"bca" insert 'c' ✓ dp=3 | **3** | [a, ba, bca] |
| 4 | "bda"| j=2: isPred("ba","bda")? "ba"→"bda" insert 'd' ✓ dp=3 | **3** | [a, ba, bda] |
| 5 | "bdca"| j=3: isPred("bca","bdca")? "bca"→"bdca" insert 'd' ✓ dp=4. j=4: isPred("bda","bdca")? "bda"→"bdca" insert 'c' ✓ dp=4 (tie) | **4** | [a, ba, bda, bdca] |

**Answer: max(dp) = 4** ✓

---

**Trace for words = ["xbc","pcxbcf","xb","cxbc","pcxbc"]:**

After sorting by length: `["xb", "xbc", "cxbc", "pcxbc", "pcxbcf"]`
(lengths: 2, 3, 4, 5, 6)

| i | word | dp[i] | Chain |
|---|------|-------|-------|
| 0 | "xb"     | 1 | [xb] |
| 1 | "xbc"    | 2 | [xb, xbc] — isPred("xb","xbc") ✓ |
| 2 | "cxbc"   | 3 | [xb, xbc, cxbc] — isPred("xbc","cxbc") ✓ |
| 3 | "pcxbc"  | 4 | [xb, xbc, cxbc, pcxbc] — isPred("cxbc","pcxbc") ✓ |
| 4 | "pcxbcf" | 5 | [..., pcxbc, pcxbcf] — isPred("pcxbc","pcxbcf") ✓ |

**Answer: 5** ✓

---

## 🔍 The HashMap Alternative -- O(N × L²)

Instead of checking all pairs O(N²), use a hashmap:

For each word, try REMOVING each character one at a time.
If the resulting shorter word exists in the map, it's a predecessor.

```cpp
int longestStrChain(vector<string>& words) {
    sort(words.begin(), words.end(), [](string& a, string& b) {
        return a.size() < b.size();
    });
    unordered_map<string, int> dp;
    int ans = 1;
    for (auto& word : words) {
        dp[word] = 1;
        for (int i = 0; i < word.size(); i++) {
            string prev = word.substr(0, i) + word.substr(i + 1);
            if (dp.count(prev)) {
                dp[word] = max(dp[word], dp[prev] + 1);
            }
        }
        ans = max(ans, dp[word]);
    }
    return ans;
}
```

For each word of length L, generate L shorter words (remove each char).
Look up each in the hashmap. O(N × L²) total (L for substr).

This is faster when N is large but words are short (L ≤ 16).

---

## 🔍 LIS Family Comparison

| Problem | Sort by | Condition | Check |
|---------|---------|-----------|-------|
| LIS | — (preserve order) | `nums[j] < nums[i]` | Value comparison |
| Divisible Subset | Value | `nums[i] % nums[j] == 0` | Divisibility |
| **String Chain** | **Length** | **isPredecessor(j, i)** | **Two-pointer** |
| Bitonic Subseq | — | LIS + LDS | Two passes |

Same skeleton every time. Sort (if needed). Change the condition.

---

### 🧠 Memory of the Word Chain Law

-   **Sort by word length** — predecessors always come first
-   **`dp[i]`** = longest chain ending at word `i`
-   **Condition:** `isPredecessor(words[j], words[i])` — length differs by 1, two-pointer check
-   **Two-pointer predecessor check:** walk both, allow exactly one skip in the longer word
-   **HashMap alternative:** remove each character, look up shorter word. O(N × L²).
-   **Answer:** max over all dp values
-   **Time:** O(N² × L) or O(N × L²) with hashmap. **Space:** O(N).

Thus is remembered the saga of **Longest String Chain**,
where the Oracle sorted words by length,
then ran the LIS machinery with a new condition --
not less-than, not divisibility,
but the predecessor check:
one letter inserted, two pointers walking side by side,
allowing exactly one skip --
building the longest chain of words
where each grew from the last
by a single added letter. 🔤⛓️✨
