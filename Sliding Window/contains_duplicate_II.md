## 🕰️⚔️ _The Watchers of Near Echoes: The Contains Duplicate II Saga_

> \*"In the Timeline of Numbers,
> values appeared again and again —
> echoes of the past returning in the present.
>
> But not every echo was dangerous.
>
> The Oracle was given a precise warning:
>
> **‘If the same number appears twice
> within **k steps of time**,
> sound the alarm.’**
>
> Distant echoes were harmless.
> Only **near repeats** threatened the balance.
>
> The Oracle could not remember all history —
> only the **recent past** mattered.
>
> Thus she carried with her
> a **sliding window of memory**,
> forgetting what lay too far behind."\*

---

This is the saga of **Contains Duplicate II**.

You are given:

-   an integer array `nums`
-   an integer `k`

Your task:

-   Return `true` if there exist **two equal values**
-   Such that the **absolute difference of their indices ≤ k**
-   Otherwise, return `false`

---

## 🧠 The Oracle’s Core Insight — Only Recent History Matters

The Oracle realized:

-   A duplicate is dangerous **only if it appears nearby**
-   Any value older than `k` steps ago can be forgotten
-   A **sliding window of size k** is enough

So she used:

-   A **hash set** to remember the last `k` elements
-   A moving window that grows and shrinks with time

---

### 📜 The Scroll of Passing Moments

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## ⚔️ The Oracle’s Sliding Memory Ritual

_Remember the last k steps only_

```cpp
bool containsNearbyDuplicate(vector<int>& nums, int k) {
    unordered_set<int> window;
```

The Oracle prepared a memory window
that would hold at most `k` recent values.

---

### 🕰️ Walk the Timeline

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

Each step forward represented time passing.

---

### 🚨 Detect a Near Echo

```cpp
        if (window.count(nums[i])) {
            return true;
        }
```

If the value already existed in memory,
a nearby duplicate had been found.

---

### 🧠 Record the Current Moment

```cpp
        window.insert(nums[i]);
```

The Oracle remembered the current value.

---

### 🧹 Forget the Distant Past

```cpp
        if (window.size() > k) {
            window.erase(nums[i - k]);
        }
    }
    return false;
}
```

Anything older than `k` steps
was erased from memory.

---

### 🎺 The Trial of Near Echoes

```cpp
int main() {
    vector<int> nums = {1, 2, 3, 1};
    int k = 3;

    cout << containsNearbyDuplicate(nums, k) << endl; // true
    return 0;
}
```

The value `1` returned within `3` steps.
The alarm was sounded.

---

### 🧠 Memory of the Near-Echo Law

-   Only duplicates within distance `k` matter
-   Use a hash set as a sliding window
-   Insert current value
-   Remove value that exits the window
-   **Time:** O(n)
-   **Space:** O(min(n, k))

Thus is remembered the saga of **Contains Duplicate II**,
where the Oracle guards the timeline not by remembering everything,
but by **remembering just enough** —
watching for echoes that return too soon,
and ignoring those lost to distant history. 🕰️✨
