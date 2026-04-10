## 🏰🔎 _The Twin Sentinels of the Sorted Realm: The Find First and Last Position Saga_

> \_"In the Hall of Sorted Numbers,
> some warriors bore the same banner --
> duplicates standing shoulder to shoulder
> in a continuous block within the ordered line.
>
> The Oracle was summoned with a dual decree:
>
> **'Find the FIRST position where this banner appears,
> and the LAST position where it appears.
> If the banner does not exist -- return [-1, -1].'**
>
> A single binary search could find ONE occurrence --
> but not the boundaries of the block.
>
> The Oracle devised a twin ritual:
>
> **Run binary search twice.
> Once to find the LEFT boundary --
> the first occurrence.
> Once to find the RIGHT boundary --
> the last occurrence.**
>
> Both searches were O(log N).
> Together they revealed the full extent
> of the target's domain."\_

---

This is the saga of **Find First and Last Position of Element in Sorted Array**.

You are given a sorted array `nums` (non-decreasing)
and a target value `target`.

-   Find the **starting** and **ending** position of `target`.
-   If not found, return `[-1, -1]`.
-   Must run in **O(log N)** time.

```
Input:  nums = [5,7,7,8,8,10], target = 8  →  Output: [3, 4]
Input:  nums = [5,7,7,8,8,10], target = 6  →  Output: [-1, -1]
Input:  nums = [], target = 0              →  Output: [-1, -1]
```

---

## 🧠 The Oracle's Core Insight -- Two Searches, Two Boundaries

Standard binary search finds ANY occurrence.
We need the FIRST and the LAST.

The trick: **don't stop** when the target is found.

**Left boundary (first occurrence):**
When `nums[mid] == target` → record `mid`, search LEFT (`right = mid - 1`).
There might be an earlier occurrence.

**Right boundary (last occurrence):**
When `nums[mid] == target` → record `mid`, search RIGHT (`left = mid + 1`).
There might be a later occurrence.

Both use the same `left <= right` framework with answer tracking.

```
Time:  O(log N) -- two binary searches
Space: O(1)
```

---

### 📜 The Scroll of the Twin Boundaries

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Left Sentinel -- Find the First Occurrence

```cpp
int findLeft(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    int result = -1;
```

`result` started at `-1` -- assuming the target did not exist.

---

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            result = mid;
            right = mid - 1;
        }
```

Found the target at `mid` -- but did not stop.
Recorded `mid` and pushed `right` leftward,
searching for an even earlier occurrence.

> _"The left sentinel never settles for the first sighting.
> She always asks: is there one even further left?"_

---

```cpp
        else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return result;
}
```

When the loop ended, `result` held the leftmost index -- or `-1`.

---

## ⚔️ The Right Sentinel -- Find the Last Occurrence

```cpp
int findRight(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    int result = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            result = mid;
            left = mid + 1;
        }
```

Mirror image -- found the target, recorded it,
but pushed `left` rightward to find a later occurrence.

---

```cpp
        else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return result;
}
```

When the loop ended, `result` held the rightmost index -- or `-1`.

---

## 🏰 The Twin Sentinels Unite

```cpp
vector<int> searchRange(vector<int>& nums, int target) {
    return {findLeft(nums, target), findRight(nums, target)};
}
```

Two searches. Two boundaries. One answer.
If the target didn't exist, both returned `-1`.

---

### 🎺 The Trial of the Twin Boundaries

```cpp
int main() {
    vector<int> nums1 = {5, 7, 7, 8, 8, 10};
    auto ans1 = searchRange(nums1, 8);
    cout << "[" << ans1[0] << ", " << ans1[1] << "]" << endl; // [3, 4]

    auto ans2 = searchRange(nums1, 6);
    cout << "[" << ans2[0] << ", " << ans2[1] << "]" << endl; // [-1, -1]

    vector<int> nums3 = {};
    auto ans3 = searchRange(nums3, 0);
    cout << "[" << ans3[0] << ", " << ans3[1] << "]" << endl; // [-1, -1]

    return 0;
}
```

---

**Full trace for `[5, 7, 7, 8, 8, 10]`, target = 8:**

**findLeft (first occurrence):**

| Step | left | right | mid | nums[mid] | Action                    | result |
| ---- | ---- | ----- | --- | --------- | ------------------------- | ------ |
| 1    | 0    | 5     | 2   | 7         | 7 < 8 → left = 3         | -1     |
| 2    | 3    | 5     | 4   | 8         | Found! result=4, right=3  | 4      |
| 3    | 3    | 3     | 3   | 8         | Found! result=3, right=2  | 3      |
| 4    | 3    | 2     | --  | --        | left > right → exit       | **3**  |

**findRight (last occurrence):**

| Step | left | right | mid | nums[mid] | Action                    | result |
| ---- | ---- | ----- | --- | --------- | ------------------------- | ------ |
| 1    | 0    | 5     | 2   | 7         | 7 < 8 → left = 3         | -1     |
| 2    | 3    | 5     | 4   | 8         | Found! result=4, left=5   | 4      |
| 3    | 5    | 5     | 5   | 10        | 10 > 8 → right = 4       | 4      |
| 4    | 5    | 4     | --  | --        | left > right → exit       | **4**  |

**Answer: [3, 4]** ✓

---

**Full trace for `[5, 7, 7, 8, 8, 10]`, target = 6:**

**findLeft:**

| Step | left | right | mid | nums[mid] | Action                    | result |
| ---- | ---- | ----- | --- | --------- | ------------------------- | ------ |
| 1    | 0    | 5     | 2   | 7         | 7 > 6 → right = 1        | -1     |
| 2    | 0    | 1     | 0   | 5         | 5 < 6 → left = 1         | -1     |
| 3    | 1    | 1     | 1   | 7         | 7 > 6 → right = 0        | -1     |
| 4    | 1    | 0     | --  | --        | left > right → exit       | **-1** |

**findRight:** same flow → **-1**.

**Answer: [-1, -1]** ✓

---

**Trace for `[1]`, target = 1:**

findLeft: mid=0, found → result=0, right=-1 → exit. **0**
findRight: mid=0, found → result=0, left=1 → exit. **0**

**Answer: [0, 0]** ✓ — single element, both boundaries same.

---

**Trace for `[8,8,8,8,8]`, target = 8:**

findLeft: keeps pushing right leftward → result lands at **0**.
findRight: keeps pushing left rightward → result lands at **4**.

**Answer: [0, 4]** ✓ — entire array is the target.

---

## ⚠️ Why Not Find One and Expand?

A tempting approach:

```
Find any occurrence → expand left and right linearly.
```

This is O(N) worst case -- if all elements are the target,
expansion walks the entire array. The twin binary search
is O(log N) guaranteed, even when the target fills everything.

---

### 🧠 Memory of the Twin Sentinel Law

-   **Two binary searches** -- one for left boundary, one for right
-   **Left search:** `nums[mid] == target` → record `mid`, push `right = mid - 1`
-   **Right search:** `nums[mid] == target` → record `mid`, push `left = mid + 1`
-   **Do NOT stop** when target is found -- keep narrowing toward the boundary
-   Both use `result = -1` as default (not found)
-   Equivalent to `lower_bound` (left) and `upper_bound - 1` (right)
-   **Never expand linearly** from a found occurrence -- O(N) worst case
-   **Time:** O(log N) -- two binary searches
-   **Space:** O(1)
-   **Edge cases:**
    -   Empty array → [-1, -1]
    -   Single element match → [i, i]
    -   All elements are target → [0, n-1]
    -   Target not present → [-1, -1]

Thus is remembered the saga of **Find First and Last Position**,
where the Oracle sent two sentinels into the sorted hall --
one marching left to find the earliest banner,
one marching right to find the latest --
both refusing to stop at the first sighting,
always pushing deeper toward the true boundary --
until the full domain of the target was revealed
in two swift passes of logarithmic time. 🏰🔎✨
