## 🔢➗ _The Divisible Chain: The Largest Divisible Subset Saga_

> \_"The Oracle was given a set of distinct positive integers.
>
> She was commanded:
>
> **'Find the largest subset where every pair of elements
> is divisible — either a divides b, or b divides a.'**
>
> The Oracle saw the connection immediately:
>
> **Sort the array. Then it's LIS with divisibility.**
>
> In a sorted array, if `a` divides `b` and `b` divides `c`,
> then `a` divides `c` (transitivity).
> So she only needed to check consecutive pairs in the chain —
> not every pair.
>
> Sort. Run LIS-style DP with `nums[i] % nums[j] == 0`
> instead of `nums[j] < nums[i]`.
> Track parents. Backtrack to print the subset."\_

---

This is the saga of **Largest Divisible Subset (LeetCode 368)**.

Given a set of **distinct** positive integers `nums`:
-   Find the largest subset where for every pair `(a, b)`,
    either `a % b == 0` or `b % a == 0`.

```
Input:  nums = [1,2,3]
Output: [1,2] or [1,3]   (size 2)

Input:  nums = [1,2,4,8]
Output: [1,2,4,8]         (size 4 — every pair divides)

Input:  nums = [4,8,10,240]
Output: [4,8,240]          (size 3)
```

---

## 🧠 The Key Insight -- Sort + Transitivity

**Why sort?**

In a sorted array `[a, b, c]` where `a < b < c`:
if `a | b` and `b | c`, then `a | c` (transitivity of divisibility).

This means we only need to check ADJACENT pairs in the chain.
If each element divides the next, ALL pairs in the chain are valid.

Without sorting, we'd need to check every pair — O(N²) per subset.
With sorting, the LIS-style DP handles it naturally.

**Why does transitivity hold for divisibility?**

If `a | b` then `b = k₁ * a` for some integer k₁.
If `b | c` then `c = k₂ * b = k₂ * k₁ * a`.
So `a | c`. ✓

This is why sorting + checking only `nums[i] % nums[j] == 0`
for `j < i` is sufficient. The chain property propagates.

---

## 🧠 The Algorithm -- LIS with Divisibility

```
1. Sort nums.
2. dp[i] = size of largest divisible subset ending at index i.
3. parent[i] = predecessor of i in the best chain.
4. For each i, check all j < i:
   If nums[i] % nums[j] == 0 AND dp[j] + 1 > dp[i]:
     dp[i] = dp[j] + 1
     parent[i] = j
5. Find maxIdx. Backtrack through parents.
```

The ONLY difference from Print LIS:
`nums[j] < nums[i]` becomes `nums[i] % nums[j] == 0`.

---

### 📜 The Scroll of the Divisible Chain

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ➗ Sort the Array

```cpp
vector<int> largestDivisibleSubset(vector<int>& nums) {
    int n = nums.size();
    sort(nums.begin(), nums.end());
```

Sorting is essential. Without it, transitivity doesn't help us,
and we can't use the LIS-style "check only predecessors" approach.

After sorting, if `j < i` and `nums[i] % nums[j] == 0`,
then `nums[j]` can come before `nums[i]` in the divisible chain.

---

## ➗ Initialize dp and parent

```cpp
    vector<int> dp(n, 1);
    vector<int> parent(n, -1);
```

Every element alone is a valid divisible subset of size 1.
No predecessors yet.

---

## ➗ Fill dp — LIS-style with divisibility check

```cpp
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
```

For each `i`, check all `j < i`.

---

### If nums[j] divides nums[i] AND gives a longer chain

```cpp
            if (nums[i] % nums[j] == 0 && dp[j] + 1 > dp[i]) {
                dp[i] = dp[j] + 1;
                parent[i] = j;
            }
```

`nums[i] % nums[j] == 0` — `j` can be a predecessor (j divides i).
`dp[j] + 1 > dp[i]` — this predecessor gives a strictly longer chain.

Why `nums[i] % nums[j]` and not `nums[j] % nums[i]`?
Because the array is sorted: `nums[j] ≤ nums[i]`.
The smaller number divides the larger. So we check if `i` is divisible by `j`.

> _"Element i asks every earlier element:
> 'Do you divide me evenly?
> If so, I can extend your divisible chain.
> Give me your longest chain, and I'll add myself.'"_

---

```cpp
        }
    }
```

---

## ➗ Find where the best chain ends

```cpp
    int maxLen = 0, maxIdx = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] > maxLen) {
            maxLen = dp[i];
            maxIdx = i;
        }
    }
```

---

## ➗ Backtrack through parents

```cpp
    vector<int> result;
    for (int i = maxIdx; i != -1; i = parent[i]) {
        result.push_back(nums[i]);
    }
    reverse(result.begin(), result.end());
    return result;
}
```

Follow the parent chain backward. Collect elements. Reverse.
Identical to Print LIS backtracking.

---

### 🎺 The Trial of the Divisible Chain

```cpp
int main() {
    vector<int> n1 = {1, 2, 3};
    auto r1 = largestDivisibleSubset(n1);
    for (int x : r1) cout << x << " ";
    cout << endl; // expected: 1 2 (or 1 3)

    vector<int> n2 = {1, 2, 4, 8};
    auto r2 = largestDivisibleSubset(n2);
    for (int x : r2) cout << x << " ";
    cout << endl; // expected: 1 2 4 8

    vector<int> n3 = {4, 8, 10, 240};
    auto r3 = largestDivisibleSubset(n3);
    for (int x : r3) cout << x << " ";
    cout << endl; // expected: 4 8 240

    return 0;
}
```

---

**Full trace for nums = [1, 2, 4, 8] (already sorted):**

| i | nums[i] | Check j < i | dp[i] | parent[i] |
|---|---------|-------------|-------|-----------|
| 0 | 1       | —           | 1     | -1        |
| 1 | 2       | j=0: 2%1==0 ✓ dp=2 | **2** | 0 |
| 2 | 4       | j=0: 4%1==0 ✓ dp=2, j=1: 4%2==0 ✓ dp=3 | **3** | 1 |
| 3 | 8       | j=0: 8%1==0 ✓ dp=2, j=1: 8%2==0 ✓ dp=3, j=2: 8%4==0 ✓ dp=4 | **4** | 2 |

**maxIdx = 3. Backtrack: 3→2→1→0.**
**Result: [1, 2, 4, 8]** ✓ — every pair divides.

---

**Trace for nums = [4, 8, 10, 240] (already sorted):**

| i | nums[i] | Check j < i | dp[i] | parent[i] |
|---|---------|-------------|-------|-----------|
| 0 | 4       | —           | 1     | -1        |
| 1 | 8       | j=0: 8%4==0 ✓ dp=2 | **2** | 0 |
| 2 | 10      | j=0: 10%4≠0 ✗, j=1: 10%8≠0 ✗ | 1 | -1 |
| 3 | 240     | j=0: 240%4==0 ✓ dp=2, j=1: 240%8==0 ✓ dp=3, j=2: 240%10==0 ✓ dp=2 | **3** | 1 |

**maxIdx = 3. Backtrack: 3→1→0.**
**Result: [4, 8, 240]** ✓

Note: 240%10==0 is true, but dp[2]+1=2 which is less than dp[1]+1=3.
So parent[3]=1 (via 8), not 2 (via 10). The longer chain wins.

---

**Trace for nums = [1, 2, 3] (already sorted):**

| i | nums[i] | dp[i] | parent[i] |
|---|---------|-------|-----------|
| 0 | 1       | 1     | -1        |
| 1 | 2       | 2     | 0         |
| 2 | 3       | 2     | 0         |

Both dp[1] and dp[2] = 2. First max found at index 1.
**Result: [1, 2]** ✓ (or [1, 3] if we pick index 2)

---

## 🔍 LIS vs Largest Divisible Subset

| Aspect | LIS | Largest Divisible Subset |
|--------|-----|------------------------|
| Sort first? | No (order matters) | **Yes** (order doesn't matter for subsets) |
| Condition | `nums[j] < nums[i]` | `nums[i] % nums[j] == 0` |
| Transitivity | `a < b < c` → `a < c` ✓ | `a\|b` and `b\|c` → `a\|c` ✓ |
| Print | Parent backtracking | Parent backtracking (same) |
| Everything else | Identical | Identical |

Sort + change the comparison. That's the entire difference.

---

## 🔍 Why Sorting Makes This Work

Without sorting: [8, 4, 2, 1]. We'd need to check both directions
(does j divide i? does i divide j?). Messy.

With sorting: [1, 2, 4, 8]. We always check `nums[i] % nums[j]`
where `j < i` means `nums[j] ≤ nums[i]`. One direction only.

Sorting also ensures transitivity works in the chain:
if each element divides the next in sorted order,
every pair in the chain satisfies the divisibility condition.

---

### 🧠 Memory of the Divisible Chain Law

-   **Sort first** — enables one-direction divisibility check
-   **`dp[i]`** = largest divisible subset ending at index `i`
-   **Condition:** `nums[i] % nums[j] == 0` (instead of `<`)
-   **Parent tracking + backtracking** — same as Print LIS
-   **Transitivity:** if `a|b` and `b|c` then `a|c` — only check adjacent
-   **Time:** O(N² + N log N). **Space:** O(N).

Thus is remembered the saga of **Largest Divisible Subset**,
where the Oracle sorted the numbers,
then ran the LIS machinery with a single change --
divisibility instead of less-than --
trusting that transitivity would ensure
every pair in the chain divided evenly,
and backtracking through parents
to reveal the longest divisible chain. 🔢➗✨
