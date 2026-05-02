## ⚖️🎯🧠 _The Closest Split (Memoization): The Minimum Subset Sum Difference Saga_

> \_"The Oracle was given an array of positive integers.
>
> She was commanded:
>
> **'Split this array into two subsets.
> Minimize the absolute difference between their sums.'**
>
> In Partition Equal Subset Sum, the Oracle asked:
> 'Can I split into two EQUAL halves?'
> That was a yes/no question.
>
> Now the question changed:
> 'What is the CLOSEST I can get to an equal split?'
>
> The Oracle realized:
>
> **If the total sum is `S`,
> and one subset sums to `s1`,
> the other sums to `S - s1`.
> The difference is `|s1 - (S - s1)| = |2*s1 - S|`.**
>
> To minimize this difference,
> `s1` should be as close to `S/2` as possible.
>
> **Find the largest achievable subset sum
> that does NOT exceed `S/2`.**
>
> That's Subset Sum DP --
> but instead of asking 'can I hit exactly S/2?',
> we ask 'what's the closest I can get to S/2?'
>
> Run Subset Sum for all targets 0 to S/2.
> Find the largest target that returns true.
> The minimum difference = `S - 2 * that target`."\_

---

This is the saga of **Minimum Subset Sum Difference (Memoization)**.

Given an array of positive integers:
-   Split into two subsets.
-   Minimize `|sum of subset 1 - sum of subset 2|`.

```
Input:  arr = [1, 6, 11, 5]
Output: 1   (subsets {1,5,6}=12 and {11}=11, diff = 1)

Input:  arr = [1, 2, 3, 4]
Output: 0   (subsets {1,4}=5 and {2,3}=5, diff = 0)

Input:  arr = [3, 1, 5, 2, 8]
Output: 1   (subsets {3,5,2}=10 and {1,8}=9, diff = 1)
```

---

## 🧠 Building the Intuition -- Why S/2?

If total = `S` and one subset sums to `s1`:

```
Other subset = S - s1
Difference = |s1 - (S - s1)| = |2*s1 - S|
```

To minimize `|2*s1 - S|`, we want `s1` as close to `S/2` as possible.

If `s1 = S/2` exactly → difference = 0. Perfect split.
If `s1 = S/2 - 1` → difference = 2. Close.
If `s1 = S/2 - 5` → difference = 10. Not great.

**So the problem becomes:**
Find the largest `s1 ≤ S/2` that is achievable as a subset sum.

**How to find it?**
Run Subset Sum DP for target = `S/2`.
The dp table tells us which sums from 0 to S/2 are achievable.
Pick the largest achievable sum.
Minimum difference = `S - 2 * that sum`.

---

## 🧠 The Two-Step Approach

```
Step 1: Build the Subset Sum table for target = S/2.
        dp[i][t] = can we achieve sum t using elements 0 to i?

Step 2: Scan the last row from t = S/2 down to 0.
        The first t where dp[n-1][t] == true is the closest achievable sum.
        Minimum difference = S - 2*t.
```

---

### 📜 The Scroll of the Closest Split

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## ⚖️ The Memoization Approach

We'll build the Subset Sum table using memoization,
then scan for the best achievable sum.

### Compute the Total and Target

```cpp
int minSubsetSumDifference(vector<int>& arr) {
    int n = arr.size();
    int total = accumulate(arr.begin(), arr.end(), 0);
    int target = total / 2;
```

`target = total / 2` -- we want `s1` as close to this as possible.

---

### Build the Subset Sum Table

```cpp
    vector<vector<int>> memo(n, vector<int>(target + 1, -1));

    // Fill the memo table by querying every possible sum
    for (int t = 0; t <= target; t++) {
        solve(n - 1, t, arr, memo);
    }
```

We call `solve` for every possible target from 0 to `target`.
This fills the memo table completely.

Alternatively, we can just build the tabulation table directly
(which is cleaner -- see the tab version).

But for the memoization approach, we need to know
which sums are achievable. So we query all of them.

---

### The Subset Sum Recursion (Same as Before)

```cpp
bool solve(int index, int remaining, vector<int>& arr,
           vector<vector<int>>& memo) {
    if (remaining == 0) return true;
    if (index < 0) return false;
    if (memo[index][remaining] != -1) return memo[index][remaining] == 1;

    bool include = false;
    if (arr[index] <= remaining) {
        include = solve(index - 1, remaining - arr[index], arr, memo);
    }
    bool exclude = solve(index - 1, remaining, arr, memo);

    bool result = include || exclude;
    memo[index][remaining] = result ? 1 : 0;
    return result;
}
```

Standard Subset Sum memoization. Nothing new here.

---

### Find the Best Achievable Sum

```cpp
    int minDiff = total;

    for (int t = target; t >= 0; t--) {
        if (memo[n - 1][t] == 1) {
            minDiff = total - 2 * t;
            break;
        }
    }
    return minDiff;
}
```

Scan from `target` (= S/2) downward.
The FIRST achievable sum we find is the closest to S/2.

`minDiff = total - 2 * t`:
-   If `t = S/2` → diff = 0. Perfect split.
-   If `t = S/2 - 1` → diff = 2.
-   And so on.

Why scan downward? Because we want the LARGEST achievable sum ≤ S/2.
The first `true` we hit scanning from the top is the answer.

---

### 🎺 The Trial of the Closest Split

```cpp
int main() {
    vector<int> a1 = {1, 6, 11, 5};
    cout << minSubsetSumDifference(a1) << endl; // expected: 1

    vector<int> a2 = {1, 2, 3, 4};
    cout << minSubsetSumDifference(a2) << endl; // expected: 0

    vector<int> a3 = {3, 1, 5, 2, 8};
    cout << minSubsetSumDifference(a3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for arr = [1, 6, 11, 5]:**

```
total = 23. target = 23/2 = 11.
```

Achievable sums from {1, 6, 11, 5}:
-   {} = 0 ✓
-   {1} = 1 ✓
-   {5} = 5 ✓
-   {6} = 6 ✓
-   {1,5} = 6 ✓
-   {1,6} = 7 ✓
-   {11} = 11 ✓
-   {1,5,6} = 12 (> 11, not in our range)
-   ...

Scan from t=11 downward:
-   t=11: achievable? {11} = 11. YES!

`minDiff = 23 - 2*11 = 23 - 22 = 1`.

**Answer: 1** ✓

Subsets: {11} = 11 and {1, 6, 5} = 12. Difference = |11 - 12| = 1.

---

**Trace for arr = [1, 2, 3, 4]:**

```
total = 10. target = 5.
```

Achievable sums: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10.
(With 4 elements, many sums are achievable.)

t=5: {1,4} = 5 or {2,3} = 5. YES!

`minDiff = 10 - 2*5 = 0`.

**Answer: 0** ✓ -- perfect split exists.

---

**Trace for arr = [3, 1, 5, 2, 8]:**

```
total = 19. target = 9.
```

Achievable sums ≤ 9: ..., 8 ({8}), 9 ({1,8} or {3,1,5}).

t=9: {3,1,5} = 9. YES!

`minDiff = 19 - 2*9 = 1`.

**Answer: 1** ✓

---

## 🔍 The Key Insight -- From Exact Match to Closest Match

| Problem                    | Question                          | How to answer              |
| -------------------------- | --------------------------------- | -------------------------- |
| Subset Sum                 | Can I hit target exactly?         | `dp[n-1][target]`         |
| Partition Equal Subset     | Can I hit total/2 exactly?        | `dp[n-1][total/2]`        |
| Min Subset Sum Difference  | What's the closest to total/2?    | Scan `dp[n-1][t]` from total/2 down |

Same DP table. Different question asked of it.

Subset Sum gives a YES/NO for one target.
Min Difference scans the ENTIRE last row for the best achievable target.

---

## 🔍 Why `S - 2*t` Is the Difference

```
s1 = t           (one subset's sum)
s2 = S - t       (other subset's sum)
diff = |s1 - s2| = |t - (S - t)| = |2t - S| = S - 2t  (since t ≤ S/2)
```

When `t ≤ S/2`, `2t ≤ S`, so `S - 2t ≥ 0`. No absolute value needed.

---

### 🧠 Memory of the Closest Split (Memoization) Law

-   **Reduce:** find largest achievable sum ≤ total/2
-   **Build Subset Sum table** for target = total/2
-   **Scan last row** from total/2 downward for first `true`
-   **Minimum difference** = `total - 2 * bestAchievableSum`
-   **Same Subset Sum DP** -- just a different question asked of the table
-   **Time:** O(N × total/2). **Space:** O(N × total/2).

Thus is remembered the saga of **Minimum Subset Sum Difference (Memoization)**,
where the Oracle built the Subset Sum table
and then asked not "can I hit the target?"
but "what's the closest I can get?" --
scanning the table for the largest achievable sum
within half the total --
and the difference between that sum and its complement
was the minimum possible split. ⚖️🎯🧠✨
