## 🏢📅 _The Peak Overlap: The Meeting Rooms II Saga_

> \_"In a corporate building,
> meetings were scheduled throughout the day.
> Each meeting had a start time and an end time.
>
> The Oracle was commanded:
>
> **'What is the MINIMUM number of conference rooms needed
> so that no two overlapping meetings share a room?'**
>
> The Oracle recognized this as the same problem
> as **Minimum Platforms** --
> just with meetings instead of trains,
> and rooms instead of platforms.
>
> **The answer = the maximum number of meetings
> happening simultaneously at any point in time.**
>
> Three approaches existed:
>
> **Approach 1 -- Sort separately + two pointers.**
> Sort start times and end times independently.
> Walk with two pointers, counting overlaps.
>
> **Approach 2 -- Event list (pairs).**
> Merge all starts and ends into one sorted event list.
> Walk through: start → count++, end → count--.
>
> **Approach 3 -- Min-heap.**
> Sort meetings by start time.
> Use a min-heap to track the earliest ending meeting.
> If the next meeting starts after the earliest end → reuse that room.
> Otherwise → need a new room.
>
> All three give the same answer.
> The min-heap approach is the most intuitive for interviews."\_

---

This is the saga of **Meeting Rooms II** (LeetCode 253).

Given an array of meeting intervals `[start, end]`:

-   Return the **minimum** number of conference rooms required.

```
Input:  intervals = [[0,30],[5,10],[15,20]]
Output: 2

Input:  intervals = [[7,10],[2,4]]
Output: 1

Input:  intervals = [[0,5],[5,10],[10,15]]
Output: 1   (meetings are back-to-back, not overlapping)
```

---

## 🧠 Approach 1 -- Sort Separately + Two Pointers

Same as Minimum Platforms. Sort starts and ends independently.
Walk with two pointers.

```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    int n = intervals.size();
    vector<int> starts(n), ends(n);

    for (int i = 0; i < n; i++) {
        starts[i] = intervals[i][0];
        ends[i] = intervals[i][1];
    }

    sort(starts.begin(), starts.end());
    sort(ends.begin(), ends.end());
```

Extract and sort start times and end times separately.

---

```cpp
    int rooms = 0, maxRooms = 0;
    int i = 0, j = 0;

    while (i < n) {
        if (starts[i] < ends[j]) {
            rooms++;
            i++;
        } else {
            rooms--;
            j++;
        }
        maxRooms = max(maxRooms, rooms);
    }
    return maxRooms;
}
```

If next start < next end → a meeting begins before one ends → need more rooms.
If next start >= next end → a meeting ends before/when the next starts → free a room.

Track the peak rooms needed.

> _"Every start that comes before an end
> means one more meeting is happening simultaneously.
> Every end that comes before a start
> means a room is freed."_

```
Time:  O(n log n)
Space: O(n)
```

---

## 🧠 Approach 2 -- Event List (Array of Pairs)

Merge all events into one sorted list. Walk and count.

```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    vector<pair<int, int>> events;

    for (auto& iv : intervals) {
        events.push_back({iv[0], 1});   // start = +1
        events.push_back({iv[1], -1});  // end = -1
    }
```

Each start is `+1` (room needed). Each end is `-1` (room freed).

---

```cpp
    sort(events.begin(), events.end());
```

Sort by time. On ties, `-1` (end) comes before `+1` (start)
because `{10, -1} < {10, 1}` naturally.
This means if a meeting ends at 10 and another starts at 10,
the room is freed first → they can share.

---

```cpp
    int rooms = 0, maxRooms = 0;

    for (auto& [time, type] : events) {
        rooms += type;
        maxRooms = max(maxRooms, rooms);
    }
    return maxRooms;
}
```

Walk sorted events. Accumulate. Track peak.

> _"Starts add rooms. Ends free rooms.
> The peak of the running count
> is the minimum rooms needed."_

```
Time:  O(n log n)
Space: O(n)
```

---

## 🧠 Approach 3 -- Min-Heap (Priority Queue)

The most intuitive approach for interviews.

**Idea:** sort meetings by start time.
Use a min-heap to track when each room becomes free (earliest end time on top).

For each meeting:
-   If the earliest-ending room is free (its end ≤ current start) → reuse it (pop + push new end).
-   Otherwise → need a new room (just push new end).

The heap size at any point = number of rooms in use.

```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
```

Sort by start time. Process meetings in chronological order.

---

```cpp
    priority_queue<int, vector<int>, greater<int>> pq;
```

Min-heap of end times. The top = the room that frees up earliest.

---

```cpp
    for (auto& iv : intervals) {
        int start = iv[0];
        int end = iv[1];
```

---

### 🔍 Can We Reuse a Room?

```cpp
        if (!pq.empty() && pq.top() <= start) {
            pq.pop();
        }
```

If the earliest-ending meeting has already ended (end ≤ current start) →
that room is free. Pop it (we'll reuse it by pushing the new end).

**Why `<=` (not `<`)?**
If a meeting ends at 10 and the next starts at 10 → no overlap.
The room is free at time 10. The new meeting can use it.

> _"The room that frees up earliest --
> if it's free before this meeting starts,
> we reclaim it. No new room needed."_

---

### 🏢 Assign This Meeting a Room

```cpp
        pq.push(end);
    }
```

Push this meeting's end time onto the heap.
Either we reused a room (popped the old end, pushed new end)
or we added a new room (just pushed).

---

### 📤 The Answer

```cpp
    return pq.size();
}
```

The heap size = number of rooms currently in use.
After processing all meetings, the heap size = the peak rooms needed.

**Why?** We only pop when a room is reused. We push for every meeting.
If a meeting can't reuse any room → heap grows by 1 (new room).
The final heap size = maximum simultaneous meetings = answer.

> _"The heap holds one entry per active room.
> When a room is reused, one entry is replaced.
> When a new room is needed, the heap grows.
> Its final size is the answer."_

---

### 🎺 The Trial of the Peak Overlap

```cpp
int main() {
    vector<vector<int>> i1 = {{0,30},{5,10},{15,20}};
    cout << minMeetingRooms(i1) << endl; // expected: 2

    vector<vector<int>> i2 = {{7,10},{2,4}};
    cout << minMeetingRooms(i2) << endl; // expected: 1

    vector<vector<int>> i3 = {{0,5},{5,10},{10,15}};
    cout << minMeetingRooms(i3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for intervals = [[0,30],[5,10],[15,20]] (min-heap approach):**

After sorting by start: `[[0,30],[5,10],[15,20]]`.

| Meeting | start | end | pq.top() | top <= start? | Action          | pq (heap)  |
|---------|-------|-----|----------|---------------|-----------------|------------|
| [0,30]  | 0     | 30  | (empty)  | --            | Push 30.        | {30}       |
| [5,10]  | 5     | 10  | 30       | 30 <= 5? No   | Push 10. (new room) | {10, 30}   |
| [15,20] | 15    | 20  | 10       | 10 <= 15? Yes | Pop 10. Push 20. (reuse) | {20, 30}   |

**pq.size() = 2. Answer: 2** ✓

Meeting [5,10] overlaps with [0,30] → needs a second room.
Meeting [15,20] starts after [5,10] ends → reuses that room.

---

**Trace for intervals = [[7,10],[2,4]]:**

After sorting: `[[2,4],[7,10]]`.

| Meeting | pq.top() | top <= start? | Action       | pq     |
|---------|----------|---------------|--------------|--------|
| [2,4]   | (empty)  | --            | Push 4.      | {4}    |
| [7,10]  | 4        | 4 <= 7? Yes   | Pop 4. Push 10. | {10} |

**pq.size() = 1. Answer: 1** ✓ -- no overlap, one room suffices.

---

**Trace for intervals = [[0,5],[5,10],[10,15]]:**

After sorting: `[[0,5],[5,10],[10,15]]`.

| Meeting | pq.top() | top <= start? | Action       | pq     |
|---------|----------|---------------|--------------|--------|
| [0,5]   | (empty)  | --            | Push 5.      | {5}    |
| [5,10]  | 5        | 5 <= 5? Yes   | Pop 5. Push 10. | {10} |
| [10,15] | 10       | 10 <= 10? Yes | Pop 10. Push 15. | {15} |

**pq.size() = 1. Answer: 1** ✓ -- back-to-back meetings share one room.

---

**Trace for intervals = [[1,5],[2,6],[3,7],[4,8]]:**

After sorting: `[[1,5],[2,6],[3,7],[4,8]]`.

| Meeting | pq.top() | top <= start? | Action       | pq           |
|---------|----------|---------------|--------------|--------------|
| [1,5]   | (empty)  | --            | Push 5.      | {5}          |
| [2,6]   | 5        | 5 <= 2? No    | Push 6.      | {5, 6}       |
| [3,7]   | 5        | 5 <= 3? No    | Push 7.      | {5, 6, 7}   |
| [4,8]   | 5        | 5 <= 4? No    | Push 8.      | {5, 6, 7, 8}|

**pq.size() = 4. Answer: 4** ✓ -- all four meetings overlap. Need 4 rooms.

---

## 🔍 Three Approaches Compared

| Approach              | Time       | Space  | Intuition                    |
| --------------------- | ---------- | ------ | ---------------------------- |
| Sort + Two Pointers   | O(n log n) | O(n)   | Count arrivals vs departures |
| Event List (Pairs)    | O(n log n) | O(n)   | +1 for start, -1 for end    |
| **Min-Heap**          | O(n log n) | O(n)   | Track earliest room to free  |

All give the same answer. Min-heap is most natural to explain in interviews
("assign meetings to rooms, reuse the earliest-freeing room").

---

## 🔍 This IS Minimum Platforms

| Minimum Platforms               | Meeting Rooms II                  |
| ------------------------------- | --------------------------------- |
| Trains arrive/depart            | Meetings start/end                |
| Platforms needed                | Rooms needed                      |
| Same algorithm                  | Same algorithm                    |

Literally the same problem with different names.

---

### 🧠 Memory of the Peak Overlap Law

**Three approaches (all O(n log n)):**

-   **Sort + Two Pointers:** sort starts/ends separately, walk with i/j, count peak
-   **Event List:** `{time, +1/-1}` pairs, sort, accumulate, track max
-   **Min-Heap:** sort by start, heap of end times, pop if room free, push new end, answer = heap size

**Min-Heap logic:**
-   Sort meetings by start time
-   For each meeting: if `pq.top() <= start` → reuse room (pop + push). Else → new room (push).
-   Answer = `pq.size()`

**Key insight:** the answer = maximum number of simultaneous meetings

-   **Time:** O(n log n). **Space:** O(n).
-   **Edge cases:**
    -   No meetings → 0
    -   All overlap → n rooms
    -   No overlaps → 1 room
    -   Back-to-back (end == next start) → same room (no overlap)

Thus is remembered the saga of **Meeting Rooms II**,
where the Oracle tracked the peak overlap of meetings --
whether by counting arrivals against departures,
or by maintaining a heap of room end-times,
always reusing the earliest-freeing room when possible,
and allocating a new room only when all were occupied --
until the minimum number of rooms was revealed
as the peak of simultaneous gatherings. 🏢📅✨
