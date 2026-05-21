## ⛓️🧠 _The Optimal Split (Memoization): The Matrix Chain Multiplication Saga_

> \_"The Oracle was given a chain of matrices
> to multiply together.
>
> Matrix multiplication is associative --
> `(A × B) × C = A × (B × C)` --
> the result is the same regardless of order.
>
> But the NUMBER OF OPERATIONS changes drastically
> depending on where we place the parentheses.
>
> Given matrices with dimensions:
> `A(10×30), B(30×5), C(5×60)`
>
> `(A × B) × C` = 10×30×5 + 10×5×60 = 1500 + 3000 = **4500 ops**
> `A × (B × C)` = 30×5×60 + 10×30×60 = 9000 + 18000 = **27000 ops**
>
> Same result. 6× difference in cost.
>
> The Oracle was commanded:
>
> **'Find the minimum number of scalar multiplications
> needed to multiply the entire chain.'**
>
> The Oracle saw the pattern:
>
> **Try every possible PARTITION point.**
>
> To multiply matrices `i` through `j`,
> split at every possible position `k` between `i` and `j`:
> -   Multiply the left half: matrices `i` to `k`.
> -   Multiply the right half: matrices `k+1` to `j`.
> -   Multiply the two resulting matrices together.
>
> The cost = left cost + right cost + cost of final multiplication.
> Try every `k`. Take the minimum.
>
> This was the birth of **Partition DP** --
> the pattern that powers Burst Balloons,
> Cut the Stick, and Boolean Expression."\_

---

This is the saga of **Matrix Chain Multiplication (Memoization)**.

Given `n` matrices where matrix `i` has dimensions `arr[i-1] × arr[i]`:

-   Find the **minimum** number of scalar multiplications
    to multiply the entire chain.

The dimensions are given as an array `arr` of size `n+1`:
matrix `i` (1-indexed) has dimensions `arr[i-1] × arr[i]`.

```
Input:  arr = [10, 30, 5, 60]  (3 matrices: 10×30, 30×5, 5×60)
Output: 4500

Input:  arr = [40, 20, 30, 10, 30]  (4 matrices: 40×20, 20×30, 30×10, 10×30)
Output: 26000

Input:  arr = [10, 20, 30]  (2 matrices: 10×20, 20×30)
Output: 6000  (only one way: 10×20×30)
```

---

## 🧠 The Partition DP Pattern

This is the **foundation** of all partition DP problems.

**The idea:** given a range `[i, j]`, try every possible split point `k`
within that range. Solve the left half, solve the right half,
combine them. Take the minimum (or maximum) across all splits.

```
solve(i, j):
  If i == j: return 0  (single matrix, no multiplication needed)

  min_cost = infinity
  For k = i to j-1:
    left  = solve(i, k)       (cost to multiply matrices i..k)
    right = solve(k+1, j)     (cost to multiply matrices k+1..j)
    merge = arr[i-1] * arr[k] * arr[j]  (cost to multiply the two results)
    min_cost = min(min_cost, left + right + merge)

  return min_cost
```

**Why `arr[i-1] × arr[k] × arr[j]` for the merge cost?**

After multiplying matrices `i..k`, the result has dimensions `arr[i-1] × arr[k]`.
After multiplying matrices `k+1..j`, the result has dimensions `arr[k] × arr[j]`.
Multiplying these two matrices costs `arr[i-1] × arr[k] × arr[j]`.

---

## 🧠 The Partition DP Template

```
solve(i, j):
  Base case: i == j (or i > j) → return 0 (or trivial value)

  answer = worst_case
  For every partition point k in [i, j-1]:
    left = solve(i, k)
    right = solve(k+1, j)
    cost = left + right + merge_cost(i, k, j)
    answer = best(answer, cost)

  return answer
```

This template applies to:
-   **MCM** -- minimize multiplication cost
-   **Burst Balloons** -- maximize coins
-   **Cut the Stick** -- minimize cutting cost
-   **Boolean Expression** -- count ways to evaluate to true

---

### 📜 The Scroll of the Optimal Split

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## ⛓️ Setting Up the Cache

```cpp
int matrixChainMultiplication(vector<int>& arr, int n) {
    // n = number of matrices. arr has size n+1.
    // Matrices are numbered 1 to n.
    vector<vector<int>> memo(n + 1, vector<int>(n + 1, -1));
```

`memo[i][j]` = minimum cost to multiply matrices `i` through `j`.
`-1` = not yet computed.

---

```cpp
    return solve(1, n, arr, memo);
}
```

Solve for the entire chain: matrices 1 to n.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, vector<int>& arr, vector<vector<int>>& memo) {
```

"What is the minimum cost to multiply matrices `i` through `j`?"

---

### 🛑 Base Case -- Single Matrix

```cpp
    if (i == j) return 0;
```

A single matrix requires no multiplication.
Cost = 0.

> _"One matrix stands alone.
> There is nothing to multiply.
> The cost is zero."_

---

### 📖 Cache Check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### 🔁 Try Every Partition Point

```cpp
    int minCost = INT_MAX;

    for (int k = i; k < j; k++) {
```

`k` is the partition point.
Split the chain into `[i..k]` and `[k+1..j]`.

`k` ranges from `i` to `j-1` (at least one matrix on each side).

> _"At every possible split,
> the Oracle divides the chain in two.
> She solves each half independently,
> then pays the cost of combining them."_

---

### 📐 Compute the Cost of This Split

```cpp
        int left = solve(i, k, arr, memo);
        int right = solve(k + 1, j, arr, memo);
        int merge = arr[i - 1] * arr[k] * arr[j];
        int cost = left + right + merge;
        minCost = min(minCost, cost);
    }
```

**`left`** = minimum cost to multiply matrices `i` to `k`.
**`right`** = minimum cost to multiply matrices `k+1` to `j`.
**`merge`** = cost to multiply the two resulting matrices.

The left result has dimensions `arr[i-1] × arr[k]`.
The right result has dimensions `arr[k] × arr[j]`.
Multiplying them: `arr[i-1] × arr[k] × arr[j]` operations.

**`cost`** = total cost for this particular split.
Track the minimum across all splits.

---

### 📝 Cache and Return

```cpp
    memo[i][j] = minCost;
    return minCost;
}
```

---

### 🎺 The Trial of the Optimal Split

```cpp
int main() {
    vector<int> arr1 = {10, 30, 5, 60};
    cout << matrixChainMultiplication(arr1, 3) << endl; // expected: 4500

    vector<int> arr2 = {40, 20, 30, 10, 30};
    cout << matrixChainMultiplication(arr2, 4) << endl; // expected: 26000

    vector<int> arr3 = {10, 20, 30};
    cout << matrixChainMultiplication(arr3, 2) << endl; // expected: 6000

    return 0;
}
```

---

**Full trace for arr = [10, 30, 5, 60] (3 matrices):**

```
Matrices: A(10×30), B(30×5), C(5×60)
Numbered: 1=A, 2=B, 3=C
```

**solve(1, 3):** try k=1 and k=2.

**k=1:** split into [1..1] and [2..3]
-   left = solve(1,1) = 0 (single matrix A)
-   right = solve(2,3):
    -   Single split k=2: solve(2,2)=0, solve(3,3)=0, merge=30×5×60=9000.
    -   memo[2][3] = 9000.
-   merge = arr[0]×arr[1]×arr[3] = 10×30×60 = 18000
-   cost = 0 + 9000 + 18000 = **27000**

**k=2:** split into [1..2] and [3..3]
-   left = solve(1,2):
    -   Single split k=1: solve(1,1)=0, solve(2,2)=0, merge=10×30×5=1500.
    -   memo[1][2] = 1500.
-   right = solve(3,3) = 0 (single matrix C)
-   merge = arr[0]×arr[2]×arr[3] = 10×5×60 = 3000
-   cost = 1500 + 0 + 3000 = **4500**

**minCost = min(27000, 4500) = 4500.**

**Answer: 4500** ✓

Optimal parenthesization: `(A × B) × C`.

---

**Trace for arr = [40, 20, 30, 10, 30] (4 matrices):**

```
Matrices: A(40×20), B(20×30), C(30×10), D(10×30)
```

solve(1,4) tries k=1, k=2, k=3:

-   k=1: [A] × [BCD] = 0 + solve(2,4) + 40×20×30
-   k=2: [AB] × [CD] = solve(1,2) + solve(3,4) + 40×30×30
-   k=3: [ABC] × [D] = solve(1,3) + 0 + 40×10×30

The recursion explores all sub-problems.
The minimum across all splits = **26000**.

---

## 🔍 Why This Is the Foundation of Partition DP

Every partition DP problem follows this exact skeleton:

```
solve(i, j):
  if base case → return trivial
  for k in partition range:
    left = solve(i, k)
    right = solve(k+1, j)
    cost = left + right + merge(i, k, j)
    track best
  return best
```

| Problem              | What changes?                          |
| -------------------- | -------------------------------------- |
| MCM (this)           | merge = arr[i-1]×arr[k]×arr[j], minimize |
| Burst Balloons       | merge = arr[i-1]×arr[k]×arr[j], maximize |
| Cut the Stick        | merge = cuts[j+1] - cuts[i-1], minimize |
| Boolean Expression   | count ways, combine with AND/OR/XOR    |

Learn MCM → the rest are variations.

---

## 🔍 Why `k` Goes from `i` to `j-1`

The split divides `[i..j]` into `[i..k]` and `[k+1..j]`.
-   `k = i` → left has 1 matrix, right has j-i matrices.
-   `k = j-1` → left has j-i matrices, right has 1 matrix.
-   `k = j` → right would be empty. Invalid.

Every valid split has at least one matrix on each side.

---

## 🔍 Understanding the Dimensions

```
arr = [10, 30, 5, 60]

Matrix 1: arr[0] × arr[1] = 10 × 30
Matrix 2: arr[1] × arr[2] = 30 × 5
Matrix 3: arr[2] × arr[3] = 5 × 60

After multiplying matrices i..k:
  Result dimensions = arr[i-1] × arr[k]

After multiplying matrices k+1..j:
  Result dimensions = arr[k] × arr[j]

Merging them: arr[i-1] × arr[k] × arr[j]
```

The `arr` array encodes dimensions such that
adjacent matrices share a dimension (required for multiplication).

---

### 🧠 Memory of the Optimal Split (Memoization) Law

-   **State:** `solve(i, j)` = min cost to multiply matrices `i` through `j`
-   **Base:** `i == j` → 0 (single matrix, no cost)
-   **Transition:** try every split `k` from `i` to `j-1`
    -   `cost = solve(i,k) + solve(k+1,j) + arr[i-1]*arr[k]*arr[j]`
    -   Take the minimum across all `k`
-   **Merge cost:** `arr[i-1] × arr[k] × arr[j]` (dimensions of the two result matrices)
-   **This is the PARTITION DP template** -- learn it, apply it everywhere
-   **Time:** O(n³) -- n² subproblems, each tries n splits
-   **Space:** O(n²) -- memo table
-   **Edge cases:**
    -   2 matrices → only one split, answer = arr[0]×arr[1]×arr[2]
    -   All same dimensions → any order gives same cost

Thus is remembered the saga of **Matrix Chain Multiplication (Memoization)**,
where the Oracle faced a chain of matrices
and tried every possible way to split it --
left half and right half,
each solved independently,
then combined at a cost determined by their dimensions --
always choosing the split that minimized the total work --
until the optimal parenthesization was found
through the ancient art of Partition DP. ⛓️🧠✨
