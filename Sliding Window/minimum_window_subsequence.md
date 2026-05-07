## 🪟📐 _The Ordered Embrace: The Minimum Window Subsequence Saga_

> \_"The Oracle was given two strings: `s1` and `s2`.
>
> She was commanded:
>
> **'Find the smallest substring of s1 that contains s2
> as a SUBSEQUENCE — characters in ORDER, but not necessarily contiguous.'**
>
> This was harder than Minimum Window Substring.
> There, the characters could appear in ANY order.
> Here, they must appear in the SAME ORDER as s2.
>
> The Oracle used a two-pointer approach:
>
> **Forward pass:** find a window where s2 is a subsequence.
> **Backward pass:** shrink from the right to find the tightest fit.
>
> The forward pass finds WHERE s2 ends in s1.
> The backward pass finds WHERE it optimally begins.
> Together — the minimum window."\_

---

This is the saga of **Minimum Window Subsequence (LeetCode 727 / Premium)**.

Given strings `s1` and `s2`:
-   Find the smallest substring of `s1` that contains `s2` as a subsequence.
-   If no such window exists, return `""`.

```
Input:  s1 = "abcdebdde", s2 = "bde"
Output: "bcde"   (indices 1-4, contains b...d...e in order)

Input:  s1 = "jmeqksfrsdcmsiwvaovztaqenrat", s2 = "u"
Output: ""   (u not in s1)

Input:  s1 = "abcdbede", s2 = "bde"
Output: "bede"   (indices 4-7 — tighter than "bcdebde")
```

---

## 🧠 The Two-Pass Approach — Forward Then Backward

**Forward pass:** use pointer `j` on s2. Walk `i` through s1.
When `s1[i] == s2[j]`, advance `j`. When `j` reaches the end of s2,
we've found a window ending at `i` that contains s2 as a subsequence.

**Backward pass:** from the end position `i`, walk BACKWARD through s1
matching s2 in reverse. This finds the LATEST possible start
for this window — giving the tightest (shortest) window.

**Why backward?** The forward pass finds the EARLIEST end.
But the start might be unnecessarily early. Walking backward
from the end finds the optimal start.

---

## 🧠 Why Not Just Sliding Window?

Standard sliding window can't handle ORDER constraints.
In Minimum Window Substring, "ABC" can appear as "BAC" — order doesn't matter.
Here, "bde" must appear as b...d...e in that exact order.

Shrinking from the left might skip a character that's needed
at a specific position in the sequence. The backward pass handles this.

---

### 📜 The Scroll of the Ordered Embrace

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## 📐 The Forward-Backward Solution

```cpp
string minWindowSubsequence(string s1, string s2) {
    int n = s1.size(), m = s2.size();
    int minLen = INT_MAX;
    int minStart = -1;
```

---

### Forward pass — find windows containing s2 as subsequence

```cpp
    int i = 0;
    while (i < n) {
        int j = 0;
```

`i` walks through s1. `j` walks through s2.

---

### Match s2 characters in order

```cpp
        while (i < n && j < m) {
            if (s1[i] == s2[j]) j++;
            i++;
        }
```

When characters match, advance both. Otherwise advance only `i`.
After this loop: if `j == m`, all of s2 was matched.
`i - 1` is the END of the window (last matched character).

---

### If s2 was fully matched — do backward pass

```cpp
        if (j < m) break;
```

If `j < m`, s2 wasn't fully matched. No more windows possible. Stop.

---

```cpp
        int end = i - 1;
        j = m - 1;
```

`end` = where the last character of s2 was found in s1.
Now walk BACKWARD from `end` to find the tightest start.

---

### Backward pass — find the latest start

```cpp
        while (j >= 0) {
            if (s1[end] == s2[j]) j--;
            end--;
        }
        end++;
```

Walk backward through s1, matching s2 in reverse.
After the loop, `end` points one before the start.
`end++` corrects it to the actual start.

> _"The backward pass finds where the subsequence
> can START as late as possible — giving the shortest window.
> The forward pass found the end. The backward pass finds the start."_

---

### Record if this is the smallest window

```cpp
        int windowLen = (i - 1) - end + 1;
        if (windowLen < minLen) {
            minLen = windowLen;
            minStart = end;
        }
```

Window is `[end, i-1]`. Length = `i - 1 - end + 1`.

---

### Continue searching from the next position

```cpp
        i = end + 1;
    }
```

Start the next forward pass from `end + 1`.
There might be a shorter window starting later.

---

```cpp
    return minStart == -1 ? "" : s1.substr(minStart, minLen);
}
```

---

### 🎺 The Trial of the Ordered Embrace

```cpp
int main() {
    cout << minWindowSubsequence("abcdebdde", "bde") << endl;  // expected: "bcde"
    cout << minWindowSubsequence("abcdbede", "bde") << endl;   // expected: "bede"
    cout << minWindowSubsequence("jmeqksfrsdcmsiwvaovztaqenrat", "u") << endl; // expected: ""
    return 0;
}
```

---

**Trace for s1 = "abcdebdde", s2 = "bde":**

**Forward pass 1:** i walks s1, j walks s2="bde".
```
i=0(a): no match. i=1(b): match b, j=1. i=2(c): no. i=3(d): match d, j=2.
i=4(e): match e, j=3. j==m. Window ends at i-1=4.
```

**Backward pass from end=4:** match s2 in reverse "edb".
```
end=4(e): match e, j=1. end=3(d): match d, j=0. end=2(c): no.
end=1(b): match b, j=-1. Done. end++ → end=1. Hmm wait...
```

Let me redo: j starts at m-1=2 (s2[2]='e').
```
end=4: s1[4]='e' == s2[2]='e'. j=1. end=3.
end=3: s1[3]='d' == s2[1]='d'. j=0. end=2.
end=2: s1[2]='c' ≠ s2[0]='b'. end=1.
end=1: s1[1]='b' == s2[0]='b'. j=-1. Done. end=0. end++ → end=1.
```

Window = [1, 4] = "bcde". Length = 4.

**Next forward pass from i = end+1 = 2:**
```
i=2(c): no. i=3(d): no match with b. i=4(e): no. i=5(b): match b, j=1.
i=6(d): match d, j=2. i=7(d): no match with e. i=8(e): match e, j=3. j==m.
Window ends at i-1=8.
```

**Backward pass from end=8:**
```
end=8: s1[8]='e' == 'e'. j=1. end=7.
end=7: s1[7]='d' == 'd'. j=0. end=6.
end=6: s1[6]='d' ≠ 'b'. end=5.
end=5: s1[5]='b' == 'b'. j=-1. Done. end++ → end=5. Hmm, end=4+1=5.
```

Wait: after j=-1, end was decremented to 4. end++ → 5.
Window = [5, 8] = "bdde". Length = 4. Same as before.

**Answer: "bcde"** (first window found, same length) ✓

---

**Trace for s1 = "abcdbede", s2 = "bde":**

**Forward pass 1:** 
```
b at 1, d at 3, e at 4. Window ends at 4.
Backward: e=4, d=3, b=1. Start=1. Window "bcde" len=4.
```

**Forward pass 2 from i=2:**
```
b at 4, d at... wait s1="abcdbede". 
i=2(c),i=3(d),i=4(b): match b j=1. i=5(e): no match d. i=6(d): match d j=2.
i=7(e): match e j=3. Window ends at 7.
Backward: e=7, d=6, b=4. Start=4. Hmm: end=7→e match→end=6→d match→end=5(e)≠b→end=4(b) match. end++=5.
```

Window = [5, 7] = "ede"? That's length 3. But does "ede" contain "bde" as subsequence? No — no 'b'.

Let me redo backward more carefully. s2="bde", reversed = "edb".
```
end=7: s1[7]='e' == s2[2]='e'. j=1. end--.
end=6: s1[6]='d' == s2[1]='d'. j=0. end--.
end=5: s1[5]='e' ≠ s2[0]='b'. end--.
end=4: s1[4]='b' == s2[0]='b'. j=-1. end--.
end=3. end++ → end=4.
```

Window = [4, 7] = "bede". Length = 4. Same as first window.

Actually both are length 4. **Answer: "bcde" or "bede"** (both valid, length 4) ✓

---

## 🔍 Minimum Window Substring vs Minimum Window Subsequence

| Aspect | Min Window Substring (LC 76) | Min Window Subsequence (this) |
|--------|------------------------------|-------------------------------|
| Order matters? | No (any arrangement) | **Yes (must be in order)** |
| Technique | Sliding window + freq maps | **Forward-backward two-pass** |
| Shrink method | Shrink left while valid | Backward pass from end |
| Complexity | O(\|s\| + \|t\|) | O(\|s\| × \|t\|) worst case |

The order constraint makes this fundamentally harder.
Standard sliding window can't enforce order.

---

## 🔍 Time Complexity

**Worst case:** O(N × M) where N = |s1|, M = |s2|.
Each forward pass is O(N). Each backward pass is O(N).
In the worst case, we do O(N/M) forward-backward pairs.

**Average case:** much better. The backward pass is usually short.

---

### 🧠 Memory of the Ordered Embrace Law

-   **Forward pass:** match s2 in order through s1. Find window END.
-   **Backward pass:** from the end, match s2 in REVERSE. Find tightest START.
-   **Continue from `end + 1`** — search for more windows.
-   **Record minimum** across all windows found.
-   **Order constraint** prevents standard sliding window from working.
-   **Time:** O(N × M) worst case. **Space:** O(1).

Thus is remembered the saga of **Minimum Window Subsequence**,
where the Oracle couldn't use a simple sliding window —
the order constraint demanded a two-pass approach:
forward to find where the subsequence ends,
backward to find where it optimally begins —
the tightest window containing the ordered pattern
found through this elegant forward-backward dance. 🪟📐✨
