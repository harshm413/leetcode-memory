## 🌌✨ _The Thousand Hidden Mirrors: The Palindromic Substrings Saga_

> \*"In the Kingdom of Luminous Strings,
> the Oracle gazed upon a ribbon of letters
> threaded across the dusk sky.
>
> Legends spoke that within this ribbon
> slept countless **palindromic fragments** —
> tiny mirrors glimmering in the dark,
> each reflecting itself in perfect symmetry.
>
> Her task was vast:
> **count every palindromic substring** hidden within,
> from the smallest spark to the grandest mirror."\*

---

Unlike the earlier quest
where the Oracle sought only **the longest** mirror,
now she must uncover **every** palindrome —
every substring that reads the same forward and backward.

The sacred technique remained:

### ✨ Expanding Around Centers

but now used exhaustively,
counting every glimmering palindrome
born from each center.

Thus began the saga of **Palindromic Substrings**.

---

### 📜 The Scroll of Reflective Letters

```cpp
#include <iostream>
#include <string>
using namespace std;
```

The sky-ribbon of letters awaited exploration.

---

## 🌗 The Oracle’s Expansion Ritual

_Every center may birth many palindromes_

```cpp
int expand(string& s, int left, int right) {
    int count = 0;
    while (left >= 0 && right < s.size() && s[left] == s[right]) {
        count++;
        left--;
        right++;
    }
    return count;
}
```

Starting from a center,
the Oracle expanded outward:

-   If left and right matched,
    she discovered a **new palindrome**,
    counted it,
    and expanded further.

This method captured all mirrors
from tiny single letters
to broad shining arcs.

---

## 🌙 The Grand Count of Mirrors

```cpp
int countSubstrings(string s) {
    int total = 0;
```

She carried a tally of all palindromic reflections found.

---

### 🌑 Expand Around Every Possible Center

```cpp
    for (int i = 0; i < s.size(); i++) {
        total += expand(s, i, i);     // odd length
        total += expand(s, i, i + 1); // even length
    }
    return total;
}
```

For every letter in the sky-ribbon, she inspected:

-   **odd centers** → palindromes with a single heart
-   **even centers** → palindromes with a twin heart

Each expansion yielded
a cluster of mirrored fragments.

The total count grew
as the Oracle uncovered mirror after mirror.

---

### 🎺 The Trial of the Many Mirrors

```cpp
int main() {
    cout << countSubstrings("abc") << endl;  // 3
    cout << countSubstrings("aaa") << endl;  // 6
    return 0;
}
```

For `"abc"`:
Only `"a"`, `"b"`, `"c"` — **3 palindromes**

For `"aaa"`:

-   `"a"` at each position (3)
-   `"aa"` between each pair (2)
-   `"aaa"` entire string (1)
    Total = **6 palindromes**

The Oracle counted them all.

---

### 🧠 Memory of the Mirror Multitude

-   Every single character is a palindrome.
-   Expand around all `2n - 1` centers.
-   Count each unique palindrome as it forms.
-   **Time:** O(n²)
-   **Space:** O(1)

Thus is remembered the saga of **Palindromic Substrings**,
where the Oracle wanders across a sky-thread of letters,
awakening every hidden mirror —
from the faintest spark
to wide reflections stretching across the night. 🌌✨
