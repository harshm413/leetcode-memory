## 🏚️ _The Night of Silent Thieves: The House Robber Saga_

> \*"Along a moonlit street stood a row of houses,
> each holding treasure within —
> yet bound by a curse:
>
> **No two adjacent houses may be robbed on the same night.**
>
> The Oracle was summoned by the Thieves’ Guild:
> ‘Tell us the maximum gold that can be taken
> without awakening the wrath of neighbors.’
>
> She discovered the Law of Non-Adjacent Choices —
> each house’s fate depends on the best outcome
> when choosing to rob it… or skip it."\*

---

A line of houses lay before the Oracle,
each containing some amount of gold: `nums[i]`.

The Thieves’ Guild demanded she find the **maximum** amount
that could be stolen without ever robbing two neighboring houses.

This was a saga of **dynamic programming**,
where each decision rose from two competing futures:

-   **Rob this house** → must skip previous house
-   **Skip this house** → may take best from previous house

Thus began the saga of **House Robber**.

---

### 📜 The Scroll of Golden Houses

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The street of houses awaited judgment.

---

## 🏚️ The Oracle’s Dynamic Ritual

_At each step, choose the richer of two destinies_

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 0) return 0;
    if (n == 1) return nums[0];
```

If there were no houses,
no gold could be taken.

If there was only one house,
the choice was simple: take it.

---

### 🪙 Step 1 — Initialize the First Decisions

```cpp
    vector<int> dp(n);
    dp[0] = nums[0];
    dp[1] = max(nums[0], nums[1]);
```

The Oracle established:

-   At the **first house**, the max was its own gold
-   At the **second house**, choose the richer of
    robbing the first _or_ the second

Two beginnings,
two branching destinies.

---

### 🧭 Step 2 — Build the Path of Maximum Loot

```cpp
    for (int i = 2; i < n; i++) {
        dp[i] = max(dp[i - 1], dp[i - 2] + nums[i]);
    }
```

At each house `i`, she faced two futures:

1. **Skip this house**
   → keep `dp[i - 1]`

2. **Rob this house**
   → take `nums[i] + dp[i - 2]`
   (because the previous house must remain untouched)

She chose the richer fate.

Step by step,
the Oracle built the maximum treasure path.

---

### 🌙 Step 3 — The Final Haul

```cpp
    return dp[n - 1];
}
```

At the last house,
the best possible outcome awaited.

---

### 🎺 The Trial of the Haunted Street

```cpp
int main() {
    vector<int> nums = {2, 7, 9, 3, 1};
    cout << rob(nums) << endl; // expected: 12 (2 + 9 + 1)
    return 0;
}
```

The Oracle chose:

-   Rob house 1 → 2 gold
-   Rob house 3 → 9 gold
-   Rob house 5 → 1 gold

Total = **12 gold**

A perfect silent heist.

---

### 🧠 Memory of the Thieves’ Law

-   Never rob two adjacent houses
-   Recurrence:
    `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`
-   First two values:

    -   `dp[0] = nums[0]`
    -   `dp[1] = max(nums[0], nums[1])`

-   **Time:** O(n)
-   **Space:** O(n) (or O(1) with two variables)

Thus is remembered the saga of **House Robber**,
where the Oracle walks the moonlit street,
choosing houses with perfect foresight,
and stealing every coin destiny allows
without ever waking a single neighbor. 🌙💰✨
