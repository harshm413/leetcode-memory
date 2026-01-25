## 🌈⚔️ _The Unbroken Path of Symbols: The Longest Substring Without Repeating Characters Saga_

> \*"In the Scroll of Symbols,
> characters marched one after another —
> some loyal, some treacherous,
> some returning too soon.
>
> The Oracle was commanded:
>
> **‘Find the longest continuous stretch
> where no symbol repeats.’**
>
> A symbol could not be trusted twice.
>
> The Oracle could not restart the journey each time —
> that would be too slow.
>
> Instead, she carried a **moving window of trust**,
> expanding when harmony held,
> and shrinking the moment betrayal appeared."\*

---

This is the saga of **Longest Substring Without Repeating Characters**.

You are given a string `s`.
Your task:

-   Find the **length of the longest substring**
-   With **all unique characters**

---

## 🧠 The Oracle’s Core Insight — Sliding Window of Trust

The Oracle realized:

-   A substring with all unique characters
    must lie within a window with **no duplicates**
-   When a duplicate appears,
    the window must move forward — not restart

So she used:

-   Two pointers (`left`, `right`)
-   A structure to remember **last seen positions**

---

### 📜 The Scroll of Passing Symbols

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```

---

## ⚔️ The Oracle’s Sliding Window Ritual

_Expand when unique, shrink when repeated_

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> lastSeen;
    int left = 0;
    int best = 0;
```

The Oracle prepared:

-   `lastSeen` → last index of each character
-   `left` → start of the current window

---

### 🌊 Walk the Scroll One Symbol at a Time

```cpp
    for (int right = 0; right < s.size(); right++) {
        char c = s[right];
```

Each character stepped into the window.

---

### 🚨 Detect Repetition and Shift the Window

```cpp
        if (lastSeen.count(c) && lastSeen[c] >= left) {
            left = lastSeen[c] + 1;
        }
```

If the character was already inside the window,
the Oracle moved `left` just past its previous position.

No overlap.
No betrayal.

---

### ✨ Record the New Longest Path

```cpp
        lastSeen[c] = right;
        best = max(best, right - left + 1);
    }
    return best;
}
```

The Oracle updated:

-   the last position of the character
-   the maximum window length seen so far

---

### 🎺 The Trial of the Symbol Path

```cpp
int main() {
    string s = "abcabcbb";
    cout << lengthOfLongestSubstring(s) << endl; // expected: 3
    return 0;
}
```

The Oracle found the longest unbroken path:

`"abc"` → length **3**

No repeated symbols.
Perfect harmony.

---

### 🧠 Memory of the Unbroken Path Law

-   Use sliding window with two pointers
-   Store last seen index of each character
-   On repetition, move left pointer forward
-   Always expand right pointer
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Longest Substring Without Repeating Characters**,
where the Oracle walks a path of symbols,
never turning back completely,
only tightening the window of trust —
until the longest stretch of pure uniqueness
stands revealed in the scroll of fate. 🌈✨
