## 🎈🧠 _The Last to Pop (Memoization): The Burst Balloons Saga_

> \_"In the Arena of Balloons,
> `n` balloons stood in a row,
> each carrying a number.
>
> When a balloon `i` was burst,
> the coins earned = `nums[i-1] × nums[i] × nums[i+1]`
> (the product of itself and its two neighbors).
>
> After bursting, the neighbors of `i` become adjacent.
>
> The Oracle was commanded:
>
> **'Burst all balloons to maximize the total coins.'**
>
> The naive approach: try every order of bursting.
> n! permutations. Impossible.
>
> The Oracle flipped the thinking:
>
> **Instead of thinking about which balloon to burst FIRST,
> think about which balloon to burst LAST.**
>
> If balloon `k` is the LAST one burst in the range `[i, j]`:
> -   When `k` is finally burst, all others in `[i, j]` are already gone.
> -   Its neighbors at that moment are `nums[i-1]` and `nums[j+1]`
>     (the boundaries OUTSIDE the range).
> -   Coins from bursting `k` last = `nums[i-1] × nums[k] × nums[j+1]`.
>
> Before `k` is burst:
> -   Everything to its left `[i, k-1]` was burst optimally.
> -   Everything to its right `[k+1, j]` was burst optimally.
>
> **The 'last to burst' trick converts this into partition DP.**
>
> `solve(i, j)` = max coins from bursting all balloons in range `[i, j]`,
> given that `nums[i-1]` and `nums[j+1]` are the boundaries."\_

---

This is the saga of **Burst Balloons**.

Given an array `nums` of balloon values:

-   Burst balloon `i` → earn `nums[i-1] × nums[i] × nums[i+1]` coins.
-   After bursting, neighbors become adjacent.
-   Return the **maximum** coins from bursting ALL balloons.

Treat `nums[-1] = nums[n] = 1` (virtual boundaries).

```
Input:  nums = [3, 1, 5, 8]
Output: 167

Input:  nums = [1, 5]
Output: 10
```

---

## 🧠 The Key Insight -- Think About the LAST Balloon Burst

**Why "last" and not "first"?**

If we think about which balloon to burst FIRST,
the remaining array changes (neighbors shift).
The subproblems become dependent on each other -- messy.

If we think about which balloon to burst LAST in a range:
-   When it's finally burst, everything else in the range is already gone.
-   Its neighbors are the BOUNDARIES of the range (fixed, known).
-   The left and right sub-ranges are INDEPENDENT.

This independence is what makes partition DP work.

---

## 🧠 The Recurrence

Add virtual boundaries: `nums = [1, ...original..., 1]`.
Now balloons are indexed 1 to n (boundaries at 0 and n+1).

`solve(i, j)` = max coins from bursting all balloons in range `[i, j]`,
where `nums[i-1]` and `nums[j+1]` are the fixed boundaries.

```
solve(i, j):
  If i > j: return 0  (no balloons in this range)

  maxCoins = 0
  For k = i to j:  (k is the LAST balloon burst in [i, j])
    left  = solve(i, k-1)     (burst everything left of k first)
    right = solve(k+1, j)     (burst everything right of k first)
    burst = nums[i-1] * nums[k] * nums[j+1]  (coins from bursting k LAST)
    maxCoins = max(maxCoins, left + right + burst)

  return maxCoins
```

**Why `nums[i-1] × nums[k] × nums[j+1]`?**

When `k` is the LAST balloon burst in `[i, j]`:
-   All balloons in `[i, k-1]` are already gone.
-   All balloons in `[k+1, j]` are already gone.
-   The only things adjacent to `k` are the BOUNDARIES: `nums[i-1]` and `nums[j+1]`.

---

### 📜 The Scroll of the Last Pop

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🎈 Setting Up

```cpp
int maxCoins(vector<int>& nums) {
    int n = nums.size();

    // Add virtual boundaries
    nums.insert(nums.begin(), 1);
    nums.push_back(1);
```

Original `nums = [3, 1, 5, 8]` becomes `[1, 3, 1, 5, 8, 1]`.
Balloons are at indices 1 to n. Boundaries at 0 and n+1.

---

```cpp
    vector<vector<int>> memo(n + 2, vector<int>(n + 2, -1));
    return solve(1, n, nums, memo);
}
```

Solve for all balloons: range [1, n].

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, vector<int>& nums, vector<vector<int>>& memo) {
```

"What is the maximum coins from bursting all balloons in [i, j],
given that nums[i-1] and nums[j+1] are the boundaries?"

---

### 🛑 Base Case -- Empty Range

```cpp
    if (i > j) return 0;
```

No balloons in this range. No coins to earn.

---

### 📖 Cache Check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### 🔁 Try Every Balloon as the LAST to Burst

```cpp
    int maxCoins = 0;

    for (int k = i; k <= j; k++) {
```

`k` is the balloon we choose to burst LAST in range [i, j].

---

### 📐 Compute the Coins

```cpp
        int left = solve(i, k - 1, nums, memo);
        int right = solve(k + 1, j, nums, memo);
        int burst = nums[i - 1] * nums[k] * nums[j + 1];
        maxCoins = max(maxCoins, left + right + burst);
    }
```

**`left`** = max coins from bursting everything in [i, k-1] (left of k).
**`right`** = max coins from bursting everything in [k+1, j] (right of k).
**`burst`** = coins from bursting `k` itself (the LAST one in this range).

When `k` is burst last, its neighbors are the boundaries:
`nums[i-1]` (left boundary) and `nums[j+1]` (right boundary).

> _"If k is the last to fall in this range,
> everything else is already gone.
> Only the boundaries remain beside k.
> The coins are boundary × k × boundary."_

---

### 📝 Cache and Return

```cpp
    memo[i][j] = maxCoins;
    return maxCoins;
}
```

---

### 🎺 The Trial of the Last Pop

```cpp
int main() {
    vector<int> nums1 = {3, 1, 5, 8};
    cout << maxCoins(nums1) << endl; // expected: 167

    vector<int> nums2 = {1, 5};
    cout << maxCoins(nums2) << endl; // expected: 10

    return 0;
}
```

---

**Full trace for nums = [3, 1, 5, 8]:**

After adding boundaries: `nums = [1, 3, 1, 5, 8, 1]`
Balloons at indices 1-4. Boundaries at 0 and 5.

**solve(1, 4):** try k=1, 2, 3, 4.

**k=1 (balloon 3 is last):**
-   left = solve(1, 0) = 0 (empty)
-   right = solve(2, 4)
-   burst = nums[0]×nums[1]×nums[5] = 1×3×1 = 3
-   Need solve(2, 4)...

**k=4 (balloon 8 is last):**
-   left = solve(1, 3)
-   right = solve(5, 4) = 0 (empty)
-   burst = nums[0]×nums[4]×nums[5] = 1×8×1 = 8
-   Need solve(1, 3)...

Let me compute key sub-problems:

**solve(1, 1):** only k=1. burst = nums[0]×nums[1]×nums[2] = 1×3×1 = 3. → **3**
**solve(2, 2):** only k=2. burst = nums[1]×nums[2]×nums[3] = 3×1×5 = 15. → **15**
**solve(3, 3):** only k=3. burst = nums[2]×nums[3]×nums[4] = 1×5×8 = 40. → **40**
**solve(4, 4):** only k=4. burst = nums[3]×nums[4]×nums[5] = 5×8×1 = 40. → **40**

**solve(3, 4):**
-   k=3: solve(3,2)=0 + solve(4,4)=40 + nums[2]×nums[3]×nums[5] = 1×5×1 = 5. Total = 45.
-   k=4: solve(3,3)=40 + solve(5,4)=0 + nums[2]×nums[4]×nums[5] = 1×8×1 = 8. Total = 48.
-   → **48**

**solve(2, 4):**
-   k=2: 0 + 48 + nums[1]×nums[2]×nums[5] = 3×1×1 = 3. Total = 51.
-   k=3: solve(2,2)=15 + solve(4,4)=40 + nums[1]×nums[3]×nums[5] = 3×5×1 = 15. Total = 70.
-   k=4: solve(2,3) + 0 + nums[1]×nums[4]×nums[5] = 3×8×1 = 24.
    -   solve(2,3): k=2: 0+40+3×1×8=24→64. k=3: 15+0+3×5×8=120→135. → **135**.
    -   Total = 135 + 0 + 24 = 159.
-   → **159**

**solve(1, 3):**
-   k=3: solve(1,2) + 0 + nums[0]×nums[3]×nums[4] = 1×5×8 = 40.
    -   solve(1,2): k=1: 0+15+1×3×5=15→30. k=2: 3+0+1×1×5=5→8. → **30**.
    -   Total = 30 + 0 + 40 = 70.
-   k=1: 0 + solve(2,3)=135 + 1×3×8=24. Total = 159.
-   k=2: solve(1,1)=3 + solve(3,3)=40 + 1×1×8=8. Total = 51.
-   → **159**

**solve(1, 4):**
-   k=1: 0 + 159 + 1×3×1 = 3. Total = 162.
-   k=2: 3 + 48 + 1×1×1 = 1. Total = 52.
-   k=3: 30 + 40 + 1×5×1 = 5. Total = 75.
-   k=4: 159 + 0 + 1×8×1 = 8. Total = **167**.
-   → **167**

**Answer: 167** ✓

The optimal strategy: burst balloon 8 LAST (it earns 1×8×1=8 at the end,
but the sub-problems [1,3] earn 159 optimally).

---

## 🔍 Why "Last to Burst" Makes Subproblems Independent

**If we think "first to burst":**
Bursting balloon `k` first makes `k-1` and `k+1` adjacent.
Now the left and right sub-arrays INTERACT (they share a new boundary).
Subproblems are NOT independent. DP doesn't work cleanly.

**If we think "last to burst":**
When `k` is the last in [i, j], everything else is already gone.
Left [i, k-1] and right [k+1, j] were solved BEFORE `k` was burst.
They don't interact -- they have fixed boundaries.
Subproblems ARE independent. DP works perfectly.

> _"The last balloon to fall sees only the boundaries.
> Everything between has already vanished.
> This isolation is what makes the partition clean."_

---

## 🔍 How This Maps to MCM

| MCM                               | Burst Balloons                    |
| --------------------------------- | --------------------------------- |
| Minimize cost                     | Maximize coins                    |
| Split at k: left [i..k], right [k+1..j] | Last burst k: left [i..k-1], right [k+1..j] |
| Merge: `arr[i-1]*arr[k]*arr[j]`  | Burst: `nums[i-1]*nums[k]*nums[j+1]` |
| k from i to j-1                   | k from i to j                     |

Same partition DP skeleton. Different semantics.

---

### 🧠 Memory of the Last Pop (Memoization) Law

-   **Add boundaries:** `nums = [1, ...original..., 1]`
-   **State:** `solve(i, j)` = max coins from bursting all in [i, j]
-   **Base:** `i > j` → 0 (empty range)
-   **Transition:** try every `k` from `i` to `j` as the LAST burst
    -   `coins = solve(i, k-1) + solve(k+1, j) + nums[i-1]*nums[k]*nums[j+1]`
    -   Take the maximum
-   **Key insight:** "last to burst" makes subproblems independent
-   **Burst cost:** `nums[i-1] × nums[k] × nums[j+1]` (boundaries are all that remain)
-   **Time:** O(n³). **Space:** O(n²).

Thus is remembered the saga of **Burst Balloons (Memoization)**,
where the Oracle did not ask "which balloon to burst first?"
but instead asked "which balloon to burst LAST?" --
knowing that the last balloon in any range
sees only the boundaries beside it --
and by trying every balloon as the last,
solving the left and right independently,
the maximum coins were found
through the ancient art of Partition DP. 🎈🧠✨
