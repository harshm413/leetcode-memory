## 🏰📜 _The Backward Ledger of Fate: The House Robber II (Tabulation) Saga_

_(table filled in the **same order** as memoization —
the **last workable house is filled first**,
and the **first house holds the final answer**)_

> \*"Once more the Oracle faced the Ring of Cursed Mansions —
> a village bound in a circle where
> the First and the Last mansion were blood-linked by fate.
>
> This time, she cast aside her mystical ledger
> and instead carved the future into a **stone table** —
> a tabulation of destinies.
>
> Yet she honored the old pattern of memoization:
>
> **the table must be filled from the farthest house backward,
> so that the earliest house holds the final destiny.**"\*

---

The two fates remained unchanged:

1. **Case A:** consider houses `0 … n-2`
2. **Case B:** consider houses `1 … n-1`

But tabulation must fill the DP table **starting from the last allowed index**,
moving **backward**,
just as memoization would compute results from deep recursion
and bubble them up to index 0.

Thus began the saga of
**House Robber II — Tabulation in Reverse Order**.

---

### 📜 The Scroll of Circular Mansions

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🪨 The Oracle’s Backward Tabulation Ritual

_Recreate memoization’s order — fill from end to start_

```cpp
int robRange(vector<int>& nums, int start, int end) {
    vector<int> dp(nums.size() + 2, 0);
```

She carved a large stone table,
offset by 2 extra positions,
because backward robbing requires safe access to `i+1` and `i+2`.

---

### 🔙 Fill the Table Backwards

_from `end` down to `start`,
matching memoization’s computation order_

```cpp
    for (int i = end; i >= start; i--) {
        int rob = nums[i] + dp[i + 2];
        int skip = dp[i + 1];
        dp[i] = max(rob, skip);
    }
```

At each mansion `i`:

-   **rob** it → take gold + value two steps ahead
-   **skip** it → take value one step ahead

The Oracle filled the table **starting from the last house**
and worked _toward the first_,
so that `dp[start]` would hold the final answer —
exactly as memoization places the result at index 0.

---

### 🏛️ Return the Final Fate

```cpp
    return dp[start];
}
```

The first cell of this reversed table
held the complete destiny of this scenario.

---

## 🌕 The Dual Timeline Invocation

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];
```

One house?
Rob it without hesitation.

---

### 🌗 Case A — Houses `[0 … n-2]`

```cpp
    int caseA = robRange(nums, 0, n - 2);
```

A world where the last mansion may not be touched.

---

### 🌘 Case B — Houses `[1 … n-1]`

```cpp
    int caseB = robRange(nums, 1, n - 1);
```

A world where the first mansion is forbidden.

---

### 🌕 Choose the Richer Destiny

```cpp
    return max(caseA, caseB);
}
```

As always,
the Oracle takes the richer future.

---

### 🎺 The Trial of the Cursed Circle

```cpp
int main() {
    vector<int> nums = {2, 3, 2};
    cout << rob(nums) << endl; // expected: 3
    return 0;
}
```

Backward tabulation revealed:

-   Case A: from 0 to 1 → best is 2
-   Case B: from 1 to 2 → best is 3

Thus the true destiny was **3**.

---

### 🧠 Memory of the Backward Stone Table

-   Tabulation filled **exactly in memoization’s order**

    -   backwards from `end` to `start`

-   Use `dp[i] = max(nums[i] + dp[i+2], dp[i+1])`
-   Two scenarios needed because of the circle
-   Final answer is the greater of
    `robRange(0, n-2)` and `robRange(1, n-1)`
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**House Robber II (Tabulation in Memoization Order)** —
where the Oracle chisels fate backward into stone,
ensuring the final answer rests at the **first box**,
just as it did in the ledger of recursion. 🏰✨
