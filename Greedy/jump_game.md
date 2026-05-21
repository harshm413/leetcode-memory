## 🦘🏁 _The Farthest Reach: The Jump Game Saga_

> \_"The Oracle stood at the start of an array.
> Each element told her the MAXIMUM jump length from that position.
>
> She was commanded:
>
> **'Can you reach the last index?'**
>
> The Oracle did not simulate every possible jump.
> She did not use DP. She did not use BFS.
>
> She used a single variable: **maxReach**.
>
> **Walk the array left to right.
> At each position, update the farthest index you can reach.
> If you can reach or pass the last index -- return true.
> If at any point your current index exceeds maxReach --
> you're stuck. Return false.**
>
> One pass. One variable. Pure greedy."\_

---

This is the saga of **Jump Game**.

Given an array `nums` where `nums[i]` is the maximum jump length from index `i`:

-   Return `true` if you can reach the last index.
-   You start at index 0.

```
Input:  nums = [2, 3, 1, 1, 4]
Output: true   (jump 1→2→3→4, or 1→3→4)

Input:  nums = [3, 2, 1, 0, 4]
Output: false  (stuck at index 3, can't pass the 0)
```

---

## 🧠 The Oracle's Core Insight -- Track the Farthest Reachable Index

Maintain `maxReach` = the farthest index reachable so far.

At each index `i`:
-   If `i > maxReach` → we can't even reach this index. Stuck. Return false.
-   Update: `maxReach = max(maxReach, i + nums[i])`.
-   If `maxReach >= n - 1` → we can reach the end. Return true.

```
Time:  O(n) -- single pass
Space: O(1) -- one variable
```

---

### 📜 The Scroll of the Farthest Reach

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🦘 The Greedy Ritual

```cpp
bool canJump(vector<int>& nums) {
    int n = nums.size();
    int maxReach = 0;
```

`maxReach` = the farthest index we can reach from any position seen so far.
Starts at 0 (we start at index 0, which is always reachable).

---

### 🔁 Walk the Array

```cpp
    for (int i = 0; i < n; i++) {
```

---

### 🛑 Can't Reach This Index -- Stuck

```cpp
        if (i > maxReach) return false;
```

If the current index is beyond our farthest reach,
we can never get here. We're stuck at some earlier position.

> _"The gap is too wide.
> No previous position could jump this far.
> The journey ends here."_

---

### 📐 Update the Farthest Reach

```cpp
        maxReach = max(maxReach, i + nums[i]);
```

From index `i`, we can jump up to `nums[i]` steps.
The farthest we can reach from here = `i + nums[i]`.
Update `maxReach` if this is farther than before.

> _"From this position, I can reach as far as i + nums[i].
> If that's farther than anything before --
> the horizon expands."_

---

### 🏁 Can Reach the End -- Early Exit

```cpp
        if (maxReach >= n - 1) return true;
    }
```

If `maxReach` reaches or passes the last index,
we can definitely get there. Return true immediately.

---

### 📤 Default Return

```cpp
    return true;
}
```

If the loop completes without returning false,
we made it through the entire array. Return true.

(This handles the case where `n == 1` -- already at the end.)

---

### 🎺 The Trial of the Farthest Reach

```cpp
int main() {
    vector<int> n1 = {2, 3, 1, 1, 4};
    cout << canJump(n1) << endl; // expected: 1 (true)

    vector<int> n2 = {3, 2, 1, 0, 4};
    cout << canJump(n2) << endl; // expected: 0 (false)

    vector<int> n3 = {0};
    cout << canJump(n3) << endl; // expected: 1 (true, already at end)

    return 0;
}
```

---

**Full trace for nums = [2, 3, 1, 1, 4]:**

| i | nums[i] | i > maxReach? | i + nums[i] | maxReach after | maxReach >= 4? |
|---|---------|---------------|-------------|----------------|----------------|
| 0 | 2       | 0 > 0? No    | 0 + 2 = 2  | max(0, 2) = 2  | 2 ≥ 4? No     |
| 1 | 3       | 1 > 2? No    | 1 + 3 = 4  | max(2, 4) = 4  | 4 ≥ 4? **YES!** |

**Return true at i=1.** ✓

From index 1, we can jump 3 steps to reach index 4 (the last index).
We don't even need to check the rest of the array.

---

**Full trace for nums = [3, 2, 1, 0, 4]:**

| i | nums[i] | i > maxReach? | i + nums[i] | maxReach after | maxReach >= 4? |
|---|---------|---------------|-------------|----------------|----------------|
| 0 | 3       | 0 > 0? No    | 0 + 3 = 3  | 3              | No             |
| 1 | 2       | 1 > 3? No    | 1 + 2 = 3  | 3              | No             |
| 2 | 1       | 2 > 3? No    | 2 + 1 = 3  | 3              | No             |
| 3 | 0       | 3 > 3? No    | 3 + 0 = 3  | 3              | No             |
| 4 | 4       | 4 > 3? **YES!** | —        | —              | —              |

**Return false at i=4.** ✓

We can reach index 3 (maxReach=3), but index 3 has value 0.
From index 3, we can't jump anywhere. maxReach stays at 3.
Index 4 is beyond maxReach. We're stuck.

---

**Trace for nums = [1, 1, 1, 1, 1]:**

| i | maxReach after |
|---|----------------|
| 0 | 1              |
| 1 | 2              |
| 2 | 3              |
| 3 | 4 ≥ 4 → **true** |

Each step extends reach by exactly 1. Barely makes it.

---

**Trace for nums = [0, 1]:**

| i | i > maxReach? |
|---|---------------|
| 0 | 0 > 0? No. maxReach = max(0, 0+0) = 0. |
| 1 | 1 > 0? **YES!** → return false. |

**Answer: false** ✓ -- stuck at index 0 (can't jump at all).

---

## 🔍 Why Greedy Works

At each position, we greedily extend our reach as far as possible.
We never need to "go back" -- if we can reach index `i`,
we can reach everything between 0 and `i` (we walked through it).

The key insight: **reachability is monotonic**.
If you can reach index 5, you can reach indices 0-5.
So we just track the farthest point and check if it covers the end.

---

## 🔍 Why Not DP?

DP would work: `dp[i] = can we reach index i?`
But it's O(n²) in the worst case (for each index, check all previous).

The greedy approach is O(n) -- strictly better.
The monotonic nature of reachability makes DP unnecessary.

---

## 🔍 Connection to Jump Game II

| Jump Game I (this)                | Jump Game II                      |
| --------------------------------- | --------------------------------- |
| CAN you reach the end? (T/F)     | MINIMUM jumps to reach the end    |
| One variable: maxReach            | Three variables: maxReach, end, jumps |
| Return true/false                 | Return jump count                 |

Jump Game I is the simpler version. Jump Game II builds on it.

---

### 🧠 Memory of the Farthest Reach Law

-   **One variable:** `maxReach` = farthest index reachable so far
-   **At each index i:**
    -   If `i > maxReach` → stuck → return false
    -   `maxReach = max(maxReach, i + nums[i])`
    -   If `maxReach >= n-1` → return true
-   **Greedy works** because reachability is monotonic
-   **Time:** O(n). **Space:** O(1).
-   **Edge cases:**
    -   Single element → always true (already at end)
    -   First element is 0 and n > 1 → false
    -   All zeros except first → depends on first element's value

Thus is remembered the saga of **Jump Game**,
where the Oracle walked the array with one question:
"How far can I reach from here?" --
extending her horizon at every step,
never looking back --
and if the horizon ever fell short of the next step,
she declared the journey impossible --
but if it reached the end,
the path was confirmed. 🦘🏁✨
