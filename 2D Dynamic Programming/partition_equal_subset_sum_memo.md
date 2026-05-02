## ⚖️🧠 _The Perfect Split (Memoization): The Partition Equal Subset Sum Saga_

> \_"The Oracle was given an array of positive integers.
>
> She was commanded:
>
> **'Can you split this array into TWO subsets
> such that both subsets have the SAME sum?'**
>
> The Oracle thought carefully.
>
> If the total sum of the array is `S`,
> then each subset must sum to exactly `S / 2`.
>
> **If `S` is odd -- impossible.**
> You can't split an odd number into two equal halves.
> Return false immediately.
>
> **If `S` is even -- the problem reduces to:**
> Can I find a subset that sums to exactly `S / 2`?
>
> That's **Subset Sum Equal to Target** with `target = S / 2`.
>
> The Oracle had already solved that problem.
> She just needed to compute the total,
> check if it's even,
> and call Subset Sum.
>
> **The entire problem was a REDUCTION --
> Partition Equal Subset Sum reduces to Subset Sum.**"\_

---

This is the saga of **Partition Equal Subset Sum (Memoization)**.

Given an array `nums` of positive integers:
-   Can you partition it into two subsets with **equal sum**?

```
Input:  nums = [1, 5, 11, 5]
Output: true   (subsets: {1,5,5} and {11}, both sum to 11)

Input:  nums = [1, 2, 3, 5]
Output: false  (total = 11, odd → impossible)

Input:  nums = [1, 2, 5]
Output: false  (total = 8, target = 4, no subset sums to 4)
```

---

## 🧠 Building the Intuition -- The Reduction

**Step 1: Compute the total sum.**

```
total = sum of all elements
```

**Step 2: Check if the total is even.**

If `total` is odd → return `false`.
You can't split an odd number into two equal integer halves.
No further computation needed.

Why? If total = 11, each half would need to be 5.5.
But all elements are integers. Integer subsets can't sum to 5.5.

**Step 3: Find a subset that sums to `total / 2`.**

If such a subset exists, the remaining elements
automatically sum to `total - total/2 = total/2`.
Both halves are equal. Partition found.

This is exactly **Subset Sum with target = total / 2**.

---

## 🧠 Why This Reduction Works

If subset A sums to `total / 2`,
then subset B (everything NOT in A) sums to `total - total/2 = total/2`.

Both subsets have the same sum. Partition achieved.

We don't need to find BOTH subsets.
Finding ONE subset with sum `total / 2` is sufficient.
The other subset is automatically correct.

---

### 📜 The Scroll of the Perfect Split

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## ⚖️ The Wrapper -- Reduce to Subset Sum

```cpp
bool canPartition(vector<int>& nums) {
    int total = accumulate(nums.begin(), nums.end(), 0);
```

Compute the total sum of all elements.

---

### 🛑 "Is the total odd?"

```cpp
    if (total % 2 != 0) return false;
```

Odd total → impossible to split into two equal halves.
Return false immediately. No DP needed.

This is the FIRST thing to check.
It eliminates half of all inputs in O(1).

---

### 🎯 "Can I find a subset summing to half?"

```cpp
    int target = total / 2;
    int n = nums.size();
    vector<vector<int>> memo(n, vector<int>(target + 1, -1));
    return solve(0, target, nums, memo);
}
```

The problem is now: Subset Sum with `target = total / 2`.
Identical to the Subset Sum saga.

---

## 🔮 The Subset Sum Recursion (Same as Before)

```cpp
bool solve(int index, int remaining, vector<int>& nums,
           vector<vector<int>>& memo) {
```

"Can I find a subset from `nums[index..n-1]` summing to `remaining`?"

---

### 🎯 "Target met!"

```cpp
    if (remaining == 0) return true;
```

Found a subset summing to `total / 2`. The partition exists.

---

### 🛑 "No more elements"

```cpp
    if (index == (int)nums.size()) return false;
```

Past the last element. Target not met. Failed.

---

### 📖 Check the cache

```cpp
    if (memo[index][remaining] != -1) {
        return memo[index][remaining] == 1;
    }
```

---

### 🎒 Include or Exclude

```cpp
    bool include = false;
    if (nums[index] <= remaining) {
        include = solve(index + 1, remaining - nums[index], nums, memo);
    }
```

**Include this element:** target shrinks. Continue with the rest.
Only if the element fits (doesn't overshoot).

---

```cpp
    bool exclude = solve(index + 1, remaining, nums, memo);
```

**Exclude this element:** target stays. Continue with the rest.

---

### ✅ Either works → true

```cpp
    bool result = include || exclude;
    memo[index][remaining] = result ? 1 : 0;
    return result;
}
```

---

### 🎺 The Trial of the Perfect Split

```cpp
int main() {
    vector<int> n1 = {1, 5, 11, 5};
    cout << canPartition(n1) << endl; // expected: 1 (true)

    vector<int> n2 = {1, 2, 3, 5};
    cout << canPartition(n2) << endl; // expected: 0 (false)

    vector<int> n3 = {1, 2, 5};
    cout << canPartition(n3) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full trace for nums = [1, 5, 11, 5]:**

```
total = 1 + 5 + 11 + 5 = 22
22 is even → target = 11
```

Now: can any subset of [1, 5, 11, 5] sum to 11?

```
solve(0, 11): include 1 → solve(1, 10)
  solve(1, 10): include 5 → solve(2, 5)
    solve(2, 5): include 11? 11 > 5, can't.
                 exclude 11 → solve(3, 5)
      solve(3, 5): include 5 → solve(4, 0) → remaining==0 → TRUE! 🎯
```

Subset found: {1, 5, 5} = 11. Remaining: {11} = 11. Equal!

**Answer: true** ✓

---

**Trace for nums = [1, 2, 3, 5]:**

```
total = 11. Odd! → return false immediately.
```

**Answer: false** ✓

No DP computation needed. The odd check saved all the work.

---

**Trace for nums = [1, 2, 5]:**

```
total = 8. Even → target = 4.
Can any subset of [1, 2, 5] sum to 4?

solve(0, 4): include 1 → solve(1, 3)
  solve(1, 3): include 2 → solve(2, 1)
    solve(2, 1): 5 > 1, can't include. exclude → solve(3, 1) → false.
  solve(1, 3): exclude 2 → solve(2, 3)
    solve(2, 3): 5 > 3, can't include. exclude → solve(3, 3) → false.
  memo[1][3] = false.
solve(0, 4): exclude 1 → solve(1, 4)
  solve(1, 4): include 2 → solve(2, 2)
    solve(2, 2): 5 > 2, can't. exclude → solve(3, 2) → false.
  solve(1, 4): exclude 2 → solve(2, 4)
    solve(2, 4): 5 > 4, can't. exclude → solve(3, 4) → false.
  memo[1][4] = false.
memo[0][4] = false.
```

**Answer: false** ✓

No subset of {1, 2, 5} sums to 4.

---

## 🔍 The Reduction Pattern -- Complex Problem → Known Problem

```
Partition Equal Subset Sum
  ↓ reduce
Subset Sum with target = total / 2
  ↓ solve
Standard include/exclude DP
```

The hard part is seeing the REDUCTION.
Once you realize "partition = subset sum on half the total,"
the code writes itself.

This reduction pattern appears often:
-   **Partition Equal Subset** → Subset Sum on total/2.
-   **Target Sum (+/-)** → Subset Sum on (total + target) / 2.
-   **Last Stone Weight II** → Subset Sum on total/2.

---

## 🔍 Why Check Odd First?

It's a free O(1) check that eliminates half of all inputs.
Without it, the DP would run on impossible inputs
and return false after O(N × target) work.

Always check for impossible cases BEFORE running expensive algorithms.

---

### 🧠 Memory of the Perfect Split (Memoization) Law

-   **Reduce:** Partition = Subset Sum with `target = total / 2`
-   **Odd total → false immediately** (can't split odd into two equal halves)
-   **Even total → Subset Sum on `total / 2`**
-   **Subset Sum:** include/exclude each element, OR the results
-   **Finding ONE subset with sum total/2 is enough** -- the other is automatic
-   **Time:** O(N × total/2). **Space:** O(N × total/2).
-   **Edge cases:**
    -   Odd total → false (instant)
    -   Single element → false (can't split one element)
    -   All equal elements, even count → true
    -   One element equals total/2 → true

Thus is remembered the saga of **Partition Equal Subset Sum (Memoization)**,
where the Oracle did not solve a new problem
but reduced it to one she already knew --
computing the total, checking for oddness,
then asking "can any subset hit half?" --
and the Subset Sum machinery answered
whether the perfect split existed. ⚖️🧠✨
