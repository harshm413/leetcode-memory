## 🔥🖨️ _Recovering the Burning Path: The Print Maximum Subarray Saga_

> \_"The Oracle had found the maximum subarray SUM.
> But the King demanded more:
>
> **'Don't just tell me the sum.
> SHOW me the actual subarray.
> Which elements? From where to where?'**
>
> The Oracle needed to track WHERE the best subarray
> started and ended — not just its sum.
>
> She added two pieces of information to Kadane's:
>
> **`start`** — where the current subarray begins.
> Every time sum resets to 0, the next element
> becomes the new starting point.
>
> **`ansStart` and `ansEnd`** — the boundaries of the best subarray.
> Every time maxi is updated, she records
> the current start and the current index as the answer.
>
> Same Kadane's algorithm. Three extra variables."\_

---

This is the saga of **Print Maximum Subarray Sum**.

Given an integer array `nums`:
-   Find the maximum subarray sum AND print the actual subarray.

```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: Sum = 6, Subarray = [4, -1, 2, 1] (indices 3 to 6)

Input:  [5, 4, -1, 7, 8]
Output: Sum = 23, Subarray = [5, 4, -1, 7, 8] (indices 0 to 4)

Input:  [-3, -2, -5]
Output: Sum = -2, Subarray = [-2] (index 1)
```

---

## 🧠 The Tracking Insight

In standard Kadane's, we reset `sum = 0` when it goes negative.
That reset is where a NEW subarray begins.

So the start of the current subarray = the index RIGHT AFTER the last reset.

We track this with a variable `start`.
Every time `sum` resets, `start` moves to `i + 1` (the next element).

When `sum > maxi` (new best found), we snapshot:
-   `ansStart = start` (where this best subarray began)
-   `ansEnd = i` (where it ends — the current index)

---

### 📜 The Scroll of the Recovered Burning Path

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 🖨️ Kadane's with Index Tracking

### Initialize

```cpp
void printMaxSubarray(vector<int>& nums) {
    long long maxi = LONG_MIN;
    long long sum = 0;
```

Same as standard Kadane's.

---

### Track the boundaries

```cpp
    int start = 0;
    int ansStart = -1, ansEnd = -1;
```

`start` = where the CURRENT subarray begins.
`ansStart` and `ansEnd` = boundaries of the BEST subarray found so far.
Initialized to -1 — no answer found yet.

---

### Walk the array

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

---

### Mark the start of a new subarray

```cpp
        if (sum == 0) start = i;
```

If `sum` is 0, we're starting a fresh subarray.
This element is the potential beginning.

This line comes BEFORE adding `nums[i]`. Why?
Because `sum == 0` means either we just reset (previous sum was negative)
or we're at the very beginning. Either way, `i` is where the new subarray starts.

> _"The Oracle checks: 'Am I starting fresh?'
> If sum is zero — this stone is the new beginning.
> She plants her flag HERE, before stepping onto the stone."_

---

### Add current element

```cpp
        sum += nums[i];
```

Extend the current subarray by including `nums[i]`.

---

### New best found — snapshot the boundaries

```cpp
        if (sum > maxi) {
            maxi = sum;
            ansStart = start;
            ansEnd = i;
        }
```

This running sum is the best we've ever seen.
Record WHERE this subarray starts and ends.

`start` was set when the current subarray began (when sum was 0).
`i` is where it ends — the current index.

> _"The Oracle finds a brighter moment.
> She marks the boundaries — 'This is the one.
> From start to here. Remember it.'"_

---

### Sum goes negative — reset

```cpp
        if (sum < 0) {
            sum = 0;
        }
    }
```

The current subarray is a net loss. Drop it.
Reset sum to 0. The NEXT iteration will detect `sum == 0`
and set `start` to the new beginning automatically.

> _"The path has turned negative. The Oracle drops it.
> Sum returns to zero. Next iteration,
> the zero-check will plant a new flag."_

---

### Print the result

```cpp
    cout << "Sum: " << maxi << endl;
    cout << "Subarray: [";
    for (int i = ansStart; i <= ansEnd; i++) {
        cout << nums[i];
        if (i < ansEnd) cout << ", ";
    }
    cout << "]" << endl;
    cout << "Indices: " << ansStart << " to " << ansEnd << endl;
}
```

---

### 🎺 The Trial of the Recovered Burning Path

```cpp
int main() {
    vector<int> n1 = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    printMaxSubarray(n1);
    // Sum: 6, Subarray: [4, -1, 2, 1], Indices: 3 to 6

    vector<int> n2 = {5, 4, -1, 7, 8};
    printMaxSubarray(n2);
    // Sum: 23, Subarray: [5, 4, -1, 7, 8], Indices: 0 to 4

    vector<int> n3 = {-3, -2, -5};
    printMaxSubarray(n3);
    // Sum: -2, Subarray: [-2], Indices: 1 to 1

    return 0;
}
```

---

**Full trace for `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`:**

| i | nums[i] | sum==0? | start | sum | sum>maxi? | maxi | ansStart | ansEnd | Reset? |
|---|---------|---------|-------|-----|-----------|------|----------|--------|--------|
| 0 | -2      | Yes→s=0 | 0 | -2  | Yes (-2>MIN) | -2 | 0 | 0 | Yes→sum=0 |
| 1 | 1       | Yes→s=1 | 1 | 1   | Yes (1>-2) | 1  | 1 | 1 | No |
| 2 | -3      | No      | 1 | -2  | No        | 1   | 1 | 1 | Yes→sum=0 |
| 3 | 4       | Yes→s=3 | 3 | 4   | Yes (4>1) | 4   | **3** | **3** | No |
| 4 | -1      | No      | 3 | 3   | No        | 4   | 3 | 3 | No |
| 5 | 2       | No      | 3 | 5   | Yes (5>4) | 5   | **3** | **5** | No |
| 6 | 1       | No      | 3 | 6   | Yes (6>5) | **6** | **3** | **6** | No |
| 7 | -5      | No      | 3 | 1   | No        | 6   | 3 | 6 | No |
| 8 | 4       | No      | 3 | 5   | No        | 6   | 3 | 6 | No |

**Answer: Sum = 6, Subarray = nums[3..6] = [4, -1, 2, 1]** ✓

Key moments:
-   Index 0: `sum == 0` → start = 0. Add -2. sum = -2, new best. Reset.
-   Index 1: `sum == 0` → start = 1. Add 1. sum = 1, new best.
-   Index 2: Add -3. sum = -2, negative → reset.
-   Index 3: `sum == 0` → start = 3. Add 4. sum = 4, new best. ansStart=3, ansEnd=3.
-   Index 6: sum = 6, new best. ansStart=3, ansEnd=6. Final answer.

---

**Trace for `[-3, -2, -5]`:**

| i | nums[i] | sum==0? | start | sum | maxi | ansStart | ansEnd | Reset? |
|---|---------|---------|-------|-----|------|----------|--------|--------|
| 0 | -3      | Yes→s=0 | 0 | -3  | -3   | 0        | 0      | Yes→sum=0 |
| 1 | -2      | Yes→s=1 | 1 | -2  | **-2** | **1**  | **1**  | Yes→sum=0 |
| 2 | -5      | Yes→s=2 | 2 | -5  | -2   | 1        | 1      | Yes→sum=0 |

**Answer: Sum = -2, Subarray = nums[1..1] = [-2]** ✓

Every element resets. But maxi captures each one before the reset.
The least negative (-2 at index 1) wins.

---

## 🔍 What Changed from Standard Kadane's

| Standard Kadane's | Print Version (this) |
|-------------------|---------------------|
| `maxi`, `sum` | + `start`, `ansStart`, `ansEnd` |
| — | `if (sum == 0) start = i` before adding |
| Reset: `sum = 0` | Same (start auto-updates next iteration) |
| Update: `maxi = sum` | + `ansStart = start`, `ansEnd = i` |
| Returns sum only | Returns sum + boundaries |

Three extra variables. Two extra assignments. Same O(N) time, O(1) space.

---

## 🔍 Why `if (sum == 0) start = i` at the Top?

When sum is 0, we're either at the very beginning or just after a reset.
Either way, the current element is the start of a new subarray.

By checking BEFORE adding `nums[i]`, we mark `i` as the start
before the element is included. This is cleaner than setting
`start = i + 1` inside the reset block — same effect, fewer edge cases.

---

## 🔍 Multiple Subarrays with Same Max Sum

If multiple subarrays have the same maximum sum,
this code returns the FIRST one found (leftmost).

Why? Because `sum > maxi` uses strict `>`.
If we used `>=`, we'd get the LAST one (rightmost).

---

### 🧠 Memory of the Recovered Burning Path Law

-   **Same Kadane's** + three extra variables: `start`, `ansStart`, `ansEnd`
-   **Before adding:** `if (sum == 0) start = i` — mark new subarray start
-   **On new best (`sum > maxi`):** `ansStart = start`, `ansEnd = i`
-   **On reset (`sum < 0`):** `sum = 0` — next iteration's zero-check sets start
-   **All-negative arrays:** each element is its own subarray, least negative wins
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Print Maximum Subarray**,
where the Oracle not only found the brightest sum
but marked the boundaries of the burning path —
planting a flag at every fresh start,
snapshotting the boundaries at every new peak —
until the exact subarray was known:
where it began, where it ended,
and every element within. 🔥🖨️✨
