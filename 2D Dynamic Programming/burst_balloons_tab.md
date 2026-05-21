## 🎈📋 _The Last to Pop (Tabulation): The Burst Balloons Saga_

> \_"The Oracle had solved Burst Balloons with memoization.
> Now she built the answer bottom-up.
>
> **`dp[i][j]` = max coins from bursting all balloons in [i, j].**
>
> Fill by increasing range length.
> Length 1: single balloons.
> Length 2: pairs.
> ...
> Length n: the full array → the answer.
>
> Same gap-based fill as MCM and Cut Stick."\_

---

This is the saga of **Burst Balloons (Tabulation)**.

Same problem:
-   Burst balloon `i` → earn `nums[i-1] × nums[i] × nums[i+1]`.
-   Maximize total coins.
-   Virtual boundaries: `nums[-1] = nums[n] = 1`.

```
Input:  nums = [3, 1, 5, 8]  →  Output: 167
Input:  nums = [1, 5]  →  Output: 10
```

---

## 🧠 The Bottom-Up Formulation

```
After adding boundaries: nums = [1, ...original..., 1]. Size = n+2.
Balloons at indices 1 to n.

dp[i][j] = max coins from bursting all balloons in range [i, j].

Base: dp[i][j] = 0 when i > j (empty range).

Fill by increasing length:
  For len = 1 to n:
    For i = 1 to n - len + 1:
      j = i + len - 1
      dp[i][j] = max over k in [i, j]:
        dp[i][k-1] + dp[k+1][j] + nums[i-1]*nums[k]*nums[j+1]
```

---

### 📜 The Scroll of the Bottom-Up Pop

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

```cpp
int maxCoins(vector<int>& nums) {
    int n = nums.size();

    // Add virtual boundaries
    nums.insert(nums.begin(), 1);
    nums.push_back(1);
```

`nums = [1, 3, 1, 5, 8, 1]`. Balloons at indices 1 to n.

---

```cpp
    // dp[i][j] = max coins from bursting all in [i, j]
    vector<vector<int>> dp(n + 2, vector<int>(n + 2, 0));
```

Size `n+2` to handle indices 0 to n+1.
All zeros — base cases (empty ranges) are already correct.

---

### 🔁 Fill by Increasing Length

```cpp
    for (int len = 1; len <= n; len++) {
        for (int i = 1; i <= n - len + 1; i++) {
            int j = i + len - 1;
```

`len` = number of balloons in the range.
`i` = start, `j` = end. Range [i, j] has `len` balloons.

---

### 🔁 Try Every Balloon as the Last to Burst

```cpp
            for (int k = i; k <= j; k++) {
                int coins = dp[i][k - 1] + dp[k + 1][j]
                          + nums[i - 1] * nums[k] * nums[j + 1];
                dp[i][j] = max(dp[i][j], coins);
            }
        }
    }
```

For each `k` in [i, j] — treat `k` as the LAST balloon burst:
-   `dp[i][k-1]` = max coins from left half (shorter, already filled).
-   `dp[k+1][j]` = max coins from right half (shorter, already filled).
-   `nums[i-1] * nums[k] * nums[j+1]` = coins from bursting `k` last.

Take the maximum across all choices of `k`.

---

### 📤 The Answer

```cpp
    return dp[1][n];
}
```

`dp[1][n]` = max coins from bursting all balloons (range [1, n]).

---

### 🎺 The Trial of the Bottom-Up Pop

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

**Full table trace for nums = [3, 1, 5, 8]:**

After boundaries: `nums = [1, 3, 1, 5, 8, 1]`. n = 4.

**Length 1 (single balloons):**

| i | j | k | coins = dp[i][k-1] + dp[k+1][j] + nums[i-1]*nums[k]*nums[j+1] | dp[i][j] |
|---|---|---|----------------------------------------------------------------|----------|
| 1 | 1 | 1 | 0 + 0 + nums[0]×nums[1]×nums[2] = 1×3×1 = 3                  | **3**    |
| 2 | 2 | 2 | 0 + 0 + nums[1]×nums[2]×nums[3] = 3×1×5 = 15                 | **15**   |
| 3 | 3 | 3 | 0 + 0 + nums[2]×nums[3]×nums[4] = 1×5×8 = 40                 | **40**   |
| 4 | 4 | 4 | 0 + 0 + nums[3]×nums[4]×nums[5] = 5×8×1 = 40                 | **40**   |

**Length 2:**

`dp[1][2]`: i=1, j=2.
-   k=1: dp[1][0]+dp[2][2]+nums[0]×nums[1]×nums[3] = 0+15+1×3×5 = 30.
-   k=2: dp[1][1]+dp[3][2]+nums[0]×nums[2]×nums[3] = 3+0+1×1×5 = 8.
-   dp[1][2] = **30**

`dp[2][3]`: i=2, j=3.
-   k=2: 0+40+nums[1]×nums[2]×nums[4] = 0+40+3×1×8 = 64.
-   k=3: 15+0+nums[1]×nums[3]×nums[4] = 15+0+3×5×8 = 135.
-   dp[2][3] = **135**

`dp[3][4]`: i=3, j=4.
-   k=3: 0+40+nums[2]×nums[3]×nums[5] = 0+40+1×5×1 = 45.
-   k=4: 40+0+nums[2]×nums[4]×nums[5] = 40+0+1×8×1 = 48.
-   dp[3][4] = **48**

**Length 3:**

`dp[1][3]`: i=1, j=3.
-   k=1: dp[1][0]+dp[2][3]+nums[0]×nums[1]×nums[4] = 0+135+1×3×8 = 159.
-   k=2: dp[1][1]+dp[3][3]+nums[0]×nums[2]×nums[4] = 3+40+1×1×8 = 51.
-   k=3: dp[1][2]+dp[4][3]+nums[0]×nums[3]×nums[4] = 30+0+1×5×8 = 70.
-   dp[1][3] = **159**

`dp[2][4]`: i=2, j=4.
-   k=2: 0+48+nums[1]×nums[2]×nums[5] = 0+48+3×1×1 = 51.
-   k=3: 15+40+nums[1]×nums[3]×nums[5] = 15+40+3×5×1 = 70.
-   k=4: 135+0+nums[1]×nums[4]×nums[5] = 135+0+3×8×1 = 159.
-   dp[2][4] = **159**

**Length 4 (full array):**

`dp[1][4]`: i=1, j=4.
-   k=1: dp[1][0]+dp[2][4]+nums[0]×nums[1]×nums[5] = 0+159+1×3×1 = 162.
-   k=2: dp[1][1]+dp[3][4]+nums[0]×nums[2]×nums[5] = 3+48+1×1×1 = 52.
-   k=3: dp[1][2]+dp[4][4]+nums[0]×nums[3]×nums[5] = 30+40+1×5×1 = 75.
-   k=4: dp[1][3]+dp[5][4]+nums[0]×nums[4]×nums[5] = 159+0+1×8×1 = **167**.
-   dp[1][4] = **167**

**Answer: dp[1][4] = 167** ✓

---

**Trace for nums = [1, 5]:**

After boundaries: `nums = [1, 1, 5, 1]`. n = 2.

**Length 1:**
-   dp[1][1] = 1×1×5 = 5.
-   dp[2][2] = 1×5×1 = 5.

**Length 2:**
-   dp[1][2]: k=1: 0+5+1×1×1=6. k=2: 5+0+1×5×1=10. → **10**.

**Answer: 10** ✓

---

## 🔍 The Partition DP Family -- Complete Picture

| Problem              | What's "last"?      | Merge cost                    | Optimize |
| -------------------- | ------------------- | ----------------------------- | -------- |
| MCM                  | Last multiplication | `arr[i-1]*arr[k]*arr[j]`     | Minimize |
| Cut Stick            | Last cut            | `cuts[j] - cuts[i]`          | Minimize |
| **Burst Balloons**   | Last balloon burst  | `nums[i-1]*nums[k]*nums[j+1]`| Maximize |

All three use the same `solve(i, j)` + try every `k` skeleton.
The only differences: what the merge cost is, and min vs max.

---

## 🔍 Memoization vs Tabulation

| Memoization                       | Tabulation                        |
| --------------------------------- | --------------------------------- |
| `solve(i, j)` recursive          | `dp[i][j]` iterative             |
| Base: `i > j` → 0                | Base: all zeros (empty ranges)    |
| Fills only needed subproblems     | Fills all subproblems by length   |
| Easier to derive                  | Requires length-based fill order  |
| Same O(n³) time                   | Same O(n³) time                   |

---

### 🧠 Memory of the Last Pop (Tabulation) Law

-   **Add boundaries:** `nums = [1, ...original..., 1]`
-   **State:** `dp[i][j]` = max coins from bursting all in [i, j]
-   **Base:** all zeros (empty ranges, `i > j`)
-   **Fill order:** increasing length (1, 2, ..., n)
-   **Transition:** `dp[i][j] = max over k in [i, j]: dp[i][k-1] + dp[k+1][j] + nums[i-1]*nums[k]*nums[j+1]`
-   **Answer:** `dp[1][n]`
-   **k = last balloon burst** in range [i, j]
-   **Time:** O(n³). **Space:** O(n²).

Thus is remembered the saga of **Burst Balloons (Tabulation)**,
where the Oracle filled the table length by length --
single balloons first, then pairs, then triples --
at each range trying every balloon as the last to pop,
earning coins from the boundaries that remained --
until the full range revealed
the maximum coins achievable
by popping every balloon in the optimal order. 🎈📋✨
