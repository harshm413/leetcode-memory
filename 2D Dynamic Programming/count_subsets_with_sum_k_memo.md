## 🎒🔢🧠 _Counting the Ways (Memoization): The Count Subsets with Sum K Saga_

> \_"The Oracle had already answered YES or NO --
> 'does a subset exist that sums to the target?'
>
> Now the King demanded more:
>
> **'HOW MANY such subsets exist?'**
>
> Not just 'is there one?' but 'how many are there?'
>
> The Oracle saw that the structure was identical
> to Subset Sum -- include or exclude each element.
>
> But the operation changed:
>
> **Subset Sum used OR (any path to success).**
> **Count Subsets uses ADDITION (count ALL paths to success).**
>
> `||` became `+`.
> `true/false` became `integer count`.
> One symbol changed. The entire problem shifted
> from existence to counting."\_

---

This is the saga of **Count Subsets with Sum K (Memoization)**.

Given:
-   An array `arr` of non-negative integers.
-   A target sum `k`.

Return the **number of subsets** that sum to exactly `k`.

```
Input:  arr = [1, 2, 3, 3], k = 6
Output: 3   (subsets: {1,2,3}, {1,2,3}, {3,3})

Input:  arr = [1, 1, 1], k = 2
Output: 3   (subsets: {1,1} three ways -- pick any 2 of the 3 ones)

Input:  arr = [1, 2, 3], k = 7
Output: 0   (no subset sums to 7)
```

---

## 🧠 Building the Intuition -- From OR to ADDITION

In Subset Sum (existence):

```
solve(i, t) = solve(i+1, t-arr[i]) || solve(i+1, t)
              include              OR  exclude
```

"Does ANY path work?" → OR.

In Count Subsets (counting):

```
solve(i, t) = solve(i+1, t-arr[i]) + solve(i+1, t)
              include              +   exclude
```

"How many paths work?" → ADD.

The include path gives some number of valid subsets.
The exclude path gives some number of valid subsets.
The total = the sum of both.

**Why addition?**

Because the subsets from including element `i`
are COMPLETELY DIFFERENT from the subsets excluding element `i`.
They don't overlap. So the total count = sum of both counts.

Same logic as Climbing Stairs:
ways to reach step `n` = ways from `n-1` + ways from `n-2`.
Two separate sets of paths. Add them.

---

## 🧠 The Base Cases -- What Changes?

**Target becomes 0:**
We've found a valid subset. Return `1` (one way found).

In Subset Sum, this returned `true`.
Here, it returns `1` -- one valid subset counted.

**Past the last element (`index == n`) and target > 0:**
No more elements. Target not met. Return `0` (zero ways).

In Subset Sum, this returned `false`.
Here, it returns `0` -- no valid subsets.

---

## ⚠️ Handling Zeros in the Array

If the array contains zeros, things get tricky.

A zero can be included or excluded without changing the sum.
Each zero DOUBLES the number of valid subsets.

Example: `arr = [0, 0, 1], k = 1`.
Subsets summing to 1: {1}, {0,1}, {0,1}, {0,0,1} → 4 subsets.

The recursion handles this naturally:
when `arr[i] = 0`, including it doesn't change the target,
so both include and exclude paths lead to the same remaining target.
Both contribute their counts. The zeros multiply the count.

---

### 📜 The Scroll of the Counting Ways

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🎒 Setting Up the Cache

```cpp
int countSubsets(vector<int>& arr, int k) {
    int n = arr.size();
    vector<vector<int>> memo(n, vector<int>(k + 1, -1));
```

`memo[index][remaining]` = number of subsets from `arr[index..n-1]`
that sum to `remaining`.
`-1` = not yet computed.

---

```cpp
    return solve(0, k, arr, memo);
}
```

Start at element 0 with the full target.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int index, int remaining, vector<int>& arr,
          vector<vector<int>>& memo) {
```

"How many subsets from `arr[index..n-1]` sum to `remaining`?"

---

### 🎯 "Target met!"

```cpp
    if (remaining == 0) return 1;
```

The remaining target is 0.
The elements already picked form a valid subset.
Count it: return 1.

Why 1 and not `true`?
Because we're COUNTING. Each successful path contributes 1 to the total.

---

### 🛑 "No more elements"

```cpp
    if (index == (int)arr.size()) return 0;
```

Past the last element. Target not met.
No valid subsets from here. Return 0.

---

### 📖 Check the cache

```cpp
    if (memo[index][remaining] != -1) return memo[index][remaining];
```

---

### 🎒 Include This Element

```cpp
    int include = 0;
    if (arr[index] <= remaining) {
        include = solve(index + 1, remaining - arr[index], arr, memo);
    }
```

If the element fits → include it.
The remaining target shrinks.
`include` = number of valid subsets that USE this element.

---

### 🚶 Exclude This Element

```cpp
    int exclude = solve(index + 1, remaining, arr, memo);
```

Skip this element. Target unchanged.
`exclude` = number of valid subsets that DON'T use this element.

---

### ➕ Add Both Counts

```cpp
    memo[index][remaining] = include + exclude;
    return memo[index][remaining];
}
```

The total count = subsets that include this element
PLUS subsets that exclude it.

**This is the ONLY line that differs from Subset Sum.**
Subset Sum: `include || exclude` (OR).
Count Subsets: `include + exclude` (ADD).

One operator changed. Everything else is identical.

---

### 🎺 The Trial of the Counting Ways

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 3};
    cout << countSubsets(a1, 6) << endl; // expected: 3

    vector<int> a2 = {1, 1, 1};
    cout << countSubsets(a2, 2) << endl; // expected: 3

    vector<int> a3 = {1, 2, 3};
    cout << countSubsets(a3, 7) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for arr = [1, 2, 3, 3], k = 6:**

```
solve(0, 6): "How many subsets of [1,2,3,3] sum to 6?"
  INCLUDE 1: solve(1, 5) "subsets of [2,3,3] summing to 5"
    INCLUDE 2: solve(2, 3) "subsets of [3,3] summing to 3"
      INCLUDE 3: solve(3, 0) → remaining==0 → 1 ✓
      EXCLUDE 3: solve(3, 3) "subsets of [3] summing to 3"
        INCLUDE 3: solve(4, 0) → 1 ✓
        EXCLUDE 3: solve(4, 3) → index==4 → 0
      memo[3][3] = 1 + 0 = 1
    memo[2][3] = 1 + 1 = 2
    EXCLUDE 2: solve(2, 5) "subsets of [3,3] summing to 5"
      INCLUDE 3: solve(3, 2) "subsets of [3] summing to 2"
        INCLUDE 3: 3 > 2, can't.
        EXCLUDE 3: solve(4, 2) → 0
      memo[3][2] = 0
      EXCLUDE 3: solve(3, 5) "subsets of [3] summing to 5"
        INCLUDE 3: solve(4, 2) → 0
        EXCLUDE 3: solve(4, 5) → 0
      memo[3][5] = 0
    memo[2][5] = 0 + 0 = 0
  memo[1][5] = 2 + 0 = 2

  EXCLUDE 1: solve(1, 6) "subsets of [2,3,3] summing to 6"
    INCLUDE 2: solve(2, 4) "subsets of [3,3] summing to 4"
      ... → 0 (no subset of [3,3] sums to 4)
    EXCLUDE 2: solve(2, 6) "subsets of [3,3] summing to 6"
      INCLUDE 3: solve(3, 3) → 1 (cached!)
      EXCLUDE 3: solve(3, 6) → 0
    memo[2][6] = 1 + 0 = 1
  memo[1][6] = 0 + 1 = 1

memo[0][6] = 2 + 1 = 3
```

**Answer: 3** ✓

The 3 subsets:
1. {1, 2, 3} (first 3) = 6
2. {1, 2, 3} (second 3) = 6
3. {3, 3} = 6

---

**Trace for arr = [1, 1, 1], k = 2:**

Each pair of 1s sums to 2. There are C(3,2) = 3 pairs.

```
solve(0, 2):
  include 1: solve(1, 1)
    include 1: solve(2, 0) → 1
    exclude 1: solve(2, 1)
      include 1: solve(3, 0) → 1
      exclude 1: solve(3, 1) → 0
    memo[2][1] = 1
  memo[1][1] = 1 + 1 = 2
  exclude 1: solve(1, 2)
    include 1: solve(2, 1) → 1 (cached!)
    exclude 1: solve(2, 2)
      include 1: solve(3, 1) → 0
      exclude 1: solve(3, 2) → 0
    memo[2][2] = 0
  memo[1][2] = 1 + 0 = 1
memo[0][2] = 2 + 1 = 3
```

**Answer: 3** ✓

---

## 🔍 The One Operator That Changed Everything

| Problem              | Combine operation | Return type | Base (target=0) | Base (index=n) |
| -------------------- | ----------------- | ----------- | --------------- | -------------- |
| Subset Sum (exist)   | `\|\|` (OR)       | bool        | true            | false          |
| Count Subsets (count) | `+` (ADD)         | int         | 1               | 0              |
| Min Subset Sum Diff  | scan last row     | int         | --              | --             |

Same skeleton. Same include/exclude. One operator changed.

---

## 🔍 When Modulo Is Needed

For large arrays, the count can be astronomically large.
Many problems ask for the answer modulo `10⁹ + 7`.

```cpp
const int MOD = 1e9 + 7;
memo[index][remaining] = (include + exclude) % MOD;
```

Always check if the problem requires modular arithmetic.

---

### 🧠 Memory of the Counting Ways (Memoization) Law

-   **Same as Subset Sum** but `||` becomes `+`
-   **Include:** `solve(index+1, remaining - arr[index])` if element fits
-   **Exclude:** `solve(index+1, remaining)`
-   **Combine:** `include + exclude` (ADD, not OR)
-   **Base:** `remaining == 0` → 1 (one valid subset). `index == n` → 0.
-   **Zeros in array:** handled naturally (both paths have same remaining)
-   **Modulo** if needed: `(include + exclude) % MOD`
-   **Time:** O(N × K). **Space:** O(N × K).

Thus is remembered the saga of **Count Subsets with Sum K (Memoization)**,
where the Oracle changed one symbol --
OR became PLUS --
and the question shifted from "does it exist?"
to "how many exist?" --
counting every valid subset
by adding the include path and the exclude path
at every element. 🎒🔢🧠✨
