## 🌸⏳ _The Bloom of Patience: The Minimum Number of Days to Make m Bouquets Saga_

> \_"In the Garden of Waiting Flowers,
> each bloom had its own destined day --
> the day it would finally open its petals.
>
> The Florist was commanded:
>
> **'You must make exactly m bouquets.
> Each bouquet requires k ADJACENT bloomed flowers.
> Find the minimum number of days to wait
> so that enough flowers have bloomed
> to form all m bouquets.'**
>
> Too few days -- and not enough flowers had opened.
> Too many days -- and patience was wasted.
>
> The Florist realized this was not about counting flowers --
> it was a search through the **space of possible days**.
>
> The minimum day was `min(bloomDay)` --
> the earliest any flower could bloom.
> The maximum day was `max(bloomDay)` --
> by then every flower had opened.
>
> For any candidate day, she could simulate:
> walk the garden, count consecutive bloomed flowers,
> form bouquets greedily.
> If she could make m bouquets -- try fewer days.
> If she couldn't -- try more days.
>
> **Binary search on the answer space.**
> The same ancient pattern."\_

---

This is the saga of **Minimum Number of Days to Make m Bouquets**.

You are given:

-   `bloomDay[]` -- the day each flower blooms.
-   `m` -- the number of bouquets needed.
-   `k` -- the number of **adjacent** flowers per bouquet.

Find the **minimum number of days** to make `m` bouquets.
If it's impossible (not enough flowers), return `-1`.

```
Input:  bloomDay = [1,10,3,10,2], m = 3, k = 1
Output: 3

Input:  bloomDay = [1,10,3,10,2], m = 3, k = 2
Output: -1   (only 5 flowers, need 3*2=6)

Input:  bloomDay = [7,7,7,7,12,7,7], m = 2, k = 3
Output: 12
```

---

## 🧠 The Oracle's Core Insight -- Binary Search on Days

First check: if `m * k > n`, it's impossible -- not enough flowers. Return `-1`.

Otherwise, the answer lies in `[min(bloomDay), max(bloomDay)]`.

For any candidate day `d`, the feasibility check is:

```
Walk the garden left to right.
Count consecutive flowers where bloomDay[i] <= d (bloomed by day d).
Every time the count reaches k → one bouquet formed, reset count.
If total bouquets >= m → day d is sufficient.
```

The greedy simulation is optimal --
forming a bouquet as soon as k adjacent flowers are available
never wastes flowers that could help a later bouquet.

```
Time:  O(N * log(max - min)) -- log range for search, N per simulation
Space: O(1)
```

---

### 📜 The Scroll of the Waiting Garden

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🌺 The Feasibility Check -- Can We Make m Bouquets by Day d?

```cpp
bool canMake(vector<int>& bloomDay, int m, int k, int day) {
    int bouquets = 0;
    int consecutive = 0;
```

The Florist walked the garden with two counters:
`bouquets` -- how many bouquets formed so far.
`consecutive` -- the current streak of adjacent bloomed flowers.

---

```cpp
    for (int i = 0; i < (int)bloomDay.size(); i++) {
        if (bloomDay[i] <= day) {
            consecutive++;
            if (consecutive == k) {
                bouquets++;
                consecutive = 0;
            }
        } else {
            consecutive = 0;
        }
    }
    return bouquets >= m;
}
```

For each flower:

**If it has bloomed by day `d`** (`bloomDay[i] <= day`) --
extend the consecutive streak.
When the streak reaches `k` → one bouquet formed, reset streak.

**If it hasn't bloomed** -- the streak is broken. Reset to 0.

The greedy approach: form a bouquet the instant k adjacent flowers
are available. Never skip or delay -- this maximizes total bouquets.

---

## ⏳ The Oracle's Day-Finding Ritual

```cpp
int minDays(vector<int>& bloomDay, int m, int k) {
    int n = bloomDay.size();
    if ((long long)m * k > n) return -1;
```

**Impossible check:** if we need `m * k` flowers total
but only `n` exist -- return `-1` immediately.

`(long long)m * k` prevents overflow when `m` and `k` are large.

---

```cpp
    int left = *min_element(bloomDay.begin(), bloomDay.end());
    int right = *max_element(bloomDay.begin(), bloomDay.end());
    int answer = right;
```

The search space:

-   `left = min(bloomDay)` -- earliest any flower blooms.
    No point checking earlier -- nothing has opened.
-   `right = max(bloomDay)` -- by this day every flower has bloomed.
    If m bouquets are possible at all, they're possible by this day.
-   `answer = right` -- initialized to worst case (always valid).

---

### 🔁 Halve the Range of Days

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle tested the middle day.

---

### ✅ Enough Bouquets -- Try Fewer Days

```cpp
        if (canMake(bloomDay, m, k, mid)) {
            answer = mid;
            right = mid - 1;
        }
```

The simulation succeeded -- m bouquets could be formed by day `mid`.

Record `mid` as the best answer so far,
then search for an even earlier day: `right = mid - 1`.

---

### ❌ Not Enough -- Need More Days

```cpp
        else {
            left = mid + 1;
        }
    }
    return answer;
}
```

Not enough flowers had bloomed by day `mid`.
The Florist needed more patience: `left = mid + 1`.

When the loop ended, `answer` held the minimum sufficient day.

---

### 🎺 The Trial of the Waiting Garden

```cpp
int main() {
    vector<int> b1 = {1, 10, 3, 10, 2};
    cout << minDays(b1, 3, 1) << endl; // expected: 3

    vector<int> b2 = {1, 10, 3, 10, 2};
    cout << minDays(b2, 3, 2) << endl; // expected: -1

    vector<int> b3 = {7, 7, 7, 7, 12, 7, 7};
    cout << minDays(b3, 2, 3) << endl; // expected: 12

    return 0;
}
```

---

**Full trace for `[1, 10, 3, 10, 2]`, m=3, k=1:**

Search range: `left=1`, `right=10`.

| Step | left | right | mid | Simulation                                  | Feasible? | Action          |
| ---- | ---- | ----- | --- | ------------------------------------------- | --------- | --------------- |
| 1    | 1    | 10    | 5   | bloomed: [1,_,3,_,2] → 3 singles → 3 bouq  | Yes       | ans=5, right=4  |
| 2    | 1    | 4     | 2   | bloomed: [1,_,_,_,2] → 2 singles → 2 bouq  | No        | left=3          |
| 3    | 3    | 4     | 3   | bloomed: [1,_,3,_,2] → 3 singles → 3 bouq  | Yes       | ans=3, right=2  |
| 4    | 3    | 2     | --  | left > right → exit                         | --        | --              |

**Answer: 3** ✓

By day 3, flowers at indices 0, 2, 4 have bloomed.
Each is a bouquet of k=1. Three bouquets formed.

---

**Trace for `[1, 10, 3, 10, 2]`, m=3, k=2:**

`m * k = 6 > n = 5` → **return -1** immediately. ✓

Not enough flowers exist to form 3 bouquets of 2.

---

**Full trace for `[7, 7, 7, 7, 12, 7, 7]`, m=2, k=3:**

Search range: `left=7`, `right=12`.

**Simulation at day 7:**
Bloomed: `[7,7,7,7,_,7,7]` → consecutive: 4 at start → bouquet at index 2 (reset),
then 1 more → break at index 4 → consecutive: 2 at end → no second bouquet.
Bouquets = 1. **Not feasible.**

**Simulation at day 12:**
Bloomed: all 7 flowers → consecutive: 3 → bouquet! reset → 3 → bouquet! reset → 1.
Bouquets = 2. **Feasible.**

| Step | left | right | mid | Feasible? | Action          |
| ---- | ---- | ----- | --- | --------- | --------------- |
| 1    | 7    | 12    | 9   | No (1 bouquet) | left=10     |
| 2    | 10   | 12    | 11  | No (1 bouquet) | left=12     |
| 3    | 12   | 12    | 12  | Yes (2 bouquets)| ans=12, right=11 |
| 4    | 12   | 11    | --  | left > right   | Exit         |

**Answer: 12** ✓

The flower at index 4 blooms on day 12 -- the bottleneck.
Only when it opens can two groups of 3 adjacent flowers form.

---

## 🔍 Why Greedy Simulation Is Optimal

When checking a candidate day, we form bouquets greedily --
the moment k consecutive bloomed flowers appear, we take them.

Why not save flowers for a "better" bouquet later?

Because flowers are fixed in position. A bouquet needs k **adjacent** ones.
Taking the earliest possible group never blocks a later group --
the remaining flowers to the right are still available.
Greedy is provably optimal here.

---

### 🧠 Memory of the Blooming Patience Law

-   **Impossible check:** if `m * k > n` → return `-1` (use `long long` for product)
-   **Binary search on the answer** -- range `[min(bloomDay), max(bloomDay)]`
-   **Feasibility check:** walk garden, count consecutive bloomed flowers (`bloomDay[i] <= day`)
    -   Every time streak reaches k → one bouquet, reset streak
    -   Unbloomed flower → reset streak to 0
    -   If `bouquets >= m` → day is sufficient
-   **Binary search logic:**
    -   Feasible → `answer = mid`, `right = mid - 1` (try fewer days)
    -   Not feasible → `left = mid + 1` (need more days)
-   **Greedy is optimal** -- take bouquets as soon as k adjacent flowers are available
-   **Time:** O(N × log(max - min))
-   **Space:** O(1)

Thus is remembered the saga of **Minimum Days to Make m Bouquets**,
where the Florist did not wait blindly
but halved the calendar with binary search --
testing each candidate day with a greedy walk through the garden,
counting consecutive blooms and forming bouquets on sight --
until the earliest day was found
when patience finally bore enough flowers
to fill every bouquet the kingdom demanded. 🌸⏳✨
