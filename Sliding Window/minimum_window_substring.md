## 🪟👑 _The Smallest Window of Destiny: The Minimum Window Substring Saga_

> \*"Within the Grand Scroll lay many letters,
> scattered, repeating, disguising one another.
>
> The Oracle was commanded:
>
> **‘Find the smallest window
> that contains every required symbol —
> not just present,
> but present in the right counts.’**
>
> This was no simple search.
>
> A letter once found was not enough —
> duplicates mattered,
> and excess meant nothing.
>
> The Oracle knew she must expand with patience
> and shrink with precision —
> guarding a window that held **exactly what was needed**,
> and nothing more."\*

---

This is the saga of **Minimum Window Substring**.

You are given:

-   a string `s` (the great scroll)
-   a string `t` (the required sigil)

Your task:

-   Find the **smallest substring of `s`**
-   That contains **all characters of `t`**
-   Including correct **frequencies**
-   Return `""` if no such window exists

---

## 🧠 The Oracle’s Core Insight — Balance Required vs Formed

The Oracle tracked two truths:

-   `need[c]` → how many times character `c` is required
-   `window[c]` → how many times `c` appears in the current window

And two sacred counts:

-   `required` → number of **distinct** characters in `t`
-   `formed` → how many of those are currently satisfied

Only when:

```
formed == required
```

was the window valid.

Then — and only then — could it be shrunk.

---

### 📜 The Scroll of Sacred Counts

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```

---

## ⚔️ The Oracle’s Sliding Window Ritual

_Expand to gather, shrink to perfect_

```cpp
string minWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;
```

The Oracle inscribed the sigil’s requirements.

---

### 🧭 Prepare the Journey

```cpp
    int left = 0, right = 0;
    int formed = 0;
    int required = need.size();
```

Two boundaries were placed.
The journey began.

---

### 🧲 Expand the Right Boundary

```cpp
    int minLen = INT_MAX;
    int start = 0;

    while (right < s.size()) {
        char c = s[right];
        window[c]++;
```

Each character entered the window.

---

### ✨ Check If a Requirement Is Satisfied

```cpp
        if (need.count(c) && window[c] == need[c]) {
            formed++;
        }
```

A required symbol reached perfect balance.

---

### 🔻 Shrink While Window Is Valid

```cpp
        while (left <= right && formed == required) {
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                start = left;
            }
```

The Oracle recorded the smallest valid window so far.

---

### 🧹 Remove the Leftmost Character

```cpp
            char d = s[left];
            window[d]--;
            if (need.count(d) && window[d] < need[d]) {
                formed--;
            }
            left++;
        }
        right++;
    }
```

If removing a character broke balance,
the window stopped shrinking.

---

### 🏁 Reveal the Window of Destiny

```cpp
    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

If no valid window was found,
emptiness was returned.

---

### 🎺 The Trial of the Sacred Sigil

```cpp
int main() {
    string s = "ADOBECODEBANC";
    string t = "ABC";

    cout << minWindow(s, t) << endl; // expected: "BANC"
    return 0;
}
```

The Oracle found:

-   `"BANC"`
-   The smallest window containing `A`, `B`, and `C`

No shorter truth existed.

---

### 🧠 Memory of the Window Law

-   Sliding window with two pointers
-   Track required vs current frequencies
-   Expand until all requirements met
-   Shrink to minimize length
-   Duplicates in `t` must be matched exactly
-   **Time:** O(n)
-   **Space:** O(1) (bounded alphabet)

Thus is remembered the saga of
**Minimum Window Substring**,
where the Oracle gathers symbols with patience,
then tightens her grip with precision —
revealing the **smallest possible window**
that satisfies destiny’s exact demands. 🪟✨
