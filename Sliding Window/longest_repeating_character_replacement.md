## 🪟🔁 _The Replacement Budget: The Longest Repeating Character Replacement Saga_

> \_"The Oracle was given a string of uppercase letters and a number `k`.
>
> She was commanded:
>
> **'You may replace at most k characters with any other character.
> What is the longest substring of all the SAME character
> you can achieve?'**
>
> The Oracle reframed the problem:
>
> **'Find the longest window where the number of characters
> that are NOT the most frequent character ≤ k.'**
>
> In any window, the most frequent character stays.
> Everything else gets replaced.
> If the "everything else" count ≤ k — the window is valid.
>
> `window_size - max_frequency ≤ k`
>
> That's the entire condition.
> Expand right. Track frequencies. Shrink when the condition breaks."\_

---

This is the saga of **Longest Repeating Character Replacement (LeetCode 424)**.

Given a string `s` of uppercase English letters and integer `k`:
-   You may replace at most `k` characters.
-   Return the length of the longest substring with all same characters.

```
Input:  s = "ABAB", k = 2
Output: 4   (replace both A's or both B's → "BBBB" or "AAAA")

Input:  s = "AABABBA", k = 1
Output: 4   ("AABABBA" → "AABA_BA" → best window "ABAB"→"AAAA" or similar)
```

---

## 🧠 The Key Insight — Window Size Minus Max Frequency

In any window of characters, the optimal strategy is:

**Keep the most frequent character. Replace everything else.**

The number of replacements needed = `window_size - max_frequency`.

If `window_size - max_frequency ≤ k` → the window is valid.
All non-majority characters can be replaced within the budget.

```
Window "AABA" (size 4):
  Frequencies: A=3, B=1. maxFreq = 3.
  Replacements needed: 4 - 3 = 1.
  If k ≥ 1 → valid. Replace B with A → "AAAA".
```

---

## 🧠 The maxFreq Trick — Why It Never Needs to Decrease

As we expand the window, we track `maxFreq` — the highest frequency
of any single character in the current window.

**Key insight: we never decrease `maxFreq`.**

Why? Because we're looking for the LONGEST valid window.
A window is valid when `size - maxFreq ≤ k`.
A larger `maxFreq` makes the condition EASIER to satisfy.

If `maxFreq` was 5 at some point, even if the character that had
frequency 5 leaves the window, we don't lower `maxFreq`.
Because any future window that beats the current best
MUST have a `maxFreq` ≥ the current one.

This means `maxFreq` is a "high water mark" — it only goes up.
This avoids the O(26) scan to recompute the true max frequency.

> _"The Oracle doesn't recompute the true maximum frequency.
> She only remembers the highest she's ever seen.
> A smaller maxFreq can never produce a longer window
> than what she's already found."_

---

### 📜 The Scroll of the Replacement Budget

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔁 The Sliding Window Solution

### Initialize

```cpp
int characterReplacement(string s, int k) {
    vector<int> freq(26, 0);
    int left = 0;
    int maxFreq = 0;
    int maxLen = 0;
```

`freq[c]` = count of character `c` in the current window.
`maxFreq` = highest frequency of any single character (high water mark).
`maxLen` = longest valid window found.

---

### Expand with right pointer

```cpp
    for (int right = 0; right < s.size(); right++) {
        freq[s[right] - 'A']++;
```

Add the new character to the frequency array.

---

### Update maxFreq

```cpp
        maxFreq = max(maxFreq, freq[s[right] - 'A']);
```

If this character's frequency is the highest we've ever seen — update.
`maxFreq` only increases. Never decreases.

---

### Check validity — shrink if needed

```cpp
        int windowSize = right - left + 1;
        if (windowSize - maxFreq > k) {
            freq[s[left] - 'A']--;
            left++;
        }
```

`windowSize - maxFreq` = characters that need replacing.
If this exceeds `k` — the window is invalid.

Shrink by one: remove the leftmost character, advance `left`.

**Note: `if` not `while`.** We shrink by exactly 1.
The window either grows by 1 (valid) or slides by 1 (invalid).
It never shrinks below its current best size.

This is the non-shrinking window optimization.
Since we only care about the MAXIMUM window,
a smaller window can never beat the current best.

> _"The window either grows (found a better answer)
> or slides forward (maintaining its size).
> It never shrinks. A smaller window is useless."_

---

### Update the maximum

```cpp
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

---

### 🎺 The Trial of the Replacement Budget

```cpp
int main() {
    cout << characterReplacement("ABAB", 2) << endl;    // expected: 4
    cout << characterReplacement("AABABBA", 1) << endl;  // expected: 4
    cout << characterReplacement("AAAA", 2) << endl;     // expected: 4
    cout << characterReplacement("ABCD", 0) << endl;     // expected: 1
    return 0;
}
```

---

**Full trace for s = "AABABBA", k = 1:**

| right | char | freq (A,B) | maxFreq | winSize | winSize-maxFreq | >k? | left | maxLen |
|-------|------|-----------|---------|---------|-----------------|-----|------|--------|
| 0 | A | 1,0 | 1 | 1 | 0 | No | 0 | 1 |
| 1 | A | 2,0 | 2 | 2 | 0 | No | 0 | 2 |
| 2 | B | 2,1 | 2 | 3 | 1 | No | 0 | 3 |
| 3 | A | 3,1 | 3 | 4 | 1 | No | 0 | **4** |
| 4 | B | 3,2 | 3 | 5 | 2 | **Yes** | 1 | 4 |
| 5 | B | 2,3 | 3 | 5 | 2 | **Yes** | 2 | 4 |
| 6 | A | 3,3 | 3 | 5 | 2 | **Yes** | 3 | 4 |

**Answer: 4** ✓

At index 3: window "AABA" (size 4). A appears 3 times. Replace 1 B. Valid with k=1.
At index 4: window "AABAB" (size 5). maxFreq=3. 5-3=2 > k=1. Slide.
The window never grows past 4 again.

---

**Trace for s = "ABAB", k = 2:**

| right | char | freq | maxFreq | winSize | winSize-maxFreq | left | maxLen |
|-------|------|------|---------|---------|-----------------|------|--------|
| 0 | A | 1,0 | 1 | 1 | 0 | 0 | 1 |
| 1 | B | 1,1 | 1 | 2 | 1 | 0 | 2 |
| 2 | A | 2,1 | 2 | 3 | 1 | 0 | 3 |
| 3 | B | 2,2 | 2 | 4 | 2 | 0 | **4** |

**Answer: 4** ✓ (replace both B's → "AAAA", or both A's → "BBBB")

---

## 🔍 Why `if` Instead of `while`?

With `while`: the window shrinks until valid. Standard approach.
With `if`: the window shrinks by at most 1. Non-shrinking optimization.

Both give the correct answer. The `if` version is faster in practice
because the window size is monotonically non-decreasing.

**Why is this safe?** We only care about the MAXIMUM valid window.
Once we've found a window of size X, any future answer must be ≥ X.
Shrinking below X is pointless. So we just slide forward.

---

## 🔍 Why maxFreq Never Decreases — The Proof

Suppose the current best window has size `L` with `maxFreq = F`.
Then `L - F ≤ k`, so `L ≤ F + k`.

For a future window to be LONGER than L, it needs size > L.
That means `size > F + k`, which requires `maxFreq > F`
(otherwise `size - maxFreq > k` and the window is invalid).

So a longer window MUST have a higher maxFreq.
Decreasing maxFreq can never help find a longer window.

---

## 🔍 The Sliding Window Budget Family

| Problem | Budget = | Condition |
|---------|----------|-----------|
| Max Consecutive Ones III | zeros in window | zeros ≤ k |
| Fruit Into Baskets | distinct types | types ≤ 2 |
| **This problem** | **non-majority chars** | **winSize - maxFreq ≤ k** |
| Longest K Distinct | distinct chars | distinct ≤ k |

All follow the same expand/shrink pattern. The "budget" definition changes.

---

### 🧠 Memory of the Replacement Budget Law

-   **Condition:** `windowSize - maxFreq ≤ k` (replacements needed ≤ budget)
-   **maxFreq** = high water mark, never decreases
-   **`if` not `while`** — window only grows or slides, never shrinks
-   **Frequency array** `freq[26]` for uppercase letters
-   **Why maxFreq doesn't decrease:** a longer window needs a higher maxFreq
-   **Time:** O(N). **Space:** O(26) = O(1).

Thus is remembered the saga of **Longest Repeating Character Replacement**,
where the Oracle tracked the most frequent character in the window —
everything else was the replacement cost —
and when the cost exceeded the budget,
the window slid forward without shrinking —
because a smaller window could never beat the current best —
until the longest achievable uniform substring was found. 🪟🔁✨
