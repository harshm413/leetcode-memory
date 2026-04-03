## 🌀🔑 _The Gathering of Shuffled Souls: The Group Anagrams Saga_

> _"In the Kingdom of Words,
> countless scrolls arrived at the Royal Archive —
> each bearing a word, each seemingly different.
>
> But the Archivist knew a secret:
>
> **Some words were kin —
> the same letters, merely shuffled.
> 'eat' and 'tea' and 'ate' —
> three faces, one soul.**
>
> The Queen commanded:
>
> **'Group these scrolls by kinship.
> All anagrams must stand together.
> All who share the same letters —
> no matter the order —
> belong in the same family.'**
>
> The Archivist thought carefully.
>
> She could compare every word with every other word —
> but that would take O(N²) time, far too slow.
>
> Then she realized the key:
>
> **Every anagram, when sorted, becomes identical.**
>
> 'eat' → sort → 'aet'
> 'tea' → sort → 'aet'
> 'ate' → sort → 'aet'
>
> The sorted form was the **signature** —
> the hidden identity shared by all kin.
>
> She opened the **Ledger of Kinship** —
> a map from signature to family —
> and began sorting each scroll's letters.
>
> Every scroll with the same signature
> was placed into the same family.
>
> When the last scroll was judged,
> the families were revealed —
> anagrams united, strangers separated,
> order restored to the chaotic archive."_

---

This is the saga of **Group Anagrams**.

You are given an array of strings `strs`.

Your task:

- Group all **anagrams** together.
- An anagram is a word formed by rearranging the letters of another word.
- Return the groups in **any order**.

```
Input:  ["eat","tea","tan","ate","nat","bat"]
Output: [["eat","tea","ate"],["tan","nat"],["bat"]]

Input:  [""]
Output: [[""]]

Input:  ["a"]
Output: [["a"]]
```

---

## 🧠 The Oracle's Core Insight — Sorted Form is the Signature

The naive approach would be:

```
For each word, compare it with every other word to check if they're anagrams.
O(N²) comparisons, each taking O(K) time to verify.
Total: O(N² × K) — too slow.
```

The key insight:

> **Two words are anagrams if and only if
> their sorted forms are identical.**

```
"eat" → sort → "aet"
"tea" → sort → "aet"
"ate" → sort → "aet"
```

All three produce the same sorted string `"aet"`.
This sorted form is the **signature** — the unique identifier
shared by all anagrams.

The algorithm:

```
1. For each word, compute its signature (sorted form).
2. Use a HashMap: signature → list of words with that signature.
3. After processing all words, collect all the lists.
```

**Time:** O(N × K log K) — N words, each sorted in O(K log K) where K = word length
**Space:** O(N × K) — storing all words in the map

This is optimal for the general case.

---

### 📜 The Scroll of the Archive

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;
```

The scribes gathered their tools:

- `vector<string>` — the scrolls awaiting judgment
- `unordered_map` — the Ledger of Kinship
- `algorithm` — for the sorting spell that reveals each word's signature

---

## 🗝️ The Ledger of Kinship is Opened

```cpp
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> groups;
```

The Archivist opened the **Ledger of Kinship** —
a map from each signature (sorted form)
to the family of words sharing that signature.

`groups[signature]` would hold all words
whose sorted form equals `signature`.

---

## 📖 The Sorting of Souls — Reveal Each Word's Signature

```cpp
    for (string s : strs) {
```

One by one, each scroll was lifted from the pile.

---

```cpp
        string key = s;
        sort(key.begin(), key.end());
```

The Archivist made a copy of the word — `key` —
and sorted its letters alphabetically.

This sorted form was the **signature** —
the hidden identity that all anagrams share.

For `"eat"`, the signature was `"aet"`.
For `"tea"`, the signature was also `"aet"`.
For `"bat"`, the signature was `"abt"`.

> _"Though their faces differ,
> their sorted hearts are the same."_

---

```cpp
        groups[key].push_back(s);
    }
```

The original word `s` was placed into the family
identified by its signature `key`.

`groups["aet"]` would collect `["eat", "tea", "ate"]`.
`groups["ant"]` would collect `["tan", "nat"]`.
`groups["abt"]` would collect `["bat"]`.

Every word found its kin.

---

## 🌌 The Gathering — Collect All Families

```cpp
    vector<vector<string>> result;
    for (auto& entry : groups) {
        result.push_back(entry.second);
    }
    return result;
}
```

When the last scroll had been judged,
the Archivist closed the Ledger.

She walked through every entry in the map —
each entry was a signature and its family.

She collected only the families (`entry.second`) —
the signatures themselves were no longer needed.

All families were gathered into `result` and returned.

The scattered scrolls had been united by kinship.

---

### 🎺 The Trial of Six Scrolls

```cpp
int main() {
    vector<string> strs = {"eat","tea","tan","ate","nat","bat"};

    vector<vector<string>> ans = groupAnagrams(strs);
    for (auto& group : ans) {
        cout << "[ ";
        for (auto& word : group) cout << word << " ";
        cout << "]" << endl;
    }
    // Expected output (order may vary):
    // [ eat tea ate ]
    // [ tan nat ]
    // [ bat ]
    return 0;
}
```

Six scrolls fluttered into the archive:
`"eat", "tea", "tan", "ate", "nat", "bat"`

**Trace of the sorting and grouping:**

| Word  | Sorted (signature) | groups[signature] after push |
|-------|-------------------|------------------------------|
| "eat" | "aet"             | {"aet": ["eat"]}             |
| "tea" | "aet"             | {"aet": ["eat","tea"]}       |
| "tan" | "ant"             | {"aet": [...], "ant": ["tan"]} |
| "ate" | "aet"             | {"aet": ["eat","tea","ate"], "ant": ["tan"]} |
| "nat" | "ant"             | {"aet": [...], "ant": ["tan","nat"]} |
| "bat" | "abt"             | {"aet": [...], "ant": [...], "abt": ["bat"]} |

**Final groups map:**
```
{
  "aet": ["eat", "tea", "ate"],
  "ant": ["tan", "nat"],
  "abt": ["bat"]
}
```

**Result after collecting families:**
```
[
  ["eat", "tea", "ate"],
  ["tan", "nat"],
  ["bat"]
]
```

The Archivist revealed the truth:

- `"eat", "tea", "ate"` — bound by the signature `"aet"`
- `"tan", "nat"` — bound by the signature `"ant"`
- `"bat"` — alone, yet whole, with signature `"abt"`

The people marveled, for the kinship of shuffled souls was made clear.

---

## 🔄 Alternative Approach — Counting Signature (For Lowercase Letters Only)

If the input is guaranteed to be lowercase English letters only,
there's an alternative signature that avoids sorting:

```cpp
string getSignature(string s) {
    int count[26] = {0};
    for (char c : s) count[c - 'a']++;
    
    string sig = "";
    for (int i = 0; i < 26; i++) {
        if (count[i] > 0) {
            sig += string(count[i], 'a' + i);
        }
    }
    return sig;
}
```

This counts the frequency of each letter
and builds a canonical string like `"aaet"` for `"eat"`.

**Time:** O(N × K) — linear in word length, no sorting
**Space:** O(N × K)

But for the general case (Unicode, mixed case),
sorting is simpler and more robust.

---

### 🧠 Memory of the Shuffled Souls Law

- **Anagrams** = words with the same letters in different order
- **Signature** = sorted form of the word — unique identifier for all anagrams
- `sort(key.begin(), key.end())` — reveals the hidden heart of each word
- **`unordered_map<string, vector<string>>`** — the Ledger of Kinship
- `groups[key].push_back(s)` — bind each scroll to its family
- After processing all words, collect all `entry.second` (the families)
- **Time:** O(N × K log K) — N words, each sorted in O(K log K)
- **Space:** O(N × K) — storing all words in the map
- **Edge cases:**
  - Empty string `""` — signature is `""`, forms its own group
  - Single character — signature is itself
  - All words identical — all go into one group
  - All words unique (no anagrams) — N groups of size 1

Thus is remembered the saga of **Group Anagrams**,
where the Archivist sorted every scroll's letters
to reveal its hidden signature —
the one true form shared by all kin —
and gathered every family together
in the Ledger of Kinship,
so that no anagram stood alone,
and no stranger was mistaken for kin. 🌀🔑✨
