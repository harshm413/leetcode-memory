## 🔐 _The Unbroken Thread: The Longest Substring Without Repeating Characters Saga_

> _"In the loom of letters, some threads never knot.
> We seek the longest stretch where each color is unique —
> a ribbon unspoiled by repeating threads."_

---

A weaver sat before a spool of characters, pulling thread to form the longest ribbon where no color repeated. If a color recurred, the weaver slid the left hand forward to remove the earlier instance and continued — always keeping a window of unique colors, measuring its length, and remembering the largest ribbon seen so far.

Thus began the ritual of the **Longest Substring Without Repeating Characters**.

---

### 📜 The Loom and the Shuttle

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <unordered_map>
using namespace std;
```

The weaver set out tools: the string of thread (`string s`), a map to remember the last index of each character (`unordered_map<char,int>`), and two hands — `left` and `right` — forming a sliding window across the cloth.

---

### 🧵 The Weaving Rite — Sliding Window

```cpp
int lengthOfLongestSubstring(const string& s) {
    unordered_map<char, int> lastIndex;
    int left = 0;
    int best = 0;
```

`lastIndex` records where a color last appeared. `left` is the start of the current unique window. `best` keeps the length of the longest spotless ribbon so far.

---

```cpp
    for (int right = 0; right < s.size(); ++right) {
        char c = s[right];
```

The right hand draws a new thread `c` into the window. Each arrival must be checked: has this color been seen inside the current ribbon?

---

```cpp
        if (lastIndex.count(c) && lastIndex[c] >= left) {
            // a repeat inside the current window — slide left past its previous occurrence
            left = lastIndex[c] + 1;
        }
```

If `c` was seen before and its last spot lies within the current window (`>= left`), the weaver moves the left hand to just after that previous occurrence. This removes the earlier color from the window, restoring uniqueness.

---

```cpp
        lastIndex[c] = right;            // record the newest position of c
        best = max(best, right - left + 1); // update best length
    }
    return best;
}
```

After adjusting, the weaver records the latest index of `c` and measures the window’s length (`right - left + 1`). If it’s the longest ribbon yet, `best` grows. The loop continues until the spool is exhausted.

---

### 🎺 The Trial of Threads

```cpp
int main() {
    vector<string> tests = {"abcabcbb", "bbbbb", "pwwkew", "", "au", "dvdf"};
    for (auto &t : tests) {
        cout << "\"" << t << "\" -> " << lengthOfLongestSubstring(t) << endl;
    }
    return 0;
}
```

From these spools the weaver draws ribbons:

-   `"abcabcbb"` → longest unique is `"abc"` length `3`.
-   `"bbbbb"` → only `"b"` length `1`.
-   `"pwwkew"` → `"wke"` or `"kew"` length `3`.
-   `""` → empty spool `0`.
-   `"au"` → `2`.
-   `"dvdf"` → `"vdf"` length `3` (note how left moves past the first `d` when the second appears).

---

### 🧠 Memory of the Loom

-   **Sliding window** — `left` and `right` hands maintain a current unique substring.
-   **lastIndex map** — remember the most recent position of each character.
-   **When a repeat within window appears:** move `left = lastIndex[c] + 1`.
-   **Update lastIndex\[c] = right** and **best = max(best, right-left+1)** each step.
-   **Time:** O(n) average, **Space:** O(min(n, charset_size)).

Thus is remembered the saga of the **Longest Substring Without Repeating Characters** — the weaver’s quest to pull the longest unbroken ribbon of unique colors from the spool of letters.
