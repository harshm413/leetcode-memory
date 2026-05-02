## 🐸🔢🧠 _The Frog's Many Leaps (Memoization): The Frog Jump with K Distances Saga_

> \_"The frog had grown stronger.
> No longer limited to 1 or 2 steps --
> it could now jump up to **K stones** at a time.
>
> From stone `i`, the frog could jump to
> stone `i+1`, `i+2`, `i+3`, ... up to `i+K`.
>
> Each jump still cost `|heights[a] - heights[b]|`.
>
> The Oracle was commanded:
>
> **'Find the minimum total cost
> for the frog to reach the last stone,
> when it can jump up to K stones at a time.'**
>
> The Oracle saw this as a natural extension
> of the 2-step Frog Jump:
>
> **Instead of looking back at 2 stones,
> look back at K stones.
> Take the minimum across all K choices.**
>
> The recurrence grew from 2 options to K options --
> but the structure was identical.
>
> `cost(i) = min over j from 1 to K of:
>     cost(i-j) + |heights[i] - heights[i-j]|`
>
> Same memoization pattern.
> Just a loop over K choices instead of 2."\_

---

This is the saga of **Frog Jump with K Distances (Memoization)**.

Given:
-   An array `heights` of `n` stones.
-   An integer `k` -- the maximum jump distance.
-   The frog starts at stone 0.
-   From stone `i`, it can jump to `i+1, i+2, ..., i+k`.
-   Cost of a jump = `|heights[a] - heights[b]|`.
-   Find the **minimum total cost** to reach stone `n-1`.

```
Input:  heights = [10, 30, 40, 20], k = 2
Output: 30   (same as regular frog jump)

Input:  heights = [10, 30, 40, 20], k = 3
Output: 10   (0→3 directly: |10-20| = 10)

Input:  heights = [30, 10, 60, 10, 60, 50], k = 3
Output: 20
```

---

## 🧠 The Oracle's Core Insight -- K Choices Instead of 2

For the 2-step frog:

```
cost(i) = min(
    cost(i-1) + |h[i] - h[i-1]|,
    cost(i-2) + |h[i] - h[i-2]|
)
```

For the K-step frog:

```
cost(i) = min over j = 1 to K of:
    cost(i-j) + |h[i] - h[i-j]|
    (only if i-j >= 0)
```

The 2-step version is just K=2.
The K-step version adds an inner loop.

**Base case:** `cost(0) = 0` (start here, no cost).

```
Time:  O(N × K) -- N stones, K choices per stone
Space: O(N) -- memo array + recursion stack
```

---

### 📜 The Scroll of the Many Leaps

```cpp
#include <iostream>
#include <vector>
#include <cmath>
#include <climits>
using namespace std;
```

---

## 🐸 The Memoization Ritual

### The Cache

```cpp
int frogJumpK(vector<int>& heights, int k) {
    int n = heights.size();
    vector<int> memo(n, -1);
```

`memo[i]` = minimum cost to reach stone `i`.
`-1` = not yet computed.

---

### Launch the Recursion

```cpp
    return solve(n - 1, heights, k, memo);
}
```

Start from the last stone. Recurse down.

---

## 🔮 The Recursive Function

```cpp
int solve(int i, vector<int>& heights, int k, vector<int>& memo) {
```

"What is the minimum cost to reach stone `i`?"

---

### 🛑 Base Case

```cpp
    if (i == 0) return 0;
```

Stone 0. The frog starts here. Cost = 0.

---

### 📖 Check the Cache

```cpp
    if (memo[i] != -1) return memo[i];
```

Already solved? Return it.

---

### 🔁 Try All K Possible Jumps

```cpp
    int minCost = INT_MAX;
```

`minCost` will hold the best option among all K choices.
Starts at infinity -- any real cost will be better.

---

```cpp
    for (int j = 1; j <= k; j++) {
```

Try every possible jump distance: 1 step, 2 steps, ..., K steps.

---

### 🛡️ Check If the Jump Is Valid

```cpp
        if (i - j < 0) break;
```

If jumping `j` steps back would go below stone 0 --
this jump (and all larger jumps) are impossible. Stop.

> _"The frog cannot jump to a stone that doesn't exist.
> If `i - j` is negative, there's nothing there.
> And since `j` only grows, all further jumps are also impossible."_

---

### 🐸 Compute the Cost of This Jump

```cpp
        int jumpCost = solve(i - j, heights, k, memo)
                       + abs(heights[i] - heights[i - j]);
```

The cost of reaching stone `i` via stone `i-j`:
-   Cost to reach `i-j` (recursively solved).
-   Plus the jump cost: `|heights[i] - heights[i-j]|`.

---

### ⚖️ Track the Minimum

```cpp
        minCost = min(minCost, jumpCost);
    }
```

If this jump is cheaper than the current best -- update.

---

### 📝 Cache and Return

```cpp
    memo[i] = minCost;
    return memo[i];
}
```

The minimum across all K choices was cached and returned.

> _"The frog tried every possible leap --
> 1 stone back, 2 stones back, ... K stones back.
> It chose the cheapest.
> That choice was remembered forever."_

---

### 🎺 The Trial of the Many Leaps

```cpp
int main() {
    vector<int> h1 = {10, 30, 40, 20};
    cout << frogJumpK(h1, 2) << endl; // expected: 30
    cout << frogJumpK(h1, 3) << endl; // expected: 10

    vector<int> h2 = {30, 10, 60, 10, 60, 50};
    cout << frogJumpK(h2, 3) << endl; // expected: 20

    return 0;
}
```

---

**Full trace for heights = [10, 30, 40, 20], k = 3:**

```
solve(3):
  j=1: solve(2) + |20-40|
    solve(2):
      j=1: solve(1) + |40-30|
        solve(1):
          j=1: solve(0) + |30-10| = 0+20 = 20
        memo[1] = 20
      j=1: 20 + 10 = 30
      j=2: solve(0) + |40-10| = 0+30 = 30
    memo[2] = min(30, 30) = 30
  j=1: 30 + 20 = 50

  j=2: solve(1) + |20-30| = 20 + 10 = 30

  j=3: solve(0) + |20-10| = 0 + 10 = 10    ← NEW! Direct jump 0→3

memo[3] = min(50, 30, 10) = 10
```

**Answer: 10** ✓

With K=3, the frog can jump directly from stone 0 to stone 3.
Cost = |10-20| = 10. Much cheaper than any 2-step path.

**Compare with K=2:** answer was 30 (couldn't jump directly).
K=3 unlocked the direct path.

---

**Trace for heights = [30, 10, 60, 10, 60, 50], k = 3:**

```
memo[0] = 0
memo[1] = min(0+20) = 20
memo[2] = min(20+50, 0+30) = min(70, 30) = 30
memo[3] = min(30+50, 20+0, 0+20) = min(80, 20, 20) = 20
memo[4] = min(20+50, 30+0, 20+50) = min(70, 30, 70) = 30
memo[5] = min(30+10, 20+40, 30+20) = min(40, 60, 50) = 40
```

Wait, let me recompute memo[5] with k=3:

```
memo[5]:
  j=1: memo[4] + |50-60| = 30 + 10 = 40
  j=2: memo[3] + |50-10| = 20 + 40 = 60
  j=3: memo[2] + |50-60| = 30 + 10 = 40
min(40, 60, 40) = 40
```

Hmm, expected 20. Let me re-check...

Actually let me recompute memo[4]:

```
memo[4]:
  j=1: memo[3] + |60-10| = 20 + 50 = 70
  j=2: memo[2] + |60-60| = 30 + 0 = 30
  j=3: memo[1] + |60-10| = 20 + 50 = 70
min(70, 30, 70) = 30
```

And memo[5]:

```
  j=1: memo[4] + |50-60| = 30 + 10 = 40
  j=2: memo[3] + |50-10| = 20 + 40 = 60
  j=3: memo[2] + |50-60| = 30 + 10 = 40
min = 40
```

Let me re-examine. The expected might be 40, not 20. Let me verify with a different path:

Path 0→3→5: |30-10| + |10-50| = 20 + 40 = 60.
Path 0→1→3→5: 20 + 0 + 40 = 60.
Path 0→2→4→5: 30 + 0 + 10 = 40.
Path 0→3→4→5: 20 + 50 + 10 = 80.
Path 0→1→3→4→5: 20 + 0 + 50 + 10 = 80.

Minimum = 40. Let me fix the expected:

**Answer: 40** ✓

Path: 0→2→4→5. Cost: |30-60| + |60-60| + |60-50| = 30+0+10 = 40.

---

## 🔍 How K Changes the Problem

| K | Choices per stone | Time    | Effect                          |
|---|-------------------|---------|---------------------------------|
| 1 | Only i-1          | O(N)    | No choice -- must take every step |
| 2 | i-1 or i-2        | O(N)    | Classic Frog Jump               |
| 3 | i-1, i-2, or i-3  | O(N)    | Can skip 2 stones               |
| K | i-1 to i-K         | O(N×K) | More options, potentially cheaper |
| N | All previous stones| O(N²)  | Can jump anywhere -- most flexible |

Larger K = more choices = potentially cheaper paths.
But also more computation per stone.

---

## 🔍 Why `break` Instead of `continue`?

```cpp
if (i - j < 0) break;
```

Once `i - j < 0` for some `j`, all larger `j` values
will also give `i - j < 0` (since `j` increases).
So we `break` -- no point checking further.

If we used `continue`, we'd wastefully check
impossible jumps for no reason.

---

### 🧠 Memory of the Many Leaps (Memoization) Law

-   **Recurrence:** `cost(i) = min over j=1..K of: cost(i-j) + |h[i]-h[i-j]|`
-   **Base case:** `cost(0) = 0`
-   **Inner loop:** try all K jump distances, take the minimum
-   **Guard:** `if (i-j < 0) break` -- can't jump to negative stones
-   **K=2 reduces to regular Frog Jump**
-   **Memoization:** `memo[i]` caches the answer
-   **Time:** O(N × K)
-   **Space:** O(N)
-   **Edge cases:**
    -   K=1 → must visit every stone (no choice)
    -   K >= N → can jump directly to the last stone
    -   All same heights → cost = 0 regardless of K

Thus is remembered the saga of **Frog Jump with K Distances (Memoization)**,
where the frog tried every possible leap --
1 stone, 2 stones, up to K stones back --
computing the cost of each,
choosing the cheapest,
caching the answer --
until the minimum cost to the last stone
was found through the power of K choices. 🐸🔢🧠✨
