## 📊🔗 _The Merging Timelines: The Merge Intervals Saga_

> \_"The Oracle was given a list of intervals.
>
> She was commanded:
>
> **'Merge all overlapping intervals.
> Return the list of non-overlapping intervals
> that cover the same range.'**
>
> `[[1,3],[2,6],[8,10],[15,18]]` → `[[1,6],[8,10],[15,18]]`
>
> [1,3] and [2,6] overlap (3 ≥ 2) → merge into [1,6].
> [8,10] and [15,18] don't overlap → stay separate.
>
> The Oracle's strategy:
>
> **Sort by start time.**
> Then walk through the intervals.
> If the current interval overlaps with the last merged one —
> extend the last merged interval.
> If not — start a new merged interval.
>
> One sort. One pass. Done."\_

---

This is the saga of **Merge Intervals (LeetCode 56)**.

Given an array of intervals `[start, end]`:
-   Merge all overlapping intervals.
-   Return the merged list.

```
Input:  [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]

Input:  [[1,4],[4,5]]
Output: [[1,5]]   (touching at 4 counts as overlapping)

Input:  [[1,4],[0,4]]
Output: [[0,4]]

Input:  [[1,4],[2,3]]
Output: [[1,4]]   (one interval fully inside another)
```

---

## 🧠 The Key Insight — Sort by Start, Then Merge

After sorting by start time, overlapping intervals are ADJACENT.
No interval later in the sorted order can overlap with an earlier one
without also overlapping with the ones in between.

**Two intervals overlap if:** `current.start ≤ lastMerged.end`.
The current interval starts before (or at) the end of the last merged one.

**When they overlap:** extend the last merged interval's end
to `max(lastMerged.end, current.end)`.
Why max? Because the current interval might end earlier
(fully contained) or later (extends beyond).

**When they don't overlap:** the current interval starts a new group.
Push it as a new merged interval.

---

### 📜 The Scroll of the Merging Timelines

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 📊 Sort by Start Time

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
```

Sorting `vector<vector<int>>` by default sorts by the first element,
then by the second on ties. That's exactly what we need — sort by start.

After sorting, intervals with the smallest start come first.
Overlapping intervals are guaranteed to be adjacent.

---

## 📊 Initialize the Result

```cpp
    vector<vector<int>> merged;
    merged.push_back(intervals[0]);
```

The first interval (after sorting) always starts a new group.
Push it as the initial merged interval.

---

## 📊 Walk and Merge

```cpp
    for (int i = 1; i < intervals.size(); i++) {
```

Start from the second interval. Compare each with the last merged.

---

### Overlapping — extend the last merged interval

```cpp
        if (intervals[i][0] <= merged.back()[1]) {
            merged.back()[1] = max(merged.back()[1], intervals[i][1]);
        }
```

`intervals[i][0]` = current start.
`merged.back()[1]` = last merged interval's end.

If current start ≤ last end → they overlap.
Extend the end to whichever reaches further.

Why `max`? Consider `[1,10]` and `[2,5]`.
The second is fully inside the first. `max(10, 5) = 10`.
The merged interval stays `[1,10]`. Correct.

> _"The current interval overlaps with the last.
> They merge — the end extends to cover both.
> The larger end wins."_

---

### Not overlapping — start a new group

```cpp
        else {
            merged.push_back(intervals[i]);
        }
    }
    return merged;
}
```

Current start > last end → gap between them. No overlap.
This interval starts a fresh group.

> _"A gap exists. The timelines don't touch.
> A new merged interval begins."_

---

### 🎺 The Trial of the Merging Timelines

```cpp
int main() {
    vector<vector<int>> i1 = {{1,3},{2,6},{8,10},{15,18}};
    auto r1 = merge(i1);
    for (auto& v : r1) cout << "[" << v[0] << "," << v[1] << "] ";
    cout << endl; // expected: [1,6] [8,10] [15,18]

    vector<vector<int>> i2 = {{1,4},{4,5}};
    auto r2 = merge(i2);
    for (auto& v : r2) cout << "[" << v[0] << "," << v[1] << "] ";
    cout << endl; // expected: [1,5]

    vector<vector<int>> i3 = {{1,4},{0,4}};
    auto r3 = merge(i3);
    for (auto& v : r3) cout << "[" << v[0] << "," << v[1] << "] ";
    cout << endl; // expected: [0,4]

    return 0;
}
```

---

**Trace for [[1,3],[2,6],[8,10],[15,18]]:**

After sorting (already sorted): `[[1,3],[2,6],[8,10],[15,18]]`

| i | interval | merged.back() | Overlap? | Action | merged |
|---|----------|---------------|----------|--------|--------|
| — | [1,3]    | —             | —        | init   | [[1,3]] |
| 1 | [2,6]    | [1,3]         | 2 ≤ 3 ✓ | extend end: max(3,6)=6 | [[1,**6**]] |
| 2 | [8,10]   | [1,6]         | 8 > 6 ✗ | new group | [[1,6],[8,10]] |
| 3 | [15,18]  | [8,10]        | 15 > 10 ✗| new group | [[1,6],[8,10],[15,18]] |

**Result: [[1,6],[8,10],[15,18]]** ✓

---

**Trace for [[1,4],[0,4]]:**

After sorting: `[[0,4],[1,4]]`

| i | interval | merged.back() | Overlap? | Action | merged |
|---|----------|---------------|----------|--------|--------|
| — | [0,4]    | —             | —        | init   | [[0,4]] |
| 1 | [1,4]    | [0,4]         | 1 ≤ 4 ✓ | extend: max(4,4)=4 | [[0,4]] |

**Result: [[0,4]]** ✓ (sorting fixed the order)

---

**Trace for [[1,4],[2,3]]:**

After sorting: `[[1,4],[2,3]]`

| i | interval | merged.back() | Overlap? | Action | merged |
|---|----------|---------------|----------|--------|--------|
| — | [1,4]    | —             | —        | init   | [[1,4]] |
| 1 | [2,3]    | [1,4]         | 2 ≤ 4 ✓ | extend: max(4,3)=4 | [[1,4]] |

**Result: [[1,4]]** ✓ (fully contained interval — max keeps the larger end)

---

## 🔍 Edge Cases

**Single interval:** `[[5,7]]` → `[[5,7]]`. Nothing to merge.

**All overlapping:** `[[1,4],[2,5],[3,6]]` → `[[1,6]]`. One big merge.

**None overlapping:** `[[1,2],[3,4],[5,6]]` → same. All separate.

**Touching endpoints:** `[[1,4],[4,5]]` → `[[1,5]]`.
`4 ≤ 4` is true → they overlap. Touching counts as overlapping.

---

## 🔍 Why Sorting by Start Is Sufficient

After sorting by start, for any two adjacent intervals `A` and `B`:
-   `A.start ≤ B.start` (guaranteed by sort).
-   If `B.start ≤ A.end` → overlap. Merge.
-   If `B.start > A.end` → gap. No overlap.

We never need to look beyond the immediate next interval
because sorting ensures all potential overlaps are adjacent.

---

## 🔍 The Interval Problem Family

| Problem | Key operation |
|---------|--------------|
| **Merge Intervals (this)** | Sort + merge overlapping |
| Insert Interval | Find overlap range, merge, rebuild |
| Non-overlapping Intervals | Count removals (greedy by end) |
| Meeting Rooms | Check if any overlap exists |
| Meeting Rooms II | Count max simultaneous overlaps |

All start with sorting. The merge logic varies.

---

### 🧠 Memory of the Merging Timelines Law

-   **Sort by start time** — overlapping intervals become adjacent
-   **Walk and compare:** `current.start ≤ lastMerged.end` → overlap
-   **Overlap:** extend end with `max(lastMerged.end, current.end)`
-   **No overlap:** push current as a new merged interval
-   **Touching endpoints count as overlapping** (`≤` not `<`)
-   **`max` for end** handles fully contained intervals correctly
-   **Time:** O(N log N) for sort + O(N) for merge. **Space:** O(N) for result.

Thus is remembered the saga of **Merge Intervals**,
where the Oracle sorted the timelines by their start,
then walked through them one by one —
overlapping intervals merged, their ends extending
to cover the union of both —
non-overlapping intervals stood alone —
until every timeline was accounted for
in a clean, non-overlapping sequence. 📊🔗✨
