## ⚡🏹 _The Longest Streak of Victory: The Maximum Consecutive Ones Saga_

> \*"Across the Plains of Binary Warriors,
> only two kinds marched —
> **Ones**, fierce and triumphant,
> and **Zeros**, cold and unyielding.
>
> The Oracle was asked a simple yet decisive question:
>
> **‘What is the longest unbroken streak
> of victorious Ones?’**
>
> No substitutions were allowed.
> No flips of fate.
>
> The Oracle would simply walk the line,
> counting triumphs until a defeat reset the march."\*

---

This is the saga of **Maximum Consecutive Ones**.

You are given a binary array `nums`
containing only `0` and `1`.

Your task:

-   Find the **maximum number of consecutive `1`s**

No extra space.
One clean march through the array.

---

## 🧠 The Oracle’s Core Insight — Count and Reset

The Oracle realized:

-   Each `1` extends the current streak
-   Each `0` **breaks** the streak
-   The longest streak ever seen is the answer

Thus she needed only:

-   `current` → current streak of ones
-   `best` → maximum streak found so far

---

### 📜 The Scroll of Binary Fields

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Single-March Ritual

_Count victories, reset on defeat_

```cpp
int findMaxConsecutiveOnes(vector<int>& nums) {
    int current = 0;
    int best = 0;
```

The Oracle began with no victories recorded.

---

### 🏃 Walk the Binary Field

```cpp
    for (int x : nums) {
```

Each warrior stepped forward.

---

### 🟢 Victory Extends the Streak

```cpp
        if (x == 1) {
            current++;
            best = max(best, current);
        }
```

Every `1` strengthened the march
and updated the longest streak if needed.

---

### 🔴 Defeat Resets the March

```cpp
        else {
            current = 0;
        }
    }
    return best;
}
```

A `0` shattered the streak.
The Oracle reset and marched on.

---

### 🎺 The Trial of the Binary Plains

```cpp
int main() {
    vector<int> nums = {1, 1, 0, 1, 1, 1};
    cout << findMaxConsecutiveOnes(nums) << endl; // expected: 3
    return 0;
}
```

The Oracle found the greatest streak:

`1 1 1` → **3 consecutive ones**

Victory recorded.

---

### 🧠 Memory of the Streak Law

-   Count consecutive `1`s
-   Reset count on `0`
-   Track maximum streak seen
-   One pass only
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Maximum Consecutive Ones**,
where the Oracle walks the binary plains,
counting uninterrupted victories —
and declaring the longest streak of triumph
written in the march of Ones. ⚡✨
