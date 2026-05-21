## 📅✂️ _The Minimum Sacrifices: The Non-Overlapping Intervals Saga_

> \_"The Oracle was given a list of intervals.
> Some overlapped. Some didn't.
>
> She was commanded:
>
> **'What is the MINIMUM number of intervals to REMOVE
> so that the remaining intervals do NOT overlap?'**
>
> The Oracle recognized this instantly:
>
> **This is Activity Selection in disguise.**
>
> Activity Selection asks: "What's the MAXIMUM number of
> non-overlapping intervals you can KEEP?"
>
> Non-overlapping Intervals asks: "What's the MINIMUM number
> you must REMOVE?"
>
> **removed = total - kept.**
>
> Maximize kept (Activity Selection) → minimize removed.
>
> **Sort by END time.
> Greedily keep intervals that don't overlap with the last kept.
> Count the kept ones.
> Answer = total - kept.**
>
> Same greedy as N Meetings in One Room.
> Different output format."\_

---

This is the saga of **Non-Overlapping Intervals**.

Given an array of intervals `[start, end]`:

-   Return the **minimum** number of intervals to remove
    so that the rest are non-overlapping.

```
Input:  intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1   (remove [1,3], keep [1,2],[2,3],[3,4])

Input:  intervals = [[1,2],[1,2],[1,2]]
Output: 2   (keep one [1,2], remove two)

Input:  intervals = [[1,2],[2,3]]
Output: 0   (already non-overlapping, [1,2] and [2,3] don't overlap)
```

---

## 🧠 The Oracle's Core Insight -- Activity Selection Inverted

**Maximum non-overlapping intervals we can KEEP:**
Sort by end time. Greedily pick intervals that don't overlap.

**Minimum intervals to REMOVE:**
`removed = n - kept`.

```
Sort intervals by END time (ascending).
kept = 1 (first interval is always kept)
lastEnd = intervals[0].end

For each subsequent interval:
  If interval.start >= lastEnd:
    No overlap. Keep it. kept++. Update lastEnd.
  Else:
    Overlap. This one is "removed" (skipped).

Return n - kept.
```

**Why sort by end time?**
The interval that ends earliest leaves the most room for future intervals.
Keeping it maximizes the total number of non-overlapping intervals.

**Why `start >= lastEnd` (not `>`)?**
`[1,2]` and `[2,3]`: start(2) == end(2). They DON'T overlap.
They share a single point but don't occupy the same time range.
(This matches the problem's definition: `[1,2)` and `[2,3)` are disjoint.)

```
Time:  O(n log n) -- sorting
Space: O(1) extra
```

---

### 📜 The Scroll of the Minimum Sacrifices

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ✂️ The Greedy Ritual

```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    int n = intervals.size();
    if (n <= 1) return 0;
```

0 or 1 intervals → nothing to remove.

---

### 📊 Sort by End Time

```cpp
    sort(intervals.begin(), intervals.end(), [](auto& a, auto& b) {
        return a[1] < b[1];
    });
```

Sort by the SECOND element (end time) ascending.
The interval that ends earliest gets priority.

> _"The one that finishes first
> leaves the most room for others.
> It earns its place."_

---

### 🔁 Greedily Keep Non-Overlapping Intervals

```cpp
    int kept = 1;
    int lastEnd = intervals[0][1];

    for (int i = 1; i < n; i++) {
        if (intervals[i][0] >= lastEnd) {
            kept++;
            lastEnd = intervals[i][1];
        }
    }
```

Start by keeping the first interval (earliest end).

For each subsequent interval:
-   If it starts at or after `lastEnd` → no overlap. Keep it.
-   Otherwise → it overlaps with the last kept. Skip (implicitly "remove").

---

### 📤 The Answer

```cpp
    return n - kept;
}
```

`n - kept` = number of intervals removed.

> _"Total minus kept equals removed.
> Maximize kept → minimize removed.
> The greedy choice handles both."_

---

### 🎺 The Trial of the Minimum Sacrifices

```cpp
int main() {
    vector<vector<int>> i1 = {{1,2},{2,3},{3,4},{1,3}};
    cout << eraseOverlapIntervals(i1) << endl; // expected: 1

    vector<vector<int>> i2 = {{1,2},{1,2},{1,2}};
    cout << eraseOverlapIntervals(i2) << endl; // expected: 2

    vector<vector<int>> i3 = {{1,2},{2,3}};
    cout << eraseOverlapIntervals(i3) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for intervals = [[1,2],[2,3],[3,4],[1,3]]:**

**After sorting by end time:** [[1,2],[2,3],[1,3],[3,4]]

Wait — let me sort correctly:
-   [1,2] end=2
-   [2,3] end=3
-   [1,3] end=3
-   [3,4] end=4

Sorted: [[1,2],[2,3],[1,3],[3,4]] (ties broken arbitrarily).

| i | Interval | start >= lastEnd? | Action       | kept | lastEnd |
|---|----------|---------------------|--------------|------|---------|
| — | [1,2]    | (first)             | Keep.        | 1    | 2       |
| 1 | [2,3]    | 2 >= 2 ✓           | Keep.        | 2    | 3       |
| 2 | [1,3]    | 1 >= 3 ✗           | Skip (remove)| 2    | 3       |
| 3 | [3,4]    | 3 >= 3 ✓           | Keep.        | 3    | 4       |

kept = 3. removed = 4 - 3 = **1** ✓

Removed [1,3] (it overlaps with [2,3]). The other three are non-overlapping.

---

**Full trace for intervals = [[1,2],[1,2],[1,2]]:**

Sorted by end: [[1,2],[1,2],[1,2]] (all same).

| i | Interval | start >= lastEnd? | Action | kept |
|---|----------|---------------------|--------|------|
| — | [1,2]    | (first)             | Keep.  | 1    |
| 1 | [1,2]    | 1 >= 2 ✗           | Skip.  | 1    |
| 2 | [1,2]    | 1 >= 2 ✗           | Skip.  | 1    |

kept = 1. removed = 3 - 1 = **2** ✓

---

**Trace for intervals = [[1,2],[2,3]]:**

Sorted: [[1,2],[2,3]].

| i | Interval | start >= lastEnd? | Action | kept |
|---|----------|---------------------|--------|------|
| — | [1,2]    | (first)             | Keep.  | 1    |
| 1 | [2,3]    | 2 >= 2 ✓           | Keep.  | 2    |

kept = 2. removed = 2 - 2 = **0** ✓ (touching endpoints don't overlap).

---

## 🔍 This IS Activity Selection

| Activity Selection (N Meetings)   | Non-Overlapping Intervals (this)  |
| --------------------------------- | --------------------------------- |
| Maximize meetings KEPT            | Minimize intervals REMOVED        |
| Sort by end time                  | Sort by end time                  |
| Pick if start > lastEnd           | Keep if start >= lastEnd          |
| Answer = kept                     | Answer = n - kept                 |

**Literally the same algorithm.** Different output.

The only subtle difference: `>` vs `>=` for the overlap check.
This depends on whether touching endpoints count as overlapping.
In this LeetCode problem, `[1,2]` and `[2,3]` do NOT overlap → use `>=`.

---

## 🔍 Why Sort by End (Not Start)

Sorting by start time doesn't work:
`[[1,10],[2,3],[4,5]]` sorted by start → keep [1,10], skip [2,3] and [4,5].
But the optimal is: keep [2,3] and [4,5], remove [1,10]. Only 1 removal.

Sorting by end time: [[2,3],[4,5],[1,10]] → keep [2,3], keep [4,5], skip [1,10].
Correct: 1 removal.

The interval that ends earliest is always the safest to keep.

---

### 🧠 Memory of the Minimum Sacrifices Law

-   **Sort by END time** (ascending) — same as Activity Selection
-   **Keep first interval.** For each next: if `start >= lastEnd` → keep, else skip.
-   **Answer = n - kept** (total minus non-overlapping count)
-   **This IS Activity Selection** — maximize kept = minimize removed
-   **`>=` for non-overlap** (touching endpoints are NOT overlapping in this problem)
-   **Why end time?** Earliest end leaves most room for future intervals
-   **Time:** O(n log n). **Space:** O(1).
-   **Edge cases:**
    -   Already non-overlapping → 0
    -   All identical → n-1 removals
    -   Single interval → 0

Thus is remembered the saga of **Non-Overlapping Intervals**,
where the Oracle sorted intervals by their end time --
greedily keeping those that didn't overlap with the last kept --
and counted the sacrifices:
total minus kept equals removed --
the minimum number of intervals
that must fall so the rest may coexist in peace. 📅✂️✨
