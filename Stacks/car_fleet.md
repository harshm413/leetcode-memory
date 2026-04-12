## 🚗🏁 _The Race to the Finish Line: The Car Fleet Saga_

> \_"On a single-lane highway stretching toward a target,
> cars drove at different speeds from different starting positions.
>
> A faster car behind a slower car could not pass --
> the lane was too narrow.
> When it caught up, it slowed down
> and they became a **fleet** --
> traveling together at the slower car's speed.
>
> The Oracle was commanded:
>
> **'How many fleets will arrive at the target?'**
>
> The Oracle realized the key insight:
>
> **Sort the cars by starting position, closest to target first.
> Compute how long each car takes to reach the target.
> Walk from the car closest to the target backward.
> If a car behind takes LESS or EQUAL time than the car ahead,
> it catches up and merges into the same fleet.
> If it takes MORE time, it forms a new fleet.**
>
> A stack tracked the arrival times of fleet leaders.
> Each new car either merged into the fleet ahead
> or became the leader of a new fleet.
>
> The number of entries left on the stack
> was the number of fleets."\_

---

This is the saga of **Car Fleet**.

Given:

-   `target` -- the destination (mile marker).
-   `position[]` -- starting positions of each car.
-   `speed[]` -- speed of each car.

Cars drive toward `target` on a single lane.
A faster car that catches a slower car forms a **fleet**
and travels at the slower car's speed.

Return the **number of car fleets** that arrive at the target.

```
Input:  target = 12, position = [10,8,0,5,3], speed = [2,4,1,1,3]
Output: 3

Input:  target = 10, position = [3], speed = [3]
Output: 1

Input:  target = 100, position = [0,2,4], speed = [4,2,1]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Sort by Position, Compare Arrival Times

Each car's **time to reach the target** is:

```
time = (target - position) / speed
```

A car closer to the target is a potential **blocker**.
A car behind it can only merge if it arrives at the same time or earlier.

The algorithm:

```
1. Pair each car as (position, speed).
2. Sort by position DESCENDING (closest to target first).
3. Compute arrival time for each car.
4. Walk through the sorted cars:
   - If this car's time > stack top's time → new fleet (push).
   - If this car's time <= stack top's time → it merges (skip).
5. Stack size = number of fleets.
```

Why sort closest-to-target first?
Because the car closest to the target is the first potential blocker.
Cars behind it either catch up (merge) or don't (new fleet).

```
Time:  O(N log N) -- sorting dominates
Space: O(N) -- the stack and sorted pairs
```

---

### 📜 The Scroll of the Highway

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle's Fleet-Counting Ritual

```cpp
int carFleet(int target, vector<int>& position, vector<int>& speed) {
    int n = position.size();
    vector<pair<int, int>> cars(n);
    for (int i = 0; i < n; i++) {
        cars[i] = {position[i], speed[i]};
    }
```

Each car was paired as `{position, speed}`.

---

### 🔄 Sort by Position -- Closest to Target First

```cpp
    sort(cars.begin(), cars.end(), [](auto& a, auto& b) {
        return a.first > b.first;
    });
```

Cars were sorted by starting position in **descending** order.

The car closest to the target came first --
it was the first potential blocker on the highway.

---

### 🏎️ Compute Arrival Times and Count Fleets

```cpp
    stack<double> st;

    for (auto& [pos, spd] : cars) {
        double time = (double)(target - pos) / spd;
```

For each car (now in order from closest to farthest from target),
the Oracle computed its arrival time.

`(target - pos) / spd` = how long this car takes to reach the target
if it drives unobstructed.

---

### 🚗 New Fleet or Merge?

```cpp
        if (st.empty() || time > st.top()) {
            st.push(time);
        }
    }
```

**If the stack is empty** -- this is the first car (closest to target).
It starts its own fleet. Push its time.

**If this car's time > stack top's time** --
this car takes LONGER to reach the target
than the fleet ahead of it.
It can never catch up. It forms a **new fleet**.
Push its time.

**If this car's time <= stack top's time** --
this car arrives at the same time or earlier
than the fleet ahead. It catches up and **merges**.
Do NOT push -- it joins the existing fleet.

> _"A car that arrives sooner than the one ahead
> will be blocked by it on the single lane.
> They become one fleet, traveling at the slower speed.
> Only a car that takes LONGER forms a new fleet."_

---

## 🏁 The Fleet Count

```cpp
    return st.size();
}
```

Each entry on the stack represented one distinct fleet.
The stack's size was the answer.

---

### 🎺 The Trial of the Highway

```cpp
int main() {
    vector<int> pos1 = {10, 8, 0, 5, 3}, spd1 = {2, 4, 1, 1, 3};
    cout << carFleet(12, pos1, spd1) << endl; // expected: 3

    vector<int> pos2 = {3}, spd2 = {3};
    cout << carFleet(10, pos2, spd2) << endl; // expected: 1

    vector<int> pos3 = {0, 2, 4}, spd3 = {4, 2, 1};
    cout << carFleet(100, pos3, spd3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for target=12, position=[10,8,0,5,3], speed=[2,4,1,1,3]:**

**After sorting by position descending:**

| Car | Position | Speed | Time = (12-pos)/spd |
| --- | -------- | ----- | ------------------- |
| A   | 10       | 2     | 1.0                 |
| B   | 8        | 4     | 1.0                 |
| C   | 5        | 1     | 7.0                 |
| D   | 3        | 3     | 3.0                 |
| E   | 0        | 1     | 12.0                |

**Stack processing:**

| Car | Time | Stack top | time > top? | Action      | Stack after       |
| --- | ---- | --------- | ----------- | ----------- | ----------------- |
| A   | 1.0  | (empty)   | --          | New fleet   | [1.0]             |
| B   | 1.0  | 1.0       | No (equal)  | Merges      | [1.0]             |
| C   | 7.0  | 1.0       | Yes         | New fleet   | [1.0, 7.0]        |
| D   | 3.0  | 7.0       | No          | Merges      | [1.0, 7.0]        |
| E   | 12.0 | 7.0       | Yes         | New fleet   | [1.0, 7.0, 12.0]  |

**Answer: 3 fleets** ✓

Fleet 1: Cars A and B (both arrive at time 1.0).
Fleet 2: Cars C and D (D catches C, both travel at C's slower pace).
Fleet 3: Car E alone (takes 12 hours, never catches anyone).

---

**Trace for target=100, position=[0,2,4], speed=[4,2,1]:**

**After sorting by position descending:**

| Car | Position | Speed | Time = (100-pos)/spd |
| --- | -------- | ----- | -------------------- |
| A   | 4        | 1     | 96.0                 |
| B   | 2        | 2     | 49.0                 |
| C   | 0        | 4     | 25.0                 |

| Car | Time | Stack top | time > top? | Action    | Stack after |
| --- | ---- | --------- | ----------- | --------- | ----------- |
| A   | 96.0 | (empty)   | --          | New fleet | [96.0]      |
| B   | 49.0 | 96.0      | No          | Merges    | [96.0]      |
| C   | 25.0 | 96.0      | No          | Merges    | [96.0]      |

**Answer: 1 fleet** ✓

Car A is the slowest and closest to the target.
Both B and C are faster but stuck behind A.
Everyone merges into one fleet.

---

**Trace for target=10, position=[3], speed=[3]:**

Single car. Time = 7/3 ≈ 2.33. Push. Stack size = 1.

**Answer: 1** ✓

---

## 🔍 Why Sort Closest-to-Target First?

Processing from closest to farthest mirrors the physical reality:

The car closest to the target is the **first potential blocker**.
Every car behind it either catches up (merges) or doesn't (new fleet).

If we sorted farthest-first, we'd need to look ahead
to see if a closer car blocks us -- much harder to reason about.

Closest-first lets us process linearly:
each car only needs to compare against the fleet directly ahead.

---

## 🔄 Why Use a Stack?

The stack tracks fleet leaders' arrival times.
Each new car compares against the top (the fleet directly ahead).

You could also just use a counter:

```cpp
int fleets = 0;
double lastTime = 0;
for (auto& [pos, spd] : cars) {
    double time = (double)(target - pos) / spd;
    if (time > lastTime) {
        fleets++;
        lastTime = time;
    }
}
return fleets;
```

This works because we only ever compare with the most recent fleet.
The stack makes the pattern explicit and connects to the monotonic stack family.

---

### 🧠 Memory of the Fleet Law

-   **Sort by position descending** -- closest to target first
-   **Arrival time:** `(target - position) / speed` -- use `double`
-   **Stack of arrival times** -- each entry = one fleet leader
    -   `time > stack.top()` → new fleet, push
    -   `time <= stack.top()` → merges into fleet ahead, skip
-   **Answer:** `stack.size()` = number of fleets
-   A car that takes longer than the one ahead can never catch up → new fleet
-   A car that takes less or equal time catches up → merges
-   **Time:** O(N log N) -- sorting dominates
-   **Space:** O(N)
-   **Edge cases:**
    -   Single car → 1 fleet
    -   All same speed → each car is its own fleet (unless same position)
    -   All cars at position 0 → fastest is blocked by slowest ahead after sort

Thus is remembered the saga of **Car Fleet**,
where the Oracle sorted the highway by proximity to the finish,
computed each car's unobstructed arrival time,
and walked the line with a stack of fleet leaders --
merging every car that caught the fleet ahead,
crowning a new fleet for every car that couldn't --
until the stack held exactly the number of fleets
that would cross the finish line. 🚗🏁✨
