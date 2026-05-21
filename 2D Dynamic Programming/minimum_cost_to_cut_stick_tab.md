## 🪵📋 _The Cheapest Cuts (Tabulation): The Minimum Cost to Cut a Stick Saga_

> \_"The Oracle had solved Cut Stick with memoization.
> Now she built the answer bottom-up.
>
> **`dp[i][j]` = minimum cost to make all cuts
> between boundary `cuts[i]` and boundary `cuts[j]`.**
>
> Fill by increasing gap size (j - i).
> Gap of 1: adjacent boundaries → cost 0.
> Gap of 2: one cut point between → one option.
> Gap of 3: two cut points → two options.
> ...
> Gap of m-1: the full stick → the answer.
>
> Same length-based fill as MCM tabulation."\_

---

This is the saga of **Minimum Cost to Cut a Stick (Tabulation)**.

Same problem:
-   Stick of length `n`. Cuts at given positions.
-   Cost of a cut = length of the segment being cut.
-   Minimize total cost.

```
Input:  n = 7, cuts = [1, 3, 4, 5]  →  Output: 16
Input:  n = 9, cuts = [5, 6, 1, 4, 2]  →  Output: 22
```

---

## 🧠 The Bottom-Up Formulation

```
After adding 0 and n, sort: cuts = [0, c1, c2, ..., ck, n]. Size = m.

dp[i][j] = min cost for all cuts between cuts[i] and cuts[j].

Base: dp[i][i+1] = 0 for all i (adjacent, no cuts between).

Fill by increasing gap (j - i):
  For gap = 2 to m-1:
    For i = 0 to m-1-gap:
      j = i + gap
      dp[i][j] = min over k in [i+1, j-1]:
        dp[i][k] + dp[k][j] + (cuts[j] - cuts[i])
```

---

### 📜 The Scroll of the Bottom-Up Cuts

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;
```

---

## 📋 The Tabulation Ritual

```cpp
int minCost(int n, vector<int>& cuts) {
    cuts.push_back(0);
    cuts.push_back(n);
    sort(cuts.begin(), cuts.end());

    int m = cuts.size();
    vector<vector<int>> dp(m, vector<int>(m, 0));
```

Add boundaries, sort. Create `m × m` table initialized to 0.
Base cases (`dp[i][i+1] = 0`) are already correct (all zeros).

---

### 🔁 Fill by Increasing Gap

```cpp
    for (int gap = 2; gap < m; gap++) {
        for (int i = 0; i + gap < m; i++) {
            int j = i + gap;
            dp[i][j] = INT_MAX;
```

`gap` = distance between `i` and `j` in the sorted cuts array.
Gap of 2 means one cut point between. Gap of 3 means two. Etc.

---

### 🔁 Try Every Cut Point

```cpp
            for (int k = i + 1; k < j; k++) {
                int cost = dp[i][k] + dp[k][j] + (cuts[j] - cuts[i]);
                dp[i][j] = min(dp[i][j], cost);
            }
        }
    }
    return dp[0][m - 1];
}
```

For each `k` between `i` and `j`:
-   `dp[i][k]` = cost of left half (smaller gap, already filled).
-   `dp[k][j]` = cost of right half (smaller gap, already filled).
-   `cuts[j] - cuts[i]` = cost of this cut (segment length).

**Answer:** `dp[0][m-1]` = cost for the entire stick.

---

### 🎺 The Trial of the Bottom-Up Cuts

```cpp
int main() {
    vector<int> c1 = {1, 3, 4, 5};
    cout << minCost(7, c1) << endl; // expected: 16

    vector<int> c2 = {5, 6, 1, 4, 2};
    cout << minCost(9, c2) << endl; // expected: 22

    return 0;
}
```

---

**Full table trace for n=7, cuts=[1,3,4,5]:**

After setup: `cuts = [0, 1, 3, 4, 5, 7]`, m = 6.

**Gap 1 (base):** all dp[i][i+1] = 0. ✓

**Gap 2:**

| i | j | k options | Best cost |
|---|---|-----------|-----------|
| 0 | 2 | k=1: dp[0][1]+dp[1][2]+(3-0) = 0+0+3 = **3** | 3 |
| 1 | 3 | k=2: dp[1][2]+dp[2][3]+(4-1) = 0+0+3 = **3** | 3 |
| 2 | 4 | k=3: dp[2][3]+dp[3][4]+(5-3) = 0+0+2 = **2** | 2 |
| 3 | 5 | k=4: dp[3][4]+dp[4][5]+(7-4) = 0+0+3 = **3** | 3 |

**Gap 3:**

| i | j | k options | Best cost |
|---|---|-----------|-----------|
| 0 | 3 | k=1: 0+3+4=7. k=2: 3+0+4=7. | **7** |
| 1 | 4 | k=2: 0+2+4=6. k=3: 3+0+4=7. | **6** |
| 2 | 5 | k=3: 0+3+4=7. k=4: 2+0+4=6. | **6** |

**Gap 4:**

| i | j | k options | Best cost |
|---|---|-----------|-----------|
| 0 | 4 | k=1: 0+6+5=11. k=2: 3+2+5=10. k=3: 7+0+5=12. | **10** |
| 1 | 5 | k=2: 0+6+6=12. k=3: 3+3+6=12. k=4: 6+0+6=12. | **12** |

**Gap 5 (full stick):**

| i | j | k options | Best cost |
|---|---|-----------|-----------|
| 0 | 5 | k=1: 0+12+7=19. k=2: 3+6+7=16. k=3: 7+3+7=17. k=4: 10+0+7=17. | **16** |

**Answer: dp[0][5] = 16** ✓

Best first cut is at position 3 (k=2 in the array), costing 7 for the full stick,
then optimally cutting each half.

---

## 🔍 Memoization vs Tabulation

| Memoization                       | Tabulation                        |
| --------------------------------- | --------------------------------- |
| `solve(i, j)` recursive          | `dp[i][j]` iterative             |
| Base: `j - i <= 1` → 0           | Base: gap 1 → 0 (pre-filled)     |
| Fills only needed subproblems     | Fills all subproblems             |
| Easier to think about             | Requires gap-based fill order     |
| Same O(m³) time                   | Same O(m³) time                   |

---

### 🧠 Memory of the Cheapest Cuts (Tabulation) Law

-   **Add 0 and n, sort.** `cuts = [0, c1, ..., ck, n]`, size = m.
-   **State:** `dp[i][j]` = min cost for cuts between `cuts[i]` and `cuts[j]`
-   **Base:** gap 1 → 0 (adjacent boundaries, no cuts)
-   **Fill order:** increasing gap (2, 3, ..., m-1)
-   **Transition:** `dp[i][j] = min over k in [i+1, j-1]: dp[i][k] + dp[k][j] + (cuts[j] - cuts[i])`
-   **Answer:** `dp[0][m-1]`
-   **Same template as MCM tabulation** -- fill by length/gap
-   **Time:** O(m³). **Space:** O(m²).

Thus is remembered the saga of **Minimum Cost to Cut a Stick (Tabulation)**,
where the Oracle filled the table gap by gap --
starting with adjacent boundaries (cost zero),
building up to longer segments --
at each gap trying every internal cut point,
paying the segment length as the cost --
until the full stick's minimum cost
was revealed at `dp[0][m-1]`. 🪵📋✨
