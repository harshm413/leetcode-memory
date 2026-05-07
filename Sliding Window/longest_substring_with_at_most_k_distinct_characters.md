## 🪟🎨 _The K-Color Palette: The Longest Substring With At Most K Distinct Characters Saga_

> \_"The Oracle was given a string and a number K.
>
> She was commanded:
>
> **'Find the longest substring with at most K distinct characters.'**
>
> The Oracle recognized this immediately:
>
> **Same as Fruit Into Baskets — but with variable K instead of 2.**
>
> Sliding window. Hashmap tracks character frequencies.
> `map.size()` = distinct characters in the window.
> Expand right. Shrink left when `map.size() > K`.
> Track maximum window size.
>
> This is the GENERALIZED version of the 'at most K distinct' pattern.
> Fruit Into Baskets is the K=2 special case.
> Longest Substring Without Repeating is the 'all distinct' special case."\_

---

This is the saga of **Longest Substring With At Most K Distinct Characters (LeetCode 340)**.

Given a string `s` and integer `k`:
-   Find the length of the longest substring with at most `k` distinct characters.

```
Input:  s = "eceba", k = 2
Output: 3   ("ece" — 2 distinct: e, c)

Input:  s = "aa", k = 1
Output: 2   ("aa" — 1 distinct)

Input:  s = "aabacbebebe", k = 3
Output: 7   ("cbebebe" — 3 distinct: c, b, e)
```

---

## 🧠 The Sliding Window Mechanics

**Hashmap:** `char → count` (frequency of each character in the window).
**`map.size()`** = number of distinct characters currently in the window.

**Expand:** move `right` forward. Add `s[right]` to the map.
**Shrink:** when `map.size() > K`, shrink from left.
Remove `s[left]` from the map. If its count hits 0, erase it.
**Track:** maximum window size = `right - left + 1`.

The window always maintains ≤ K distinct characters.
The longest such window is the answer.

> _"The palette holds at most K colors.
> When a new color arrives and the palette overflows —
> shrink from the left until a color disappears entirely."_

---

### 📜 The Scroll of the K-Color Palette

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```

---

## 🎨 The Sliding Window Solution

### Initialize

```cpp
int lengthOfLongestSubstringKDistinct(string s, int k) {
    if (k == 0) return 0;
    unordered_map<char, int> freq;
    int left = 0, maxLen = 0;
```

`freq` maps each character to its count in the current window.
`freq.size()` = distinct characters in the window.

Edge case: if K=0, no characters allowed. Answer is 0.

---

### Expand with right pointer

```cpp
    for (int right = 0; right < s.size(); right++) {
        freq[s[right]]++;
```

Add the new character to the frequency map.
If it's a new character, `freq.size()` increases by 1.

---

### Shrink when too many distinct characters

```cpp
        while (freq.size() > k) {
            freq[s[left]]--;
            if (freq[s[left]] == 0) {
                freq.erase(s[left]);
            }
            left++;
        }
```

More than K distinct characters in the window. Shrink from left.

Decrement the count of the leftmost character.
If its count drops to 0 — it's completely gone from the window.
Erase it from the map. `freq.size()` decreases.

Keep shrinking until `freq.size() <= K`.

> _"The palette overflows. The Oracle removes characters from the left.
> When a character's last copy leaves the window — erase it from the map.
> The palette shrinks back to K colors."_

---

### Track the maximum window size

```cpp
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

After shrinking (if needed), the window is valid.
Update the maximum length if this window is the longest seen.

---

### 🎺 The Trial of the K-Color Palette

```cpp
int main() {
    cout << lengthOfLongestSubstringKDistinct("eceba", 2) << endl;      // expected: 3
    cout << lengthOfLongestSubstringKDistinct("aa", 1) << endl;          // expected: 2
    cout << lengthOfLongestSubstringKDistinct("aabacbebebe", 3) << endl; // expected: 7
    cout << lengthOfLongestSubstringKDistinct("abcdef", 6) << endl;      // expected: 6
    cout << lengthOfLongestSubstringKDistinct("abcdef", 1) << endl;      // expected: 1
    return 0;
}
```

---

**Full trace for s = "eceba", k = 2:**

| right | char | freq | size | size>2? | left | window | maxLen |
|-------|------|------|------|---------|------|--------|--------|
| 0 | e | {e:1} | 1 | No | 0 | "e" | 1 |
| 1 | c | {e:1,c:1} | 2 | No | 0 | "ec" | 2 |
| 2 | e | {e:2,c:1} | 2 | No | 0 | "ece" | **3** |
| 3 | b | {e:2,c:1,b:1} | **3** | **Yes** → shrink | | | |
|   |   | freq[e]=1, left=1. size=3 still. | | | | |
|   |   | freq[c]=0→erase. {e:1,b:1}. size=2. ✓ left=2 | 2 | "eb" | 3 |
| 4 | a | {e:1,b:1,a:1} | **3** | **Yes** → shrink | | | |
|   |   | freq[e]=0→erase. {b:1,a:1}. size=2. ✓ left=3 | 3 | "ba" | 3 |

**Answer: 3** ✓ ("ece" at indices 0-2)

---

**Trace for s = "aabacbebebe", k = 3:**

| right | char | freq | size | size>3? | left | maxLen |
|-------|------|------|------|---------|------|--------|
| 0 | a | {a:1} | 1 | No | 0 | 1 |
| 1 | a | {a:2} | 1 | No | 0 | 2 |
| 2 | b | {a:2,b:1} | 2 | No | 0 | 3 |
| 3 | a | {a:3,b:1} | 2 | No | 0 | 4 |
| 4 | c | {a:3,b:1,c:1} | 3 | No | 0 | 5 |
| 5 | b | {a:3,b:2,c:1} | 3 | No | 0 | 6 |
| 6 | e | {a:3,b:2,c:1,e:1} | **4** | **Yes** → shrink | | |
|   |   | remove a's until a type disappears... | | | | |
|   |   | left=0: a=2. left=1: a=1. left=2: b=1. left=3: a=0→erase. | | | | |
|   |   | {b:2,c:1,e:1}. size=3. ✓ left=4 | 4 | 3 | 6 |
| 7 | b | {b:3,c:1,e:1} | 3 | No | 4 | 4 | 6 |
| 8 | e | {b:3,c:1,e:2} | 3 | No | 4 | 5 | 6 |
| 9 | b | {b:4,c:1,e:2} | 3 | No | 4 | 6 | 6 |
| 10| e | {b:4,c:1,e:3} | 3 | No | 4 | **7** | **7** |

**Answer: 7** ✓ ("cbebebe" at indices 4-10, distinct: c, b, e)

---

**Trace for s = "aa", k = 1:**

| right | char | freq | size | left | maxLen |
|-------|------|------|------|------|--------|
| 0 | a | {a:1} | 1 | 0 | 1 |
| 1 | a | {a:2} | 1 | 0 | **2** |

**Answer: 2** ✓ (only 1 distinct character, entire string is valid)

---

## 🔍 The "At Most K Distinct" Family — Complete View

| Problem | K | Goal | Tracking |
|---------|---|------|----------|
| Longest Substring Without Repeating | all unique | Longest | Set (no duplicates) |
| Fruit Into Baskets (LC 904) | 2 | Longest | Hashmap, size ≤ 2 |
| **This problem (LC 340)** | **K (given)** | **Longest** | **Hashmap, size ≤ K** |
| Subarrays with K Different (LC 992) | K | Count exactly K | atMost(K) - atMost(K-1) |

This problem is the GENERAL template. Others are special cases.

---

## 🔍 Why Erase at Count 0 Is Critical

```cpp
if (freq[s[left]] == 0) freq.erase(s[left]);
```

Without erasing, `freq.size()` would still count that key
even though its value is 0. The `size > k` check would be wrong.

A zero-count entry is a ghost — it must be removed.
The map must only contain characters ACTUALLY in the window.

---

## 🔍 Non-Shrinking Window Optimization

Instead of `while`, use `if`:

```cpp
if (freq.size() > k) {
    freq[s[left]]--;
    if (freq[s[left]] == 0) freq.erase(s[left]);
    left++;
}
maxLen = max(maxLen, right - left + 1);
```

The window never shrinks — it only grows or slides.
Since we want the LONGEST, a smaller window can never beat the current best.
Same answer, slightly different behavior.

---

## 🔍 Edge Cases

**K = 0:** no characters allowed. Answer = 0.
**K ≥ 26 (or ≥ distinct chars in s):** entire string is valid. Answer = n.
**All same characters:** answer = n regardless of K (only 1 distinct).
**Empty string:** answer = 0.

---

### 🧠 Memory of the K-Color Palette Law

-   **Hashmap:** `char → count`. `map.size()` = distinct in window.
-   **Shrink when:** `freq.size() > K`
-   **Erase at 0** — critical for correct `map.size()`
-   **This is the GENERAL template** — Fruit Into Baskets is K=2
-   **Non-shrinking optimization:** `if` instead of `while` (window only grows/slides)
-   **Time:** O(N). **Space:** O(K).

Thus is remembered the saga of **Longest Substring With At Most K Distinct Characters**,
where the Oracle maintained a palette of at most K colors —
expanding right to explore new characters,
shrinking left when the palette overflowed —
erasing colors that left the window entirely —
until the longest K-colored substring was found
in a single sweep through the string. 🪟🎨✨
