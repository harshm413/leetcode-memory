## 🧩⚔️ _The Hidden Rearrangement: The Permutation in String Saga_

> \*"Within the Great Scroll lay a smaller sigil —
> its letters scattered,
> its order lost.
>
> The Oracle was commanded:
>
> **‘Tell us whether any rearrangement
> of this sigil appears
> as a continuous fragment
> inside the greater scroll.’**
>
> The sigil could be rearranged freely,
> but it had to appear **contiguously**.
>
> The Oracle knew this was not a search for exact order,
> but for **exact frequency** —
> the same letters,
> the same counts,
> merely reshuffled."\*

---

This is the saga of **Permutation in String**.

You are given:

-   a string `s1` (the sigil)
-   a string `s2` (the great scroll)

Your task:

-   Return `true` if **any permutation of `s1`**
-   Appears as a **substring of `s2`**
-   Otherwise, return `false`

---

## 🧠 The Oracle’s Core Insight — Frequencies, Not Order

The Oracle realized:

-   Any permutation has **the same character counts**
-   Order does not matter
-   A window of size `|s1|` sliding across `s2`
    can be tested for matching frequencies

Thus she used:

-   A **fixed-size sliding window**
-   Two frequency maps (or arrays)

---

## ⚔️ The Oracle’s Fixed-Window Ritual

_Slide, compare, and reveal_

```cpp
bool checkInclusion(string s1, string s2) {
    if (s1.size() > s2.size()) return false;

    vector<int> need(26, 0), window(26, 0);
```

The Oracle prepared two ledgers:

-   `need` → required character counts from `s1`
-   `window` → current window’s counts in `s2`

---

### 📜 Record the Sigil’s Signature

```cpp
    for (char c : s1) {
        need[c - 'a']++;
    }
```

The sigil’s frequency signature was fixed.

---

### 🌊 Slide Across the Great Scroll

```cpp
    int left = 0;
    for (int right = 0; right < s2.size(); right++) {
        window[s2[right] - 'a']++;
```

Each character entered the window.

---

### 🔻 Maintain Window Size

```cpp
        if (right - left + 1 > s1.size()) {
            window[s2[left] - 'a']--;
            left++;
        }
```

The window never exceeded the sigil’s length.

---

### ✨ Check for Perfect Match

```cpp
        if (window == need) return true;
    }
    return false;
}
```

If frequencies matched exactly,
a permutation was found.

---

### 🎺 The Trial of the Hidden Sigil

```cpp
int main() {
    string s1 = "ab";
    string s2 = "eidbaooo";

    cout << checkInclusion(s1, s2) << endl; // true
    return 0;
}
```

The substring `"ba"` appeared —
a rearrangement of `"ab"`.

The Oracle declared success.

---

### 🧠 Memory of the Hidden Rearrangement Law

-   Order does not matter, frequency does
-   Use a fixed-size sliding window
-   Compare frequency arrays
-   Window size = `|s1|`
-   **Time:** O(n)
-   **Space:** O(1) (fixed alphabet)

Thus is remembered the saga of
**Permutation in String**,
where the Oracle does not chase every ordering,
but instead listens for the **signature of characters**,
sliding patiently through the great scroll —
until a hidden rearrangement
reveals itself in perfect balance. 🧩✨
