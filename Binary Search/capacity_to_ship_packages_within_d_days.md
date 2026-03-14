## 🚢⚔️ _The Weight of the Tides: The Capacity To Ship Packages Within D Days Saga_

> \*"Along the great harbour of the Kingdom of Cargo,
> ships waited to carry packages across the sea.
>
> Each day, a ship could carry only so much weight —
> no more than its capacity.
>
> Packages were loaded in order, one by one.
> If a new package would exceed the ship's limit,
> it was held for the next voyage.
>
> The Oracle was commanded:
>
> **'Find the minimum ship capacity
> such that all packages are delivered
> within exactly D days.'**
>
> Too weak a ship — and the days would run out.
> Too mighty a ship — and capacity was wasted.
>
> So the Oracle did not test every possible capacity.
>
> She searched the sea of answers
> with the ancient ritual of halving —
> narrowing the range
> until the minimum sufficient capacity
> stood alone at the water's edge."\*

---

This is the saga of **Capacity To Ship Packages Within D Days**.

You are given:

-   `weights[]` → the weight of each package, in order
-   `days` → the number of days available to ship everything

Rules:

-   Packages must be shipped **in order** (no reordering).
-   Each day, packages are loaded until the next would exceed capacity.
-   Goal: **find the minimum ship capacity** to finish within `days` days.

---

## 🧠 The Oracle's Core Insight — Binary Search on Capacity

The possible capacity lies between:

```
max(weights)  → minimum possible (must fit the heaviest package)
sum(weights)  → maximum possible (ship everything in one day)
```

For any chosen capacity `mid`:

Simulate loading:

-   Walk packages in order.
-   Accumulate weight into the current day.
-   If adding the next package would exceed `mid` → start a new day.

If:

-   `days needed <= D` → capacity is valid
-   Otherwise → capacity is too small

Thus we **binary search on the capacity**.

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

## ⚓ The Oracle's Helper — Can We Ship in Time?

_Simulate loading at a given capacity_

```cpp
bool canShip(vector<int>& weights, int days, int capacity) {
    int daysNeeded = 1;
    int currentLoad = 0;
```

The Oracle began with one day and an empty ship.

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

-   If it fit within the remaining capacity → load it aboard.
-   If not → set sail, begin a new day, start fresh.

---

```cpp
    return daysNeeded <= days;
}
```

If the voyage was completed within the allotted days,
the capacity was declared sufficient.

---

## 🌊 The Oracle's Capacity-Finding Ritual

_Binary search with the classic left <= right sentinel_

```cpp
int shipWithinDays(vector<int>& weights, int days) {
    int left = *max_element(weights.begin(), weights.end());
    int right = accumulate(weights.begin(), weights.end(), 0);
    int answer = right;
```

-   `left` → the heaviest single package (the minimum possible capacity)
-   `right` → the total weight of all packages (one voyage covers all)
-   `answer` → records the best valid capacity found so far

---

### 🧭 Narrow the Range of Capacities

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle tested the middle capacity.
The loop continues as long as the search space has not been exhausted.

---

### ✅ If Capacity Is Sufficient

```cpp
        if (canShip(weights, days, mid)) {
            answer = mid;
            right = mid - 1;
        }
```

The capacity worked —
the Oracle recorded it as the best known answer,
then pulled `right` inward to search for something smaller still.

---

### ❌ If Capacity Is Too Small

```cpp
        else {
            left = mid + 1;
        }
    }
    return answer;
}
```

The ship was too weak.
The Oracle demanded a stronger vessel
and pushed `left` upward.

When `left > right`, the search space collapsed.
The last recorded `answer` held the minimum sufficient capacity.

---

### 🎺 The Trial of the Harbour

```cpp
int main() {
    vector<int> weights = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    int days = 5;

    cout << shipWithinDays(weights, days) << endl;
    // expected: 15
    return 0;
}
```

The Oracle determined:

-   With capacity `15`, the packages ship in exactly 5 days:
    -   Day 1: 1 + 2 + 3 + 4 + 5 = 15
    -   Day 2: 6 + 7 = 13
    -   Day 3: 8 = 8
    -   Day 4: 9 = 9
    -   Day 5: 10 = 10

No smaller ship could complete the voyage in time.

---

### 🧠 Memory of the Harbour Law

-   Binary search on **answer space** (capacity), not on the array
-   Lower bound = heaviest single package
-   Upper bound = sum of all packages
-   Simulate loading greedily to test feasibility
-   If feasible → save answer, shrink right; else → grow left
-   Loop ends when `left > right`; return saved `answer`
-   **Time:** O(n log(sum − max))
-   **Space:** O(1)

Thus is remembered the saga of
**Capacity To Ship Packages Within D Days**,
where the Oracle does not weigh every possible ship blindly —
but halves the harbour of possibilities again and again,
testing each candidate with a swift simulation,
until the mightiest-yet-smallest vessel is found —
just strong enough to carry all cargo
before the tide of deadline runs out. 🚢✨
