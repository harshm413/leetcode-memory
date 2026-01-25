## 🏔️⚔️ _The Peaks of the Moving Horizon: The Sliding Window Maximum Saga_

> \*"Across the Plains of Numbers,
> a window moved steadily forward —
> never stopping, never looking back.
>
> At each position,
> the Oracle was commanded:
>
> **‘Tell us the strongest peak
> visible inside the window.’**
>
> Searching again and again would waste time.
> Forgetting the past entirely would lose strength.
>
> The Oracle needed memory —
> but only of what truly mattered.
>
> Thus she summoned a **Monotonic Deque**,
> where only worthy peaks could remain."\*

---

This is the saga of **Sliding Window Maximum**.

You are given:

-   an integer array `nums`
-   an integer `k` (window size)

Your task:

-   Return an array of the **maximum value in every sliding window**
-   Each window moves one step to the right

---

## 🧠 The Oracle’s Core Insight — Only Peaks Matter

The Oracle realized:

-   Any number **smaller than a future number**
    can never be the maximum again
-   Keep candidates in **decreasing order**
-   The front of the structure
    always holds the current window’s maximum

Thus was born the **monotonic decreasing deque**.

---

### 📜 The Scroll of Peaks

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;
```

---

## ⚔️ The Oracle’s Monotonic Deque Ritual

_Maintain decreasing order of values’ indices_

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;
    vector<int> result;
```

The deque stored **indices**,
not values —
so the Oracle could know when peaks expired.

---

### 🌊 March Through the Array

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

Each step moved the window forward.

---

### 🚪 Remove Outdated Peaks

```cpp
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
```

If a peak lay outside the window,
it was dismissed.

---

### 🔻 Remove Weaker Peaks

```cpp
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
```

Any smaller number behind the current one
was unworthy of remaining.

---

### 🏔️ Add the Current Peak

```cpp
        dq.push_back(i);
```

The new peak joined the horizon.

---

### 📣 Record the Window’s Maximum

```cpp
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    return result;
}
```

Once the first full window formed,
the Oracle recorded the strongest peak.

---

### 🎺 The Trial of the Moving Horizon

```cpp
int main() {
    vector<int> nums = {1,3,-1,-3,5,3,6,7};
    int k = 3;

    vector<int> ans = maxSlidingWindow(nums, k);
    for (int x : ans) cout << x << " ";
    cout << endl; // expected: 3 3 5 5 6 7
    return 0;
}
```

Each window revealed its greatest height
without re-searching the land.

---

### 🧠 Memory of the Horizon Law

-   Use a **monotonic decreasing deque**
-   Store indices, not values
-   Remove out-of-window elements
-   Remove smaller elements from the back
-   Front always holds the maximum
-   **Time:** O(n)
-   **Space:** O(k)

Thus is remembered the saga of
**Sliding Window Maximum**,
where the Oracle walks a moving horizon,
carrying only the strongest peaks in memory —
and with disciplined order,
reveals the maximum at every step
without ever turning back. 🏔️✨
