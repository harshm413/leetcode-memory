## 🌊📏 _The Golden Stretch of the River: The Longest Subarray with Sum K (Positives) Saga_

> \*"In the Land of Positive Currents,
> a river of numbers flowed only forward —
> every value **greater than zero**,
> every step adding weight to the stream.
>
> The Oracle was summoned with a precise command:
>
> **‘Find the longest continuous stretch of this river
> whose total flow equals exactly K.’**
>
> There were no backward currents here,
> no negative tides to confuse the waters.
>
> And so the Oracle chose a powerful technique —
> not memory, not ledgers,
> but the art of the **Sliding Window**,
> where the riverbanks expand and shrink
> until balance is found."\*

---

This is the saga of
**Longest Subarray with Given Sum K (Positives Only)**.

You are given:

-   an array `nums` containing **only positive integers**
-   an integer target `k`

Your task:

-   Find the **length of the longest subarray**
-   whose sum is **exactly `k`**

---

## 🧠 The Oracle’s Core Insight — Positive Rivers Never Decrease

Because **all numbers are positive**:

-   Expanding the window → sum **increases**
-   Shrinking the window → sum **decreases**

This creates a perfect condition for the **Two-Pointer Sliding Window**.

No backtracking.
No confusion.
Just controlled expansion and contraction.

---

## 🪟 The Sliding Window Law

Maintain:

-   `left` → start of window
-   `right` → end of window
-   `sum` → sum of current window

Rules:

-   If `sum < k` → expand right
-   If `sum > k` → shrink left
-   If `sum == k` → record window length

---

### 📜 The Scroll of Flowing Positives

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Sliding Window Ritual

_Expand to grow power, shrink to restore balance_

```cpp
int longestSubarrayWithSumK(vector<int>& nums, int k) {
    int left = 0;
    int sum = 0;
    int maxLen = 0;
```

The Oracle placed the window at the river’s start.

---

### 🌊 Walk the River with the Right Bank

```cpp
    for (int right = 0; right < nums.size(); right++) {
        sum += nums[right];
```

The river expanded as the right bank moved forward.

---

### 🔻 Shrink While the River Overflows

```cpp
        while (sum > k && left <= right) {
            sum -= nums[left];
            left++;
        }
```

If the flow exceeded `k`,
the Oracle tightened the window from the left.

---

### ✨ Record the Golden Stretch

```cpp
        if (sum == k) {
            maxLen = max(maxLen, right - left + 1);
        }
    }
    return maxLen;
}
```

Whenever balance was achieved,
the Oracle measured the window
and preserved the longest one.

---

### 🎺 The Trial of the Golden River

```cpp
int main() {
    vector<int> nums = {1, 2, 3, 1, 1, 1, 1, 4, 2, 3};
    int k = 3;

    cout << longestSubarrayWithSumK(nums, k) << endl; // expected: 3
    return 0;
}
```

The Oracle discovered:

-   The stretch `{1, 1, 1}`
-   Length = **3**

No longer balanced stretch existed.

---

### 🧠 Memory of the Golden Stretch Law

-   Works **only** when all numbers are positive
-   Sliding window with two pointers
-   Expand when sum < k
-   Shrink when sum > k
-   Record when sum == k
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Longest Subarray with Given Sum K (Positives)**,
where the Oracle walks a river that only flows forward,
carefully adjusting its banks —
until the longest perfectly balanced stretch
is revealed in golden harmony. 🌊✨
