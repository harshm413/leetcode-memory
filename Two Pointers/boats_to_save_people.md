## 🚤⚔️ _The Last Ferry Across the Storm: The Boats to Save People Saga_

> \*"On the Shores of Burdened Souls,
> people gathered with weights upon their backs —
> some light as feathers,
> others heavy with fate.
>
> The Oracle was commanded:
>
> **‘Save everyone using the fewest boats possible.
> Each boat can carry at most two people,
> and their combined weight must not exceed the sacred limit.’**
>
> Panic urged haste,
> but the Oracle chose order.
>
> She lined the people by weight,
> placed one sentinel at the lightest,
> another at the heaviest —
> and let balance decide who could share a ride."\*

---

This is the saga of **Boats to Save People**.

You are given:

-   an array `people` where `people[i]` is a person’s weight
-   an integer `limit` representing the boat’s maximum capacity

Your task:

-   Find the **minimum number of boats** needed
-   Each boat can carry **at most two people**
-   The sum of weights in a boat must be `<= limit`

---

## 🧠 The Oracle’s Core Insight — Pair Light with Heavy

The Oracle understood:

-   The **heaviest person** must go on a boat no matter what
-   The best chance to save a boat is to pair them
    with the **lightest remaining person**
-   If they can’t pair, the heavy goes alone

Thus she used:

-   **Sorting**
-   **Two pointers**: lightest and heaviest

Every decision reduced the problem optimally.

---

### 📜 The Scroll of Ordered Souls

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle’s Two-Sentinel Ferry Ritual

_One light, one heavy — test the balance_

```cpp
int numRescueBoats(vector<int>& people, int limit) {
    sort(people.begin(), people.end());
    int left = 0;
    int right = people.size() - 1;
    int boats = 0;
```

The people were ordered by weight.
Two sentinels took position:

-   `left` → lightest soul
-   `right` → heaviest soul

---

### 🌊 Launch Boats Until All Are Saved

```cpp
    while (left <= right) {
        if (people[left] + people[right] <= limit) {
            left++;
            right--;
        } else {
            right--;
        }
        boats++;
    }
    return boats;
}
```

At each launch:

-   If the lightest and heaviest could share → both boarded
-   Otherwise → the heaviest went alone
-   In both cases → **one boat was consumed**

No boat was wasted.
No soul left behind.

---

### 🎺 The Trial of the Stormy Shore

```cpp
int main() {
    vector<int> people = {3, 2, 2, 1};
    int limit = 3;

    cout << numRescueBoats(people, limit) << endl; // expected: 3
    return 0;
}
```

The Oracle launched:

-   Boat 1: `1 + 2`
-   Boat 2: `2`
-   Boat 3: `3`

All were saved with **3 boats** — the minimum.

---

### 🧠 Memory of the Ferry Law

-   Sort the array first
-   Pair the lightest with the heaviest if possible
-   Otherwise, send the heaviest alone
-   Each iteration uses exactly one boat
-   **Time:** O(n log n)
-   **Space:** O(1)

Thus is remembered the saga of **Boats to Save People**,
where the Oracle calms a storm with order and balance,
launching each ferry with careful judgment —
pairing where possible,
separating where necessary —
until every soul crosses the water
and the last boat returns empty beneath a quiet sky. 🚤✨
