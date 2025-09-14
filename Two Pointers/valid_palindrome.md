## 🔍 _The Mirror Path: The Valid Palindrome Saga_

> _"A path reads the same forward and back,
> if only you ignore the weeds and scars along the way.
> Clean the stone, lower the banner, and the mirror will show truth."_

---

A long corridor of letters and marks stretched through the castle — some letters noble, some punctuation like dirt on cobblestones, some spaces like fallen leaves. The Keeper of Runes wished to know whether the message carved on the hall read the same from either end — but only after sweeping away the dirt and treating uppercase and lowercase as kin.

Thus began the rite of the **Valid Palindrome**.

---

### 📜 The Keeper’s Tools

```cpp
#include <iostream>
#include <string>
#include <cctype>
using namespace std;
```

The Keeper took parchment and lamp. `cctype` provided lanterns to spot valid stones (alphanumeric) and to lower banners (tolower) so that `A` and `a` would be treated as the same spirit.

---

### 🪞 The Ritual — Two Fingers of the Mirror

```cpp
bool isPalindrome(const string& s) {
    int i = 0, j = s.size() - 1;
```

Two fingers reached from the ends of the corridor: `i` from the left, `j` from the right. They would move inward, comparing only the polished stones (alphanumeric characters).

---

```cpp
    while (i < j) {
        while (i < j && !isalnum(s[i])) i++; // skip left dirt
        while (i < j && !isalnum(s[j])) j--; // skip right dirt
```

When the left finger found a puddle of punctuation or a space, it stepped over it; the right did the same. Both advanced until they found valid letters or crossed paths.

---

```cpp
        if (tolower(s[i]) != tolower(s[j])) return false; // mismatch revealed
        i++; j--;
    }
    return true;
}
```

If, once cleaned and normalized, the two characters differed, the corridor was not a mirror — the ritual returned `false`. If all mirrored pairs matched until the fingers met or crossed, the Keeper declared `true`: the path reads the same both ways.

---

### 🎺 The Trials of the Hall

```cpp
int main() {
    vector<string> tests = {
        "A man, a plan, a canal: Panama",
        "race a car",
        "",
        "0P",
        "Able was I, ere I saw Elba"
    };
    for (auto& t : tests) {
        cout << "\"" << t << "\" -> " << (isPalindrome(t) ? "true" : "false") << endl;
    }
    return 0;
}
```

The Keeper tested many halls: a famous canal proclamation, a clumsy race whisper, an empty banner, a trick with digits and letters, and an old palindrome that rings like legend. Each was swept and judged in turn.

---

### 🧠 Memory of the Mirror

-   **Two-pointer** technique — one from each end, meeting in the middle.
-   **Skip non-alphanumeric** — treat punctuation and spaces as cobblestone dirt to step over.
-   **tolower comparison** — uppercase and lowercase are considered the same.
-   **Return false on first mismatch** — fail fast when the mirror breaks.
-   **O(n) time, O(1) space** — only two indices and a few checks are needed.

Thus is remembered the saga of the **Valid Palindrome**, where a corridor’s message becomes clear only after the Keeper sweeps away the weeds and views its reflected truth.
