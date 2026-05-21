## 🏷️✂️ _The Last Appearance Decides: The Partition Labels Saga_

> \_"The Oracle was given a string.
>
> She was commanded:
>
> **'Partition the string into as many parts as possible
> so that each letter appears in AT MOST one part.'**
>
> If letter `'a'` appears at positions 0, 5, and 8 --
> then the first partition must extend AT LEAST to position 8.
> Otherwise `'a'` would be split across two parts.
>
> The Oracle saw the key insight:
>
> **The LAST occurrence of each character
> determines the minimum extent of its partition.**
>
> Walk the string left to right.
> Track the farthest last-occurrence of any character seen so far.
> When the current index REACHES that farthest point --
> the partition is complete. Cut here.
>
> **Why?** At this point, every character in the current partition
> has its last occurrence within this partition.
> No character will appear again later.
> It's safe to cut.
>
> Precompute last occurrences. Walk and cut."\_

---

This is the saga of **Partition Labels**.

Given a string `s`:

-   Partition it into as many parts as possible.
-   Each letter appears in at most one part.
-   Return the sizes of the parts.

```
Input:  s = "ababcbacadefegdehijhklij"
Output: [9, 7, 8]

Input:  s = "eccbbbbdec"
Output: [10]

Input:  s = "abc"
Output: [1, 1, 1]
```

---

## 🧠 The Oracle's Core Insight -- Last Occurrence + Expanding Window

**Step 1:** precompute the LAST index of every character in the string.

**Step 2:** walk the string. Maintain a window `[start, end]`.
For each character at index `i`:
-   Expand `end` to `max(end, lastOccurrence[char])`.
-   When `i == end` → the window is complete. All characters within it
    have their last occurrence inside this window. Cut here.

```
Precompute: last[c] = last index where character c appears.

start = 0, end = 0.
For i = 0 to n-1:
  end = max(end, last[s[i]])
  If i == end:
    Partition found: size = end - start + 1.
    start = end + 1.
```

**Why does `i == end` mean we can cut?**

`end` is the farthest last-occurrence of ANY character seen so far.
When `i` catches up to `end`, it means:
-   Every character from `start` to `i` has its last occurrence ≤ `i`.
-   No character in this range will appear again after `i`.
-   It's safe to close this partition.

```
Time:  O(n) -- one pass to precompute, one pass to partition
Space: O(1) -- 26-character array (constant)
```

---

### 📜 The Scroll of the Last Appearance

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🏷️ Step One -- Precompute Last Occurrences

```cpp
vector<int> partitionLabels(string s) {
    int n = s.size();
    vector<int> last(26, 0);

    for (int i = 0; i < n; i++) {
        last[s[i] - 'a'] = i;
    }
```

`last[c]` = the last index where character `c` appears.

Walk the string once. For each character, record its position.
Since we walk left to right, the last write wins → gives the last occurrence.

> _"Before cutting, the Oracle must know
> where each letter makes its final appearance.
> This knowledge determines where each partition must end."_

---

## ✂️ Step Two -- Walk and Cut

```cpp
    vector<int> result;
    int start = 0, end = 0;

    for (int i = 0; i < n; i++) {
```

`start` = beginning of the current partition.
`end` = the farthest we MUST extend the current partition.

---

### 📐 Expand the Window

```cpp
        end = max(end, last[s[i] - 'a']);
```

The current character `s[i]` appears for the last time at `last[s[i]]`.
The partition must extend at least that far.
Update `end` if this character's last occurrence is farther.

> _"This character appears again later at position `last[c]`.
> The partition cannot end before that point.
> The window expands to cover it."_

---

### ✂️ Window Complete -- Cut Here

```cpp
        if (i == end) {
            result.push_back(end - start + 1);
            start = end + 1;
        }
    }
    return result;
}
```

When `i` reaches `end`:
-   Every character in `[start, end]` has its last occurrence ≤ `end`.
-   No character from this partition will appear in any future partition.
-   It's safe to cut.

Record the partition size. Start the next partition at `end + 1`.

> _"The current index has caught up to the farthest last-occurrence.
> Every character in this window is fully contained.
> None will appear again.
> Cut. Begin the next partition."_

---

### 🎺 The Trial of the Last Appearance

```cpp
int main() {
    auto r1 = partitionLabels("ababcbacadefegdehijhklij");
    for (int x : r1) cout << x << " ";
    cout << endl;
    // expected: 9 7 8

    auto r2 = partitionLabels("eccbbbbdec");
    for (int x : r2) cout << x << " ";
    cout << endl;
    // expected: 10

    auto r3 = partitionLabels("abc");
    for (int x : r3) cout << x << " ";
    cout << endl;
    // expected: 1 1 1

    return 0;
}
```

---

**Full trace for s = "ababcbacadefegdehijhklij":**

**Step 1 -- Last occurrences:**

| Char | Last index |
|------|-----------|
| a    | 8         |
| b    | 5         |
| c    | 7         |
| d    | 14        |
| e    | 15        |
| f    | 11        |
| g    | 13        |
| h    | 19        |
| i    | 22        |
| j    | 23        |
| k    | 20        |
| l    | 21        |

**Step 2 -- Walk and cut:**

| i  | s[i] | last[s[i]] | end = max(end, last) | i == end? | Action |
|----|------|-----------|----------------------|-----------|--------|
| 0  | a    | 8         | 8                    | No        |        |
| 1  | b    | 5         | 8                    | No        |        |
| 2  | a    | 8         | 8                    | No        |        |
| 3  | b    | 5         | 8                    | No        |        |
| 4  | c    | 7         | 8                    | No        |        |
| 5  | b    | 5         | 8                    | No        |        |
| 6  | a    | 8         | 8                    | No        |        |
| 7  | c    | 7         | 8                    | No        |        |
| 8  | a    | 8         | 8                    | **YES!**  | Cut! Size = 8-0+1 = **9**. start=9. |
| 9  | d    | 14        | 14                   | No        |        |
| 10 | e    | 15        | 15                   | No        |        |
| 11 | f    | 11        | 15                   | No        |        |
| 12 | e    | 15        | 15                   | No        |        |
| 13 | g    | 13        | 15                   | No        |        |
| 14 | d    | 14        | 15                   | No        |        |
| 15 | e    | 15        | 15                   | **YES!**  | Cut! Size = 15-9+1 = **7**. start=16. |
| 16 | h    | 19        | 19                   | No        |        |
| 17 | i    | 22        | 22                   | No        |        |
| 18 | j    | 23        | 23                   | No        |        |
| 19 | h    | 19        | 23                   | No        |        |
| 20 | k    | 20        | 23                   | No        |        |
| 21 | l    | 21        | 23                   | No        |        |
| 22 | i    | 22        | 23                   | No        |        |
| 23 | j    | 23        | 23                   | **YES!**  | Cut! Size = 23-16+1 = **8**. |

**Result: [9, 7, 8]** ✓

Three partitions: "ababcbaca" (9), "defegde" (7), "hijhklij" (8).
Each letter appears in exactly one partition.

---

**Trace for s = "eccbbbbdec":**

Last occurrences: e=9, c=9, b=6, d=7.

| i | s[i] | end |
|---|------|-----|
| 0 | e    | 9   |
| 1 | c    | 9   |
| ... | ... | 9 (never decreases) |
| 9 | c    | 9   | i==end → Cut! Size = 10. |

**Result: [10]** ✓ -- entire string is one partition (e and c appear at both ends).

---

**Trace for s = "abc":**

Last occurrences: a=0, b=1, c=2.

| i | s[i] | end | i==end? |
|---|------|-----|---------|
| 0 | a    | 0   | YES → Cut! Size=1. |
| 1 | b    | 1   | YES → Cut! Size=1. |
| 2 | c    | 2   | YES → Cut! Size=1. |

**Result: [1, 1, 1]** ✓ -- each character appears only once, maximum partitions.

---

## 🔍 Why This Gives the MAXIMUM Number of Partitions

We cut as EARLY as possible -- the moment `i == end`.
We never extend a partition beyond what's necessary.

If we waited longer to cut, we'd merge partitions unnecessarily.
Cutting at the earliest valid point maximizes the number of parts.

---

## 🔍 Why `i == end` Guarantees Safety

At the moment `i == end`:
-   `end` = the farthest last-occurrence of any character in `[start, i]`.
-   Since `i == end`, no character in this range appears after index `i`.
-   The partition `[start, i]` is self-contained.
-   Characters after `i` are guaranteed to be different from those before.

---

## 🔍 The Expanding Window Pattern

This is a **greedy expanding window**:
-   Start with a window of size 1.
-   Each new character might force the window to expand (if its last occurrence is farther).
-   When the window's right boundary is reached → close it.

Similar to the "farthest reach" in Jump Game --
but here we're tracking the farthest LAST OCCURRENCE instead of jump distance.

---

## 🔍 Connection to Other Problems

| Problem              | What determines the boundary?      |
| -------------------- | ---------------------------------- |
| **Partition Labels (this)** | Last occurrence of each character |
| Jump Game            | Maximum jump distance              |
| Merge Intervals      | Overlapping end times              |

All use the "expanding boundary" greedy pattern.

---

### 🧠 Memory of the Last Appearance Law

-   **Precompute:** `last[c]` = last index of character `c`
-   **Walk left to right:** expand `end = max(end, last[s[i]])`
-   **Cut when `i == end`:** partition complete, all chars fully contained
-   **Start next partition** at `end + 1`
-   **Maximizes partitions** by cutting as early as possible
-   **Why safe?** At `i == end`, no character in [start, end] appears after end
-   **Time:** O(n). **Space:** O(1) (26-char array).
-   **Edge cases:**
    -   All unique characters → n partitions of size 1
    -   One character repeated → 1 partition of size n
    -   Single character → [1]

Thus is remembered the saga of **Partition Labels**,
where the Oracle first mapped every letter's final appearance,
then walked the string with an expanding window --
each new character potentially pushing the boundary farther --
and the moment the current index caught up to the boundary,
she knew every character within was fully contained,
and she cut --
maximizing the number of partitions
where no letter was ever split across two parts. 🏷️✂️✨
