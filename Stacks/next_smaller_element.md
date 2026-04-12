## 🕳️⚔️ _The Prophecy of the Weaker Successor: The Next Smaller Element Saga_

> \_"In the Line of Numbers,
> each warrior stood facing right --
> not searching for someone stronger this time,
> but dreading the arrival of someone WEAKER.
>
> The Oracle was commanded:
>
> **'For every warrior,
> reveal the next SMALLER element to the right.
> If none exists -- mark their fate as -1.'**
>
> The Oracle smiled.
>
> This was the mirror image of the Next Greater Element.
> Where NGE used a monotonic DECREASING stack,
> NSE used a monotonic INCREASING stack.
>
> The stack held warriors in increasing order.
> When a smaller element arrived,
> it shattered the order --
> resolving every larger warrior on the stack
> whose downfall had been foretold.
>
> Same pattern. Flipped comparison.
> One pass. One stack.
> Every prophecy of weakness fulfilled or forever marked -1."\_

---

This is the saga of **Next Smaller Element** --
the mirror of Next Greater Element.

Given an array `nums`:

-   For each element, find the **next smaller element to its right**.
-   If no smaller element exists → return `-1`.

```
Input:  [4, 5, 2, 10, 8]
Output: [2, 2, -1, 8, -1]

Input:  [3, 1, 2]
Output: [1, -1, -1]

Input:  [1, 2, 3, 4, 5]
Output: [-1, -1, -1, -1, -1]
```

---

## 🧠 The Oracle's Core Insight -- Monotonic Increasing Stack

For Next Greater Element: monotonic **decreasing** stack.
For Next Smaller Element: monotonic **increasing** stack.

The stack holds indices in **increasing** order of value (bottom to top).

When `nums[i]` arrives and is **smaller** than the stack's top:

```
While stack is not empty AND nums[i] < nums[stack.top()]:
  result[stack.top()] = nums[i]   (the next smaller is found)
  Pop the top                      (resolved)

Push i onto the stack              (now waiting for its own smaller)
```

Elements left on the stack at the end have no smaller successor.
Their answer stays `-1`.

**Right-to-left alternative:**
Walk right to left, pop everything **>=** current,
the surviving top is the next smaller element.

```
Time:  O(N) -- each index pushed once, popped at most once
Space: O(N)
```

---

### 📜 The Scroll of the Falling Line

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ Approach One -- Left to Right (Resolve on Arrival)

```cpp
vector<int> nextSmallerElement_LR(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[i] < nums[st.top()]) {
            result[st.top()] = nums[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```

Walk left to right. The stack holds indices in increasing value order.

When a smaller element arrives, it resolves every larger element
on the stack -- they've found their next smaller successor.

> _"A single weak arrival
> can seal the fate of many strong warriors at once.
> The stack unwinds until it meets someone even smaller --
> or empties entirely."_

---

## ⚔️ Approach Two -- Right to Left (Read from Stack)

```cpp
vector<int> nextSmallerElement_RL(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && st.top() >= nums[i]) {
            st.pop();
        }
        result[i] = st.empty() ? -1 : st.top();
        st.push(nums[i]);
    }
    return result;
}
```

Walk right to left. The stack stores **values** (not indices).

At each position, pop everything **>=** current.
The surviving top is the next smaller element.
If stack empty → no smaller exists, answer = -1.

Push the current value for future elements to read.

> _"Pop everything that is not strictly smaller.
> Whatever survives on top is the nearest smaller successor."_

---

### 🎺 The Trial of the Falling Line

```cpp
int main() {
    vector<int> nums1 = {4, 5, 2, 10, 8};
    auto r1 = nextSmallerElement_LR(nums1);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 2 2 -1 8 -1

    auto r2 = nextSmallerElement_RL(nums1);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 2 2 -1 8 -1

    vector<int> nums2 = {3, 1, 2};
    auto r3 = nextSmallerElement_LR(nums2);
    for (int x : r3) cout << x << " "; cout << endl;
    // expected: 1 -1 -1

    return 0;
}
```

---

**Full trace (Left to Right) for `[4, 5, 2, 10, 8]`:**

| i | nums[i] | Stack before (indices) | Pops & resolutions                      | Stack after |
|---|---------|------------------------|-----------------------------------------|-------------|
| 0 | 4       | []                     | --                                      | [0]         |
| 1 | 5       | [0]                    | 5 > 4 → no pop (not smaller)           | [0, 1]      |
| 2 | 2       | [0, 1]                | 2 < nums[1]=5 → result[1]=2, pop 1     |             |
|   |         | [0]                    | 2 < nums[0]=4 → result[0]=2, pop 0     | [2]         |
| 3 | 10      | [2]                   | 10 > 2 → no pop                        | [2, 3]      |
| 4 | 8       | [2, 3]                | 8 < nums[3]=10 → result[3]=8, pop 3    |             |
|   |         | [2]                    | 8 > nums[2]=2 → stop                   | [2, 4]      |

Remaining: [2, 4] → result[2]=-1, result[4]=-1 (default).

**Answer: [2, 2, -1, 8, -1]** ✓

At i=2: element `2` resolved both index 1 (value 5) and index 0 (value 4).
At i=4: element `8` resolved index 3 (value 10) but stopped at index 2 (value 2).

---

**Full trace (Right to Left) for `[4, 5, 2, 10, 8]`:**

| i | nums[i] | Stack before (values) | Pops           | NSE          | Stack after |
|---|---------|----------------------|----------------|--------------|-------------|
| 4 | 8       | []                   | --             | -1           | [8]         |
| 3 | 10      | [8]                  | --             | 8            | [10, 8]     |

Wait -- stack stores values, and 10 > 8, so we push 10 on top of 8.
But the stack should be increasing (smallest on top for NSE)...

Actually for right-to-left NSE: we pop everything `>=` current.
The stack naturally holds values in increasing order from top to bottom?
No -- let me re-trace carefully:

| i | nums[i] | Stack before | Pop while top >= nums[i] | NSE         | Push  | Stack after |
|---|---------|-------------|--------------------------|-------------|-------|-------------|
| 4 | 8       | []          | --                       | -1          | 8     | [8]         |
| 3 | 10      | [8]         | 8 >= 10? No              | 8           | 10    | [10, 8]     |
| 2 | 2       | [10, 8]     | 10>=2? Yes pop. 8>=2? Yes pop. | -1   | 2     | [2]         |
| 1 | 5       | [2]         | 2>=5? No                 | 2           | 5     | [5, 2]      |
| 0 | 4       | [5, 2]      | 5>=4? Yes pop. 2>=4? No  | 2           | 4     | [4, 2]      |

**Answer: [2, 2, -1, 8, -1]** ✓

Both approaches give the same result.

---

**Trace for `[3, 1, 2]`:**

Left to right:

| i | nums[i] | Pops                          | Stack after |
|---|---------|-------------------------------|-------------|
| 0 | 3       | --                            | [0]         |
| 1 | 1       | 1 < 3 → result[0]=1, pop 0   | [1]         |
| 2 | 2       | 2 > 1 → no pop               | [1, 2]      |

**Answer: [1, -1, -1]** ✓

---

**Trace for `[1, 2, 3, 4, 5]` (strictly increasing):**

No element is ever smaller than the previous.
The stack grows monotonically. No pops ever happen.

**Answer: [-1, -1, -1, -1, -1]** ✓

---

**Trace for `[5, 4, 3, 2, 1]` (strictly decreasing):**

Left to right:

| i | nums[i] | Pops                                    | Stack after |
|---|---------|----------------------------------------|-------------|
| 0 | 5       | --                                      | [0]         |
| 1 | 4       | 4<5 → result[0]=4, pop 0               | [1]         |
| 2 | 3       | 3<4 → result[1]=3, pop 1               | [2]         |
| 3 | 2       | 2<3 → result[2]=2, pop 2               | [3]         |
| 4 | 1       | 1<2 → result[3]=1, pop 3               | [4]         |

**Answer: [4, 3, 2, 1, -1]** ✓

Every element is immediately resolved by its successor.

---

## 🔄 NGE vs NSE -- The Mirror

| Next Greater Element (NGE)        | Next Smaller Element (NSE)        |
| --------------------------------- | --------------------------------- |
| Monotonic **decreasing** stack    | Monotonic **increasing** stack    |
| Pop when `nums[i] > st.top()`    | Pop when `nums[i] < st.top()`    |
| R-to-L: pop while `top <= nums[i]` | R-to-L: pop while `top >= nums[i]` |
| Finds the next **larger** value   | Finds the next **smaller** value  |

The structure is identical. Only the comparison flips.

---

## 🌐 The Four Monotonic Stack Variants

| Variant                    | Stack Order | Pop Condition (L→R)     | Use Case                    |
| -------------------------- | ----------- | ----------------------- | --------------------------- |
| Next Greater Element       | Decreasing  | `nums[i] > top`         | Daily Temperatures, NGE I/II|
| Next Smaller Element       | Increasing  | `nums[i] < top`         | Histogram, Sum of Subarray Mins |
| Previous Greater Element   | Decreasing  | `nums[i] >= top`        | Stock Span, Sum of Subarray Maxs |
| Previous Smaller Element   | Increasing  | `nums[i] >= top`        | Histogram (left boundary)   |

Master all four and every monotonic stack problem becomes a lookup.

---

### 🧠 Memory of the Weaker Successor Law

-   **Monotonic increasing stack** (values increase bottom→top)
-   **Left-to-right:** when `nums[i] < nums[st.top()]` → resolve top, pop
-   **Right-to-left:** pop while `st.top() >= nums[i]`, read surviving top
-   **Default:** `-1` for elements with no smaller successor
-   Mirror of Next Greater Element -- same structure, flipped comparison
-   Each index pushed once, popped at most once → O(N)
-   **Time:** O(N)
-   **Space:** O(N)
-   **Edge cases:**
    -   Strictly increasing → no pops, all -1
    -   Strictly decreasing → each resolved by immediate successor
    -   All equal → no element is strictly smaller, all -1

Thus is remembered the saga of **Next Smaller Element**,
where the Oracle walked the line with a monotonic increasing stack --
the mirror of the greater prophecy --
resolving every warrior whose downfall was foretold
the moment a weaker successor appeared --
until the line was fully walked
and only the unanswered remained,
their strength unchallenged to the end. 🕳️⚔️✨
