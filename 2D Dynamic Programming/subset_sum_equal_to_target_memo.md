## 🎒✅🧠 _The Knapsack of Exact Weight (Memoization): The Subset Sum Equal to Target Saga_

> \_"The Oracle was given a bag of stones,
> each with a different weight.
>
> She was commanded:
>
> **'Can you pick some stones from this bag
> such that their total weight equals EXACTLY the target?'**
>
> Not all stones. Not the closest sum.
> EXACTLY the target. Yes or no.
>
> The Oracle thought about each stone one by one:
>
> **For every stone, I have two choices:
> INCLUDE it in my selection, or EXCLUDE it.**
>
> If I INCLUDE this stone,
> the remaining target shrinks by this stone's weight.
> I need to find the reduced target from the remaining stones.
>
> If I EXCLUDE this stone,
> the target stays the same.
> I need to find the full target from the remaining stones.
>
> If EITHER choice leads to success -- the answer is YES.
>
> This was the **pick or skip** pattern again --
> the same skeleton as House Robber --
> but instead of maximizing gold,
> the Oracle was asking a YES/NO question:
> can I hit the target exactly?"\_

---

This is the saga of **Subset Sum Equal to Target (Memoization)**.

Given:
-   An array `arr` of positive integers.
-   A target sum `target`.

Return `true` if any subset of `arr` sums to exactly `target`.

```
Input:  arr = [1, 2, 3, 4], target = 4
Output: true   (subsets: {4} or {1,3})

Input:  arr = [2, 3, 7, 8], target = 14
Output: true   (subset: {7, 3, ...} wait -- {3, 7} = 10, {2, 3, 7} = 12, {7, 8} = 15... 
                actually {2, 3, 8} = 13... hmm. Let me check: no subset sums to 14? 
                Actually {2+3+8} = 13, {3+7} = 10, {2+7} = 9, {7+8} = 15, {2+3+7+8} = 20.
                Hmm, none sum to 14. Let me use a better example.)

Input:  arr = [3, 34, 4, 12, 5, 2], target = 9
Output: true   (subset: {4, 5} = 9)

Input:  arr = [3, 34, 4, 12, 5, 2], target = 30
Output: false  (no subset sums to 30)
```

---

## 🧠 Building the Intuition -- The Choice at Every Stone

Imagine the stones laid out in a line: `arr[0], arr[1], ..., arr[n-1]`.

The Oracle walks the line from left to right.
At each stone, she faces the same question:

> **"Should I INCLUDE this stone in my selection, or EXCLUDE it?"**

**If she INCLUDES stone `i`:**
She takes it. The remaining target shrinks.
New target = `target - arr[i]`.
She needs to find this new target from stones `i+1` to `n-1`.

**If she EXCLUDES stone `i`:**
She leaves it. The target stays the same.
She needs to find the full target from stones `i+1` to `n-1`.

**If EITHER choice succeeds -- the answer is true.**

```
solve(index, target) = solve(index+1, target - arr[index])   ← include
                    OR solve(index+1, target)                  ← exclude
```

**Base cases:**

**Target becomes 0:**
We've found a subset that sums to exactly the target.
Return `true`. Success!

**We've gone past the last stone (`index == n`) and target > 0:**
No more stones to pick. Target not met.
Return `false`. Failure.

---

## 🧠 Why the State Is 2D

The state needs TWO pieces of information:

1. **Which stone are we deciding about?** (`index`)
2. **How much target is left?** (`target`)

The same stone at different remaining targets
has different answers. So both dimensions matter.

```
solve(index, target) → true or false
```

Total states: `n × (target + 1)`.

---

## 🔍 Why Memoization?

Without caching, the recursion tree branches at every stone.
Two choices per stone → 2ⁿ paths. Exponential.

But many paths reach the same `(index, target)` state.
Memoization ensures each state is solved only once.

With memoization: O(n × target) time.

---

### 📜 The Scroll of the Exact Weight

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🎒 Setting Up the Cache

```cpp
bool subsetSumToTarget(vector<int>& arr, int target) {
    int n = arr.size();
    vector<vector<int>> memo(n, vector<int>(target + 1, -1));
```

2D cache: `memo[index][remaining]`.
`-1` = not yet computed. `0` = false. `1` = true.

Why `int` and not `bool`? Because we need THREE states:
not computed (-1), false (0), true (1).
A `bool` can only hold two.

---

```cpp
    return solve(0, target, arr, memo);
}
```

Start at stone 0 with the full target.

---

## 🔮 The Recursive Thinking

```cpp
bool solve(int index, int remaining, vector<int>& arr,
           vector<vector<int>>& memo) {
```

"Can I find a subset from stones `index` to `n-1`
that sums to exactly `remaining`?"

---

### 🎯 "The target is met!"

```cpp
    if (remaining == 0) return true;
```

The remaining target is 0. The stones already picked sum to the target.
Success! Return true.

Why is this checked FIRST, before the index check?
Because even if we've looked at all stones (`index == n`),
if the remaining is 0, we've succeeded.
Checking `remaining == 0` first catches this.

---

### 🛑 "No more stones, but target not met"

```cpp
    if (index == arr.size()) return false;
```

We've gone past the last stone.
The remaining target is still > 0.
No more stones to pick. Failure.

---

### 📖 Check the cache

```cpp
    if (memo[index][remaining] != -1) {
        return memo[index][remaining] == 1;
    }
```

Already solved this (index, remaining) pair?
Return the cached answer.

---

### 🎒 Choice 1 -- INCLUDE This Stone

```cpp
    bool include = false;
    if (arr[index] <= remaining) {
        include = solve(index + 1, remaining - arr[index], arr, memo);
    }
```

**Can I even include this stone?**
Only if `arr[index] <= remaining`.
If the stone is heavier than the remaining target,
including it would overshoot. Skip this choice.

**If I include it:**
The remaining target shrinks by this stone's weight.
Continue with the next stone and the reduced target.

Why the `if` guard?
Without it, `remaining - arr[index]` could go negative.
A negative target makes no sense.
The guard prevents exploring an impossible branch.

---

### 🚶 Choice 2 -- EXCLUDE This Stone

```cpp
    bool exclude = solve(index + 1, remaining, arr, memo);
```

Leave this stone behind.
The target stays the same.
Continue with the next stone.

No guard needed -- excluding is always valid.

---

### ✅ Either Choice Succeeds → True

```cpp
    bool result = include || exclude;
    memo[index][remaining] = result ? 1 : 0;
    return result;
}
```

If INCLUDING this stone leads to a valid subset → true.
If EXCLUDING this stone leads to a valid subset → true.
If NEITHER works → false.

`||` (OR) because we need just ONE successful path.

Cache the result as 1 (true) or 0 (false).

---

### 🎺 The Trial of the Exact Weight

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 4};
    cout << subsetSumToTarget(a1, 4) << endl; // expected: 1 (true)

    vector<int> a2 = {3, 34, 4, 12, 5, 2};
    cout << subsetSumToTarget(a2, 9) << endl; // expected: 1 (true)

    cout << subsetSumToTarget(a2, 30) << endl; // expected: 0 (false)

    return 0;
}
```

---

**Full trace for arr = [1, 2, 3, 4], target = 4:**

```
solve(0, 4): "Can I hit 4 from stones [1,2,3,4]?"
  INCLUDE 1: solve(1, 3) "Can I hit 3 from [2,3,4]?"
    INCLUDE 2: solve(2, 1) "Can I hit 1 from [3,4]?"
      INCLUDE 3: 3 > 1, can't include.
      EXCLUDE 3: solve(3, 1) "Can I hit 1 from [4]?"
        INCLUDE 4: 4 > 1, can't include.
        EXCLUDE 4: solve(4, 1) → index==4, remaining>0 → false
      memo[3][1] = false
    memo[2][1] = false
    EXCLUDE 2: solve(2, 3) "Can I hit 3 from [3,4]?"
      INCLUDE 3: solve(3, 0) → remaining==0 → TRUE! 🎯
    memo[2][3] = true
  memo[1][3] = true (include 2 failed, exclude 2 succeeded)
  → include path succeeded!
memo[0][4] = true
```

**Answer: true** ✓

Subset found: {1, 3} → 1 + 3 = 4.

But there's also {4} → solve(0,4) exclude 1 → solve(1,4) exclude 2 → solve(2,4) exclude 3 → solve(3,4) include 4 → solve(4,0) → true!

Multiple valid subsets exist. The DP finds ANY one.

---

**Trace for arr = [3, 34, 4, 12, 5, 2], target = 9:**

The recursion explores many branches.
Eventually finds: include 4 (remaining=5), include 5 (remaining=0) → true!

Subset: {4, 5} = 9. **Answer: true** ✓

---

**Trace for target = 30:**

No combination of {3, 34, 4, 12, 5, 2} sums to 30.
(Total = 60. Closest subsets: {3+4+12+5+2}=26, {34}=34, {3+4+12+5}=24...)

Every branch returns false. **Answer: false** ✓

---

## 🔍 The "Include or Exclude" Pattern -- The 0/1 Knapsack Family

This is the foundation of the **0/1 Knapsack** family:

```
At each item:
  Include: take it (reduce capacity/target) + solve remaining
  Exclude: skip it (keep capacity/target) + solve remaining
  Combine with OR (existence), MAX (optimization), or + (counting)
```

| Problem              | Question          | Combine with |
| -------------------- | ----------------- | ------------ |
| Subset Sum           | Can I hit target? | OR (any path works) |
| 0/1 Knapsack         | Max value ≤ capacity? | MAX |
| Count Subsets with Sum | How many subsets? | + (addition) |
| Partition Equal Subset | Can I split 50/50? | Subset Sum on total/2 |
| Target Sum (+/-)     | How many ways to hit target? | + (addition) |

Master Subset Sum and the entire family opens up.

---

## 🔍 How to Arrive at This Solution From Scratch

1. **Identify the choice:** include or exclude each element.
2. **Define the state:** `(index, remaining target)`.
3. **Write the recurrence:**
   - Include: `solve(index+1, remaining - arr[index])` if `arr[index] <= remaining`.
   - Exclude: `solve(index+1, remaining)`.
   - Result: `include || exclude`.
4. **Base cases:** `remaining == 0` → true. `index == n` → false.
5. **Memoize:** 2D cache `memo[index][remaining]`.

---

### 🧠 Memory of the Exact Weight (Memoization) Law

-   **State:** `(index, remaining)` -- which stone, how much target left
-   **Include:** `solve(index+1, remaining - arr[index])` if stone fits
-   **Exclude:** `solve(index+1, remaining)`
-   **Combine:** `include || exclude` (OR -- any path to success)
-   **Base:** `remaining == 0` → true. `index == n` → false.
-   **Guard:** `arr[index] <= remaining` before including (can't overshoot)
-   **This is the 0/1 Knapsack pattern** -- include or exclude each item
-   **Time:** O(N × target). **Space:** O(N × target).

Thus is remembered the saga of **Subset Sum (Memoization)**,
where the Oracle walked the line of stones,
at each one asking "include or exclude?" --
including shrunk the target,
excluding left it unchanged --
and if EITHER path led to a target of zero,
the answer was yes --
a subset existed that weighed exactly right. 🎒✅🧠✨
