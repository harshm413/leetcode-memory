## 🪟🔤 _The Complete Trio: The Number of Substrings Containing All Three Characters Saga_

> \_"The Oracle was given a string containing only 'a', 'b', and 'c'.
>
> She was commanded:
>
> **'Count the number of substrings that contain
> at least one of EACH character — at least one a, one b, one c.'**
>
> The Oracle used a sliding window with a twist:
>
> **Once the window contains all three characters,
> EVERY substring that starts at `left` or earlier
> and ends at `right` or later is valid.**
>
> So instead of counting `right - left + 1` (subarrays ending at right),
> she counted from the LEFT side:
>
> **When the window is valid, shrink from left.
> Each shrink position contributes `n - right` valid substrings
> (all extensions to the right are also valid).**
>
> Or equivalently: once valid, count `left + 1`
> (all starting positions from 0 to left work)."\_

---

This is the saga of **Number of Substrings Containing All Three Characters (LeetCode 1358)**.

Given a string `s` consisting only of 'a', 'b', 'c':
-   Count substrings containing at least one of each character.

```
Input:  s = "abcabc"
Output: 10

Input:  s = "aaacb"
Output: 3   ("aaacb", "aacb", "acb")

Input:  s = "abc"
Output: 1
```

---

## 🧠 The Key Insight — Once Valid, Count from the Right

In most sliding window problems, we count subarrays ENDING at `right`.
Here, we think differently:

**Once the window `[left, right]` contains all three characters,
every substring starting at `left` and ending at `right` OR BEYOND is valid.**

Why? Adding more characters to the right can't remove a, b, or c.
If `[left, right]` has all three, so does `[left, right+1]`, `[left, right+2]`, etc.

So when the window is valid: **count += `n - right`**.
That's all substrings starting at `left` and ending anywhere from `right` to `n-1`.

Then shrink `left` to find the next valid position.

---

## 🧠 The Algorithm

```
For each right:
  Add s[right] to frequency.
  While window has all three (freq[a]>=1 AND freq[b]>=1 AND freq[c]>=1):
    count += n - right    (all extensions to the right are valid)
    Remove s[left] from frequency.
    left++
```

The `while` loop shrinks as long as the window is valid.
Each valid `left` position contributes `n - right` substrings.

---

### 📜 The Scroll of the Complete Trio

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## 🔤 The Sliding Window Solution

### Initialize

```cpp
int numberOfSubstrings(string s) {
    int n = s.size();
    int freq[3] = {0, 0, 0};
    int left = 0, count = 0;
```

`freq[0]` = count of 'a' in window.
`freq[1]` = count of 'b' in window.
`freq[2]` = count of 'c' in window.

---

### Expand with right pointer

```cpp
    for (int right = 0; right < n; right++) {
        freq[s[right] - 'a']++;
```

Add the new character to the frequency array.

---

### While window contains all three — count and shrink

```cpp
        while (freq[0] >= 1 && freq[1] >= 1 && freq[2] >= 1) {
            count += n - right;
```

The window `[left, right]` has all three characters.
Every substring starting at `left` and ending at `right` to `n-1` is valid.
That's `n - right` substrings.

> _"The window is complete — it has a, b, and c.
> Every extension to the right keeps it complete.
> Count all of them: n - right."_

---

### Shrink from left

```cpp
            freq[s[left] - 'a']--;
            left++;
        }
    }
    return count;
}
```

Remove the leftmost character. Move left forward.
Check again — maybe the window is STILL valid (has all three).
If so, count again from the new `left`.

The `while` loop continues until the window loses one of the three characters.

> _"The Oracle shrinks the window.
> If it's still complete — count again.
> Keep shrinking until one character is lost.
> Then expand right to find the next valid window."_

---

### 🎺 The Trial of the Complete Trio

```cpp
int main() {
    cout << numberOfSubstrings("abcabc") << endl; // expected: 10
    cout << numberOfSubstrings("aaacb") << endl;  // expected: 3
    cout << numberOfSubstrings("abc") << endl;    // expected: 1
    return 0;
}
```

---

**Full trace for s = "abcabc":**

| right | char | freq[a,b,c] | All≥1? | Action | count |
|-------|------|-------------|--------|--------|-------|
| 0 | a | [1,0,0] | No | — | 0 |
| 1 | b | [1,1,0] | No | — | 0 |
| 2 | c | [1,1,1] | **Yes** | count += 6-2=4. Remove 'a', left=1. | 4 |
|   |   | [0,1,1] | No | Stop shrinking. | 4 |
| 3 | a | [1,1,1] | **Yes** | count += 6-3=3. Remove 'b', left=2. | 7 |
|   |   | [1,0,1] | No | Stop. | 7 |
| 4 | b | [1,1,1] | **Yes** | count += 6-4=2. Remove 'c', left=3. | 9 |
|   |   | [1,1,0] | No | Stop. | 9 |
| 5 | c | [1,1,1] | **Yes** | count += 6-5=1. Remove 'a', left=4. | 10 |
|   |   | [0,1,1] | No | Stop. | 10 |

**Answer: 10** ✓

---

**Trace for s = "aaacb":**

| right | char | freq[a,b,c] | All≥1? | Action | count |
|-------|------|-------------|--------|--------|-------|
| 0 | a | [1,0,0] | No | — | 0 |
| 1 | a | [2,0,0] | No | — | 0 |
| 2 | a | [3,0,0] | No | — | 0 |
| 3 | c | [3,0,1] | No | — | 0 |
| 4 | b | [3,1,1] | **Yes** | count += 5-4=1. Remove 'a', left=1. | 1 |
|   |   | [2,1,1] | **Yes** | count += 5-4=1. Remove 'a', left=2. | 2 |
|   |   | [1,1,1] | **Yes** | count += 5-4=1. Remove 'a', left=3. | 3 |
|   |   | [0,1,1] | No | Stop. | 3 |

**Answer: 3** ✓ (substrings: "aaacb", "aacb", "acb")

---

## 🔍 Why `n - right` and Not `right - left + 1`?

In most problems, we count subarrays ENDING at `right`: `right - left + 1`.

Here, we count subarrays STARTING at `left`: `n - right`.

Why the difference? Because the condition is "at least" (contains all three).
Once satisfied, extending RIGHT keeps it satisfied.
So for a fixed `left`, ALL endings from `right` to `n-1` work.

For "at most" problems (like sum ≤ K), extending right might BREAK the condition.
So we count endings at the current `right` only: `right - left + 1`.

| Condition type | What's guaranteed | Count formula |
|---------------|-------------------|---------------|
| At most K | Shorter subarrays are valid | `right - left + 1` (endings at right) |
| **At least K** | **Longer subarrays are valid** | **`n - right` (endings from right onward)** |

---

## 🔍 Alternative — Using Last Seen Indices

```cpp
int numberOfSubstrings(string s) {
    int last[3] = {-1, -1, -1};
    int count = 0;
    for (int i = 0; i < s.size(); i++) {
        last[s[i] - 'a'] = i;
        count += 1 + min({last[0], last[1], last[2]});
    }
    return count;
}
```

`last[c]` = most recent index of character c.
`min(last[0], last[1], last[2])` = the earliest "last seen" among all three.
If all three have been seen (min ≥ 0), then `min + 1` = number of valid
starting positions (from 0 to min).

Simpler code. Same O(N) time. But the sliding window version
is more generalizable to other "at least" problems.

---

### 🧠 Memory of the Complete Trio Law

-   **"At least" condition:** once valid, extending right stays valid
-   **Count formula:** `n - right` (all endings from right onward)
-   **Shrink while valid:** each valid `left` contributes `n - right`
-   **Frequency check:** `freq[0]>=1 && freq[1]>=1 && freq[2]>=1`
-   **Alternative:** `last seen` array, count = `1 + min(last[a], last[b], last[c])`
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Number of Substrings Containing All Three Characters**,
where the Oracle shrank the window while it was complete —
each valid starting position contributing `n - right` substrings —
because once all three characters were present,
every extension to the right preserved the completeness —
counting from the left side, not the right. 🪟🔤✨
