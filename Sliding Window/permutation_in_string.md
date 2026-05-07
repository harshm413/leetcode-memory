## 🪟🔀 _The Anagram Window: The Permutation in String Saga_

> \_"The Oracle was given two strings: `s1` and `s2`.
>
> She was commanded:
>
> **'Does any permutation of s1 exist as a substring of s2?'**
>
> A permutation of s1 = an anagram of s1.
> So the question is: does s2 contain a substring
> that is an anagram of s1?
>
> The Oracle used a **fixed-size sliding window** of size `s1.length()`.
> She slid it across s2, comparing character frequencies
> at each position.
>
> If the window's frequency matches s1's frequency → found it.
>
> Fixed window. Frequency comparison. One pass."\_

---

This is the saga of **Permutation in String (LeetCode 567)**.

Given strings `s1` and `s2`:
-   Return `true` if s2 contains a permutation of s1 as a substring.

```
Input:  s1 = "ab", s2 = "eidbaooo"
Output: true   ("ba" is a permutation of "ab", found in s2)

Input:  s1 = "ab", s2 = "eidboaoo"
Output: false  (no window of size 2 in s2 is an anagram of "ab")

Input:  s1 = "adc", s2 = "dcda"
Output: true   ("dcd"? No. "cda" → has c,d,a = permutation of "adc" ✓)
```

---

## 🧠 The Fixed-Window Frequency Match

A permutation of s1 has the SAME character frequencies as s1,
just in a different order. So we need a substring of s2
with the exact same frequency distribution as s1.

**Window size = s1.length().** Fixed. Never changes.
**Slide across s2.** At each position, check if frequencies match.

Two approaches for the frequency check:
1. Compare two frequency arrays (O(26) per step).
2. Use a `matches` counter (O(1) per step — optimized).

We'll use the `matches` counter approach.

---

## 🧠 The Matches Counter — O(1) Per Step

Instead of comparing 26 frequencies every step:

Track `matches` = number of characters (out of 26) where
the window's frequency equals s1's frequency.

When `matches == 26` → all characters match → permutation found.

When we slide the window:
-   One character ENTERS (right side). Update its frequency. Update matches.
-   One character LEAVES (left side). Update its frequency. Update matches.

Each slide: O(1) work. Total: O(N).

---

### 📜 The Scroll of the Anagram Window

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔀 The Optimized Solution

### Build s1's frequency and initial window

```cpp
bool checkInclusion(string s1, string s2) {
    if (s1.size() > s2.size()) return false;

    vector<int> s1Freq(26, 0), windowFreq(26, 0);
    for (int i = 0; i < s1.size(); i++) {
        s1Freq[s1[i] - 'a']++;
        windowFreq[s2[i] - 'a']++;
    }
```

Build frequency of s1. Build frequency of the first window in s2
(first `s1.size()` characters).

---

### Count initial matches

```cpp
    int matches = 0;
    for (int i = 0; i < 26; i++) {
        if (s1Freq[i] == windowFreq[i]) matches++;
    }
```

For each of 26 characters, check if the frequencies already match.
Characters not in either string both have frequency 0 — they match too.

---

### Check initial window

```cpp
    if (matches == 26) return true;
```

The first window might already be a permutation.

---

### Slide the window

```cpp
    int windowSize = s1.size();
    for (int right = windowSize; right < s2.size(); right++) {
        int left = right - windowSize;
```

`right` = new character entering. `left` = character leaving.
Window is always `[left+1, right]` after the slide.

---

### Add the entering character

```cpp
        int in = s2[right] - 'a';
        windowFreq[in]++;
        if (windowFreq[in] == s1Freq[in]) matches++;
        else if (windowFreq[in] == s1Freq[in] + 1) matches--;
```

After incrementing:
-   If it NOW equals s1's frequency → one more match. `matches++`.
-   If it WAS equal but now exceeds → lost a match. `matches--`.

Why `s1Freq[in] + 1`? Before incrementing, it was `s1Freq[in]` (matched).
After incrementing, it's `s1Freq[in] + 1` (no longer matches).

---

### Remove the leaving character

```cpp
        int out = s2[left] - 'a';
        windowFreq[out]--;
        if (windowFreq[out] == s1Freq[out]) matches++;
        else if (windowFreq[out] == s1Freq[out] - 1) matches--;
```

After decrementing:
-   If it NOW equals s1's frequency → one more match. `matches++`.
-   If it WAS equal but now falls below → lost a match. `matches--`.

Why `s1Freq[out] - 1`? Before decrementing, it was `s1Freq[out]` (matched).
After decrementing, it's `s1Freq[out] - 1` (no longer matches).

---

### Check if all match

```cpp
        if (matches == 26) return true;
    }
    return false;
}
```

---

### 🎺 The Trial of the Anagram Window

```cpp
int main() {
    cout << boolalpha;
    cout << checkInclusion("ab", "eidbaooo") << endl;  // true
    cout << checkInclusion("ab", "eidboaoo") << endl;  // false
    cout << checkInclusion("adc", "dcda") << endl;     // true
    return 0;
}
```

---

**Trace for s1 = "ab", s2 = "eidbaooo":**

```
s1Freq: a=1, b=1. Window size = 2.

Initial window "ei": windowFreq: e=1, i=1.
matches: a(0==1?no), b(0==1?no), e(1==0?no), i(1==0?no), rest(0==0?yes×22) = 22.

Slide to "id": remove 'e', add 'd'.
  e: windowFreq[e]=0. 0==0(s1Freq)? yes → matches=23.
  d: windowFreq[d]=1. 1==0? no. Was 0==0? yes → matches=22.
  matches=22.

Slide to "db": remove 'i', add 'b'.
  i: 0==0? yes → matches=23.
  b: windowFreq[b]=1. 1==1(s1Freq)? yes → matches=24.
  matches=24.

Slide to "ba": remove 'd', add 'a'.
  d: 0==0? yes → matches=25.
  a: windowFreq[a]=1. 1==1? yes → matches=26. FOUND!
```

**Answer: true** ✓ (window "ba" is a permutation of "ab")

---

## 🔍 The Simple Approach — Compare Arrays Each Step

```cpp
bool checkInclusion(string s1, string s2) {
    if (s1.size() > s2.size()) return false;
    vector<int> s1Freq(26, 0), windowFreq(26, 0);
    for (char c : s1) s1Freq[c - 'a']++;
    for (int i = 0; i < s1.size(); i++) windowFreq[s2[i] - 'a']++;
    if (s1Freq == windowFreq) return true;

    for (int i = s1.size(); i < s2.size(); i++) {
        windowFreq[s2[i] - 'a']++;
        windowFreq[s2[i - s1.size()] - 'a']--;
        if (s1Freq == windowFreq) return true;
    }
    return false;
}
```

Simpler code. Compares two vectors (O(26) = O(1)) at each step.
Same overall O(N) time. The `matches` approach avoids the 26-comparison
but both are effectively O(N).

---

## 🔍 This vs Minimum Window Substring

| Aspect | Permutation in String (this) | Min Window Substring |
|--------|------------------------------|---------------------|
| Window size | **Fixed** (= s1.length()) | Variable (shrink to find min) |
| Match condition | **Exact** frequency match | At least the required frequency |
| Result | Boolean (exists or not) | The actual substring |
| Complexity | Simpler | More complex (have/need counter) |

Permutation = fixed window + exact match.
Min Window = variable window + at-least match.

---

### 🧠 Memory of the Anagram Window Law

-   **Fixed window** of size `s1.length()`
-   **Frequency match:** `matches == 26` means all characters align
-   **Slide:** add entering char, remove leaving char, update matches
-   **matches++** when a frequency becomes equal to s1's
-   **matches--** when a frequency deviates from s1's
-   **Simple alternative:** compare frequency arrays directly (O(26) per step)
-   **Time:** O(N). **Space:** O(1) (two arrays of size 26).

Thus is remembered the saga of **Permutation in String**,
where the Oracle slid a fixed window across s2 —
tracking how many of 26 characters had matching frequencies —
adding one, removing one, updating the match count —
until all 26 aligned and the anagram was found. 🪟🔀✨
