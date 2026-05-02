## 🐸🔢📋 _The Frog's Many Leaps (Tabulation): The Frog Jump with K Distances Saga_

> \_"The Oracle had solved the K-step frog with memoization.
> Now she built the answer from the ground up.
>
> **For each stone, try all K possible jumps backward.
> Take the minimum cost.
> Fill the table left to right.**
>
> No recursion. Pure iteration.
> An inner loop of K choices at each stone."\_

---

This is the saga of **Frog Jump with K Distances (Tabulation)**.

Same problem:
-   Frog jumps 1 to K stones. Cost = `|heights[a] - heights[b]|`.
-   Find minimum total cost to reach the last stone.

```
Input:  heights = [10, 30, 40, 20], k = 3  →  Output: 10
Input:  heights = [30, 10, 60, 10, 60, 50], k = 3  →  Output: 40
```

---

## 🧠 The Recurrence (Bottom-Up)

```
dp[0] = 0
dp[i] = min over j = 1 to K of:
    dp[i-j] + |heights[i] - heights[i-j]|
    (only if i-j >= 0)
```

Fill left to right. Each stone looks back at up to K previous stones.

```
Time:  O(N × K)
Space: O(N)
```

---

### 📜 The Scroll of the Bottom-Up Many Leaps

```cpp
#include <iostream>
#include <vector>
#include <cmath>
#include <climits>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int frogJumpK(vector<int>& heights, int k) {
    int n = heights.size();
    if (n == 1) return 0;

    vector<int> dp(n, INT_MAX);
```

`dp[i]` = minimum cost to reach stone `i`.
All start at `INT_MAX` (unreached) except stone 0.

---

### 🏁 Base Case

```cpp
    dp[0] = 0;
```

Stone 0: the frog starts here. Cost = 0.

---

### 🔁 Fill the Table Stone by Stone

```cpp
    for (int i = 1; i < n; i++) {
```

For each stone from 1 to n-1.

---

### 🔁 Try All K Possible Jumps Backward

```cpp
        for (int j = 1; j <= k; j++) {
```

Try jumping from stone `i-j` to stone `i`.
`j` = the jump distance (1, 2, ..., K).

---

### 🛡️ Check If the Jump Is Valid

```cpp
            if (i - j < 0) break;
```

Can't jump from a negative stone. Stop.

---

### 🐸 Compute and Compare

```cpp
            int jumpCost = dp[i - j] + abs(heights[i] - heights[i - j]);
            dp[i] = min(dp[i], jumpCost);
        }
    }
```

Cost to reach `i` via `i-j`:
the cost to reach `i-j` (already in the table)
plus the jump cost.

If this is cheaper than the current `dp[i]` → update.

After the inner loop, `dp[i]` holds the minimum
across all K possible jumps.

> _"Each stone asks K stones behind it:
> 'How much would it cost to come from you?'
> The cheapest answer wins."_

---

### 📤 Return the Answer

```cpp
    return dp[n - 1];
}
```

---

### 🎺 The Trial of the Bottom-Up Many Leaps

```cpp
int main() {
    vector<int> h1 = {10, 30, 40, 20};
    cout << frogJumpK(h1, 2) << endl; // expected: 30
    cout << frogJumpK(h1, 3) << endl; // expected: 10

    vector<int> h2 = {30, 10, 60, 10, 60, 50};
    cout << frogJumpK(h2, 3) << endl; // expected: 40

    return 0;
}
```

---

**Full table trace for heights = [10, 30, 40, 20], k = 3:**

| i | j=1                    | j=2                    | j=3                    | dp[i]     |
|---|------------------------|------------------------|------------------------|-----------|
| 0 | --                     | --                     | --                     | 0 (base)  |
| 1 | dp[0]+\|30-10\|=0+20=20| i-2<0 → break          | --                     | 20        |
| 2 | dp[1]+\|40-30\|=20+10=30| dp[0]+\|40-10\|=0+30=30| i-3<0 → break         | 30        |
| 3 | dp[2]+\|20-40\|=30+20=50| dp[1]+\|20-30\|=20+10=30| dp[0]+\|20-10\|=0+10=**10** | **10** |

**dp = [0, 20, 30, 10]**

**Answer: 10** ✓

At stone 3 with K=3: the direct jump from stone 0 (j=3) costs only 10.
This beats the 1-step (50) and 2-step (30) options.

---

**Compare K=2 vs K=3 on the same input:**

| K | dp[3] | Best path     | Cost |
|---|-------|---------------|------|
| 2 | 30    | 0→1→3         | 30   |
| 3 | 10    | 0→3 (direct)  | 10   |

K=3 unlocked the direct jump. Bigger K = more options = potentially cheaper.

---

**Table trace for heights = [30, 10, 60, 10, 60, 50], k = 3:**

| i | j=1          | j=2          | j=3          | dp[i] |
|---|--------------|--------------|--------------|-------|
| 0 | --           | --           | --           | 0     |
| 1 | 0+20=20      | break        | --           | 20    |
| 2 | 20+50=70     | 0+30=30      | break        | 30    |
| 3 | 30+50=80     | 20+0=20      | 0+20=20      | 20    |
| 4 | 20+50=70     | 30+0=30      | 20+50=70     | 30    |
| 5 | 30+10=40     | 20+40=60     | 30+10=40     | 40    |

**dp = [0, 20, 30, 20, 30, 40]**

**Answer: 40** ✓

---

**Table trace for K=1 (must visit every stone):**

heights = [10, 30, 40, 20], k = 1:

| i | j=1 only              | dp[i] |
|---|-----------------------|-------|
| 0 | --                    | 0     |
| 1 | 0+20=20               | 20    |
| 2 | 20+10=30              | 30    |
| 3 | 30+20=50              | 50    |

**Answer: 50** -- must visit every stone. No skipping allowed.

---

**Table trace for K=N (can jump anywhere):**

heights = [10, 30, 40, 20], k = 4:

| i | j=1  | j=2  | j=3  | j=4   | dp[i] |
|---|------|------|------|-------|-------|
| 0 | --   | --   | --   | --    | 0     |
| 1 | 20   | break| --   | --    | 20    |
| 2 | 30   | 30   | break| --    | 30    |
| 3 | 50   | 30   | 10   | break | 10    |

Same as K=3 for this input. K >= N-1 means the frog can always jump directly.

---

## 🔍 Can We Space-Optimize?

For K=2, we only needed 2 variables (prev1, prev2).

For general K, `dp[i]` depends on `dp[i-1], dp[i-2], ..., dp[i-K]`.
We'd need a **sliding window of K values** -- possible but more complex.

```cpp
// Space O(K) using a deque or circular buffer
// Not commonly asked -- the O(N) table is fine for interviews.
```

For most practical purposes, the O(N) table is sufficient.

---

## 🔍 The Progression of Frog Problems

| Problem              | Choices | Operation | Time   |
| -------------------- | ------- | --------- | ------ |
| Climbing Stairs      | 1 or 2  | COUNT (+) | O(N)   |
| Frog Jump            | 1 or 2  | MIN cost  | O(N)   |
| Frog Jump K          | 1 to K  | MIN cost  | O(N×K) |
| Min Cost Climbing    | 1 or 2  | MIN cost  | O(N)   |

Each problem adds one layer of complexity.
The DP skeleton stays the same.

---

### 🧠 Memory of the Many Leaps (Tabulation) Law

-   **Recurrence:** `dp[i] = min over j=1..K of: dp[i-j] + |h[i]-h[i-j]|`
-   **Base case:** `dp[0] = 0`
-   **Outer loop:** `i = 1` to `n-1`
-   **Inner loop:** `j = 1` to `K` (try all jump distances)
-   **Guard:** `if (i-j < 0) break`
-   **K=1:** must visit every stone (no choice)
-   **K=2:** classic Frog Jump
-   **K>=N:** can jump directly to any stone
-   **Time:** O(N × K)
-   **Space:** O(N)
-   **Edge cases:**
    -   Single stone → 0
    -   K=1 → sum of all consecutive differences
    -   All same heights → 0

Thus is remembered the saga of **Frog Jump with K Distances (Tabulation)**,
where the Oracle filled the table stone by stone --
at each stone trying K possible leaps backward,
computing the cost of each,
keeping the cheapest --
until the last stone's minimum cost was known,
built from the ground up
through the power of K choices at every step. 🐸🔢📋✨
