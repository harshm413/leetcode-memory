## 📐🔀 _The Seamless Insertion: The Insert Interval Saga_

> \_"The Oracle was given a list of NON-OVERLAPPING intervals,
> sorted by start time.
>
> A new interval needed to be inserted.
>
> She was commanded:
>
> **'Insert the new interval and merge if necessary.
> Return the resulting list of non-overlapping intervals.'**
>
> The Oracle saw three phases:
>
> **Phase 1:** Add all intervals that END BEFORE the new interval starts.
> (No overlap. They go straight to the result.)
>
> **Phase 2:** Merge all intervals that OVERLAP with the new interval.
> (Expand the new interval to cover them all.)
>
> **Phase 3:** Add all intervals that START AFTER the new interval ends.
> (No overlap. They go straight to the result.)
>
> Three phases. One pass. No sorting needed
> (the input is already sorted)."\_

---

This is the saga of **Insert Interval**.

Given a list of non-overlapping intervals sorted by start time,
and a new interval to insert:

-   Insert the new interval, merging overlapping intervals.
-   Return the resulting sorted, non-overlapping list.

```
Input:  intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]

Input:  intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]

Input:  intervals = [], newInterval = [5,7]
Output: [[5,7]]
```

---

## 🧠 The Oracle's Core Insight -- Three Phases

Since intervals are already sorted and non-overlapping:

**Phase 1 -- Before the new interval (no overlap):**
All intervals where `end < newStart` → add directly to result.

**Phase 2 -- Overlapping with the new interval:**
All intervals where `start <= newEnd` → merge into the new interval.
Merging: expand newInterval to cover the union.
`newStart = min(newStart, interval.start)`
`newEnd = max(newEnd, interval.end)`

**Phase 3 -- After the new interval (no overlap):**
All remaining intervals → add directly to result.

After phase 2, add the merged new interval to the result.

```
Time:  O(n) -- single pass
Space: O(n) -- the result array
```

---

### 📜 The Scroll of the Seamless Insertion

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔀 The Three-Phase Ritual

```cpp
vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
    vector<vector<int>> result;
    int i = 0;
    int n = intervals.size();
```

---

### 📐 Phase 1 -- Add All Intervals Before the New One

```cpp
    while (i < n && intervals[i][1] < newInterval[0]) {
        result.push_back(intervals[i]);
        i++;
    }
```

While the current interval's END is BEFORE the new interval's START →
no overlap possible. Add it directly.

`intervals[i][1] < newInterval[0]` → the interval ends before the new one begins.

> _"These intervals live entirely before the newcomer.
> They pass through untouched."_

---

### 🔗 Phase 2 -- Merge All Overlapping Intervals

```cpp
    while (i < n && intervals[i][0] <= newInterval[1]) {
        newInterval[0] = min(newInterval[0], intervals[i][0]);
        newInterval[1] = max(newInterval[1], intervals[i][1]);
        i++;
    }
    result.push_back(newInterval);
```

While the current interval's START is ≤ the new interval's END →
they overlap. Merge them into the new interval.

**Merge = expand the new interval:**
-   `newStart = min(newStart, interval.start)` → extend left if needed.
-   `newEnd = max(newEnd, interval.end)` → extend right if needed.

After all overlapping intervals are consumed,
add the merged new interval to the result.

> _"These intervals collide with the newcomer.
> They are absorbed -- their boundaries expand the newcomer
> until no more overlaps remain."_

---

### 📐 Phase 3 -- Add All Intervals After the New One

```cpp
    while (i < n) {
        result.push_back(intervals[i]);
        i++;
    }
    return result;
}
```

All remaining intervals start AFTER the new interval ends.
No overlap. Add them directly.

> _"These intervals live entirely after the newcomer.
> They pass through untouched."_

---

### 🎺 The Trial of the Seamless Insertion

```cpp
int main() {
    vector<vector<int>> i1 = {{1,3},{6,9}};
    vector<int> n1 = {2,5};
    auto r1 = insert(i1, n1);
    for (auto& iv : r1) cout << "[" << iv[0] << "," << iv[1] << "] ";
    cout << endl;
    // expected: [1,5] [6,9]

    vector<vector<int>> i2 = {{1,2},{3,5},{6,7},{8,10},{12,16}};
    vector<int> n2 = {4,8};
    auto r2 = insert(i2, n2);
    for (auto& iv : r2) cout << "[" << iv[0] << "," << iv[1] << "] ";
    cout << endl;
    // expected: [1,2] [3,10] [12,16]

    return 0;
}
```

---

**Full trace for intervals=[[1,3],[6,9]], newInterval=[2,5]:**

**Phase 1:** `intervals[0]=[1,3]`. End=3 < newStart=2? No (3 ≥ 2). Phase 1 ends immediately.

**Phase 2:** `intervals[0]=[1,3]`. Start=1 ≤ newEnd=5? Yes. Merge.
-   newInterval = [min(2,1), max(5,3)] = [1, 5].
-   `intervals[1]=[6,9]`. Start=6 ≤ newEnd=5? No. Phase 2 ends.
-   Add [1, 5] to result.

**Phase 3:** `intervals[1]=[6,9]`. Add directly.

**Result: [[1,5], [6,9]]** ✓

---

**Full trace for intervals=[[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval=[4,8]:**

**Phase 1:** `[1,2]`. End=2 < newStart=4? Yes. Add [1,2]. i=1.

**Phase 2:**
-   `[3,5]`. Start=3 ≤ newEnd=8? Yes. Merge: new=[min(4,3), max(8,5)] = [3, 8]. i=2.
-   `[6,7]`. Start=6 ≤ 8? Yes. Merge: new=[min(3,6), max(8,7)] = [3, 8]. i=3.
-   `[8,10]`. Start=8 ≤ 8? Yes. Merge: new=[min(3,8), max(8,10)] = [3, 10]. i=4.
-   `[12,16]`. Start=12 ≤ 10? No. Phase 2 ends.
-   Add [3, 10] to result.

**Phase 3:** `[12,16]`. Add directly.

**Result: [[1,2], [3,10], [12,16]]** ✓

---

**Trace for intervals=[], newInterval=[5,7]:**

Phase 1: empty. Phase 2: empty. Add [5,7]. Phase 3: empty.

**Result: [[5,7]]** ✓

---

## 🔍 The Overlap Condition

Two intervals `[a, b]` and `[c, d]` overlap if and only if:
`a <= d AND c <= b` (neither ends before the other starts).

In our phases:
-   Phase 1 ends when `intervals[i][1] >= newInterval[0]` (potential overlap starts).
-   Phase 2 continues while `intervals[i][0] <= newInterval[1]` (overlap continues).
-   Phase 3 starts when `intervals[i][0] > newInterval[1]` (no more overlap).

---

## 🔍 Why No Sorting Needed

The input is ALREADY sorted by start time and non-overlapping.
The three-phase approach exploits this sorted order --
all "before" intervals come first, then overlapping, then "after."

If the input were unsorted, we'd need to sort first (like Merge Intervals).

---

## 🔍 Connection to Merge Intervals

| Insert Interval (this)            | Merge Intervals                   |
| --------------------------------- | --------------------------------- |
| Input already sorted              | Must sort first                   |
| Insert ONE new interval           | Merge ALL overlapping intervals   |
| Three-phase approach              | Single-pass merge                 |
| O(n) time                         | O(n log n) time (sorting)         |

Insert Interval is simpler because the input is pre-sorted.

---

### 🧠 Memory of the Seamless Insertion Law

-   **Three phases:** before (add directly), overlap (merge), after (add directly)
-   **Phase 1:** while `intervals[i].end < newInterval.start` → add, i++
-   **Phase 2:** while `intervals[i].start <= newInterval.end` → merge (min start, max end), i++
-   **After phase 2:** add the merged newInterval
-   **Phase 3:** add all remaining intervals
-   **No sorting needed** (input is pre-sorted)
-   **Time:** O(n). **Space:** O(n) for result.
-   **Edge cases:**
    -   Empty intervals → just return [newInterval]
    -   New interval before all → added at the front
    -   New interval after all → added at the end
    -   New interval covers everything → single merged interval

Thus is remembered the saga of **Insert Interval**,
where the Oracle walked the sorted intervals in three phases --
passing through those that came before untouched,
absorbing those that overlapped into the newcomer,
and passing through those that came after untouched --
until the new interval was seamlessly woven
into the non-overlapping sequence. 📐🔀✨
