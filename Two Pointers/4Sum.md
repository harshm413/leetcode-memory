## 🎯🎯🎯🎯 _The Four Warriors: The 4Sum Saga_

> \_"The Oracle had mastered 3Sum.
>
> Now the King demanded:
>
> **'Find ALL unique quadruplets that sum to a given target.'**
>
> The Oracle smiled. Same pattern, one more layer:
>
> **Fix TWO elements. Two-pointer the rest.**
>
> 3Sum: fix 1, two-pointer 2. O(N²).
> 4Sum: fix 2, two-pointer 2. O(N³).
>
> The duplicate skipping now had FOUR levels —
> outer `i`, inner `j`, and both `left` and `right`.
>
> And one new danger: **integer overflow.**
> Four numbers summed together can exceed `int` range.
> The Oracle used `long long` for the sum."\_

---

This is the saga of **4Sum (LeetCode 18)**.

Given an integer array `nums` and a `target`:
-   Find all unique quadruplets `[a, b, c, d]` where `a + b + c + d = target`.
-   No duplicate quadruplets.

```
Input:  nums = [1,0,-1,0,-2,2], target = 0
Output: [[-2,-1,1,2], [-2,0,0,2], [-1,0,0,1]]

Input:  nums = [2,2,2,2,2], target = 8
Output: [[2,2,2,2]]
```

---

## 🧠 The Pattern — From 2Sum to 4Sum

```
2Sum: two pointers.                    O(N)
3Sum: fix 1 + two pointers.            O(N²)
4Sum: fix 2 + two pointers.            O(N³)
KSum: fix K-2 + two pointers.          O(N^(K-1))
```

Each additional element adds one outer loop.
The innermost pair is always solved with two pointers.

---

### 📜 The Scroll of the Four Warriors

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🎯 Sort the Array

```cpp
vector<vector<int>> fourSum(vector<int>& nums, int target) {
    vector<vector<int>> result;
    sort(nums.begin(), nums.end());
    int n = nums.size();
```

---

## 🎯 First Fixed Element — Outer Loop i

```cpp
    for (int i = 0; i < n - 3; i++) {
```

Need at least 3 elements after `i` for a quadruplet.

---

### Skip duplicate i

```cpp
        if (i > 0 && nums[i] == nums[i - 1]) continue;
```

Same value as previous `i` → all its quadruplets already found. Skip.

---

## 🎯 Second Fixed Element — Inner Loop j

```cpp
        for (int j = i + 1; j < n - 2; j++) {
```

`j` starts after `i`. Need at least 2 elements after `j`.

---

### Skip duplicate j

```cpp
            if (j > i + 1 && nums[j] == nums[j - 1]) continue;
```

Same value as previous `j` → skip.

Why `j > i + 1` and not `j > 0`? Because the first `j` after each `i`
should NOT be skipped — it's the first time this `(i, j)` pair appears.
Only skip when `j` has moved past its starting position for this `i`.

> _"The second warrior checks: 'Am I a clone of the one before me
> in THIS round?' Only then does he step aside."_

---

## 🎯 Two Pointers for the Remaining Pair

```cpp
            int left = j + 1, right = n - 1;
```

---

```cpp
            while (left < right) {
                long long sum = (long long)nums[i] + nums[j] + nums[left] + nums[right];
```

**`long long` cast is critical.** Four integers can overflow `int`.
Example: `nums = [1000000000, 1000000000, 1000000000, 1000000000]`.
Sum = 4 billion, which exceeds `INT_MAX` (≈2.1 billion).

Casting the FIRST operand to `long long` promotes the entire expression.

---

### Sum too small

```cpp
                if (sum < target) {
                    left++;
                }
```

---

### Sum too big

```cpp
                else if (sum > target) {
                    right--;
                }
```

---

### Found a quadruplet

```cpp
                else {
                    result.push_back({nums[i], nums[j], nums[left], nums[right]});
```

---

### Skip duplicate left and right

```cpp
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
```

Move past all duplicates of the current left and right values.

---

### Advance both pointers

```cpp
                    left++;
                    right--;
                }
            }
        }
    }
    return result;
}
```

---

### 🎺 The Trial of the Four Warriors

```cpp
int main() {
    vector<int> n1 = {1, 0, -1, 0, -2, 2};
    auto r1 = fourSum(n1, 0);
    for (auto& q : r1) {
        cout << "[";
        for (int k = 0; k < 4; k++) cout << q[k] << (k < 3 ? "," : "");
        cout << "] ";
    }
    cout << endl;
    // expected: [-2,-1,1,2] [-2,0,0,2] [-1,0,0,1]

    vector<int> n2 = {2, 2, 2, 2, 2};
    auto r2 = fourSum(n2, 8);
    for (auto& q : r2) {
        cout << "[";
        for (int k = 0; k < 4; k++) cout << q[k] << (k < 3 ? "," : "");
        cout << "] ";
    }
    cout << endl;
    // expected: [2,2,2,2]

    return 0;
}
```

---

**Trace for nums = [1,0,-1,0,-2,2], target = 0:**

After sorting: `[-2, -1, 0, 0, 1, 2]`

**i=0 (-2), j=1 (-1):** target for pair = 0-(-2)-(-1) = 3. left=2, right=5.
-   sum = -2-1+0+2 = -1 < 0 → left++.
-   sum = -2-1+0+2 = -1... left=3: sum = -2-1+0+2 = -1 < 0 → left++.
-   sum = -2-1+1+2 = 0 ✓ → record **[-2,-1,1,2]**. left=5, right=4 → done.

**i=0 (-2), j=2 (0):** target for pair = 0-(-2)-0 = 2. left=3, right=5.
-   sum = -2+0+0+2 = 0 ✓ → record **[-2,0,0,2]**. Skip dups. left=4, right=4 → done.

**i=0 (-2), j=3 (0):** j=3, nums[3]=0 == nums[2]=0 → **skip** (duplicate j).

**i=1 (-1), j=2 (0):** target for pair = 0-(-1)-0 = 1. left=3, right=5.
-   sum = -1+0+0+2 = 1 ✓ → record **[-1,0,0,1]**... wait, sum = -1+0+0+2 = 1 ≠ 0.

Let me recompute: total sum = nums[i]+nums[j]+nums[left]+nums[right] vs target=0.
-   -1+0+0+2 = 1 > 0 → right--.
-   -1+0+0+1 = 0 ✓ → record **[-1,0,0,1]**. Done.

**Result: [[-2,-1,1,2], [-2,0,0,2], [-1,0,0,1]]** ✓

---

**Trace for nums = [2,2,2,2,2], target = 8:**

After sorting: `[2, 2, 2, 2, 2]`

**i=0, j=1:** left=2, right=4. sum = 2+2+2+2 = 8 ✓ → record **[2,2,2,2]**.
Skip dups: left skips to 4, right skips to 2 → done.

**i=0, j=2:** nums[2]=2 == nums[1]=2 → skip.
**i=1:** nums[1]=2 == nums[0]=2 → skip.

**Result: [[2,2,2,2]]** ✓

---

## 🔍 The Four Levels of Duplicate Skipping

| Level | What | Code | Guard |
|-------|------|------|-------|
| i | First fixed element | `nums[i] == nums[i-1]` | `i > 0` |
| j | Second fixed element | `nums[j] == nums[j-1]` | `j > i + 1` |
| left | Left pointer after match | `nums[left] == nums[left+1]` | `left < right` |
| right | Right pointer after match | `nums[right] == nums[right-1]` | `left < right` |

Miss any one → duplicate quadruplets in the result.

---

## 🔍 The KSum Generalization

```
2Sum → O(N)
3Sum → O(N²)
4Sum → O(N³)
KSum → O(N^(K-1))
```

For any K: fix K-2 elements with nested loops,
two-pointer the last pair. Skip duplicates at every level.

---

## 🔍 The Overflow Trap

```cpp
// WRONG — overflows int:
int sum = nums[i] + nums[j] + nums[left] + nums[right];

// CORRECT — promotes to long long:
long long sum = (long long)nums[i] + nums[j] + nums[left] + nums[right];
```

LeetCode test cases include values near `INT_MAX` and `INT_MIN`.
Always use `long long` for the sum in 4Sum.

3Sum with target=0 doesn't overflow (three ints fit in int range).
But 4Sum with arbitrary targets can.

---

### 🧠 Memory of the Four Warriors Law

-   **Sort + fix 2 + two-pointer 2** — same pattern as 3Sum with one more loop
-   **Four duplicate skips:** i, j, left, right
-   **`long long` for sum** — four ints can overflow
-   **j guard:** `j > i + 1` (not `j > 0`) — relative to current i
-   **Time:** O(N³). **Space:** O(1) extra.

Thus is remembered the saga of **4Sum**,
where the Oracle added one more layer to the 3Sum pattern —
two fixed warriors and two marching pointers —
skipping duplicates at four levels,
guarding against overflow with long long —
until every unique quadruplet was found
in the disciplined march of N-cubed. 🎯🎯🎯🎯✨
