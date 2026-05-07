## 🪟🔤 _The Unique Window: The Longest Substring Without Repeating Characters Saga_

> \_"The Oracle was given a string.
>
> She was commanded:
>
> **'Find the length of the longest substring
> without any repeating characters.'**
>
> A substring is contiguous. Every character in it must be unique.
>
> `"abcabcbb"` → `"abc"` (length 3).
> `"bbbbb"` → `"b"` (length 1).
> `"pwwkew"` → `"wke"` (length 3).
>
> The Oracle used a **sliding window** with a hashmap:
>
> **Expand the right pointer** to include new characters.
> **When a duplicate is found** — shrink the left pointer
> past the previous occurrence of that character.
>
> The window always contains only unique characters.
> Track the maximum window size."\_

---

This is the saga of **Longest Substring Without Repeating Characters (LeetCode 3)**.

Given a string `s`:
-   Find the length of the longest substring with all unique characters.

```
Input:  s = "abcabcbb"
Output: 3   ("abc")

Input:  s = "bbbbb"
Output: 1   ("b")

Input:  s = "pwwkew"
Output: 3   ("wke")

Input:  s = ""
Output: 0
```

---

## 🧠 The Sliding Window Insight

Maintain a window `[left, right]` where all characters are unique.

**Expand:** move `right` forward, adding characters to the window.
**Shrink:** when a duplicate enters, move `left` forward
until the duplicate is removed.

But instead of shrinking one step at a time (slow),
use a hashmap to JUMP `left` directly past the duplicate.

**HashMap stores:** `char → last index where it appeared`.

When `s[right]` is already in the map AND its last index ≥ left:
-   Jump `left` to `map[s[right]] + 1` (one past the duplicate).
-   This removes the old occurrence from the window instantly.

---

## 🧠 Why `map[s[right]] + 1` and Not Just `left++`?

Consider `"abba"`:

```
Window: [a, b] → right moves to 'b' at index 2.
'b' was last at index 1. Jump left to 1+1 = 2.
Window: [b] → right moves to 'a' at index 3.
'a' was last at index 0. But 0 < left (2). It's OUTSIDE the window.
Don't jump. Just update the map.
```

The `>= left` check ensures we only react to duplicates
INSIDE the current window. Old occurrences outside the window
are irrelevant — they've already been "removed."

---

### 📜 The Scroll of the Unique Window

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```

---

## 🪟 The Sliding Window Solution

### Initialize

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> lastSeen;
    int left = 0;
    int maxLen = 0;
```

`lastSeen` maps each character to the last index where it appeared.
`left` = left boundary of the window.
`maxLen` = longest unique substring found so far.

---

### Expand the window with right pointer

```cpp
    for (int right = 0; right < s.size(); right++) {
        char c = s[right];
```

`right` scans every character. The window is `[left, right]`.

---

### If duplicate found inside the window — jump left

```cpp
        if (lastSeen.count(c) && lastSeen[c] >= left) {
            left = lastSeen[c] + 1;
        }
```

Two conditions must BOTH hold:
-   `lastSeen.count(c)`: we've seen this character before.
-   `lastSeen[c] >= left`: the previous occurrence is INSIDE the current window.

If both true: jump `left` to one past the previous occurrence.
This removes the old duplicate from the window in O(1).

Why not just `left++`? That would shrink by one step.
The duplicate might be far back — we'd need many steps.
Jumping directly is O(1).

> _"The Oracle doesn't inch the window forward.
> She teleports the left boundary past the offender.
> One jump. The duplicate is gone."_

---

### Update the map

```cpp
        lastSeen[c] = right;
```

Record (or update) the latest position of this character.
Whether it was a duplicate or not, the map always holds
the MOST RECENT index.

---

### Update the maximum length

```cpp
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

Window size = `right - left + 1`. Update if it's the longest seen.

---

### 🎺 The Trial of the Unique Window

```cpp
int main() {
    cout << lengthOfLongestSubstring("abcabcbb") << endl; // expected: 3
    cout << lengthOfLongestSubstring("bbbbb") << endl;    // expected: 1
    cout << lengthOfLongestSubstring("pwwkew") << endl;   // expected: 3
    cout << lengthOfLongestSubstring("") << endl;         // expected: 0
    cout << lengthOfLongestSubstring("abba") << endl;     // expected: 2
    return 0;
}
```

---

**Full trace for s = "abcabcbb":**

| right | c | lastSeen[c] | ≥ left? | left | window | size | maxLen |
|-------|---|-------------|---------|------|--------|------|--------|
| 0 | a | — | — | 0 | [a] | 1 | 1 |
| 1 | b | — | — | 0 | [a,b] | 2 | 2 |
| 2 | c | — | — | 0 | [a,b,c] | 3 | **3** |
| 3 | a | 0 | 0≥0 ✓ | **1** | [b,c,a] | 3 | 3 |
| 4 | b | 1 | 1≥1 ✓ | **2** | [c,a,b] | 3 | 3 |
| 5 | c | 2 | 2≥2 ✓ | **3** | [a,b,c] | 3 | 3 |
| 6 | b | 4 | 4≥3 ✓ | **5** | [c,b] | 2 | 3 |
| 7 | b | 6 | 6≥5 ✓ | **7** | [b] | 1 | 3 |

**Answer: 3** ✓ (window "abc" at multiple positions)

---

**Trace for s = "abba":**

| right | c | lastSeen[c] | ≥ left? | left | window | maxLen |
|-------|---|-------------|---------|------|--------|--------|
| 0 | a | — | — | 0 | [a] | 1 |
| 1 | b | — | — | 0 | [a,b] | 2 |
| 2 | b | 1 | 1≥0 ✓ | **2** | [b] | 2 |
| 3 | a | 0 | 0≥2? **No** | 2 | [b,a] | **2** |

**Answer: 2** ✓

The critical moment: at index 3, 'a' was last seen at index 0.
But `0 < left (2)` — it's OUTSIDE the window. Ignore it.
Without the `>= left` check, we'd wrongly jump left to 1.

---

**Trace for s = "bbbbb":**

| right | c | lastSeen[c] | ≥ left? | left | window | maxLen |
|-------|---|-------------|---------|------|--------|--------|
| 0 | b | — | — | 0 | [b] | 1 |
| 1 | b | 0 | 0≥0 ✓ | 1 | [b] | 1 |
| 2 | b | 1 | 1≥1 ✓ | 2 | [b] | 1 |
| 3 | b | 2 | 2≥2 ✓ | 3 | [b] | 1 |
| 4 | b | 3 | 3≥3 ✓ | 4 | [b] | 1 |

**Answer: 1** ✓ (every character is a duplicate, window never grows past 1)

---

## 🔍 Using Array Instead of HashMap

For ASCII characters (128 possible), an array is faster than a hashmap:

```cpp
int lengthOfLongestSubstring(string s) {
    vector<int> lastSeen(128, -1);
    int left = 0, maxLen = 0;
    for (int right = 0; right < s.size(); right++) {
        if (lastSeen[s[right]] >= left) {
            left = lastSeen[s[right]] + 1;
        }
        lastSeen[s[right]] = right;
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

Initialize to -1 (never seen). No `count()` check needed —
`-1 >= left` is always false (left ≥ 0). Cleaner.

O(1) lookup instead of O(1) amortized. Slightly faster in practice.

---

## 🔍 The Sliding Window Pattern Family

| Problem | Window type | Shrink condition |
|---------|------------|-----------------|
| **This problem** | Variable, expand right | Duplicate found → jump left |
| Min Window Substring | Variable, expand right | All chars covered → shrink left |
| Max Consecutive Ones III | Variable, expand right | Too many flips → shrink left |
| Permutation in String | Fixed size | Frequency mismatch |

This is the foundational variable-window sliding window problem.
Master this and the pattern applies everywhere.

---

### 🧠 Memory of the Unique Window Law

-   **Sliding window** `[left, right]` with all unique characters
-   **HashMap:** `char → last index seen`
-   **Duplicate inside window:** `lastSeen[c] >= left` → jump `left = lastSeen[c] + 1`
-   **Duplicate outside window:** `lastSeen[c] < left` → ignore, just update map
-   **Window size:** `right - left + 1`
-   **Array alternative:** `vector<int>(128, -1)` for ASCII — faster, cleaner
-   **Time:** O(N). **Space:** O(min(N, 128)) = O(1) for ASCII.

Thus is remembered the saga of **Longest Substring Without Repeating Characters**,
where the Oracle maintained a window of unique characters —
expanding right to explore,
jumping left to banish duplicates —
the hashmap remembering where each character last stood,
the `>= left` check distinguishing live duplicates from ghosts —
until the longest unique window was found
in a single sweep through the string. 🪟🔤✨
