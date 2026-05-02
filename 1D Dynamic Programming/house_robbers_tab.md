## 🏠📋 _The Thief's Dilemma (Tabulation): The House Robber Saga_

> \_"The Oracle had solved the thief's dilemma with memoization --
> thinking backward from the last house.
>
> Now she flipped the direction.
>
> **Instead of standing at the end and asking 'how did I get here?',
> she stood at the beginning and asked 'what's the best I can do so far?'**
>
> House by house, left to right,
> she built up the answer --
> each house inheriting the wisdom of the houses before it."\_

---

This is the saga of **House Robber / Maximum Sum of Non-Adjacent Elements (Tabulation)**.

Same problem:
-   Cannot rob two adjacent houses.
-   Maximize total gold.

```
Input:  nums = [2, 7, 9, 3, 1]  →  Output: 12
Input:  nums = [1, 2, 3, 1]     →  Output: 4
```

---

## 🧠 Flipping the Direction -- From Top-Down to Bottom-Up

In memoization, the thief started at the last house
and asked "what's the best from here to the beginning?"

In tabulation, the thief starts at the FIRST house
and asks "what's the best I can do from the beginning to HERE?"

`dp[i]` = the maximum gold from houses `0` through `i`.

The thief walks forward. At each house, the same dilemma:

**"If I rob this house, the best I had before is `dp[i-2]`
(because `i-1` is forbidden)."**

**"If I skip this house, the best I had before is `dp[i-1]`
(because nothing is forbidden)."**

```
dp[i] = max(nums[i] + dp[i-2], dp[i-1])
```

But what about the first two houses? They're special.

**House 0:** only one house. Rob it. `dp[0] = nums[0]`.

**House 1:** two houses, side by side. Can't rob both.
Rob whichever is richer. `dp[1] = max(nums[0], nums[1])`.

Why `max(nums[0], nums[1])` and not just `nums[1]`?
Because `dp[1]` means "best from houses 0 to 1."
That's either robbing house 0 OR robbing house 1 -- whichever is better.
If we wrote `dp[1] = nums[1]`, we'd forget that house 0 might be richer.

---

### 📜 The Scroll of the Bottom-Up Thief

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Handle the Trivial Street

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];
```

One house. Rob it. No dilemma.

---

### Build the Table

```cpp
    vector<int> dp(n);
```

`dp[i]` will hold the answer to:
"What's the most gold from houses 0 through i?"

---

### The First House -- No Choice

```cpp
    dp[0] = nums[0];
```

One house on the street so far. The thief robs it.
There's nothing else to consider.

---

### The Second House -- The First Real Choice

```cpp
    dp[1] = max(nums[0], nums[1]);
```

Now there are two houses. They're neighbors -- can't rob both.
The thief picks the richer one.

This is the first moment the thief actually has to CHOOSE.
And the answer must account for BOTH options --
that's why it's `max`, not just `nums[1]`.

---

### Every House After -- The Recurring Dilemma

```cpp
    for (int i = 2; i < n; i++) {
```

From house 2 onward, the pattern repeats.

---

### The Two Futures

```cpp
        int pick = nums[i] + dp[i - 2];
```

**"I rob this house."**
I take its gold. But house `i-1` is now forbidden.
The best I had from houses `0` to `i-2` is `dp[i-2]`.

Why `dp[i-2]`? Because that's the most recent "safe" past --
the best the thief could do without touching house `i-1`.
Robbing house `i` creates a gap of one house.
`dp[i-2]` is the world before that gap.

---

```cpp
        int skip = dp[i - 1];
```

**"I skip this house."**
I take nothing. House `i-1` is free.
The best from houses `0` to `i-1` carries forward unchanged.

Why `dp[i-1]`? Because skipping costs nothing.
The entire previous best is still valid.
No gap. No penalty. Just... pass.

---

### The Thief Chooses

```cpp
        dp[i] = max(pick, skip);
    }
```

The richer future wins. The answer is locked in.

---

### The Final Answer

```cpp
    return dp[n - 1];
}
```

The last cell holds the answer for the entire street.

---

### 🎺 The Trial of the Bottom-Up Thief

```cpp
int main() {
    vector<int> n1 = {1, 2, 3, 1};
    cout << rob(n1) << endl; // expected: 4

    vector<int> n2 = {2, 7, 9, 3, 1};
    cout << rob(n2) << endl; // expected: 12

    return 0;
}
```

---

**Full table trace for nums = [2, 7, 9, 3, 1]:**

| i | nums[i] | The thief's thinking                                    | dp[i] |
|---|---------|--------------------------------------------------------|-------|
| 0 | 2       | "Only one house. I take it."                            | 2     |
| 1 | 7       | "Two houses. 7 > 2. I take house 1."                   | 7     |
| 2 | 9       | "Rob: 9 + dp[0]=2 = 11. Skip: dp[1]=7. Rob wins."     | 11    |
| 3 | 3       | "Rob: 3 + dp[1]=7 = 10. Skip: dp[2]=11. Skip wins."   | 11    |
| 4 | 1       | "Rob: 1 + dp[2]=11 = 12. Skip: dp[3]=11. Rob wins."   | 12    |

**Answer: 12** ✓

At house 4: robbing it (1 gold) plus the best from houses 0-2 (11 gold) = 12.
That beats skipping (11). So the thief robs house 4.

Working backward from the dp table: the thief robbed houses 0, 2, and 4.
Gold: 2 + 9 + 1 = 12.

---

**Table trace for nums = [1, 2, 3, 1]:**

| i | nums[i] | Thinking                                               | dp[i] |
|---|---------|-------------------------------------------------------|-------|
| 0 | 1       | "One house. Take it."                                  | 1     |
| 1 | 2       | "max(1, 2) = 2."                                       | 2     |
| 2 | 3       | "Rob: 3+1=4. Skip: 2. Rob wins."                      | 4     |
| 3 | 1       | "Rob: 1+2=3. Skip: 4. Skip wins."                     | 4     |

**Answer: 4** ✓ (rob houses 0 and 2)

---

## 🔧 Space Optimization -- The Thief Only Remembers Two Houses

Look at the recurrence: `dp[i] = max(nums[i] + dp[i-2], dp[i-1])`.

Each house only looks at the TWO houses before it.
The thief doesn't need to remember the entire street --
just the last two answers.

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];

    int prev2 = 0;
    int prev1 = nums[0];
```

`prev2` = the best from two houses back.
`prev1` = the best from one house back.

Why `prev2 = 0` and not `nums[something]`?
Because before house 0, there's nothing. Zero gold.
When house 1 considers robbing: `nums[1] + prev2 = nums[1] + 0`.
That's correct -- robbing house 1 with nothing before it.

---

```cpp
    for (int i = 1; i < n; i++) {
        int pick = nums[i] + prev2;
        int skip = prev1;
        int curr = max(pick, skip);
        prev2 = prev1;
        prev1 = curr;
    }
```

Each house:
1. **Pick:** this house's gold + two back.
2. **Skip:** one back's best.
3. **Choose** the richer.
4. **Slide forward:** yesterday's answer becomes two-back,
   today's answer becomes one-back.

The thief forgets everything except the last two answers.
That's all he needs.

---

```cpp
    return prev1;
}
```

```
Time:  O(N)
Space: O(1) -- just two variables
```

---

**Space-optimized trace for nums = [2, 7, 9, 3, 1]:**

| i | nums[i] | prev2 | prev1 | pick    | skip | curr |
|---|---------|-------|-------|---------|------|------|
| - | --      | 0     | 2     | --      | --   | --   |
| 1 | 7       | 0     | 2     | 7+0=7  | 2    | 7    |
| 2 | 9       | 2     | 7     | 9+2=11 | 7    | 11   |
| 3 | 3       | 7     | 11    | 3+7=10 | 11   | 11   |
| 4 | 1       | 11    | 11    | 1+11=12| 11   | 12   |

**Answer: 12** ✓

---

## 🔍 The Three Levels of This Solution

```
Level 1: Memoization
  Think backward. Recurse. Cache.
  Easiest to derive from the dilemma.

Level 2: Tabulation
  Think forward. Iterate. Fill a table.
  Same recurrence, different direction.

Level 3: Space Optimization
  Realize you only need two previous values.
  Replace the table with two variables.
  Same answer. Minimal memory.
```

Each level is a refinement of the same idea.
The dilemma never changes -- only how you compute it.

---

### 🧠 Memory of the Thief's Dilemma (Tabulation) Law

-   **`dp[i]` = best gold from houses 0 to i**
-   **Base:** `dp[0] = nums[0]`, `dp[1] = max(nums[0], nums[1])`
-   **Recurrence:** `dp[i] = max(nums[i] + dp[i-2], dp[i-1])`
    -   Rob: gold + two back (because one back is forbidden)
    -   Skip: one back (because nothing is forbidden)
-   **Space optimization:** only `prev1` and `prev2` needed → O(1)
-   **`dp[1] = max(nums[0], nums[1])`** not just `nums[1]` --
    because "best from 0 to 1" must consider BOTH houses
-   **Time:** O(N)
-   **Space:** O(1) optimized

Thus is remembered the saga of **House Robber (Tabulation)**,
where the thief walked the street from the first house to the last --
at each house facing the same dilemma,
rob and sacrifice the neighbor or skip and keep the neighbor free --
building the answer forward,
carrying only the last two answers in his pockets,
until the end of the street revealed
the maximum gold that could ever be stolen. 🏠📋✨
