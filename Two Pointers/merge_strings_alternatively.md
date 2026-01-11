## 🔗📜 _The Weaving of Two Voices: The Merge Strings Alternately Saga_

> \*"In the Loom of Letters,
> two voices sought to be heard —
> each with its own rhythm,
> each with its own length.
>
> The Oracle was commanded:
>
> **‘Weave the two strings together,
> taking one character from the first,
> then one from the second,
> alternating faithfully —
> and when one voice falls silent,
> let the other finish its song.’**
>
> No voice could cut ahead.
> No order could be broken.
>
> Only a careful alternation
> could produce the final hymn."\*

---

This is the saga of **Merge Strings Alternately**.

You are given two strings `word1` and `word2`.
Your task:

-   Merge them by taking characters **alternately**
-   Start with `word1`
-   If one string ends earlier, append the rest of the other

---

## 🧠 The Oracle’s Core Insight — Two Pointers, One Weave

The Oracle realized:

-   Each string must be read **left to right**
-   Characters must be taken **one at a time**
-   Two pointers are enough — one for each voice

Thus, she chose a synchronized march.

---

### 📜 The Scroll of Intertwined Words

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## ⚔️ The Oracle’s Alternating Weave Ritual

_One step from each voice_

```cpp
string mergeAlternately(string word1, string word2) {
    int i = 0, j = 0;
    string result;
```

Two fingers traced the strings:

-   `i` for `word1`
-   `j` for `word2`

---

### 🎶 Weave While Both Voices Speak

```cpp
    while (i < word1.size() && j < word2.size()) {
        result.push_back(word1[i++]);
        result.push_back(word2[j++]);
    }
```

One character from the first voice,
then one from the second —
perfect alternation.

---

### 🧩 Let the Remaining Voice Finish

```cpp
    while (i < word1.size()) {
        result.push_back(word1[i++]);
    }
    while (j < word2.size()) {
        result.push_back(word2[j++]);
    }
```

When one voice ended,
the other was allowed to sing freely.

---

### 🏁 Return the Final Hymn

```cpp
    return result;
}
```

The weave was complete.

---

### 🎺 The Trial of the Interwoven Voices

```cpp
int main() {
    string word1 = "abc";
    string word2 = "pqrstu";

    cout << mergeAlternately(word1, word2) << endl;
    // expected: "apbqcrstu"
    return 0;
}
```

The Oracle produced:

`a p b q c r s t u`

Two voices,
one seamless song.

---

### 🧠 Memory of the Weaving Law

-   Use two pointers, one per string
-   Alternate characters while both remain
-   Append remaining characters afterward
-   Order within each string is preserved
-   **Time:** O(n + m)
-   **Space:** O(n + m)

Thus is remembered the saga of **Merge Strings Alternately**,
where the Oracle weaves two voices into one melody,
never letting one dominate the other —
until silence claims one,
and the surviving voice
carries the song to its end. 🔗✨
