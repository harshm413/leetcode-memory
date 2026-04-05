## 🚢⚔️ _The Weight of the Tides: The Capacity To Ship Packages Within D Days Saga_

> \_"Along the great harbour of the Kingdom of Cargo,
> ships waited to carry packages across the sea.
>
> Each day, a ship could carry only so much weight --
> no more than its capacity.
> Packages were loaded in order, one by one.
> If a new package would exceed the ship's limit,
> it was held for the next voyage.
>
> The Oracle was commanded:
>
> **'Find the minimum ship capacity
> such that all packages are delivered
> within exactly D days.
> Packages must be shipped in order -- no reordering.'**
>
> Too weak a ship -- and the days would run out.
> Too mighty a ship -- and capacity was wasted.
>
> The Oracle realized this was not a search
> through the array of packages --
> it was a search through the **space of possible capacities**.
>
> The minimum capacity was `max(weights)` --
> the ship must at least carry the heaviest single package.
>
> The maximum capacity was `sum(weights)` --
> ship everything in one day.
>
> Somewhere between these two extremes
> lay the perfect answer.
>
> And the Oracle knew exactly how to find it:
> **Binary search on the answer space.**
>
> For each candidate capacity, simulate the loading.
> If it works within D days -- try smaller.
> If it fails -- try larger.
> Halve the range until the minimum is found."\_

---

This is the saga of **Capacity To Ship Packages Within D Days**.

You are given:

-   `weights[]` -- the weight of each package, in shipping order.
-   `days` -- the number of days available.

Rules:

-   Packages must be shipped **in order** (no reordering).
-   Each day, packages are loaded until the next would exceed capacity.
-   Find the **minimum ship capacity** to finish within `days` days.

```
Input:  weights = [1,2,3,4,5,6,7,8,9,10], days = 5
Output: 15

Input:  weights = [3,2,2,4,1,4], days = 3
Output: 6

Input:  weights = [1,2,3,1,1], days = 4
Output: 3
```

---

## 🧠 The Oracle's Core Insight -- Binary Search on the Answer

This is a classic **binary search on answer** problem.

We are not searching for a target in the array.
We are searching for the **minimum capacity** in a range of possible values.

```
left  = max(weights)    -- must carry the heaviest package
right = sum(weights)    -- ship everything in one day

For each candidate capacity mid:
  Simulate greedy loading.
  Count how many days are needed.
  If days_needed <= D  -->  capacity is sufficient, try smaller
  If days_needed > D   -->  capacity is too small, try larger
```

The greedy simulation is the **feasibility check** --
it tells us whether a given capacity can finish the job in time.

The binary search finds the **minimum capacity** that passes the check.

```
Time:  O(N * log(sum - max))  -- log range of capacities, N per simulation
Space: O(1)
```

---

### 📜 The Scroll of the Harbour Ledger

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>
using namespace std;
```

---

## ⚓ The Feasibility Check -- Can We Ship in Time?

_Simulate greedy loading at a given capacity_

```cpp
bool canShip(vector<int>& weights, int days, int capacity) {
    int daysNeeded = 1;
    int currentLoad = 0;
```

The Oracle began with day 1 and an empty ship.

`daysNeeded` started at 1 -- the first day was already in use.
`currentLoad` tracked how much weight was on the current day's ship.

---

```cpp
    for (int w : weights) {
        if (currentLoad + w > capacity) {
            daysNeeded++;
            currentLoad = 0;
        }
        currentLoad += w;
    }
```

Each package was loaded in order:

**If adding this package would exceed capacity** --
the current day's ship was full.
A new day began (`daysNeeded++`), the ship was emptied (`currentLoad = 0`),
and the package was loaded onto the fresh ship.

**If it fit** -- it was simply added to the current load.

The greedy strategy is optimal here:
load as much as possible each day before starting a new one.
This minimizes the number of days needed for any given capacity.

---

```cpp
    return daysNeeded <= days;
}
```

If the total days needed was within the limit -- the capacity was sufficient.

---

## 🌊 The Oracle's Capacity-Finding Ritual

_Binary search on the answer space_

```cpp
int shipWithinDays(vector<int>& weights, int days) {
    int left = *max_element(weights.begin(), weights.end());
    int right = accumulate(weights.begin(), weights.end(), 0);
    int answer = right;
```

The search space was defined:

-   `left = max(weights)` -- the ship must carry at least the heaviest package.
    Any capacity below this would leave that package unshippable.
-   `right = sum(weights)` -- a ship this large could carry everything in one day.
-   `answer = right` -- initialized to the worst case (always valid).

---

### 🧭 Halve the Range of Capacities

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle tested the middle capacity in the current range.

---

### ✅ Capacity Is Sufficient -- Try Smaller

```cpp
        if (canShip(weights, days, mid)) {
            answer = mid;
            right = mid - 1;
        }
```

The simulation succeeded within D days.

The Oracle recorded `mid` as the best answer so far,
then pulled `right` inward -- searching for an even smaller capacity.

> _"A valid answer is good.
> But a smaller valid answer is better.
> The Oracle always hunts for the minimum."_

---

### ❌ Capacity Is Too Small -- Try Larger

```cpp
        else {
            left = mid + 1;
        }
    }
    return answer;
}
```

The simulation needed more days than allowed.
The ship was too weak.

The Oracle pushed `left` upward -- demanding a stronger vessel.

When `left > right`, the search space collapsed.
The last recorded `answer` was the minimum sufficient capacity.

---

### 🎺 The Trial of the Harbour

```cpp
int main() {
    vector<int> weights1 = {1,2,3,4,5,6,7,8,9,10};
    cout << shipWithinDays(weights1, 5) << endl; // expected: 15

    vector<int> weights2 = {3,2,2,4,1,4};
    cout << shipWithinDays(weights2, 3) << endl; // expected: 6

    vector<int> weights3 = {1,2,3,1,1};
    cout << shipWithinDays(weights3, 4) << endl; // expected: 3

    return 0;
}
```

---

**Full trace for `[1,2,3,4,5,6,7,8,9,10]`, days = 5:**

Search range: `left = 10`, `right = 55`.

| Step | left | right | mid | Simulation (days needed)       | Feasible? | Action          |
| ---- | ---- | ----- | --- | ------------------------------ | --------- | --------------- |
| 1    | 10   | 55    | 32  | [1-10] all in 1 day = 1 day   | Yes       | ans=32, right=31|
| 2    | 10   | 31    | 20  | [1-6][7-10] = 2 days          | Yes       | ans=20, right=19|
| 3    | 10   | 19    | 14  | [1-4][5-9 no,5-6][7-8][9][10] | 5 days?   | Check...        |

Let me trace `mid=15` specifically:

**Simulation with capacity = 15:**

| Day | Packages loaded | Load | Exceeds 15? | Action    |
| --- | --------------- | ---- | ----------- | --------- |
| 1   | 1,2,3,4,5       | 15   | No          | Continue  |
| 2   | 6,7             | 13   | 8 would = 21 | New day |
| 3   | 8               | 8    | 9 would = 17 | New day |
| 4   | 9               | 9    | 10 would = 19| New day |
| 5   | 10              | 10   | Done        | --        |

Days needed = 5 <= 5. **Feasible** ✓

**Simulation with capacity = 14:**

| Day | Packages loaded | Load | Action    |
| --- | --------------- | ---- | --------- |
| 1   | 1,2,3,4         | 10   | 5 would=15 > 14, new day |
| 2   | 5,6             | 11   | 7 would=18 > 14, new day |
| 3   | 7               | 7    | 8 would=15 > 14, new day |
| 4   | 8               | 8    | 9 would=17 > 14, new day |
| 5   | 9               | 9    | 10 would=19 > 14, new day |
| 6   | 10              | 10   | Done      |

Days needed = 6 > 5. **Not feasible** ✗

So 15 is the minimum capacity. **Answer: 15** ✓

---

**Trace for `[3,2,2,4,1,4]`, days = 3:**

Search range: `left = 4`, `right = 16`.

**Simulation with capacity = 6:**

| Day | Packages loaded | Load | Action    |
| --- | --------------- | ---- | --------- |
| 1   | 3,2             | 5    | 2 would=7 > 6, new day |
| 2   | 2,4             | 6    | 1 would=7 > 6, new day |
| 3   | 1,4             | 5    | Done      |

Days needed = 3 <= 3. **Feasible** ✓

**Simulation with capacity = 5:**

| Day | Packages loaded | Load | Action    |
| --- | --------------- | ---- | --------- |
| 1   | 3,2             | 5    | 2 would=7 > 5, new day |
| 2   | 2               | 2    | 4 would=6 > 5, new day |
| 3   | 4,1             | 5    | 4 would=9 > 5, new day |
| 4   | 4               | 4    | Done      |

Days needed = 4 > 3. **Not feasible** ✗

**Answer: 6** ✓

---

### 🧠 Memory of the Harbour Law

-   **Binary search on the answer** -- not on the array, but on the range of possible capacities
-   **Search range:** `[max(weights), sum(weights)]`
-   **Feasibility check:** greedily simulate loading, count days needed
    -   Load packages in order until the next would exceed capacity
    -   Start a new day when capacity is exceeded
    -   If `daysNeeded <= D` -- capacity is sufficient
-   **Binary search logic:**
    -   If feasible -- record answer, try smaller (`right = mid - 1`)
    -   If not feasible -- try larger (`left = mid + 1`)
-   **Greedy loading is optimal** -- packing as much as possible each day minimizes total days
-   **Time:** O(N * log(sum - max)) -- log range for binary search, N per simulation
-   **Space:** O(1) -- only a few variables

Thus is remembered the saga of **Capacity To Ship Packages Within D Days**,
where the Oracle did not test every possible ship blindly
but halved the harbour of possibilities again and again --
testing each candidate with a swift greedy simulation --
until the mightiest-yet-smallest vessel was found,
just strong enough to carry all cargo
before the tide of deadline ran out. 🚢⚔️✨
