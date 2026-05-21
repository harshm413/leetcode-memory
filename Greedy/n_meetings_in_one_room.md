## 🏛️📅 _The Maximum Gatherings: The N Meetings in One Room Saga_

> \_"In the Kingdom's Grand Hall,
> `n` meetings were proposed --
> each with a start time and an end time.
>
> Only ONE meeting could occupy the room at a time.
> No two meetings could overlap.
>
> The Oracle was commanded:
>
> **'What is the MAXIMUM number of meetings
> that can be held in the room?'**
>
> The Oracle recognized this as the classic
> **Activity Selection Problem** --
> the textbook greedy problem.
>
> **Sort meetings by END TIME (ascending).
> Always pick the meeting that ENDS earliest.
> After picking, skip all meetings that overlap with it.
> Repeat.**
>
> Why sort by end time?
>
> The meeting that ends earliest
> leaves the MOST room for future meetings.
> Picking it maximizes the remaining time available.
>
> Picking a meeting that ends later
> might block two or three shorter meetings
> that could have fit in the same window."\_

---

This is the saga of **N Meetings in One Room** (Activity Selection).

Given `n` meetings with start times `start[i]` and end times `end[i]`:

-   Only one meeting can use the room at a time.
-   Return the **maximum** number of non-overlapping meetings.

```
Input:  start = [1, 3, 0, 5, 8, 5], end = [2, 4, 6, 7, 9, 9]
Output: 4   (meetings: [1,2], [3,4], [5,7], [8,9])

Input:  start = [10, 12, 20], end = [20, 25, 30]
Output: 1   (all overlap with each other)
```

---

## 🧠 The Oracle's Core Insight -- Sort by End Time, Pick Greedily

```
1. Sort meetings by end time (ascending).
2. Pick the first meeting (earliest end).
3. For each subsequent meeting:
   If its start time > last picked meeting's end time:
     Pick it. Update last end time.
   Else:
     Skip (overlaps with the last picked meeting).
4. Count = number of picked meetings.
```

**Why sort by END time (not start time)?**

Sorting by start time doesn't work:
a meeting starting early but ending very late
would block many shorter meetings.

Sorting by end time ensures we always free the room
as early as possible -- maximizing space for future meetings.

**Why `start > lastEnd` (not `>=`)?**

If a meeting starts exactly when the previous one ends,
they don't overlap. Both can be held.
Use `>=` if the problem says "start == end is allowed."
(Check the problem's definition of overlap.)

```
Time:  O(n log n) -- sorting
Space: O(n) -- for the sorted structure (or O(1) if sorting in-place)
```

---

### 📜 The Scroll of the Maximum Gatherings

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 📅 The Greedy Ritual

```cpp
int maxMeetings(vector<int>& start, vector<int>& end, int n) {
```

---

### 📊 Create Meeting Structures and Sort by End Time

```cpp
    vector<pair<int, int>> meetings(n);
    for (int i = 0; i < n; i++) {
        meetings[i] = {end[i], start[i]};
    }
    sort(meetings.begin(), meetings.end());
```

Store as `{end, start}` pairs so sorting by the first element
automatically sorts by end time.

> _"The meeting that ends first gets priority.
> It frees the room earliest,
> leaving the most space for others."_

---

### 🏛️ Greedily Pick Non-Overlapping Meetings

```cpp
    int count = 1;
    int lastEnd = meetings[0].first;
```

Pick the first meeting (earliest end time). Count = 1.
`lastEnd` = the end time of the last picked meeting.

---

```cpp
    for (int i = 1; i < n; i++) {
        int meetingStart = meetings[i].second;

        if (meetingStart > lastEnd) {
            count++;
            lastEnd = meetings[i].first;
        }
    }
    return count;
}
```

For each subsequent meeting:
-   If it starts AFTER the last picked meeting ends → no overlap. Pick it.
-   Otherwise → it overlaps. Skip it.

> _"Does this meeting start after the room is free?
> If yes -- welcome. The room is yours.
> If no -- sorry. The room is occupied.
> Come back when it's free."_

---

### 🎺 The Trial of the Maximum Gatherings

```cpp
int main() {
    vector<int> s1 = {1, 3, 0, 5, 8, 5};
    vector<int> e1 = {2, 4, 6, 7, 9, 9};
    cout << maxMeetings(s1, e1, 6) << endl; // expected: 4

    vector<int> s2 = {10, 12, 20};
    vector<int> e2 = {20, 25, 30};
    cout << maxMeetings(s2, e2, 3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for start=[1,3,0,5,8,5], end=[2,4,6,7,9,9]:**

**Step 1 -- Sort by end time:**

| Meeting | Start | End | (sorted by end) |
|---------|-------|-----|-----------------|
| A       | 1     | 2   | ← first        |
| B       | 3     | 4   |                 |
| C       | 0     | 6   |                 |
| D       | 5     | 7   |                 |
| E       | 8     | 9   |                 |
| F       | 5     | 9   |                 |

**Step 2 -- Greedy selection:**

| Meeting | Start | End | start > lastEnd? | Action       | count | lastEnd |
|---------|-------|-----|-------------------|--------------|-------|---------|
| A       | 1     | 2   | (first pick)      | PICK         | 1     | 2       |
| B       | 3     | 4   | 3 > 2 ✓          | PICK         | 2     | 4       |
| C       | 0     | 6   | 0 > 4 ✗          | Skip         | 2     | 4       |
| D       | 5     | 7   | 5 > 4 ✓          | PICK         | 3     | 7       |
| E       | 8     | 9   | 8 > 7 ✓          | PICK         | 4     | 9       |
| F       | 5     | 9   | 5 > 9 ✗          | Skip         | 4     | 9       |

**Answer: 4** ✓

Meetings picked: A[1,2], B[3,4], D[5,7], E[8,9]. No overlaps.

Meeting C[0,6] was skipped because it starts at 0 which is ≤ lastEnd(4) at that point.
Meeting F[5,9] was skipped because 5 ≤ 9.

---

**Trace for start=[10,12,20], end=[20,25,30]:**

Sorted by end: [10,20], [12,25], [20,30].

| Meeting | Start | End | start > lastEnd? | Action |
|---------|-------|-----|-------------------|--------|
| [10,20] | 10    | 20  | (first pick)      | PICK. lastEnd=20. |
| [12,25] | 12    | 25  | 12 > 20 ✗        | Skip.  |
| [20,30] | 20    | 30  | 20 > 20 ✗        | Skip.  |

**Answer: 1** ✓

All meetings overlap with the first one. Only one can be held.

---

## 🔍 Why Sort by End Time (Not Start Time, Not Duration)

**Sort by start time?** ✗
Meeting [1, 100] starts first but blocks everything.
Better to pick [1, 2], [3, 4], [5, 6]... many short meetings.

**Sort by duration?** ✗
A short meeting in the middle might overlap with two others.
Duration alone doesn't capture when the room becomes free.

**Sort by end time?** ✓
The meeting that ends earliest frees the room earliest.
This maximizes the remaining time for future meetings.
Proven optimal by exchange argument.

---

## 🔍 Connection to Other Interval Problems

| Problem                    | Sort by    | Greedy action              |
| -------------------------- | ---------- | -------------------------- |
| **N Meetings (this)**      | End time   | Pick if no overlap         |
| Non-overlapping Intervals  | End time   | Count overlaps to remove   |
| Merge Intervals            | Start time | Merge overlapping ones     |
| Insert Interval            | --         | Find insertion point       |
| Minimum Platforms          | Events     | Count simultaneous meetings|

Activity Selection (this) and Non-overlapping Intervals
are essentially the SAME problem:
-   Activity Selection: maximize meetings picked.
-   Non-overlapping: minimize meetings removed.
-   `removed = n - picked`. Same greedy, different output.

---

### 🧠 Memory of the Maximum Gatherings Law

-   **Sort by END time** (ascending) -- the key greedy choice
-   **Pick first meeting** (earliest end)
-   **For each next:** if `start > lastEnd` → pick, update lastEnd. Else skip.
-   **Why end time?** Frees the room earliest → maximizes future options
-   **Same as Non-overlapping Intervals:** `removed = n - picked`
-   **Time:** O(n log n) -- sorting
-   **Space:** O(n) for sorted structure
-   **Edge cases:**
    -   All meetings overlap → 1
    -   No overlaps → n (all fit)
    -   Meetings touching (start == prev end) → depends on problem definition

Thus is remembered the saga of **N Meetings in One Room**,
where the Oracle sorted meetings by their end time --
always picking the one that freed the room earliest --
skipping any meeting that clashed with the last chosen --
until the maximum number of non-overlapping gatherings
was found through the ancient Activity Selection greedy. 🏛️📅✨
