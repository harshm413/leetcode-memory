## 🔥🎯 _The Paths of Infinite Offerings: The Combination Sum Saga_

> \_"The Oracle was given an array of DISTINCT candidates
> and a target sum.
>
> She was commanded:
>
> **'Find ALL unique combinations where the candidates
> sum to the target.
> The SAME candidate may be used UNLIMITED times.'**
>
> The Oracle recognized this as the Subsets pattern --
> but with two twists:
>
> **Twist 1 -- Unlimited reuse.**
> In Subsets, each element is used at most once (`start = i + 1`).
> Here, the same element can be picked again (`start = i`).
>
> **Twist 2 -- Target constraint.**
> In Subsets, every state is valid.
> Here, only states where `target == 0` are valid.
> If `target < 0` → prune (dead end).
>
> Sort the array for early pruning.
> When `candidates[i] > target` → break.
> No point trying larger candidates."\_

---

This is the saga of **Combination Sum**.

Given an array `candidates` of **distinct** integers and a `target`:

-   Return all unique combinations where candidates sum to `target`.
-   The same number may be used **unlimited** times.
-   Combinations must be in non-decreasing order (no duplicates like [2,3] and [3,2]).

```
Input:  candidates = [2, 3, 6, 7], target = 7
Output: [[2, 2, 3], [7]]

Input:  candidates = [2, 3, 5], target = 8
Output: [[2, 2, 2, 2], [2, 3, 3], [3, 5]]

Input:  candidates = [2], target = 1
Output: []
```

---

## 🧠 The Oracle's Core Insight -- Subsets with Reuse + Target

This is the **Subsets template** modified:

| Subsets                           | Combination Sum                   |
| --------------------------------- | --------------------------------- |
| Add every state to result         | Add only when `target == 0`       |
| Recurse with `i + 1` (use once)  | Recurse with `i` (reuse allowed)  |
| No pruning                        | Prune when `target < 0` or `candidate > target` |

```
backtrack(start, target, current):
  If target == 0: add current to result. Return.
  If target < 0: return. (overshot)

  For i = start to n-1:
    If candidates[i] > target: break. (pruning -- sorted array)
    current.push(candidates[i])
    backtrack(i, target - candidates[i], current)  ← start = i (reuse!)
    current.pop()
```

**Why `start = i` (not `i + 1`)?**
The same candidate can be used again.
`backtrack(i, ...)` means "I can pick `candidates[i]` again next time."

**Why sort + break?**
If `candidates[i] > target`, all candidates after it are also > target (sorted).
No point continuing. Break early.

```
Time:  O(2^(target/min) × n) -- exponential but pruned
Space: O(target/min) -- recursion depth
```

---

### 📜 The Scroll of the Infinite Offerings

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🎯 The Backtracking Ritual

```cpp
void backtrack(vector<int>& candidates, int start, int target,
               vector<int>& current, vector<vector<int>>& result) {
```

The Oracle carried:
-   `candidates` -- the available numbers.
-   `start` -- the index from which she may pick (prevents duplicates like [3,2] vs [2,3]).
-   `target` -- the remaining sum needed.
-   `current` -- the combination being built.
-   `result` -- all valid combinations found.

---

### 🎯 Target Reached -- Valid Combination Found

```cpp
    if (target == 0) {
        result.push_back(current);
        return;
    }
```

The remaining target is exactly 0.
The current combination sums to the original target.
Record it.

> _"The offering is complete.
> The sum matches the sacred target.
> Record this combination and return."_

---

### 🔁 Try Each Candidate from `start` Onward

```cpp
    for (int i = start; i < (int)candidates.size(); i++) {
```

Only pick candidates from `start` onward.
This ensures combinations are in non-decreasing order --
preventing duplicates like `[2, 3]` and `[3, 2]`.

---

### ✂️ Pruning -- Candidate Too Large

```cpp
        if (candidates[i] > target) break;
```

Since the array is sorted, if `candidates[i] > target`,
all remaining candidates are also too large. Break.

This is the **key optimization** -- avoids exploring
branches that can never reach target == 0.

> _"If this flame is already too large for the offering,
> all flames beyond it are even larger.
> Stop here. No point continuing."_

---

### 🔥 Choose This Candidate

```cpp
        current.push_back(candidates[i]);
```

Add the candidate to the current combination.

---

### 🔁 Recurse -- Same Candidate Can Be Picked Again

```cpp
        backtrack(candidates, i, target - candidates[i], current, result);
```

**`start = i`** (not `i + 1`) -- the same candidate can be reused.
**`target - candidates[i]`** -- reduce the remaining target.

This is the key difference from Subsets:
-   Subsets: `backtrack(i + 1, ...)` → use each element once.
-   Combination Sum: `backtrack(i, ...)` → unlimited reuse.

> _"The same flame can be taken again.
> Its supply is infinite.
> Only when the target is met or exceeded
> does the path end."_

---

### 🔄 Backtrack -- Undo the Choice

```cpp
        current.pop_back();
    }
}
```

Remove the candidate. Try the next one in the loop.

---

## 🔮 The Main Function

```cpp
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    sort(candidates.begin(), candidates.end());
    vector<vector<int>> result;
    vector<int> current;
    backtrack(candidates, 0, target, current, result);
    return result;
}
```

**Sort first** -- enables the `break` pruning.
Start from index 0 with the full target.

---

### 🎺 The Trial of the Infinite Offerings

```cpp
int main() {
    vector<int> c1 = {2, 3, 6, 7};
    auto r1 = combinationSum(c1, 7);
    for (auto& combo : r1) {
        for (int x : combo) cout << x << " ";
        cout << endl;
    }
    // expected:
    // 2 2 3
    // 7

    vector<int> c2 = {2, 3, 5};
    auto r2 = combinationSum(c2, 8);
    for (auto& combo : r2) {
        for (int x : combo) cout << x << " ";
        cout << endl;
    }
    // expected:
    // 2 2 2 2
    // 2 3 3
    // 3 5

    return 0;
}
```

---

**Full recursion trace for candidates = [2, 3, 6, 7], target = 7:**

```
(sorted: [2, 3, 6, 7])

backtrack(start=0, target=7, current=[])
  i=0: pick 2. target=5. current=[2].
    backtrack(start=0, target=5, current=[2])
      i=0: pick 2. target=3. current=[2,2].
        backtrack(start=0, target=3, current=[2,2])
          i=0: pick 2. target=1. current=[2,2,2].
            backtrack(start=0, target=1, current=[2,2,2])
              i=0: candidates[0]=2 > target(1) → BREAK.
            pop 2. current=[2,2].
          i=1: pick 3. target=0. current=[2,2,3].
            → TARGET == 0! ADD [2,2,3]. ✓
          pop 3. current=[2,2].
          i=2: candidates[2]=6 > target(3) → BREAK.
        pop 2. current=[2].
      i=1: pick 3. target=2. current=[2,3].
        backtrack(start=1, target=2, current=[2,3])
          i=1: candidates[1]=3 > target(2) → BREAK.
        pop 3. current=[2].
      i=2: pick 6. target=-1... wait, 6 > 5? No, 6 > 5 → BREAK.
    pop 2. current=[].
  i=1: pick 3. target=4. current=[3].
    backtrack(start=1, target=4, current=[3])
      i=1: pick 3. target=1. current=[3,3].
        backtrack(start=1, target=1, current=[3,3])
          i=1: 3 > 1 → BREAK.
        pop 3. current=[3].
      i=2: pick 6. 6 > 4 → BREAK.
    pop 3. current=[].
  i=2: pick 6. target=1. current=[6].
    backtrack(start=2, target=1, current=[6])
      i=2: 6 > 1 → BREAK.
    pop 6. current=[].
  i=3: pick 7. target=0. current=[7].
    → TARGET == 0! ADD [7]. ✓
  pop 7. current=[].
```

**Result: [[2, 2, 3], [7]]** ✓

---

**Trace for candidates = [2, 3, 5], target = 8:**

Key paths:
-   `2→2→2→2` (target: 8→6→4→2→0) → **[2,2,2,2]** ✓
-   `2→3→3` (target: 8→6→3→0) → **[2,3,3]** ✓
-   `3→5` (target: 8→5→0) → **[3,5]** ✓

**Result: [[2,2,2,2], [2,3,3], [3,5]]** ✓

---

## 🔍 The Combination Sum Family

| Problem              | Reuse? | Duplicates in input? | Key difference          |
| -------------------- | ------ | -------------------- | ----------------------- |
| **Combination Sum (this)** | ✅ Yes | No (distinct)   | `start = i` (reuse)    |
| Combination Sum II   | ❌ No  | Yes (duplicates)     | `start = i+1` + skip   |
| Combination Sum III  | ❌ No  | No (1-9 only)        | Fixed size k            |
| Subsets              | ❌ No  | No                   | Every state valid       |

---

## 🔍 Why `start = i` Prevents Duplicate Combinations

Without `start`, we'd generate both `[2, 3]` and `[3, 2]`.

With `start = i`, once we move past candidate `2` (i=1 picks `3`),
we can NEVER go back to pick `2` again.
So `[3, 2]` is never generated. Only `[2, 3]` exists.

The `start` parameter enforces a non-decreasing order on combinations.

---

### 🧠 Memory of the Infinite Offerings Law

-   **Sort candidates** for pruning
-   **Backtrack with `start = i`** (not `i+1`) → allows unlimited reuse
-   **Target == 0** → valid combination found, add to result
-   **Pruning:** `if (candidates[i] > target) break` → sorted array enables this
-   **`start` parameter** prevents duplicate combinations (enforces order)
-   **Difference from Subsets:** only add when target==0, recurse with `i` not `i+1`
-   **Time:** O(2^(target/min) × n) -- exponential but heavily pruned
-   **Space:** O(target/min) -- max recursion depth
-   **Edge cases:**
    -   Target = 0 → [[]] (empty combination)
    -   No combination possible → []
    -   Single candidate divides target → one combination of repeated elements

Thus is remembered the saga of **Combination Sum**,
where the Oracle walked the sorted array of candidates,
at each step choosing a flame to add to the offering --
the same flame available again and again --
reducing the target with each choice,
pruning when the flame grew too large --
until the target reached zero
and the offering was complete,
or the flames proved insufficient
and the path was abandoned. 🔥🎯✨
