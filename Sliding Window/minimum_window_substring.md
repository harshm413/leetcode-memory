## 🪟🎯 _The Smallest Embrace: The Minimum Window Substring Saga_

> \_"The Oracle was given two strings: `s` and `t`.
>
> She was commanded:
>
> **'Find the smallest substring of s that contains
> ALL characters of t (including duplicates).'**
>
> If t = "ABC", the window must contain at least one A, one B, one C.
> If t = "AAB", the window must contain at least two A's and one B.
>
> The Oracle used a sliding window with frequency matching:
>
> **Expand right until the window contains all of t.**
> **Then shrink left to find the smallest valid window.**
> **Record the minimum. Continue expanding.**
>
> The key tracking: a `have` counter that tells us
> how many DISTINCT characters have met their required frequency.
> When `have == need` — the window is valid."\_

---

This is the saga of **Minimum Window Substring (LeetCode 76)**.

Given strings `s` and `t`:
-   Find the minimum window in `s` that contains all characters of `t`.
-   If no such window exists, return `""`.

```
Input:  s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"

Input:  s = "a", t = "a"
Output: "a"

Input:  s = "a", t = "aa"
Output: ""   (s doesn't have two a's)
```

---

## 🧠 The Two-Map + Have/Need Approach

**`tFreq`** = frequency map of `t`. What we NEED.
**`windowFreq`** = frequency map of current window. What we HAVE.

**`need`** = number of distinct characters in `t`.
**`have`** = number of distinct characters whose frequency in the window
meets or exceeds the required frequency.

When `have == need` → the window contains all of `t`. Valid.

---

## 🧠 The Algorithm

```
1. Build tFreq from t. Set need = tFreq.size().
2. Expand right: add s[right] to windowFreq.
   If windowFreq[c] == tFreq[c] → this character is satisfied. have++.
3. While have == need (window is valid):
   Record the window if it's the smallest seen.
   Shrink left: remove s[left] from windowFreq.
   If windowFreq[c] < tFreq[c] → this character is no longer satisfied. have--.
   left++.
4. Return the smallest window recorded.
```

---

### 📜 The Scroll of the Smallest Embrace

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
```

---

## 🎯 Build the Target Frequency Map

```cpp
string minWindow(string s, string t) {
    if (t.size() > s.size()) return "";

    unordered_map<char, int> tFreq, windowFreq;
    for (char c : t) tFreq[c]++;
```

`tFreq` holds what we need: each character and its required count.

---

### Set up tracking variables

```cpp
    int need = tFreq.size();
    int have = 0;
```

`need` = number of DISTINCT characters to satisfy.
`have` = how many are currently satisfied in the window.

---

```cpp
    int minLen = INT_MAX;
    int minStart = 0;
    int left = 0;
```

`minLen` and `minStart` track the best (smallest) valid window found.

---

## 🎯 Expand with Right Pointer

```cpp
    for (int right = 0; right < s.size(); right++) {
        char c = s[right];
```

---

### Only track characters that are in t

```cpp
        if (tFreq.count(c)) {
            windowFreq[c]++;
```

Only care about characters that appear in `t`.
Others are irrelevant — they don't help satisfy the requirement.

---

### Check if this character just became satisfied

```cpp
            if (windowFreq[c] == tFreq[c]) {
                have++;
            }
        }
```

When the window's count of `c` EXACTLY reaches the required count,
this character is now satisfied. `have++`.

Why only at `==` and not `>=`? Because we only want to increment `have`
ONCE per character — the moment it first meets the requirement.
If it goes above (e.g., 3 when we need 2), `have` doesn't change.

> _"The character crosses the threshold.
> It has enough copies in the window.
> One more satisfied. have grows by one."_

---

## 🎯 Shrink While Valid — Find the Smallest Window

```cpp
        while (have == need) {
```

The window contains all of `t`. It's valid.
Now shrink from the left to find the SMALLEST valid window.

---

### Record if this is the smallest

```cpp
            int windowLen = right - left + 1;
            if (windowLen < minLen) {
                minLen = windowLen;
                minStart = left;
            }
```

---

### Remove the leftmost character

```cpp
            char leftChar = s[left];
            if (tFreq.count(leftChar)) {
                windowFreq[leftChar]--;
```

Only process characters that are in `t`.

---

### Check if this character just became unsatisfied

```cpp
                if (windowFreq[leftChar] < tFreq[leftChar]) {
                    have--;
                }
            }
            left++;
        }
    }
```

When the window's count drops BELOW the required count,
this character is no longer satisfied. `have--`.
The window is no longer valid. The `while` loop exits.

> _"The Oracle shrinks the window.
> The moment a character drops below its quota —
> the window breaks. Stop shrinking. Expand again."_

---

### Return the result

```cpp
    return minLen == INT_MAX ? "" : s.substr(minStart, minLen);
}
```

---

### 🎺 The Trial of the Smallest Embrace

```cpp
int main() {
    cout << minWindow("ADOBECODEBANC", "ABC") << endl; // expected: "BANC"
    cout << minWindow("a", "a") << endl;                // expected: "a"
    cout << minWindow("a", "aa") << endl;               // expected: ""
    return 0;
}
```

---

**Trace for s = "ADOBECODEBANC", t = "ABC":**

```
tFreq = {A:1, B:1, C:1}. need = 3.

Expand until have == 3:
  right=0 (A): windowFreq={A:1}. A meets req → have=1.
  right=1 (D): not in t. skip.
  right=2 (O): not in t. skip.
  right=3 (B): windowFreq={A:1,B:1}. B meets req → have=2.
  right=4 (E): not in t. skip.
  right=5 (C): windowFreq={A:1,B:1,C:1}. C meets req → have=3. VALID!

Shrink:
  Window "ADOBEC" (0-5), len=6. Record: minLen=6, minStart=0.
  Remove A: windowFreq={A:0,B:1,C:1}. A<1 → have=2. Stop.
  left=1.

Expand:
  right=6 (O): skip. right=7 (D): skip. right=8 (E): skip.
  right=9 (B): windowFreq={A:0,B:2}. B already satisfied. have stays 2.
  right=10 (A): windowFreq={A:1}. A meets req → have=3. VALID!

Shrink:
  Window "DOBECODEBA" (1-10), len=10. Not better than 6.
  Remove D: not in t. left=2.
  Window (2-10), len=9. Not better.
  Remove O: not in t. left=3.
  ... keep shrinking until have drops ...
  Remove B: windowFreq={B:1}. Still ≥1. have=3. Continue.
  left=4. Window "ECODEBA" (4-10), len=7. Not better.
  Remove E: not in t. left=5.
  Window "CODEBA" (5-10), len=6. Ties with best.
  Remove C: windowFreq={C:0}. C<1 → have=2. Stop. left=6.

Expand:
  right=11 (N): not in t. skip.
  right=12 (C): windowFreq={C:1}. C meets req → have=3. VALID!

Shrink:
  Window "ODEBANC" (6-12), len=7. Not better.
  Remove O: not in t. left=7.
  Window "DEBANC" (7-12), len=6. Ties.
  Remove D: not in t. left=8.
  Window "EBANC" (8-12), len=5. Not better? 5 < 6! Record: minLen=5, minStart=8.
  
  Wait — let me recheck. "EBANC" has E,B,A,N,C. Contains A,B,C ✓. len=5.
  
  Remove E: not in t. left=9.
  Window "BANC" (9-12), len=4. 4 < 5! Record: minLen=4, minStart=9.
  Remove B: windowFreq={B:0}. B<1 → have=2. Stop. left=10.
```

**Answer: s.substr(9, 4) = "BANC"** ✓

---

## 🔍 The have/need Pattern — Why It's Efficient

Without `have/need`, we'd check ALL characters in `tFreq` at every step
to see if the window is valid. That's O(26) or O(|t|) per step.

With `have/need`, we only update when a character CROSSES the threshold.
The validity check is O(1): just `have == need`.

---

## 🔍 Why `==` for Incrementing have, `<` for Decrementing

```cpp
if (windowFreq[c] == tFreq[c]) have++;    // just reached the requirement
if (windowFreq[c] < tFreq[c]) have--;     // just dropped below requirement
```

**Increment at `==`:** the moment the count reaches the target.
Not at `>` — going above doesn't satisfy a NEW character.

**Decrement at `<`:** the moment the count drops below the target.
Not at `==` — being at exactly the target is still satisfied.

---

## 🔍 Edge Cases

**t longer than s:** impossible. Return "".
**t = s:** the entire string is the answer.
**No valid window:** `minLen` stays `INT_MAX`. Return "".
**Duplicate characters in t:** `tFreq` handles naturally (e.g., "AA" needs count ≥ 2).

---

### 🧠 Memory of the Smallest Embrace Law

-   **Two maps:** `tFreq` (what we need), `windowFreq` (what we have)
-   **`have/need`:** O(1) validity check. `have == need` → valid.
-   **Expand right:** add to windowFreq. If `windowFreq[c] == tFreq[c]` → have++.
-   **Shrink while valid:** record min, remove left. If `windowFreq[c] < tFreq[c]` → have--.
-   **Only track characters in t** — others are irrelevant
-   **Time:** O(|s| + |t|). **Space:** O(|s| + |t|).

Thus is remembered the saga of **Minimum Window Substring**,
where the Oracle expanded until the window embraced all of t —
then shrank to find the tightest embrace —
the have/need counter tracking satisfaction in O(1) —
until the smallest window containing every required character
was found and remembered. 🪟🎯✨
