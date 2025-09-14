## 🌀 _The Gathering of Shuffled Souls: The Group Anagrams Saga_

> _"Though their faces differ,
> their hearts are the same.
> Letters shuffled like leaves in the wind,
> yet bound together as kin."_

---

Across the kingdom, countless scrolls fluttered in the air. Each bore words, yet the words seemed scattered and strange.
The people asked the Oracle:

> _“Which scrolls are kin?
> Which carry the same letters, though jumbled in form?”_

Thus began the saga of the **Group Anagrams**.

---

### 📜 The Scroll of Kinship

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;
```

The scribes gathered their tools:

-   **vector** of scrolls, each word awaiting judgment.
-   **unordered_map**, the Ledger of Kinship, where scrolls of the same letters would be bound together.
-   And the spell of **sorting**, to reveal each scroll’s true inner form.

---

### 🔮 The Oracle’s Ritual

```cpp
vector<vector<string>> groupAnagrams(vector<string>& strs) {
```

The Oracle opened her hands and spoke **groupAnagrams**. The scrolls were placed before her, a chaotic sea of shuffled letters.

---

### 🗝️ The Hidden Key

```cpp
    unordered_map<string, vector<string>> groups;
```

The Ledger of Kinship was opened. Each group of scrolls would be tied to a hidden **key** — the sorted version of their letters.

For though “tea,” “eat,” and “ate” appeared different, their sorted hearts all whispered the same: “aet.”

---

### 📖 The Sorting of Souls

```cpp
    for (string s : strs) {
        string key = s;
        sort(key.begin(), key.end());
        groups[key].push_back(s);
    }
```

One by one, each scroll `s` was lifted. The Oracle sorted its letters, revealing its inner truth — the **key**.

The scroll was then placed into its rightful family within the Ledger. All who shared the same heart (`key`) were gathered together.

---

### 🌌 The Gathering

```cpp
    vector<vector<string>> result;
    for (auto& entry : groups) {
        result.push_back(entry.second);
    }
    return result;
}
```

When the last scroll had been judged, the Oracle closed the Ledger. From it she drew forth the families: groups of scrolls, each kin to one another, each carrying the same letters in different dances.

Thus, the scattered were united.

---

### 🎺 The Trial of Six Scrolls

```cpp
int main() {
    vector<string> strs = {"eat","tea","tan","ate","nat","bat"};
```

Six scrolls fluttered into the square: _eat, tea, tan, ate, nat, bat_.

---

```cpp
    vector<vector<string>> ans = groupAnagrams(strs);
    for (auto& group : ans) {
        for (auto& word : group) cout << word << " ";
        cout << endl;
    }
}
```

The Oracle revealed the truth:

-   _eat, tea, ate_ — bound by the heart “aet.”
-   _tan, nat_ — bound by “ant.”
-   _bat_ — alone, yet whole, with “abt.”

The people marveled, for the kinship of shuffled souls was made clear.

---

### 🧠 Memory of the Kin

-   **sort(key.begin(), key.end())** → sorting reveals the hidden heart of each word.
-   **unordered_map\<string, vector<string>>** → the Ledger of Kinship.
-   **groups\[key].push_back(s)** → bind each scroll to its family.
-   **result** → the final gathering of all kin groups.

Thus is remembered the saga of the **Group Anagrams**, where shuffled letters found their families, and no soul stood misunderstood. 🌀📜
