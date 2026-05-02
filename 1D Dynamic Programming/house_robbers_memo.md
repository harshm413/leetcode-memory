## 🏠🧠 _The Thief's Dilemma (Memoization): The House Robber Saga_

> \_"Along a street of houses,
> each house held a stash of gold.
> A thief planned to rob the street.
>
> But there was a rule:
>
> **No two adjacent houses can be robbed.**
>
> If the thief robbed house `i`,
> he could NOT rob house `i-1` or house `i+1`.
> The alarm system would trigger.
>
> The Oracle was commanded:
>
> **'What is the maximum gold the thief can steal?'"\_

---

This is the saga of **House Robber / Maximum Sum of Non-Adjacent Elements (Memoization)**.

Given an array `nums` where `nums[i]` is the gold in house `i`:
-   You cannot rob two adjacent houses.
-   Find the **maximum** gold you can steal.

```
Input:  nums = [1, 2, 3, 1]
Output: 4   (rob house 0 and house 2: 1+3=4)

Input:  nums = [2, 7, 9, 3, 1]
Output: 12  (rob house 0, 2, 4: 2+9+1=12)

Input:  nums = [2, 1, 1, 2]
Output: 4   (rob house 0 and house 3: 2+2=4)
```

---

## 🧠 Building the Intuition -- How Would the Thief Think?

Imagine the thief standing at the LAST house on the street,
looking backward at all the houses behind him.

He asks himself one question:

> **"Should I rob THIS house, or should I skip it?"**

If he **robs** this house, he gets its gold.
But then the house right next to it (the previous one) is OFF LIMITS.
The best he could have done before that is whatever he could steal
from all the houses up to TWO houses back.

If he **skips** this house, he gets nothing from it.
But the previous house is now AVAILABLE.
The best he could have done is whatever he could steal
from all the houses up to the previous one.

He picks whichever choice gives him more gold.

This is the **thief's dilemma at every house** --
rob and jump back two, or skip and stay one back.

---

## 🧠 Turning the Intuition into a Recurrence

Let's define `solve(i)` = the maximum gold the thief can steal
from houses `0` through `i`.

The thief at house `i` has two futures:

**Future 1 -- "I rob this house":**
I take `nums[i]`. I can't touch house `i-1`.
The best I could have done from houses `0` to `i-2` is `solve(i-2)`.
Total: `nums[i] + solve(i-2)`.

**Future 2 -- "I skip this house":**
I take nothing here. House `i-1` is free.
The best from houses `0` to `i-1` is `solve(i-1)`.
Total: `solve(i-1)`.

The thief is greedy for gold. He picks the richer future:

```
solve(i) = max(nums[i] + solve(i-2), solve(i-1))
```

Now we need a place to stop -- the base cases.

**What if the thief is at house 0?**
There's only one house. Rob it. `solve(0) = nums[0]`.

**What if the thief goes below house 0?**
He's gone past the street. No houses, no gold. `solve(-1) = 0`.
This happens when house 1 tries to look two back: `solve(1-2) = solve(-1) = 0`.

---

## 🧠 Why Memoization? -- The Overlapping Subproblems

If we just recurse naively, the same houses get reconsidered
over and over. `solve(3)` calls `solve(2)` and `solve(1)`.
`solve(2)` also calls `solve(1)`. That's `solve(1)` computed twice.
For large streets, this explodes exponentially.

The fix: **remember every answer the first time you compute it.**
Next time someone asks the same question -- hand them the cached answer.

That's memoization. A simple array `memo[i]` stores `solve(i)`.
Before computing, check the array. If it's there, return it.
If not, compute it, store it, then return.

---

### 📜 The Scroll of the Thief's Street

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏠 Setting Up the Cache

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    vector<int> memo(n, -1);
```

The thief prepares his notebook -- one slot per house.
`-1` means "I haven't figured out this house yet."
When he solves a house, he writes the answer here
so he never has to think about it again.

---

```cpp
    return solve(n - 1, nums, memo);
}
```

The thief starts his thinking from the LAST house
and works backward. "What's the most I can steal
from the entire street?"

---

## 🔮 The Thief's Recursive Thinking

```cpp
int solve(int i, vector<int>& nums, vector<int>& memo) {
```

The thief is standing at house `i`, looking backward,
asking: "What's the most gold I can get from houses 0 to i?"

---

### 🛑 "I've gone past the street"

```cpp
    if (i < 0) return 0;
```

The thief has walked past the beginning of the street.
There are no houses here. No gold to take.

This happens naturally when house 1 considers robbing:
it looks two back (`i-2 = -1`) and finds nothing.
Without this, the recursion would crash on a negative index.

---

### 🛑 "There's only the first house"

```cpp
    if (i == 0) return nums[0];
```

The thief is at the very first house. No choice to make.
One house, one stash. Take it.

---

### 📖 "Have I already figured this out?"

```cpp
    if (memo[i] != -1) return memo[i];
```

The thief checks his notebook. If he already solved this house
on a previous visit -- no need to think again.
Just read the answer and move on.

This is what turns exponential recursion into linear time.
Every house is solved at most once.

---

### 🏠 "What if I ROB this house?"

```cpp
    int pick = nums[i] + solve(i - 2, nums, memo);
```

The thief imagines robbing house `i`.
He pockets `nums[i]` gold.
But now house `i-1` is forbidden -- the alarm would sound.
So the best he can add to his haul is whatever he could steal
from houses `0` through `i-2`.

Why `i-2` and not `i-1`? Because robbing house `i`
makes house `i-1` untouchable. The next available house
is `i-2`. That's the "skip one" penalty of robbing.

---

### 🚶 "What if I SKIP this house?"

```cpp
    int skip = solve(i - 1, nums, memo);
```

The thief imagines walking past house `i` without touching it.
He takes nothing from this house.
But now house `i-1` is fully available -- no alarm, no penalty.
The best he can get is whatever he could steal
from houses `0` through `i-1`.

Why `i-1` and not `i-2`? Because skipping costs nothing.
The previous house is free. No penalty.

---

### ⚖️ "Which future is richer?"

```cpp
    memo[i] = max(pick, skip);
```

The thief compares both futures.
Rob and jump two back? Or skip and stay one back?
Whichever gives more gold -- that's the answer for this house.

He writes it in his notebook so he never reconsiders.

---

```cpp
    return memo[i];
}
```

---

### 🎺 The Trial of the Thief's Street

```cpp
int main() {
    vector<int> n1 = {1, 2, 3, 1};
    cout << rob(n1) << endl; // expected: 4

    vector<int> n2 = {2, 7, 9, 3, 1};
    cout << rob(n2) << endl; // expected: 12

    vector<int> n3 = {2, 1, 1, 2};
    cout << rob(n3) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for nums = [2, 7, 9, 3, 1]:**

The thief starts at house 4 (the last house).

```
solve(4): "Should I rob house 4 (gold=1) or skip it?"
  ROB: 1 + solve(2) = ?
    solve(2): "Should I rob house 2 (gold=9) or skip it?"
      ROB: 9 + solve(0) = 9 + 2 = 11
      SKIP: solve(1) = ?
        solve(1): "Should I rob house 1 (gold=7) or skip it?"
          ROB: 7 + solve(-1) = 7 + 0 = 7
          SKIP: solve(0) = 2
        memo[1] = max(7, 2) = 7
      SKIP: 7
    memo[2] = max(11, 7) = 11
  ROB: 1 + 11 = 12

  SKIP: solve(3) = ?
    solve(3): "Should I rob house 3 (gold=3) or skip it?"
      ROB: 3 + solve(1) = 3 + 7 = 10   ← memo[1] already known!
      SKIP: solve(2) = 11               ← memo[2] already known!
    memo[3] = max(10, 11) = 11
  SKIP: 11

memo[4] = max(12, 11) = 12
```

**Answer: 12** ✓

The thief's optimal plan: rob houses 0, 2, 4 → 2+9+1 = 12.

Notice how `solve(1)` and `solve(2)` were computed once
and reused from the notebook. That's memoization saving work.

---

**Trace for nums = [1, 2, 3, 1]:**

```
solve(3): rob(1+solve(1)) vs skip(solve(2))
  solve(1): rob(2+solve(-1)=2) vs skip(solve(0)=1) → max(2,1) = 2
  solve(2): rob(3+solve(0)=4) vs skip(solve(1)=2) → max(4,2) = 4
  rob: 1+2 = 3.  skip: 4.
memo[3] = max(3, 4) = 4
```

**Answer: 4** ✓ (rob houses 0 and 2: 1+3=4)

---

**Trace for nums = [2, 1, 1, 2]:**

```
solve(0) = 2
solve(1) = max(1+0, 2) = 2
solve(2) = max(1+2, 2) = 3
solve(3) = max(2+2, 3) = 4
```

**Answer: 4** ✓ (rob houses 0 and 3: 2+2=4)

---

## 🔍 The "Pick or Skip" Pattern -- The Heart of DP

Almost every optimization DP problem boils down to this:

> **At each element, you have a choice: include it or exclude it.**
> **Including it gives you something but restricts what came before.**
> **Excluding it gives you nothing but frees the previous element.**
> **Take the better option.**

This pattern appears everywhere:
-   **House Robber** -- rob (skip one back) or skip (free previous)
-   **0/1 Knapsack** -- take item (reduce capacity) or skip (keep capacity)
-   **Coin Change** -- use this coin (reduce amount) or skip (try next coin)
-   **Subset Sum** -- include number (reduce target) or skip

The skeleton is always:
```
solve(i) = max/min(
    value[i] + solve(restricted subproblem),
    solve(unrestricted subproblem)
)
```

If you can identify the **choice**, the **gain**, and the **restriction** --
you can write the recurrence for any DP problem.

---

## 🔍 How to Arrive at This Solution From Scratch

1. **Identify the choice:** at each house, rob or skip.
2. **Define the state:** `solve(i)` = best from houses 0 to i.
3. **Write the recurrence from the choice:**
   - Rob: `nums[i] + solve(i-2)` (gain + restricted subproblem)
   - Skip: `solve(i-1)` (no gain + unrestricted subproblem)
   - `solve(i) = max(rob, skip)`
4. **Find the base cases:** what happens at the smallest inputs?
   - `i < 0` → 0 (no houses)
   - `i == 0` → `nums[0]` (one house, rob it)
5. **Add memoization:** cache `solve(i)` to avoid recomputation.

This 5-step process works for EVERY memoization DP problem.

---

### 🧠 Memory of the Thief's Dilemma (Memoization) Law

-   **The choice:** rob this house or skip it
-   **Rob:** take `nums[i]`, jump two back → `nums[i] + solve(i-2)`
-   **Skip:** take nothing, stay one back → `solve(i-1)`
-   **Why i-2 when robbing?** Because robbing makes i-1 forbidden
-   **Why i-1 when skipping?** Because skipping has no penalty
-   **Base cases:** `i < 0 → 0`, `i == 0 → nums[0]`
-   **Memoization:** `memo[i]` prevents recomputation
-   **Time:** O(N) -- each house solved once
-   **Space:** O(N) -- memo + recursion stack
-   **The 5-step process:** choice → state → recurrence → base cases → memoize

Thus is remembered the saga of **House Robber (Memoization)**,
where the thief stood at the end of the street
and faced the same dilemma at every house --
rob and sacrifice the neighbor,
or skip and keep the neighbor free --
always choosing the richer future,
always writing the answer in his notebook,
never thinking about the same house twice. 🏠🧠✨
