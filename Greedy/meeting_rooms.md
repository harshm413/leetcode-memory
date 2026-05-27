## 🏢🚪 _The Overlap Check: The Meeting Rooms Saga_

> \_"In a corporate building,
> an employee had `n` meetings scheduled.
> Each meeting had a start time and an end time.
>
> The employee could only attend ONE meeting at a time.
> She could not be in two places at once.
>
> The Oracle was commanded:
>
> **'Can this person attend ALL meetings?'**
>
> The Oracle rephrased the question:
>
> **'Do ANY two meetings overlap?'**
>
> If no two meetings overlap → she can attend all. Return true.
> If any two overlap → she cannot. Return false.
>
> The Oracle's strategy:
>
> **Sort meetings by START time.
> Walk through them.
> If any meeting starts BEFORE the previous one ends → overlap → false.**
>
> Why sort by start time?
>
> After sorting, the only possible overlap
> is between ADJACENT meetings in the sorted order.
> If meeting `i` starts before meeting `i-1` ends → they clash.
> No need to check all pairs -- just consecutive ones.
>
> One sort. One pass. One comparison per pair."\_

---

This is the saga of **Meeting Rooms** (LeetCode 252).

Given an array of meeting intervals `[start, end]`:

-   Return `true` if a person can attend ALL meetings.
-   Return `false` if any two meetings overlap.

```
Input:  intervals = [[0,30],[5,10],[15,20]]
Output: false   ([0,30] overlaps with [5,10] and [15,20])

Input:  intervals = [[7,10],[2,4]]
Output: true    (no overlap: [2,4] ends before [7,10] starts)

Input:  intervals = [[1,5],[5,10]]
Output: true    (touching endpoints: [1,5] ends exactly when [5,10] starts -- no overlap)
```

---

## 🧠 The Oracle's Core Insight -- Sort and Check Adjacent Pairs

After sorting by start time:
-   Meetings are in chronological order.
-   If meeting `i` overlaps with ANY earlier meeting,
    it MUST overlap with meeting `i-1` (the one just before it).
-   Why? Because `i-1` has the latest start time among all earlier meetings.
    If `i` doesn't overlap with `i-1`, it can't overlap with anything before `i-1` either
    (those end even earlier).

**So just check consecutive pairs.**

```
Sort intervals by start time.

For i = 1 to n-1:
  If intervals[i].start < intervals[i-1].end:
    Overlap! Return false.

No overlap found. Return true.
```

**Why `start < prevEnd` (not `<=`)?**

`[1,5]` and `[5,10]`: start(5) == end(5).
They share a single point in time but don't truly overlap.
One ends exactly as the other begins -- the person can walk from one to the next.

If the problem says "meetings at the same time count as overlap" → use `<=`.
Standard definition: `<` (touching is fine).

```
Time:  O(n log n) -- sorting dominates
Space: O(1) extra (or O(n) depending on sort implementation)
```

---

### 📜 The Scroll of the Overlap Check

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🏢 The Greedy Ritual

```cpp
bool canAttendMeetings(vector<vector<int>>& intervals) {
    int n = intervals.size();
    if (n <= 1) return true;
```

0 or 1 meetings → no possible overlap. Always true.

---

### 📊 Sort by Start Time

```cpp
    sort(intervals.begin(), intervals.end());
```

Sort by the first element (start time) ascending.
`vector<vector<int>>` sorts lexicographically by default --
first by `intervals[i][0]` (start), then by `intervals[i][1]` (end) on ties.

After sorting, meetings are in the order they begin.
The only possible conflicts are between neighbors.

> _"Line up the meetings in the order they begin.
> If any meeting starts while the previous one is still running --
> there's a clash. The person cannot be in two rooms at once."_

---

### 🔁 Check Consecutive Pairs

```cpp
    for (int i = 1; i < n; i++) {
        if (intervals[i][0] < intervals[i - 1][1]) {
            return false;
        }
    }
    return true;
}
```

For each meeting (starting from the second):
-   Does it start BEFORE the previous meeting ends?
-   `intervals[i][0]` = current meeting's start.
-   `intervals[i-1][1]` = previous meeting's end.
-   If `start < prevEnd` → overlap → impossible → return false.

If we make it through all pairs without finding an overlap → return true.

> _"Each meeting asks one question:
> 'Has the previous meeting ended before I begin?'
> If yes -- no conflict. Move on.
> If no -- clash. The person cannot attend all meetings."_

---

### 🎺 The Trial of the Overlap Check

```cpp
int main() {
    vector<vector<int>> i1 = {{0,30},{5,10},{15,20}};
    cout << canAttendMeetings(i1) << endl; // expected: 0 (false)

    vector<vector<int>> i2 = {{7,10},{2,4}};
    cout << canAttendMeetings(i2) << endl; // expected: 1 (true)

    vector<vector<int>> i3 = {{1,5},{5,10}};
    cout << canAttendMeetings(i3) << endl; // expected: 1 (true)

    vector<vector<int>> i4 = {{1,5},{3,7},{9,12}};
    cout << canAttendMeetings(i4) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full trace for intervals = [[0,30],[5,10],[15,20]]:**

**After sorting by start:** [[0,30],[5,10],[15,20]].

| i | Current meeting | Previous meeting | start < prevEnd? | Result |
|---|-----------------|------------------|-------------------|--------|
| 1 | [5, 10]         | [0, 30]          | 5 < 30 ✓ OVERLAP | **return false** |

**Answer: false** ✓

Meeting [5,10] starts at 5, but [0,30] doesn't end until 30.
The person would need to be in two rooms at time 5. Impossible.

---

**Full trace for intervals = [[7,10],[2,4]]:**

**After sorting by start:** [[2,4],[7,10]].

| i | Current meeting | Previous meeting | start < prevEnd? | Result |
|---|-----------------|------------------|-------------------|--------|
| 1 | [7, 10]         | [2, 4]           | 7 < 4? **No**    | Continue |

Loop ends. **Answer: true** ✓

Meeting [2,4] ends at 4. Meeting [7,10] starts at 7. No overlap.
The person has a gap from 4 to 7 -- plenty of time.

---

**Full trace for intervals = [[1,5],[5,10]]:**

**After sorting:** [[1,5],[5,10]].

| i | Current | Previous | start < prevEnd? | Result |
|---|---------|----------|-------------------|--------|
| 1 | [5, 10] | [1, 5]   | 5 < 5? **No**    | Continue |

**Answer: true** ✓

The first meeting ends at exactly 5, and the second starts at exactly 5.
They touch but don't overlap. The person finishes one and walks into the next.

---

**Full trace for intervals = [[1,5],[3,7],[9,12]]:**

**After sorting:** [[1,5],[3,7],[9,12]].

| i | Current | Previous | start < prevEnd? | Result |
|---|---------|----------|-------------------|--------|
| 1 | [3, 7]  | [1, 5]   | 3 < 5 ✓ OVERLAP | **return false** |

**Answer: false** ✓

Meeting [3,7] starts at 3, but [1,5] doesn't end until 5.
Overlap between time 3 and 5.

---

**Trace for intervals = [[1,2],[3,4],[5,6],[7,8]]:**

After sorting: [[1,2],[3,4],[5,6],[7,8]].

| i | start < prevEnd? |
|---|-------------------|
| 1 | 3 < 2? No        |
| 2 | 5 < 4? No        |
| 3 | 7 < 6? No        |

**Answer: true** ✓ -- all meetings are well-separated. No overlaps.

---

**Trace for intervals = [[1,10],[2,3],[4,5],[6,7]]:**

After sorting: [[1,10],[2,3],[4,5],[6,7]].

| i | Current | Previous | start < prevEnd? |
|---|---------|----------|-------------------|
| 1 | [2, 3]  | [1, 10]  | 2 < 10 ✓ OVERLAP |

**Answer: false** ✓

The long meeting [1,10] swallows everything.
Even though [2,3], [4,5], [6,7] don't overlap with each other,
they all overlap with [1,10].

---

## 🔍 Why Sort by Start Time (Not End Time)

For this problem, sorting by start time is the natural choice:
-   After sorting, meetings are in the order they begin.
-   The overlap check is intuitive: "does the next meeting start before the current one ends?"

Sorting by end time would also work (check if `start[i] < end[i-1]` after sorting by end),
but it's less intuitive to reason about.

**Note:** for Activity Selection / Non-overlapping Intervals, we sort by END time
because we're MAXIMIZING selections. Here we're just CHECKING for any overlap --
start time is simpler.

---

## 🔍 Why Only Check Adjacent Pairs

After sorting by start time: `s[0] ≤ s[1] ≤ s[2] ≤ ... ≤ s[n-1]`.

If meeting `i` doesn't overlap with meeting `i-1`:
-   `s[i] >= e[i-1]` (meeting i starts after i-1 ends).
-   For any `j < i-1`: `e[j] ≤ e[i-1]` is NOT guaranteed...

Actually, the correct reasoning is:
-   If `s[i] >= e[i-1]`, meeting `i` starts after `i-1` ends.
-   But what about meeting `j` where `j < i-1`? Could `e[j] > s[i]`?
-   YES! Consider [[1,20],[2,3],[4,5]]. After sorting: [[1,20],[2,3],[4,5]].
-   Meeting [2,3] overlaps with [1,20] → caught at i=1.

The key: we catch overlaps EARLY. If [1,20] overlaps with [2,3],
we detect it when comparing [2,3] with [1,20] (adjacent pair).
We don't need to check [4,5] against [1,20] because we already returned false.

**If no adjacent pair overlaps → no pair overlaps at all.**

Proof: if meetings are sorted by start and no adjacent pair overlaps:
`e[0] ≤ s[1] ≤ e[1] ≤ s[2] ≤ ... ≤ e[n-1]`.
So `e[i] ≤ s[j]` for all `i < j`. No overlap between any pair.

---

## 🔍 Connection to Meeting Rooms II

| Meeting Rooms I (this)            | Meeting Rooms II                  |
| --------------------------------- | --------------------------------- |
| CAN you attend all? (T/F)        | How many ROOMS needed?            |
| Check for ANY overlap             | Count MAXIMUM simultaneous overlap|
| Sort + one comparison per pair    | Sort + heap / two pointers        |
| O(n log n), O(1)                  | O(n log n), O(n)                  |

Meeting Rooms I is the simpler prerequisite.
If the answer to Meeting Rooms I is `true` → Meeting Rooms II answer is 1.
If `false` → Meeting Rooms II answer is ≥ 2.

---

## 🔍 Connection to Other Interval Problems

| Problem                    | Question                           | Sort by    |
| -------------------------- | ---------------------------------- | ---------- |
| **Meeting Rooms I (this)** | Any overlap? (T/F)                 | Start time |
| Meeting Rooms II           | Max simultaneous overlap (rooms)   | Start time / events |
| Merge Intervals            | Merge all overlapping intervals    | Start time |
| Non-overlapping Intervals  | Min removals for no overlap        | End time   |
| Insert Interval            | Insert and merge one interval      | Pre-sorted |

Meeting Rooms I is the simplest interval problem --
the gateway to all others. Master this first.

---

## 🔍 Brute Force -- Check All Pairs

```cpp
bool canAttendMeetings(vector<vector<int>>& intervals) {
    int n = intervals.size();
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            // Two intervals [a,b] and [c,d] overlap if a < d AND c < b
            if (intervals[i][0] < intervals[j][1] &&
                intervals[j][0] < intervals[i][1]) {
                return false;
            }
        }
    }
    return true;
}
```

Check every pair. If any two overlap → false.

```
Time:  O(n²) -- all pairs
Space: O(1)
```

This works but is slower. Sorting reduces it to O(n log n).

**For interviews:** mention brute force O(n²) first, then optimize to sort + scan O(n log n).

---

### 🧠 Memory of the Overlap Check Law

-   **Sort by start time** (ascending)
-   **Check adjacent pairs:** if `intervals[i].start < intervals[i-1].end` → overlap → false
-   **If no adjacent overlap found** → return true (can attend all)
-   **Why adjacent only?** After sorting, if no neighbors clash, no pair clashes
-   **`<` not `<=`** for the overlap check (touching endpoints = no overlap)
-   **This is the gateway** to Meeting Rooms II, Merge Intervals, etc.
-   **Time:** O(n log n) -- sorting dominates
-   **Space:** O(1) extra
-   **Edge cases:**
    -   0 or 1 meetings → always true
    -   All meetings at the same time → false
    -   Back-to-back meetings (end == next start) → true (no overlap)
    -   One very long meeting covering all others → false (caught at first comparison)

Thus is remembered the saga of **Meeting Rooms**,
where the Oracle sorted meetings by their start time
and walked the schedule with one simple question:
"Does this meeting begin before the last one ends?" --
and if any meeting answered yes,
the person could not attend all --
but if every meeting waited its turn,
the schedule was declared conflict-free,
and the person could attend them all. 🏢🚪✨
