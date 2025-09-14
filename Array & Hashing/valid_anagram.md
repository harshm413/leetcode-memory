## 🔄 _The Twin Scrolls: The Valid Anagram Saga_

> _"Two scrolls may carry different words,
> yet if their letters weigh the same,
> they are but mirrors of each other —
> true anagrams, bound by fate."_

---

In the land of scribes, two scrolls were discovered. At first glance, their words were different, scattered in seeming chaos. Yet whispers spread:

> _“Perhaps they are not strangers at all,
> but the same song sung in shuffled order.”_

The Oracle was called to judge. Thus began the saga of the **Valid Anagram**.

---

### 📜 The Scrolls of Judgment

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```

The scribes opened their libraries (`iostream`, `string`). To weigh truth, they summoned the **Book of Counts** (`unordered_map`), which could tally the strength of each letter.

---

### 🔍 The Oracle’s Decree

```cpp
bool isAnagram(string s, string t) {
```

The Oracle raised her staff and began the incantation **isAnagram**. Two scrolls, `s` and `t`, were placed before her.

---

### 📏 The First Test

```cpp
    if (s.size() != t.size()) return false;
```

The Oracle measured the length of each scroll. If their sizes differed, the judgment was instant: _false_.

For how could twin songs exist if their notes were unequal in number?

---

### 📝 The Counting of Letters

```cpp
    unordered_map<char, int> count;
```

The **Book of Counts** was opened. Its empty pages awaited ink — to tally each letter’s weight.

---

```cpp
    for (char c : s) {
        count[c]++;
    }
```

The Oracle walked through the first scroll. Each letter `c` was tallied in the book. If a letter appeared thrice, its mark rose thrice. Thus the first scroll’s essence was captured.

---

### 🧾 The Unraveling of the Twin

```cpp
    for (char c : t) {
        if (count[c] == 0) return false;
        count[c]--;
    }
```

The Oracle turned to the second scroll. For each letter `c`, she checked the Book of Counts.

If no mark remained for that letter, the scrolls could never be twins — the verdict was _false_.
Otherwise, the count was reduced, unraveling the first scroll’s tally one step at a time.

When the scroll `t` was fully read, every count must return to balance.

---

### 🌅 The Judgment

```cpp
    return true;
}
```

If no contradictions arose, the Oracle closed the book and declared: _true_.
The two scrolls were revealed as true anagrams — twin songs written with shuffled ink.

---

### 🎺 The Trial of Two Scrolls

```cpp
int main() {
    string s = "listen";
    string t = "silent";
```

Two scrolls appeared: “listen” and “silent.” Their letters, though shuffled, bore the same weight.

---

```cpp
    cout << (isAnagram(s, t) ? "true" : "false") << endl;
}
```

The Oracle proclaimed: _true_.
The crowd gasped, for the twin scrolls had been unveiled.

---

### 🧠 Memory of the Twin Scrolls

-   **Length check** → if sizes differ, the scrolls can never be twins.
-   **First loop** → tally letters of the first scroll into the Book of Counts.
-   **Second loop** → unravel the second scroll, reducing the tallies.
-   **Final return** → if all tallies balanced, the scrolls are true anagrams.

Thus is remembered the saga of the **Valid Anagram**, where twin scrolls sang in harmony, even when their letters danced in different order. 🔄📜
