## 🦘🔢 _The Fewest Leaps: The Jump Game II Saga_

> \_"The Oracle stood at the start of an array.
> Each element told her the maximum jump length from that position.
> She was GUARANTEED to be able to reach the last index.
>
> She was commanded:
>
> **'What is the MINIMUM number of jumps to reach the end?'**
>
> The Oracle imagined the array as a BFS --
> each "level" was one jump.
> From the current jump's range,
> she found the farthest she could reach with ONE MORE jump.
> When the current range was exhausted → she HAD to jump.
>
> Three variables:
> -   `jumps` -- number of jumps taken so far.
> -   `currentEnd` -- the farthest index reachable with `jumps` jumps.
> -   `farthest` -- the farthest index reachable with `jumps + 1` jumps.
>
> When `i` reaches `currentEnd` → we MUST take a jump.
> Update `currentEnd = farthest`. Increment `jumps`.
>
> This is BFS without a queue --
> the 'levels' are implicit in the range [0, currentEnd]."\_

---

This is the saga of **Jump Game II**.

Given an array `nums` where `nums[i]` is the maximum jump from index `i`:

-   You can always reach the last index.
-   Return the **minimum number of jumps** to reach the last index.

```
Input:  nums = [2, 3, 1, 1, 4]
Output: 2   (jump to index 1, then jump to index 4)

Input:  nums = [2, 3, 0, 1, 4]
Output: 2

Input:  nums = [1, 1, 1, 1]
Output: 3
```

---

## 🧠 The Oracle's Core Insight -- Implicit BFS with Greedy

Think of it as BFS levels:
-   **Level 0:** index 0 (starting position).
-   **Level 1:** all indices reachable from index 0 in one jump.
-   **Level 2:** all indices reachable from level 1 in one more jump.
-   ...

We don't need an actual queue. Just track the BOUNDARY of each level.

```
jumps = 0        (jumps taken so far)
currentEnd = 0   (farthest index reachable with current number of jumps)
farthest = 0     (farthest index reachable with one MORE jump)

For i = 0 to n-2:
  farthest = max(farthest, i + nums[i])
  If i == currentEnd:
    jumps++
    currentEnd = farthest
    If currentEnd >= n-1: break

Return jumps
```

**Why `i == currentEnd` triggers a jump?**

`currentEnd` is the boundary of the current BFS level.
When `i` reaches this boundary, we've explored everything
reachable with `jumps` jumps. To go further → must jump again.

**Why iterate to `n-2` (not `n-1`)?**

If we're already at the last index, we don't need another jump.
Iterating to `n-2` avoids an unnecessary jump increment
when `currentEnd` happens to equal `n-1` at the last index.

```
Time:  O(n) -- single pass
Space: O(1) -- three variables
```

---

### 📜 The Scroll of the Fewest Leaps

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔢 The Greedy Ritual

```cpp
int jump(vector<int>& nums) {
    int n = nums.size();
    if (n <= 1) return 0;

    int jumps = 0;
    int currentEnd = 0;
    int farthest = 0;
```

-   `jumps` -- how many jumps we've committed to.
-   `currentEnd` -- the right boundary of the current jump's reach.
-   `farthest` -- the farthest we can reach with one more jump.

---

### 🔁 Walk the Array (Up to n-2)

```cpp
    for (int i = 0; i < n - 1; i++) {
```

Iterate through every index except the last.
(If we reach the last index, we're done -- no jump needed FROM it.)

---

### 📐 Extend the Farthest Reach

```cpp
        farthest = max(farthest, i + nums[i]);
```

From index `i`, we can reach up to `i + nums[i]`.
Track the maximum across all positions in the current level.

> _"Within this jump's range,
> I scan every position and ask:
> 'How far can you throw me?'
> The farthest answer becomes my next horizon."_

---

### 🦘 Reached the Boundary -- Must Jump

```cpp
        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
        }
    }
    return jumps;
}
```

When `i` reaches `currentEnd`:
-   We've exhausted the current level. Must take a jump.
-   `jumps++` -- one more jump committed.
-   `currentEnd = farthest` -- the new boundary is the farthest we found.

After the jump, we're now in the next BFS level.
The new level spans from `old currentEnd + 1` to `new currentEnd`.

> _"The current level is exhausted.
> Every position within reach has been scanned.
> The farthest point found becomes the new boundary.
> One jump is spent. The next level begins."_

---

### 🎺 The Trial of the Fewest Leaps

```cpp
int main() {
    vector<int> n1 = {2, 3, 1, 1, 4};
    cout << jump(n1) << endl; // expected: 2

    vector<int> n2 = {2, 3, 0, 1, 4};
    cout << jump(n2) << endl; // expected: 2

    vector<int> n3 = {1, 1, 1, 1};
    cout << jump(n3) << endl; // expected: 3

    return 0;
}
```

---

**Full trace for nums = [2, 3, 1, 1, 4]:**

```
n = 5. Iterate i = 0 to 3.
jumps = 0, currentEnd = 0, farthest = 0.
```

| i | nums[i] | i + nums[i] | farthest | i == currentEnd? | Action              | jumps | currentEnd |
|---|---------|-------------|----------|-------------------|---------------------|-------|------------|
| 0 | 2       | 2           | 2        | 0 == 0 ✓         | Jump! jumps=1, end=2| 1     | 2          |
| 1 | 3       | 4           | 4        | 1 == 2? No        | --                  | 1     | 2          |
| 2 | 1       | 3           | 4        | 2 == 2 ✓         | Jump! jumps=2, end=4| 2     | 4          |
| 3 | 1       | 4           | 4        | 3 == 4? No        | --                  | 2     | 4          |

**Answer: 2** ✓

**BFS levels:**
-   Level 0: {0} (start)
-   Level 1: {1, 2} (reachable from index 0 with 1 jump)
-   Level 2: {3, 4} (reachable from level 1 with 1 more jump)

Index 4 is in level 2 → 2 jumps.

---

**Full trace for nums = [1, 1, 1, 1]:**

```
n = 4. Iterate i = 0 to 2.
```

| i | nums[i] | farthest | i == currentEnd? | Action              | jumps | currentEnd |
|---|---------|----------|-------------------|---------------------|-------|------------|
| 0 | 1       | 1        | 0 == 0 ✓         | Jump! jumps=1, end=1| 1     | 1          |
| 1 | 1       | 2        | 1 == 1 ✓         | Jump! jumps=2, end=2| 2     | 2          |
| 2 | 1       | 3        | 2 == 2 ✓         | Jump! jumps=3, end=3| 3     | 3          |

**Answer: 3** ✓

Each position can only jump 1 step. Need 3 jumps for 4 elements.

---

**Trace for nums = [5, 1, 1, 1, 1, 1]:**

| i | farthest | i == currentEnd? | Action              | jumps | currentEnd |
|---|----------|-------------------|---------------------|-------|------------|
| 0 | 5        | 0 == 0 ✓         | Jump! jumps=1, end=5| 1     | 5          |

At i=0, farthest = 5 ≥ n-1 = 5. After the jump, currentEnd = 5.
Loop continues but i never reaches currentEnd again (i goes to 4, which is < 5).

**Answer: 1** ✓ -- one big jump covers everything.

---

## 🔍 The BFS Analogy

```
nums = [2, 3, 1, 1, 4]

Level 0: [0]           ← start here
Level 1: [1, 2]       ← reachable from level 0 (indices 0+1=1, 0+2=2)
Level 2: [3, 4]       ← reachable from level 1 (1+3=4, 2+1=3)

Target (index 4) is in level 2 → 2 jumps.
```

`currentEnd` marks the boundary between levels.
When `i` crosses it → new level → one more jump.

This is BFS without a queue -- the array indices ARE the queue,
and `currentEnd` separates the levels.

---

## 🔍 Why Iterate to n-2 (Not n-1)

If we iterate to `n-1` and `currentEnd == n-1`:
the condition `i == currentEnd` would trigger at the last index,
incrementing `jumps` unnecessarily (we're already at the end).

Stopping at `n-2` avoids this edge case cleanly.

---

## 🔄 Window Approach (Left-Right Range)

An equivalent way to think about the same greedy --
maintain a **window `[l, r]`** representing the range of indices
reachable with the current number of jumps.

```cpp
int jump(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return 0;

    int l = 0, r = 0;
    int maxfar = 0;
    int jumps = 0;

    for (int i = 0; i < n; ++i) {
        maxfar = max(maxfar, i + nums[i]);
        if (i == r) {
            l = r + 1;
            r = maxfar;
            ++jumps;
            if (r >= n - 1) return jumps;
        }
    }
    return 0;
}
```

**How it works:**

-   `[l, r]` = the window of indices reachable with `jumps` jumps.
-   As we scan indices in `[l, r]`, we find `maxfar` (farthest reachable with one more jump).
-   When `i` hits `r` (end of current window):
    -   The next window becomes `[r+1, maxfar]`.
    -   `jumps++`.
    -   If `r >= n-1` → we can reach the end. Return immediately.

This is the same implicit BFS -- `[l, r]` is one BFS level,
`[r+1, maxfar]` is the next level. Same logic, different variable names.

---

## 🔍 Jump Game I vs Jump Game II

| Jump Game I                       | Jump Game II                      |
| --------------------------------- | --------------------------------- |
| CAN you reach the end?            | MINIMUM jumps to reach the end    |
| One variable: `maxReach`          | Three variables: `jumps`, `currentEnd`, `farthest` |
| Return true/false                 | Return integer (jump count)       |
| May be impossible                 | Guaranteed reachable              |

Jump Game I just checks if `maxReach >= n-1` ever.
Jump Game II counts HOW MANY level transitions are needed.

---

## 🔄 DP Approach -- Memoization (Top-Down)

Before the greedy insight, this problem can be solved with DP.
`solve(i)` = minimum jumps to reach index `n-1` from index `i`.

```cpp
int solve(int i, vector<int>& nums, vector<int>& memo) {
    if (i >= (int)nums.size() - 1) return 0;
```

Base case: already at or past the end → 0 jumps needed.

---

```cpp
    if (memo[i] != -1) return memo[i];
```

Cache check.

---

```cpp
    int minJumps = 1e9;
    for (int jump = 1; jump <= nums[i]; jump++) {
        int result = solve(i + jump, nums, memo);
        minJumps = min(minJumps, 1 + result);
    }
```

Try every possible jump length (1 to `nums[i]`).
For each, recurse from the landing position.
Take the minimum across all options.

---

```cpp
    return memo[i] = minJumps;
}
```

Cache and return.

---

```cpp
int jump(vector<int>& nums) {
    vector<int> memo(nums.size(), -1);
    return solve(0, nums, memo);
}
```

Start from index 0.

```
Time:  O(n × maxJump) -- for each index, try up to nums[i] jumps
Space: O(n) -- memo + recursion stack
```

---

## 🔄 DP Approach -- Tabulation (Bottom-Up)

`dp[i]` = minimum jumps to reach index `i` from index 0.

```cpp
int jump(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp(n, 1e9);
    dp[0] = 0;
```

All positions start at infinity (unreachable). Source = 0 jumps.

---

```cpp
    for (int i = 0; i < n; i++) {
        for (int jump = 1; jump <= nums[i]; jump++) {
            if (i + jump < n) {
                dp[i + jump] = min(dp[i + jump], dp[i] + 1);
            }
        }
    }
```

For each position `i`, try every reachable position `i + jump`.
Update it if this path uses fewer jumps.

---

```cpp
    return dp[n - 1];
}
```

Answer at the last index.

```
Time:  O(n × maxJump) -- can be O(n²) worst case
Space: O(n)
```

---

## 🔍 Why Greedy Is Better Than DP

| DP (memo/tab)                     | Greedy (BFS)                      |
| --------------------------------- | --------------------------------- |
| O(n²) worst case                  | O(n) always                       |
| O(n) space                        | O(1) space                        |
| Explores all jump options         | Only tracks the farthest reach    |
| Correct but slow                  | Correct and fast                  |

The greedy approach exploits the structure:
within each "level" (jump), we only need the FARTHEST point --
not the exact landing position. This collapses O(n²) into O(n).

**For interviews:** mention DP as the brute-force/intuitive approach,
then optimize to greedy. Shows progression of thought.

---

### 🧠 Memory of the Fewest Leaps Law

-   **Greedy (optimal):** three variables, implicit BFS, O(n)
-   **DP memo:** `solve(i)` = min jumps from i to end, try all jump lengths
-   **DP tab:** `dp[i]` = min jumps from 0 to i, fill left to right
-   **DP is O(n²), Greedy is O(n)** -- greedy is strictly better
-   **At each index i:**
    -   `farthest = max(farthest, i + nums[i])` — extend next level's reach
    -   If `i == currentEnd` → must jump: `jumps++`, `currentEnd = farthest`
-   **Iterate to n-2** (not n-1) to avoid extra jump at the end
-   **This is implicit BFS** — `currentEnd` separates levels
-   **Edge cases:**
    -   Single element → 0 jumps
    -   First element covers the end → 1 jump
    -   All ones → n-1 jumps

Thus is remembered the saga of **Jump Game II**,
where the Oracle walked the array level by level --
within each jump's range, scanning for the farthest reach --
and when the boundary was hit,
she committed one jump,
extending the boundary to the farthest point found --
counting each jump until the end was within reach,
the minimum leaps revealed
through implicit BFS on the array. 🦘🔢✨
