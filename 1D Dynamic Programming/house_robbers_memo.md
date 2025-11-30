## 🏚️🔮 _The Whispered Ledger of Choices: The House Robber (Memoization) Saga_

> \*"Before the Oracle lay a street of houses,
> each glowing faintly with hidden gold.
>
> But the curse remained unchanged:
> **two neighboring houses may not be robbed together.**
>
> This time, however, the Oracle chose a different path —
> not climbing the street in forward order,
> but diving deep into the branching futures of each house,
> remembering each outcome in a sacred ledger
> so no destiny would be recalculated twice."\*

---

The Thieves’ Guild returned,
seeking once more the maximum fortune
that could be taken without disturbing adjacent homes.

But instead of the bottom-up walk,
the Oracle now used **top-down recursion**
guided by **memoization** —
a mystic ledger recording answers for each index
after it had been computed once.

Thus began the saga of **House Robber (Memoization)**.

---

### 📜 The Scroll of Shadowed Houses

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The houses stood in a line,
each with its treasure waiting to be considered.

---

## 🧠 The Oracle’s Recursive Ledger Ritual

_Rob from the current house forward,
remembering all computed fates_

### 🌑 Step 1 — The Recursive Spell

```cpp
int dfs(int i, vector<int>& nums, vector<int>& memo) {
    if (i >= nums.size()) return 0;
```

If the Oracle walked past the last house,
there was no gold left to take.

---

### 📒 Step 2 — Use the Memorized Destiny if Known

```cpp
    if (memo[i] != -1) return memo[i];
```

If the ledger already held the answer for house `i`,
she returned it instantly
— no repeated journeys.

---

### 🏚️ Step 3 — Choose Between Robbing or Skipping

```cpp
    int rob = nums[i] + dfs(i + 2, nums, memo);
    int skip = dfs(i + 1, nums, memo);
```

Two futures stretched before her:

1. **Rob this house**
   → take `nums[i]`
   → skip the next house → move to `i + 2`

2. **Skip this house**
   → explore from `i + 1`

---

### 🧾 Step 4 — Record the Best Fate in the Ledger

```cpp
    memo[i] = max(rob, skip);
    return memo[i];
}
```

The Oracle wrote the richer future
into the memo ledger,
ensuring she never recomputed fate
for this house again.

---

## 🔮 The Main Ritual

```cpp
int rob(vector<int>& nums) {
    vector<int> memo(nums.size(), -1);
    return dfs(0, nums, memo);
}
```

The Oracle began from the first house,
letting memoization guide her through all branching timelines
until the optimal future emerged.

---

### 🎺 The Trial of the Street of Fortune

```cpp
int main() {
    vector<int> nums = {2, 7, 9, 3, 1};
    cout << rob(nums) << endl; // expected: 12
    return 0;
}
```

The memoized ledger revealed:

-   Rob house 1 → 2
-   Rob house 3 → 9
-   Rob house 5 → 1
    Total → **12 gold**

The Oracle avoided redundant exploration
and found the optimal destiny swiftly.

---

### 🧠 Memory of the Ledger of Thieves

-   Use recursive DFS to explore all futures.
-   Memoize results for each index to avoid recomputation.
-   Choice at each house:

    -   rob → `nums[i] + dfs(i+2)`
    -   skip → `dfs(i+1)`

-   Final answer is the best fate starting from house 0.
-   **Time:** O(n) with memoization
-   **Space:** O(n) recursion + memo array.

Thus is remembered the saga of **House Robber (Memoization)**,
where the Oracle peers into branching futures,
records each destiny in her ledger,
and chooses the path of maximum silent wealth
without disturbing a single neighbor. 🏚️✨
