## 🏹📜 _The Narrowest Path of Power: The Minimum Size Subarray Sum Saga_

> \*"Across the Plains of Positivity,
> numbers marched forward —
> each carrying weight,
> each strictly **greater than zero**.
>
> The Oracle was given a demanding quest:
>
> **‘Find the shortest continuous stretch
> whose total power is **at least** the sacred target.’**
>
> Not the longest.
> Not the first.
>
> The **smallest possible window**.
>
> The Oracle knew the land’s secret:
> because all weights were positive,
> power only grew when she moved forward,
> and only shrank when she stepped back."\*

---

This is the saga of **Minimum Size Subarray Sum**.

You are given:

-   an array `nums` of **positive integers**
-   an integer `target`

Your task:

-   Find the **minimum length** of a contiguous subarray
-   Whose sum is **greater than or equal to `target`**
-   Return `0` if no such subarray exists

---

## 🧠 The Oracle’s Core Insight — Shrink When Strong Enough

Because **all numbers are positive**:

-   Expanding the window → sum **increases**
-   Shrinking the window → sum **decreases**

So the Oracle used a **sliding window**:

-   Expand to reach the target
-   Shrink to minimize length

---

## ⚔️ The Oracle’s Sliding Window Ritual

_Grow for power, shrink for precision_

```cpp
int minSubArrayLen(int target, vector<int>& nums) {
    int left = 0;
    int sum = 0;
    int best = INT_MAX;
```

The Oracle placed her window at the beginning.

---

### 🌊 Expand the Right Boundary

```cpp
    for (int right = 0; right < nums.size(); right++) {
        sum += nums[right];
```

Power accumulated as the window grew.

---

### 🔻 Shrink to Find the Minimum Path

```cpp
        while (sum >= target) {
            best = min(best, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }
```

Once power was sufficient,
the Oracle tightened the window
to find the shortest valid stretch.

---

### 🏁 Return the Answer

```cpp
    return best == INT_MAX ? 0 : best;
}
```

If no window ever reached the target,
the answer was zero.

---

### 🎺 The Trial of the Narrow Path

```cpp
int main() {
    vector<int> nums = {2, 3, 1, 2, 4, 3};
    int target = 7;

    cout << minSubArrayLen(target, nums) << endl; // expected: 2
    return 0;
}
```

The Oracle discovered:

-   Subarray `{4, 3}`
-   Length = **2**

No shorter path carried enough power.

---

### 🧠 Memory of the Narrow Path Law

-   Works only with **positive numbers**
-   Sliding window with two pointers
-   Expand until sum ≥ target
-   Shrink to minimize length
-   Return 0 if no valid subarray exists
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Minimum Size Subarray Sum**,
where the Oracle does not seek vast stretches of power,
but instead hunts for the **shortest possible path** —
tightening her steps with precision,
until the narrowest road
that meets the sacred target
is revealed. 🏹✨
