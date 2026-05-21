## 🪵🧠 _The Cheapest Cuts (Memoization): The Minimum Cost to Cut a Stick Saga_

> \_"The Oracle was given a wooden stick of length `n`
> and a list of positions where cuts must be made.
>
> The cost of each cut = the LENGTH of the stick
> being cut at that moment.
>
> The ORDER of cuts matters --
> cutting a long stick is expensive,
> cutting a short piece is cheap.
>
> The Oracle was commanded:
>
> **'Find the order of cuts that minimizes the total cost.'**
>
> She recognized this as **Partition DP** --
> the same pattern as Matrix Chain Multiplication.
>
> Between any two boundary points,
> she could choose WHERE to make the next cut.
> That cut split the segment into two halves.
> Each half was solved independently.
> The cost of this cut = length of the current segment.
>
> Try every possible cut point. Take the minimum.
>
> `solve(i, j)` = minimum cost to make all cuts
> between boundary `i` and boundary `j`.
>
> The merge cost = `cuts[j] - cuts[i]`
> (the length of the segment being cut)."\_

---

This is the saga of **Minimum Cost to Cut a Stick**.

Given:
-   A stick of length `n`.
-   An array `cuts` of positions where cuts must be made.

The cost of a cut = the length of the stick segment being cut.
Return the **minimum total cost** to make all cuts.

```
Input:  n = 7, cuts = [1, 3, 4, 5]
Output: 16

Input:  n = 9, cuts = [5, 6, 1, 4, 2]
Output: 22
```

---

## 🧠 The Oracle's Core Insight -- Partition DP on Sorted Cuts

**Key trick:** add `0` and `n` to the cuts array as boundaries,
then sort it. Now the problem becomes:

"Given sorted cut positions `[0, c1, c2, ..., ck, n]`,
find the minimum cost to make all internal cuts."

`solve(i, j)` = minimum cost to perform all cuts
between position `cuts[i]` and position `cuts[j]`.

At each step, pick a cut point `k` between `i` and `j`:
-   Cost of this cut = `cuts[j] - cuts[i]` (length of current segment).
-   Left half: `solve(i, k)` (cuts between `cuts[i]` and `cuts[k]`).
-   Right half: `solve(k, j)` (cuts between `cuts[k]` and `cuts[j]`).

Try every `k`. Take the minimum.

```
solve(i, j):
  If j - i <= 1: return 0  (no cuts needed between adjacent boundaries)

  min_cost = infinity
  For k = i+1 to j-1:
    cost = solve(i, k) + solve(k, j) + (cuts[j] - cuts[i])
    min_cost = min(min_cost, cost)

  return min_cost
```

**Base case:** `j - i <= 1` → adjacent boundaries, no cut point between them.
Nothing to cut. Cost = 0.

**Merge cost:** `cuts[j] - cuts[i]` → the length of the segment.
This is paid regardless of which `k` we choose --
it's the cost of making ONE cut in this segment.

---

### 📜 The Scroll of the Cheapest Cuts

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;
```

---

## 🪵 Setting Up

```cpp
int minCost(int n, vector<int>& cuts) {
    cuts.push_back(0);
    cuts.push_back(n);
    sort(cuts.begin(), cuts.end());
```

Add the two boundaries (0 and n) to the cuts array.
Sort everything. Now `cuts = [0, c1, c2, ..., ck, n]`.

The boundaries define the edges of the stick.
Internal elements are the actual cut positions.

---

```cpp
    int m = cuts.size();
    vector<vector<int>> memo(m, vector<int>(m, -1));
```

`memo[i][j]` = min cost to make all cuts between `cuts[i]` and `cuts[j]`.

---

```cpp
    return solve(0, m - 1, cuts, memo);
}
```

Solve for the entire stick: from boundary 0 to boundary n.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, int j, vector<int>& cuts, vector<vector<int>>& memo) {
```

"What is the minimum cost to make all cuts between `cuts[i]` and `cuts[j]`?"

---

### 🛑 Base Case -- No Cuts Needed

```cpp
    if (j - i <= 1) return 0;
```

If `i` and `j` are adjacent in the sorted cuts array,
there are no cut points between them. Nothing to do. Cost = 0.

> _"Two adjacent boundaries with nothing between them.
> No cut is needed. The segment is already final."_

---

### 📖 Cache Check

```cpp
    if (memo[i][j] != -1) return memo[i][j];
```

---

### 🔁 Try Every Cut Point Between i and j

```cpp
    int minCost = INT_MAX;

    for (int k = i + 1; k < j; k++) {
```

`k` is the cut point. It must be strictly between `i` and `j`.
`cuts[k]` is where we make the cut.

---

### 📐 Compute the Cost

```cpp
        int cost = solve(i, k, cuts, memo)
                 + solve(k, j, cuts, memo)
                 + (cuts[j] - cuts[i]);
        minCost = min(minCost, cost);
    }
```

**`solve(i, k)`** = cost to handle all cuts in the left segment.
**`solve(k, j)`** = cost to handle all cuts in the right segment.
**`cuts[j] - cuts[i]`** = cost of making THIS cut (length of current segment).

The segment length is paid no matter which `k` we choose.
But the sub-costs depend on `k`.

> _"The cost of cutting is the length of what you cut.
> A long stick costs more to cut than a short one.
> By cutting wisely -- making expensive cuts
> when the stick is already short --
> the total cost is minimized."_

---

### 📝 Cache and Return

```cpp
    memo[i][j] = minCost;
    return minCost;
}
```

---

### 🎺 The Trial of the Cheapest Cuts

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

**Full trace for n=7, cuts=[1,3,4,5]:**

After adding boundaries and sorting: `cuts = [0, 1, 3, 4, 5, 7]`

Indices: 0=0, 1=1, 2=3, 3=4, 4=5, 5=7.

**solve(0, 5):** segment [0, 7], length = 7. Try k=1,2,3,4.

**k=1 (cut at position 1):**
-   solve(0,1) = 0 (adjacent, no cuts between 0 and 1)
-   solve(1,5): segment [1,7], length=6. Try k=2,3,4.
    -   k=2: solve(1,2)=0 + solve(2,5) + 6
        -   solve(2,5): [3,7], len=4. k=3: solve(2,3)=0 + solve(3,5) + 4
            -   solve(3,5): [4,7], len=3. k=4: solve(3,4)=0 + solve(4,5)=0 + 3 = 3.
            -   solve(2,5) via k=3: 0 + 3 + 4 = 7.
            -   k=4: solve(2,4) + solve(4,5) + 4. solve(2,4)=[3,5],len=2,k=3: 0+0+2=2. → 2+0+4=6.
        -   solve(2,5) = min(7, 6) = 6.
        -   solve(1,5) via k=2: 0 + 6 + 6 = 12.
    -   (other k values for solve(1,5) give higher)
    -   solve(1,5) = 10 (after trying all k).
-   solve(0,5) via k=1: 0 + 10 + 7 = 17.

**k=3 (cut at position 4):**
-   solve(0,3): [0,4], len=4. Best = ...
-   solve(3,5): [4,7], len=3. k=4: 0+0+3=3.
-   ... (computing all sub-problems)

After trying all k values for solve(0,5):

**Answer: 16** ✓

The optimal order: cut at 3 (cost 7), then cut at 5 (cost 4), then cut at 1 (cost 3), then cut at 4 (cost 2). Total = 7+4+3+2 = 16.

---

## 🔍 How This Maps to MCM

| MCM                               | Cut Stick                         |
| --------------------------------- | --------------------------------- |
| `solve(i, j)` = min cost for matrices i..j | `solve(i, j)` = min cost for cuts between i..j |
| Split at k: left [i..k], right [k+1..j] | Cut at k: left [i..k], right [k..j] |
| Merge cost: `arr[i-1]*arr[k]*arr[j]` | Merge cost: `cuts[j] - cuts[i]` (segment length) |
| k ranges from i to j-1            | k ranges from i+1 to j-1          |

The structure is identical. Only the merge cost formula changes.

---

## 🔍 Why Add 0 and n as Boundaries?

Without boundaries, we can't express "the segment from the start of the stick
to the first cut" or "from the last cut to the end."

Adding 0 and n makes every segment expressible as `[cuts[i], cuts[j]]`.
The boundaries are never "cut" -- they just define the edges.

---

### 🧠 Memory of the Cheapest Cuts (Memoization) Law

-   **Add 0 and n to cuts, then sort**
-   **State:** `solve(i, j)` = min cost for all cuts between `cuts[i]` and `cuts[j]`
-   **Base:** `j - i <= 1` → 0 (no cuts between adjacent boundaries)
-   **Transition:** try every `k` from `i+1` to `j-1`
    -   `cost = solve(i,k) + solve(k,j) + (cuts[j] - cuts[i])`
    -   Take the minimum
-   **Merge cost = segment length** = `cuts[j] - cuts[i]`
-   **Same partition DP template as MCM**
-   **Time:** O(m³) where m = cuts.size() + 2
-   **Space:** O(m²)

Thus is remembered the saga of **Minimum Cost to Cut a Stick (Memoization)**,
where the Oracle added boundaries to the cuts array,
sorted them into order,
then faced the same partition DP question at every segment:
"Where should I cut first?" --
trying every internal cut point,
paying the segment's length as the cost,
solving both halves independently --
until the cheapest sequence of cuts was found. 🪵🧠✨
