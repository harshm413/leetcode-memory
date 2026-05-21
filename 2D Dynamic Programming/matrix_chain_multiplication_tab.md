## ⛓️📋 _The Optimal Split (Tabulation): The Matrix Chain Multiplication Saga_

> \_"The Oracle had solved MCM with memoization --
> top-down, recursive, cached.
>
> Now she built the answer bottom-up.
>
> **`dp[i][j]` = minimum cost to multiply matrices `i` through `j`.**
>
> The key insight for tabulation:
> **Fill by chain LENGTH, not by index.**
>
> Length 1: single matrices → cost 0.
> Length 2: pairs of matrices → one split each.
> Length 3: triples → two possible splits.
> ...
> Length n: the full chain → the answer.
>
> Smaller chains are solved before larger chains.
> When computing `dp[i][j]`, all sub-chains
> `dp[i][k]` and `dp[k+1][j]` are already filled.
>
> Pure iteration. No recursion."\_

---

This is the saga of **Matrix Chain Multiplication (Tabulation)**.

Same problem:
-   `n` matrices with dimensions from array `arr` (size `n+1`).
-   Matrix `i` has dimensions `arr[i-1] × arr[i]`.
-   Find minimum scalar multiplications to multiply the entire chain.

```
Input:  arr = [10, 30, 5, 60]  →  Output: 4500
Input:  arr = [40, 20, 30, 10, 30]  →  Output: 26000
```

---

## 🧠 The Bottom-Up Formulation

```
dp[i][j] = minimum cost to multiply matrices i through j.

Base: dp[i][i] = 0 for all i (single matrix, no cost).

Fill by increasing chain length:
  For length = 2 to n:
    For i = 1 to n - length + 1:
      j = i + length - 1
      dp[i][j] = min over all k in [i, j-1]:
        dp[i][k] + dp[k+1][j] + arr[i-1] * arr[k] * arr[j]
```

**Why fill by length?**

`dp[i][j]` depends on `dp[i][k]` and `dp[k+1][j]`.
Both are SHORTER chains than `[i..j]`.
If we fill shorter chains first → dependencies are always ready.

---

### 📜 The Scroll of the Bottom-Up Split

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int matrixChainMultiplication(vector<int>& arr, int n) {
    vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
```

`dp[i][j]` = min cost to multiply matrices `i` to `j`.
All initialized to 0. The diagonal (`dp[i][i] = 0`) is already correct.

---

### 🔁 Fill by Increasing Chain Length

```cpp
    for (int len = 2; len <= n; len++) {
```

Start with chains of length 2 (pairs of matrices).
Build up to length `n` (the full chain).

> _"The smallest chains are trivial.
> Build upon them to solve larger chains.
> Each length depends only on shorter lengths --
> which are already computed."_

---

### 🔁 For Each Starting Position

```cpp
        for (int i = 1; i <= n - len + 1; i++) {
            int j = i + len - 1;
```

`i` = start of the chain. `j` = end of the chain.
`j = i + len - 1` ensures the chain has exactly `len` matrices.

---

### 🔁 Try Every Partition Point

```cpp
            dp[i][j] = INT_MAX;

            for (int k = i; k < j; k++) {
                int cost = dp[i][k] + dp[k + 1][j]
                         + arr[i - 1] * arr[k] * arr[j];
                dp[i][j] = min(dp[i][j], cost);
            }
        }
    }
```

For each split point `k` between `i` and `j-1`:
-   `dp[i][k]` = cost of left half (already computed, shorter chain).
-   `dp[k+1][j]` = cost of right half (already computed, shorter chain).
-   `arr[i-1] * arr[k] * arr[j]` = cost to merge the two results.

Take the minimum across all splits.

---

### 📤 The Answer

```cpp
    return dp[1][n];
}
```

`dp[1][n]` = minimum cost to multiply the entire chain (matrices 1 to n).

---

### 🎺 The Trial of the Bottom-Up Split

```cpp
int main() {
    vector<int> arr1 = {10, 30, 5, 60};
    cout << matrixChainMultiplication(arr1, 3) << endl; // expected: 4500

    vector<int> arr2 = {40, 20, 30, 10, 30};
    cout << matrixChainMultiplication(arr2, 4) << endl; // expected: 26000

    return 0;
}
```

---

**Full table trace for arr = [10, 30, 5, 60] (n=3):**

```
Matrices: 1=10×30, 2=30×5, 3=5×60
arr = [10, 30, 5, 60]
```

**Length 1 (base case):**
```
dp[1][1] = 0, dp[2][2] = 0, dp[3][3] = 0
```

**Length 2:**

`dp[1][2]`: i=1, j=2. Only k=1.
-   cost = dp[1][1] + dp[2][2] + arr[0]×arr[1]×arr[2] = 0 + 0 + 10×30×5 = **1500**

`dp[2][3]`: i=2, j=3. Only k=2.
-   cost = dp[2][2] + dp[3][3] + arr[1]×arr[2]×arr[3] = 0 + 0 + 30×5×60 = **9000**

**Length 3:**

`dp[1][3]`: i=1, j=3. Try k=1 and k=2.

k=1: dp[1][1] + dp[2][3] + arr[0]×arr[1]×arr[3] = 0 + 9000 + 10×30×60 = 0 + 9000 + 18000 = **27000**

k=2: dp[1][2] + dp[3][3] + arr[0]×arr[2]×arr[3] = 1500 + 0 + 10×5×60 = 1500 + 0 + 3000 = **4500**

dp[1][3] = min(27000, 4500) = **4500**.

**Final table:**

```
     1      2      3
1: [ 0    1500   4500 ]
2: [ -      0    9000 ]
3: [ -      -      0  ]
```

**Answer: dp[1][3] = 4500** ✓

---

**Table for arr = [40, 20, 30, 10, 30] (n=4):**

**Length 2:**
-   dp[1][2] = 40×20×30 = 24000
-   dp[2][3] = 20×30×10 = 6000
-   dp[3][4] = 30×10×30 = 9000

**Length 3:**
-   dp[1][3]: k=1 → 0+6000+40×20×10=14000. k=2 → 24000+0+40×30×10=36000. Min = **14000**.
-   dp[2][4]: k=2 → 0+9000+20×30×30=27000. k=3 → 6000+0+20×10×30=12000. Min = **12000**.

**Length 4:**
-   dp[1][4]: k=1 → 0+12000+40×20×30=36000. k=2 → 24000+9000+40×30×30=69000. k=3 → 14000+0+40×10×30=26000. Min = **26000**.

**Answer: dp[1][4] = 26000** ✓

---

## 🔍 The Fill Order -- Why Length-Based

```
Length 1: dp[1][1], dp[2][2], dp[3][3], dp[4][4]  (base cases)
Length 2: dp[1][2], dp[2][3], dp[3][4]
Length 3: dp[1][3], dp[2][4]
Length 4: dp[1][4]  (the answer)
```

Each cell depends on cells from SHORTER lengths.
Filling by increasing length guarantees all dependencies are ready.

This is the standard fill order for ALL partition DP tabulations.

---

## 🔍 Memoization vs Tabulation

| Memoization (Top-Down)            | Tabulation (Bottom-Up)            |
| --------------------------------- | --------------------------------- |
| `solve(i, j)` recursive          | `dp[i][j]` iterative             |
| Fills only needed subproblems     | Fills ALL subproblems             |
| Recursion stack overhead          | No recursion overhead             |
| Easier to derive from recurrence  | Requires understanding fill order |
| Same time: O(n³)                  | Same time: O(n³)                  |

For partition DP, memoization is usually easier to think about.
Tabulation is useful if you need to avoid stack overflow
or want to optimize space (though space optimization is rare for partition DP).

---

## 🔍 The Partition DP Tabulation Template

```cpp
// Fill by increasing length
for (int len = 2; len <= n; len++) {
    for (int i = start; i <= end - len + 1; i++) {
        int j = i + len - 1;
        dp[i][j] = worst_case;
        for (int k = i; k < j; k++) {
            int cost = dp[i][k] + dp[k+1][j] + merge(i, k, j);
            dp[i][j] = best(dp[i][j], cost);
        }
    }
}
answer = dp[start][end];
```

This template works for MCM, Burst Balloons, Cut Stick, etc.
Only the `merge` function and `best` (min/max) change.

---

### 🧠 Memory of the Optimal Split (Tabulation) Law

-   **State:** `dp[i][j]` = min cost to multiply matrices `i` through `j`
-   **Base:** `dp[i][i] = 0` for all `i` (single matrix)
-   **Fill order:** by increasing chain LENGTH (2, 3, ..., n)
-   **Transition:** `dp[i][j] = min over k in [i, j-1]: dp[i][k] + dp[k+1][j] + arr[i-1]*arr[k]*arr[j]`
-   **Answer:** `dp[1][n]`
-   **Why length-based?** Shorter chains are dependencies → must be filled first
-   **This is the partition DP tabulation template**
-   **Time:** O(n³). **Space:** O(n²).

Thus is remembered the saga of **Matrix Chain Multiplication (Tabulation)**,
where the Oracle did not recurse from the full chain
but instead built the answer from the ground up --
filling pairs first, then triples, then longer chains --
at each length trying every split point,
combining the costs of shorter sub-chains
already sitting in the table --
until the full chain's minimum cost
was revealed at `dp[1][n]`. ⛓️📋✨
