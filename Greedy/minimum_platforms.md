## 🚂🏗️ _The Busiest Moment: The Minimum Platforms Saga_

> \_"At a railway station,
> trains arrived and departed throughout the day.
> Each train occupied a platform
> from its arrival time until its departure time.
>
> The Oracle was commanded:
>
> **'What is the MINIMUM number of platforms needed
> so that no train has to wait?'**
>
> The Oracle did not simulate each train individually.
> She thought about EVENTS:
>
> **Every arrival = +1 platform needed.
> Every departure = -1 platform freed.**
>
> Sort all arrivals and departures separately.
> Walk through them in chronological order.
> Track the maximum simultaneous trains at any moment.
>
> Two sorted arrays. Two pointers.
> The peak overlap = the answer."\_

---

This is the saga of **Minimum Number of Platforms**.

Given `n` trains with arrival times `arr[]` and departure times `dep[]`:

-   Find the minimum number of platforms needed
    so that no train waits.
-   A train occupies a platform from arrival to departure (inclusive).

```
Input:  arr = [900, 940, 950, 1100, 1500, 1800]
        dep = [910, 1200, 1120, 1130, 1900, 2000]
Output: 3

Input:  arr = [900, 1100, 1235]
        dep = [1000, 1200, 1240]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Sort Events, Two Pointers

**Idea:** sort arrivals and departures independently.
Use two pointers to walk through events in chronological order.

```
Sort arr[] ascending.
Sort dep[] ascending.

i = 0 (arrival pointer)
j = 0 (departure pointer)
platforms = 0, maxPlatforms = 0

While i < n:
  If arr[i] <= dep[j]:
    A train arrives BEFORE (or at same time as) one departs.
    platforms++. i++.
  Else:
    A train departs before the next arrival.
    platforms--. j++.
  maxPlatforms = max(maxPlatforms, platforms)

Return maxPlatforms
```

**Why sort separately (not as pairs)?**

We don't care WHICH train arrives or departs --
only HOW MANY are present simultaneously.
Sorting separately and merging with two pointers
gives us the chronological event stream.

**Why `arr[i] <= dep[j]` (not `<`)?**

If a train arrives at the same time another departs,
they BOTH need a platform at that instant.
The arriving train needs a platform before the departing one frees it.
(This depends on the problem's definition -- check carefully.)

```
Time:  O(n log n) -- sorting
Space: O(1) extra (sorting in-place)
```

---

### 📜 The Scroll of the Busiest Moment

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🏗️ The Two-Pointer Ritual

```cpp
int findPlatform(vector<int>& arr, vector<int>& dep, int n) {
    sort(arr.begin(), arr.end());
    sort(dep.begin(), dep.end());
```

Sort arrivals and departures independently.
We lose the pairing (which arrival goes with which departure)
but we don't need it -- only the count matters.

---

```cpp
    int i = 0, j = 0;
    int platforms = 0;
    int maxPlatforms = 0;
```

Two pointers: `i` for arrivals, `j` for departures.
`platforms` = currently occupied platforms.
`maxPlatforms` = the peak we've seen.

---

### 🔁 Merge Events Chronologically

```cpp
    while (i < n) {
```

Process until all arrivals are handled.
(Departures after the last arrival don't increase the count.)

---

### 🚂 Next Event Is an Arrival

```cpp
        if (arr[i] <= dep[j]) {
            platforms++;
            i++;
        }
```

The next arrival happens BEFORE (or at the same time as)
the next departure. A new train needs a platform.

`platforms++` -- one more platform occupied.
`i++` -- move to the next arrival.

> _"A train pulls in.
> It needs a platform NOW.
> The count rises."_

---

### 🚂 Next Event Is a Departure

```cpp
        else {
            platforms--;
            j++;
        }
```

The next departure happens BEFORE the next arrival.
A platform is freed.

`platforms--` -- one platform released.
`j++` -- move to the next departure.

> _"A train leaves.
> A platform is freed.
> The count drops."_

---

### 📊 Track the Peak

```cpp
        maxPlatforms = max(maxPlatforms, platforms);
    }
    return maxPlatforms;
}
```

After each event, check if the current count is a new maximum.
The peak = the minimum platforms needed.

---

### 🎺 The Trial of the Busiest Moment

```cpp
int main() {
    vector<int> arr1 = {900, 940, 950, 1100, 1500, 1800};
    vector<int> dep1 = {910, 1200, 1120, 1130, 1900, 2000};
    cout << findPlatform(arr1, dep1, 6) << endl; // expected: 3

    vector<int> arr2 = {900, 1100, 1235};
    vector<int> dep2 = {1000, 1200, 1240};
    cout << findPlatform(arr2, dep2, 3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for arr=[900,940,950,1100,1500,1800], dep=[910,1200,1120,1130,1900,2000]:**

**After sorting:**
-   arr = [900, 940, 950, 1100, 1500, 1800]
-   dep = [910, 1120, 1130, 1200, 1900, 2000]

| Step | arr[i] | dep[j] | arr[i] <= dep[j]? | Action     | platforms | maxPlatforms |
|------|--------|--------|---------------------|------------|-----------|--------------|
| 1    | 900    | 910    | 900 ≤ 910 ✓       | Arrive. i=1| 1         | 1            |
| 2    | 940    | 910    | 940 ≤ 910 ✗       | Depart. j=1| 0         | 1            |
| 3    | 940    | 1120   | 940 ≤ 1120 ✓      | Arrive. i=2| 1         | 1            |
| 4    | 950    | 1120   | 950 ≤ 1120 ✓      | Arrive. i=3| 2         | 2            |
| 5    | 1100   | 1120   | 1100 ≤ 1120 ✓     | Arrive. i=4| 3         | **3**        |
| 6    | 1500   | 1120   | 1500 ≤ 1120 ✗     | Depart. j=2| 2         | 3            |
| 7    | 1500   | 1130   | 1500 ≤ 1130 ✗     | Depart. j=3| 1         | 3            |
| 8    | 1500   | 1200   | 1500 ≤ 1200 ✗     | Depart. j=4| 0         | 3            |
| 9    | 1500   | 1900   | 1500 ≤ 1900 ✓     | Arrive. i=5| 1         | 3            |
| 10   | 1800   | 1900   | 1800 ≤ 1900 ✓     | Arrive. i=6| 2         | 3            |

i = 6 = n. Loop ends.

**Answer: 3** ✓

The peak occurs at step 5: three trains are present simultaneously
(arrived at 940, 950, 1100 -- none has departed yet at that point).

---

**Trace for arr=[900,1100,1235], dep=[1000,1200,1240]:**

After sorting: arr=[900,1100,1235], dep=[1000,1200,1240].

| Step | arr[i] | dep[j] | Action     | platforms | max |
|------|--------|--------|------------|-----------|-----|
| 1    | 900    | 1000   | Arrive.    | 1         | 1   |
| 2    | 1100   | 1000   | Depart.    | 0         | 1   |
| 3    | 1100   | 1200   | Arrive.    | 1         | 1   |
| 4    | 1235   | 1200   | Depart.    | 0         | 1   |
| 5    | 1235   | 1240   | Arrive.    | 1         | 1   |

**Answer: 1** ✓ -- no two trains overlap. One platform suffices.

---

## 🔍 Why Sort Separately (Not as Pairs)

If we sort as pairs `{arrival, departure}`, we'd need to
process events in a more complex way (merge two event types).

Sorting separately + two pointers achieves the same thing
more simply: the two-pointer merge naturally processes
events in chronological order.

We don't need to know WHICH train is arriving or departing --
just the COUNT of simultaneous trains.

---

## 🔍 The `<=` vs `<` Decision

**`arr[i] <= dep[j]`:** arrival and departure at the same time
→ the arriving train needs a platform (they overlap at that instant).

**`arr[i] < dep[j]`:** arrival and departure at the same time
→ the departing train frees the platform first (no overlap).

Check the problem statement carefully. Most versions use `<=`
(the arriving train needs a platform before the other leaves).

---

## 🔍 Connection to Other Problems

| Problem                    | Technique                          |
| -------------------------- | ---------------------------------- |
| **Minimum Platforms (this)** | Sort + two pointers, count peak  |
| Meeting Rooms II           | Same problem, different name       |
| Merge Intervals            | Sort by start, merge overlapping   |
| Non-overlapping Intervals  | Sort by end, count removals        |
| Car Pooling                | Event-based (capacity check)       |

Minimum Platforms and Meeting Rooms II are the SAME problem.
"How many rooms/platforms needed?" = "What's the max overlap?"

---

### 🧠 Memory of the Busiest Moment Law

-   **Sort arrivals and departures SEPARATELY** (ascending)
-   **Two pointers:** `i` for arrivals, `j` for departures
-   **If `arr[i] <= dep[j]`:** arrival → platforms++, i++
-   **Else:** departure → platforms--, j++
-   **Track max platforms** after each event
-   **Answer = peak simultaneous trains**
-   **Same as Meeting Rooms II**
-   **Time:** O(n log n) -- sorting
-   **Space:** O(1) extra
-   **Edge cases:**
    -   No overlaps → 1 platform
    -   All overlap → n platforms
    -   Same arrival and departure time → depends on `<=` vs `<`

Thus is remembered the saga of **Minimum Platforms**,
where the Oracle sorted arrivals and departures separately,
then walked through events with two pointers --
counting up for every arrival,
counting down for every departure --
tracking the peak moment
when the most trains stood simultaneously --
and that peak was the minimum platforms needed
so no train would ever wait. 🚂🏗️✨
