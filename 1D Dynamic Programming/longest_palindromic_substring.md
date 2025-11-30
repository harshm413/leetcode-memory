## 🌙💫 _The Mirror of Moonlit Strings: The Longest Palindromic Substring Saga_

> \*"In the Kingdom of Letters,
> a single string stretched across the night sky
> like a trail of shimmering stars.
>
> Hidden within this cosmic ribbon
> lay a secret:
> **a substring that reads the same forward and backward —
> the purest reflection of the moon.**
>
> The Oracle was summoned to find
> the longest such mirror-born fragment,
> for palindromes are the language of celestial balance."\*

---

A palindrome is a word or fragment
whose reflection perfectly matches itself.

The Oracle’s task:
from a given string `s`,
find the **longest palindromic substring**.

She used the ancient technique of:

### ✨ Expanding Around the Center

Every palindrome grows outward
from either:

-   **a single center** (odd length), or
-   **a pair of matching centers** (even length).

Thus began the saga of
**Longest Palindromic Substring**.

---

### 📜 The Scroll of Celestial Letters

```cpp
#include <iostream>
#include <string>
using namespace std;
```

The stars of the string awaited their revelation.

---

## 🌗 The Oracle’s Ritual of Expansion

```cpp
string expand(string& s, int left, int right) {
    while (left >= 0 && right < s.size() && s[left] == s[right]) {
        left--;
        right++;
    }
    return s.substr(left + 1, right - left - 1);
}
```

She placed her hands upon two positions,
mirroring them outward:

-   If the letters matched,
    she expanded further.
-   If not,
    she returned the last valid palindrome
    formed between them.

This expansion revealed
every possible palindromic core.

---

## 🌙 The Search for the Longest Mirror

```cpp
string longestPalindrome(string s) {
    string best = "";
```

Her quest began
with no palindrome chosen.

---

### 🌑 Inspect Every Center in the Sky

```cpp
    for (int i = 0; i < s.size(); i++) {
        string odd  = expand(s, i, i);
        string even = expand(s, i, i + 1);
```

She expanded around:

-   `i` alone → **odd-length palindrome**
-   `i` and `i+1` → **even-length palindrome**

Two mirrors for every star.

---

### 🌟 Keep the Brightest Reflection

```cpp
        if (odd.size()  > best.size()) best = odd;
        if (even.size() > best.size()) best = even;
    }
    return best;
}
```

Whichever palindrome
shone brightest —
longest in length —
became the new champion.

The Oracle returned this celestial jewel.

---

### 🎺 The Trial of the Lunar String

```cpp
int main() {
    cout << longestPalindrome("babad") << endl; // "bab" or "aba"
    cout << longestPalindrome("cbbd") << endl;  // "bb"
    return 0;
}
```

The Oracle unveiled:

-   From `"babad"` → **"bab"** (or "aba")
-   From `"cbbd"` → **"bb"**

The moonlit mirrors whispered their truths.

---

### 🧠 Memory of the Lunar Mirror

-   Expand around every index (odd & even).
-   Expansion stops when characters differ.
-   Keep track of the longest palindrome seen.
-   **Time:** O(n²)
-   **Space:** O(1)

Thus is remembered the saga of
**Longest Palindromic Substring**,
where the Oracle wanders the night sky,
awakening hidden mirrors within the stars,
until the longest reflection
shines in perfect celestial symmetry. 🌙✨
