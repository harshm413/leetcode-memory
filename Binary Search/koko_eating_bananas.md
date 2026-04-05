## 🍌⚔️ _The Pace of the Hungry Titan: The Koko Eating Bananas Saga_

> \_"In the Forest of Piles,
> bananas were stacked in towering heaps --
> some small, some enormous.
>
> The mighty Koko stood ready to feast,
> but she had only **h hours** before the guards returned.
>
> Koko could choose an eating speed `k` --
> bananas per hour.
> Each hour, she picked one pile
> and ate up to `k` bananas from it.
> If the pile had fewer than `k`, she finished it
> and waited for the next hour.
> She could not move to another pile mid-hour.
>
> The Oracle was commanded:
>
> **'Find the minimum eating speed k
> such that Koko finishes all bananas within h hours.'**
>
> Too slow -- and time would betray her.
> Too fast -- and the speed was wasteful.
>
> The Oracle realized this was not about counting bananas --
> it was about searching the **space of possible speeds**.
>
> The minimum speed was `1` -- eat one banana per hour.
> The maximum speed was `max(piles)` --
> the largest pile eaten in a single hour.
> Any speed beyond that was wasteful.
>
> For any candidate speed, the Oracle could simulate:
> how many hours does Koko need at this speed?
> If she finishes in time -- try slower.
> If she doesn't -- try faster.
>
> **Binary search on the answer space.**
> The same pattern as shipping packages."\_

---

This is the saga of **Koko Eating Bananas**.

You are given:

-   `piles[]` -- the number of bananas in each pile.
-   `h` -- the number of hours available.

Rules:

-   Koko chooses a fixed speed `k` (bananas per hour).
-   Each hour, she eats up to `k` bananas from **one** pile.
-   If a pile has fewer than `k` bananas, she finishes it and waits.
-   She cannot split an hour across two piles.

Find the **minimum integer `k`** such that Koko eats all bananas within `h` hours.

```
Input:  piles = [3, 6, 7, 11], h = 8
Output: 4

Input:  piles = [30, 11, 23, 4, 20], h = 5
Output: 30

Input:  piles = [30, 11, 23, 4, 20], h = 6
Output: 23
```

---

## 🧠 The Oracle's Core Insight -- Binary Search on Speed

The answer lies in the range `[1, max(piles)]`.

For any candidate speed `k`, the hours needed to eat all piles is:

```
totalHours = sum of ceil(pile / k) for each pile
```

`ceil(pile / k)` is the number of hours to finish one pile at speed `k`.
In integer arithmetic: `ceil(pile / k) = (pile + k - 1) / k`.

**Feasibility check:**

```
If totalHours <= h  -->  speed k is sufficient, try smaller
If totalHours > h   -->  speed k is too slow, try larger
```

Binary search finds the minimum `k` that passes the check.

```
Time:  O(N * log(max(piles)))  -- log range for binary search, N per simulation
Space: O(1)
```

---

### 📜 The Scroll of the Forest

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⚓ The Feasibility Check -- Can Koko Eat in Time?

_Simulate eating at a given speed_

```cpp
bool canEat(vector<int>& piles, int h, int speed) {
    long long hours = 0;
    for (int pile : piles) {
        hours += (pile + speed - 1) / speed;
    }
    return hours <= h;
}
```

For each pile, the Oracle computed the hours needed:

`(pile + speed - 1) / speed` is **ceiling division** --
the number of full hours to eat `pile` bananas at the given speed.

Why ceiling? Because even if only 1 banana remains in a pile,
Koko spends a full hour on it.

`long long` prevented overflow when summing many large piles.

If the total hours fit within `h` -- the speed was sufficient.

> _"A pile of 11 bananas at speed 4:
> (11 + 4 - 1) / 4 = 14 / 4 = 3 hours.
> Hour 1: eat 4, remaining 7.
> Hour 2: eat 4, remaining 3.
> Hour 3: eat 3, done. Waited the rest of the hour."_

---

## ⚔️ The Oracle's Pace-Finding Ritual

_Binary search with left <= right and answer tracking_

```cpp
int minEatingSpeed(vector<int>& piles, int h) {
    int left = 1;
    int right = *max_element(piles.begin(), piles.end());
    int answer = right;
```

The search space was defined:

-   `left = 1` -- the slowest possible speed (1 banana per hour).
-   `right = max(piles)` -- the fastest useful speed.
    At this speed, every pile is finished in one hour.
-   `answer = right` -- initialized to the worst case (always valid).

---

## 🔁 Halve the Speed Range

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle tested the middle speed.

The loop continued as long as the search space had at least one candidate.

---

### ✅ Speed Is Sufficient -- Record and Try Slower

```cpp
        if (canEat(piles, h, mid)) {
            answer = mid;
            right = mid - 1;
        }
```

Koko finished in time at speed `mid`.

The Oracle recorded `mid` as the best answer so far,
then pulled `right` inward -- searching for an even slower valid speed.

> _"A valid answer is good.
> But a smaller valid answer is better.
> The Oracle always hunts for the minimum."_

---

### ❌ Speed Is Too Slow -- Try Faster

```cpp
        else {
            left = mid + 1;
        }
    }
    return answer;
}
```

Koko couldn't finish in time.
The Oracle demanded a faster pace: `left = mid + 1`.

When `left > right`, the search space collapsed.
The last recorded `answer` was the minimum sufficient speed.

---

### 🎺 The Trial of the Banana Forest

```cpp
int main() {
    vector<int> piles1 = {3, 6, 7, 11};
    cout << minEatingSpeed(piles1, 8) << endl; // expected: 4

    vector<int> piles2 = {30, 11, 23, 4, 20};
    cout << minEatingSpeed(piles2, 5) << endl; // expected: 30

    vector<int> piles3 = {30, 11, 23, 4, 20};
    cout << minEatingSpeed(piles3, 6) << endl; // expected: 23

    return 0;
}
```

---

**Full trace for `[3, 6, 7, 11]`, h = 8:**

Search range: `left = 1`, `right = 11`, `answer = 11`.

| Step | left | right | mid | canEat(mid)?                              | Action            |
| ---- | ---- | ----- | --- | ----------------------------------------- | ----------------- |
| 1    | 1    | 11    | 6   | 1+1+2+2=6 <= 8? Yes                      | ans=6, right=5    |
| 2    | 1    | 5     | 3   | 1+2+3+4=10 <= 8? No                      | left=4            |
| 3    | 4    | 5     | 4   | 1+2+2+3=8 <= 8? Yes                      | ans=4, right=3    |
| 4    | 4    | 3     | --  | left > right                              | Exit loop         |

**Answer: 4** ✓

At speed 4: pile 3 takes 1h, pile 6 takes 2h, pile 7 takes 2h, pile 11 takes 3h.
Total = 8 hours. Exactly on time.

At speed 3: total = 10 hours. Too slow.
So 4 is the minimum.

---

**Full trace for `[30, 11, 23, 4, 20]`, h = 5:**

Search range: `left = 1`, `right = 30`.

At speed 30: each pile takes 1 hour = 5 hours total. Feasible.
At speed 29: pile 30 takes ceil(30/29) = 2 hours. Total >= 6. Not feasible.

**Answer: 30** ✓

When `h == number of piles`, Koko must finish each pile in exactly 1 hour.
The speed must be at least `max(piles)`.

---

**Simulation detail for `[30, 11, 23, 4, 20]`, h = 6, speed = 23:**

| Pile | ceil(pile/23)       | Hours |
| ---- | ------------------- | ----- |
| 30   | ceil(30/23) = 2     | 2     |
| 11   | ceil(11/23) = 1     | 1     |
| 23   | ceil(23/23) = 1     | 1     |
| 4    | ceil(4/23) = 1      | 1     |
| 20   | ceil(20/23) = 1     | 1     |

Total = 6 <= 6. **Feasible** ✓

At speed 22: ceil(30/22) = 2, ceil(23/22) = 2. Total = 2+1+2+1+1 = 7 > 6. Not feasible.

**Answer: 23** ✓

---

### 🧠 Memory of the Titan's Pace Law

-   **Binary search on the answer** -- search the range of possible speeds `[1, max(piles)]`
-   **Feasibility check `canEat`:** for each pile, compute `ceil(pile / k) = (pile + k - 1) / k`
    -   Sum all hours. If `totalHours <= h` -- speed is sufficient.
-   **Binary search logic (`left <= right` with answer tracking):**
    -   If feasible -- `answer = mid`, `right = mid - 1` (try slower)
    -   If not feasible -- `left = mid + 1` (need faster)
-   **Loop exits** when `left > right` -- return saved `answer`
-   **Ceiling division** is essential -- even 1 remaining banana costs a full hour
-   Use `long long` for the hours sum to prevent overflow
-   **Time:** O(N * log(max(piles))) -- log range for search, N per simulation
-   **Space:** O(1)

Thus is remembered the saga of **Koko Eating Bananas**,
where the Oracle did not test every speed blindly
but halved the pace with disciplined binary search --
asking `canEat` at each candidate speed,
counting the hours pile by pile --
until the minimum speed was found,
just fast enough for the hungry titan
to finish her feast before the guards returned. 🍌⚔️✨
