## 🌿📜 _The Banner of Shared Beginnings: The Longest Common Prefix Saga_

> _"In the Valley of Words stood many banners,
> each bearing a different name.
>
> Though their endings diverged like winding roads,
> some shared the same beginning —
> a common crest stitched at the start of every banner.
>
> The Oracle was summoned with a single question:
>
> **'What is the longest prefix
> shared by ALL these words?'**
>
> She knew the answer lay not in endings,
> but in walking the words **letter by letter together**,
> stopping the moment harmony was broken.
>
> The naive approach would compare every pair of words —
> but that was wasteful.
>
> The Oracle chose a simpler path:
>
> **Take the first word as the guide.
> Compare it character by character
> against every other word.
> Shrink the prefix whenever a mismatch appears.
> The moment the prefix vanishes — stop.
> What remains is the longest common beginning.'**
>
> One pass. One shrinking prefix.
> The shared banner revealed."_

---

This is the saga of **Longest Common Prefix**.

You are given an array of strings `strs`.

Your task:

- Find the **longest common prefix** string amongst all strings.
- If there is no common prefix, return an empty string `""`.

```
Input:  ["flower", "flow", "flight"]
Output: "fl"

Input:  ["dog", "racecar", "car"]
Output: ""

Input:  ["interspecies", "interstellar", "interstate"]
Output: "inters"
```

---

## 🧠 The Oracle's Core Insight — Horizontal Scanning

The key insight:

> **The longest common prefix of N strings
> cannot be longer than the shortest string.**

And more importantly:

> **If we start with the first string as the prefix candidate,
> and compare it with each subsequent string,
> shrinking it whenever a mismatch occurs —
> what remains at the end is the answer.**

Algorithm:

```
1. Start with prefix = strs[0]
2. For each subsequent string strs[i]:
   - Compare prefix with strs[i] character by character
   - Find the first position where they differ
   - Shrink prefix to only the matching portion
   - If prefix becomes empty → return "" immediately
3. Return the surviving prefix
```

**Time:** O(S) where S = sum of all characters in all strings
**Space:** O(1) extra space (prefix is just a substring reference)

---

### 📜 The Scroll of United Words

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🔮 The Oracle's Prefix-Walking Ritual

_Compare characters column by column_

```cpp
string longestCommonPrefix(vector<string>& strs) {
    if (strs.empty()) return "";
```

If no banners were given,
there could be no shared beginning.

---

## 🏳️ Choose the First Banner as the Guide

```cpp
    string prefix = strs[0];
```

The Oracle held the first word aloft.
It would define the **maximum possible prefix**.

No prefix can be longer than this first word.

---

## 🔍 Compare With Every Other Banner

```cpp
    for (int i = 1; i < strs.size(); i++) {
```

The Oracle walked through each subsequent banner,
comparing it against the current prefix.

---

```cpp
        int j = 0;
        while (j < prefix.size() && j < strs[i].size()
               && prefix[j] == strs[i][j]) {
            j++;
        }
```

She aligned the prefix and the current word side by side
and walked them together, character by character.

**While loop conditions:**
- `j < prefix.size()` — haven't reached the end of the prefix
- `j < strs[i].size()` — haven't reached the end of the current word
- `prefix[j] == strs[i][j]` — characters still match

The loop advanced `j` as long as all three conditions held.

When the loop stopped, `j` pointed to:
- The first mismatch position, or
- The end of the shorter string

---

```cpp
        prefix = prefix.substr(0, j);
```

The prefix was **shrunk** to only the portion that matched —
from index `0` to index `j-1`.

If `j = 0` — no characters matched — prefix became `""`.

---

## 🧱 Early Exit if Prefix Vanishes

```cpp
        if (prefix.empty()) return "";
    }
```

If at any moment the prefix became empty,
no shared beginning existed across all words.

The Oracle stopped immediately and returned `""`.

> _"Once the banner is torn completely,
> there is no point in examining the rest."_

---

```cpp
    return prefix;
}
```

If the loop completed without the prefix vanishing,
the surviving prefix was the longest common beginning
shared by all words.

---

### 🎺 The Trial of the Banners

```cpp
int main() {
    vector<string> words1 = {"flower", "flow", "flight"};
    cout << longestCommonPrefix(words1) << endl; // "fl"

    vector<string> words2 = {"dog", "racecar", "car"};
    cout << longestCommonPrefix(words2) << endl; // ""

    vector<string> words3 = {"interspecies", "interstellar", "interstate"};
    cout << longestCommonPrefix(words3) << endl; // "inters"

    return 0;
}
```

---

**Trace for `["flower", "flow", "flight"]`:**

| Step | Current word | prefix (before) | j stops at | Matching portion | prefix (after) |
|------|--------------|-----------------|------------|------------------|----------------|
| Init | —            | —               | —          | —                | "flower"       |
| 1    | "flow"       | "flower"        | 4          | "flow"           | "flow"         |
| 2    | "flight"     | "flow"          | 2          | "fl"             | "fl"           |

**Answer: "fl"** ✓

---

**Trace for `["dog", "racecar", "car"]`:**

| Step | Current word | prefix (before) | j stops at | Matching portion | prefix (after) |
|------|--------------|-----------------|------------|------------------|----------------|
| Init | —            | —               | —          | —                | "dog"          |
| 1    | "racecar"    | "dog"           | 0          | ""               | ""             |

Prefix became empty → return `""` immediately.

**Answer: ""** ✓

---

**Trace for `["interspecies", "interstellar", "interstate"]`:**

| Step | Current word     | prefix (before)  | j stops at | Matching portion | prefix (after) |
|------|------------------|------------------|------------|------------------|----------------|
| Init | —                | —                | —          | —                | "interspecies" |
| 1    | "interstellar"   | "interspecies"   | 7          | "interse"        | "interse"      |
| 2    | "interstate"     | "interse"        | 6          | "inters"         | "inters"       |

**Answer: "inters"** ✓

---

## 🔄 Alternative Approach — Vertical Scanning

Instead of comparing the first word with all others horizontally,
you can scan vertically — column by column:

```cpp
string longestCommonPrefix(vector<string>& strs) {
    if (strs.empty()) return "";
    
    for (int i = 0; i < strs[0].size(); i++) {
        char c = strs[0][i];
        for (int j = 1; j < strs.size(); j++) {
            if (i >= strs[j].size() || strs[j][i] != c) {
                return strs[0].substr(0, i);
            }
        }
    }
    return strs[0];
}
```

This checks character position `i` across all strings
before moving to position `i+1`.

Same time complexity, slightly different iteration order.

---

### 🧠 Memory of the Shared Banner Law

- **Horizontal scanning:** compare first string with all others, shrink prefix on mismatch
- Start with `prefix = strs[0]`
- For each subsequent string, find the first mismatch position `j`
- Shrink prefix to `prefix.substr(0, j)`
- If prefix becomes empty → return `""` immediately
- **Time:** O(S) where S = sum of all characters in all strings
  - Worst case: all strings identical → scan every character
  - Best case: first mismatch at position 0 → O(N) where N = number of strings
- **Space:** O(1) extra space (prefix is a substring, not a new allocation in most implementations)
- **Edge cases:**
  - Empty array → return `""`
  - Single string → return that string
  - All strings identical → return the first string
  - No common prefix → return `""`

Thus is remembered the saga of **Longest Common Prefix**,
where the Oracle aligned many words beneath a single sky,
searching for the shared crest at their beginnings —
walking letter by letter,
shrinking the banner with each mismatch,
stopping the instant unity broke —
and revealing the longest beginning
that all words held in common. 🌿📜✨
