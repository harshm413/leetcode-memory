## 🌿📜 _The Banner of Shared Beginnings: The Longest Common Prefix Saga_

> \*"In the Valley of Words stood many banners,
> each bearing a different name.
>
> Though their endings diverged like winding roads,
> some shared the same beginning —
> a common crest stitched at the start of every banner.
>
> The Oracle was summoned with a single question:
>
> **‘What is the longest prefix
> shared by all these words?’**
>
> She knew the answer lay not in endings,
> but in walking the words **letter by letter together**,
> stopping the moment harmony was broken."\*

---

The Oracle was given an array of strings.
Her task was to find the **longest common prefix**
shared by **all** strings.

If no common beginning existed,
the answer would be an empty string.

She chose a simple, powerful ritual:

> **Take the first word as a guide,
> and compare its letters
> against every other word,
> one position at a time.**

The moment a mismatch appears,
the shared banner ends.

Thus began the saga of **Longest Common Prefix**.

---

### 📜 The Scroll of United Words

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

The banners were laid out before her.

---

## 🔮 The Oracle’s Prefix-Walking Ritual

_Compare characters column by column_

```cpp
string longestCommonPrefix(vector<string>& strs) {
    if (strs.empty()) return "";
```

If no banners were given,
there could be no shared beginning.

---

### 🏳️ Choose the First Banner as the Guide

```cpp
    string prefix = strs[0];
```

The Oracle held the first word aloft.
It would define the **maximum possible prefix**.

---

### 🔍 Compare With Every Other Banner

```cpp
    for (int i = 1; i < strs.size(); i++) {
        int j = 0;
        while (j < prefix.size() && j < strs[i].size()
               && prefix[j] == strs[i][j]) {
            j++;
        }
        prefix = prefix.substr(0, j);
```

She aligned the banners side by side
and walked them together:

-   As long as letters matched → advance
-   At the first mismatch → stop

The prefix was shortened
to the portion still shared.

---

### 🧱 If the Prefix Vanishes

```cpp
        if (prefix.empty()) return "";
    }
    return prefix;
}
```

If at any moment no shared letters remained,
the Oracle declared:

> _“There is no common banner.”_

Otherwise, the surviving prefix was returned.

---

### 🎺 The Trial of the Banners

```cpp
int main() {
    vector<string> words1 = {"flower", "flow", "flight"};
    cout << longestCommonPrefix(words1) << endl; // "fl"

    vector<string> words2 = {"dog", "racecar", "car"};
    cout << longestCommonPrefix(words2) << endl; // ""

    return 0;
}
```

The Oracle revealed:

-   `"flower"`, `"flow"`, `"flight"` → **"fl"**
-   `"dog"`, `"racecar"`, `"car"` → **""** (no shared beginning)

The banners spoke clearly.

---

### 🧠 Memory of the Shared Banner Law

-   Use the first string as the initial prefix
-   Compare character by character with all others
-   Shrink prefix on first mismatch
-   Stop early if prefix becomes empty
-   **Time:** O(n × m)

    -   n = number of strings
    -   m = length of shortest string

-   **Space:** O(1)

Thus is remembered the saga of **Longest Common Prefix**,
where the Oracle aligns many words beneath a single sky,
searching for the shared crest at their beginnings —
and stopping the instant unity breaks. 🌿✨
