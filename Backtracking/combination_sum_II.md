## 🔥🚫 _The Offerings Without Twins: The Combination Sum II Saga_

> \_"The Oracle was given a collection of candidates --
> but this time, the array could contain **DUPLICATES**.
> And each candidate could be used **AT MOST ONCE**.
>
> She was commanded:
>
> **'Find all unique combinations that sum to the target.
> Each number may be used only once.
> No duplicate combinations allowed.'**
>
> This was the fusion of two patterns:
>
> **From Combination Sum I:** target-based backtracking with pruning.
> **From Subsets II:** sort + skip duplicates at the same level.
>
> The Oracle combined them:
>
> **Sort the array.
> Recurse with `start = i + 1` (use each element once).
> Skip duplicates at the same level:
> `if (i > start && nums[i] == nums[i-1]) continue`.**
>
> Same duplicate-skipping rule as Subsets II.
> Same target-based pruning as Combination Sum I.
> Two patterns merged into one."\_

---

This is the saga of **Combination Sum II**.

Given a collection `candidates` (may contain duplicates) and a `target`:

-   Return all unique combinations where candidates sum to `target`.
-   Each number may be used **at most once**.
-   No duplicate combinations in the result.

```
Input:  candidates = [10,1,2,7,6,1,5], target = 8
Output: [[1,1,6],[1,2,5],[1,7],[2,6]]

Input:  candidates = [2,5,2,1,2], target = 5
Output: [[1,2,2],[5]]
```

---

## 🧠 The Oracle's Core Insight -- Combo Sum I + Subsets II

| From Combination Sum I            | From Subsets II                   |
| --------------------------------- | --------------------------------- |
| Target-based: add when target==0  | Sort the array first              |
| Prune: `if (candidates[i] > target) break` | Skip: `if (i > start && nums[i] == nums[i-1]) continue` |
| Recurse with reduced target       | Recurse with `i + 1` (use once)   |

**Combined:**

```
Sort candidates.
backtrack(start, target, current):
  If target == 0: add current. Return.

  For i = start to n-1:
    If candidates[i] > target: break.  (pruning)
    If i > start && candidates[i] == candidates[i-1]: continue.  (skip duplicates)
    current.push(candidates[i])
    backtrack(i + 1, target - candidates[i], current)  ← i+1 (use once!)
    current.pop()
```

**`start = i + 1`** (not `i`) → each element used at most once.
**Skip duplicates** → no duplicate combinations.
**Break when too large** → sorted array pruning.

```
Time:  O(2ⁿ × n)
Space: O(n)
```

---

### 📜 The Scroll of the Unique Offerings

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🚫 The Backtracking Ritual

```cpp
void backtrack(vector<int>& candidates, int start, int target,
               vector<int>& current, vector<vector<int>>& result) {
```

---

### 🎯 Target Reached

```cpp
    if (target == 0) {
        result.push_back(current);
        return;
    }
```

The combination sums to the target. Record it.

---

### 🔁 Try Each Candidate from `start` Onward

```cpp
    for (int i = start; i < (int)candidates.size(); i++) {
```

---

### ✂️ Pruning -- Too Large

```cpp
        if (candidates[i] > target) break;
```

Sorted array → all remaining candidates are also too large. Break.

---

### 🚫 Skip Duplicates at the Same Level

```cpp
        if (i > start && candidates[i] == candidates[i - 1]) continue;
```

**THE critical line** (same as Subsets II).

If this candidate equals the previous one AT THIS LEVEL (`i > start`),
picking it would generate the same combination as before. Skip.

> _"The first twin may contribute.
> The second twin at the same level is silenced.
> But deeper in the recursion, twins may still appear --
> that's how [1, 1, 6] is generated from two separate 1s."_

---

### 🔥 Pick, Recurse, Backtrack

```cpp
        current.push_back(candidates[i]);
        backtrack(candidates, i + 1, target - candidates[i], current, result);
        current.pop_back();
    }
}
```

**`i + 1`** (not `i`) → each element used at most once.
This is the difference from Combination Sum I (`start = i` for reuse).

---

## 🔮 The Main Function

```cpp
vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
    sort(candidates.begin(), candidates.end());
    vector<vector<int>> result;
    vector<int> current;
    backtrack(candidates, 0, target, current, result);
    return result;
}
```

Sort first. Then backtrack.

---

### 🎺 The Trial of the Unique Offerings

```cpp
int main() {
    vector<int> c1 = {10, 1, 2, 7, 6, 1, 5};
    auto r1 = combinationSum2(c1, 8);
    for (auto& combo : r1) {
        for (int x : combo) cout << x << " ";
        cout << endl;
    }
    // expected: 1 1 6 | 1 2 5 | 1 7 | 2 6

    vector<int> c2 = {2, 5, 2, 1, 2};
    auto r2 = combinationSum2(c2, 5);
    for (auto& combo : r2) {
        for (int x : combo) cout << x << " ";
        cout << endl;
    }
    // expected: 1 2 2 | 5

    return 0;
}
```

---

**Full trace for candidates = [10,1,2,7,6,1,5], target = 8:**

After sorting: `[1, 1, 2, 5, 6, 7, 10]`

```
backtrack(start=0, target=8, current=[])
  i=0: pick 1. target=7. current=[1].
    backtrack(start=1, target=7, current=[1])
      i=1: pick 1. target=6. current=[1,1].
        backtrack(start=2, target=6, current=[1,1])
          i=2: pick 2. target=4. current=[1,1,2].
            backtrack(start=3, target=4, current=[1,1,2])
              i=3: 5 > 4 → BREAK.
            pop 2. current=[1,1].
          i=3: pick 5. target=1. current=[1,1,5].
            backtrack(start=4, target=1, current=[1,1,5])
              i=4: 6 > 1 → BREAK.
            pop 5. current=[1,1].
          i=4: pick 6. target=0. current=[1,1,6].
            → TARGET == 0! ADD [1,1,6]. ✓
          pop 6. current=[1,1].
          i=5: 7 > 6 → BREAK.
        pop 1. current=[1].
      i=2: pick 2. target=5. current=[1,2].
        backtrack(start=3, target=5, current=[1,2])
          i=3: pick 5. target=0. current=[1,2,5].
            → TARGET == 0! ADD [1,2,5]. ✓
          pop 5. current=[1,2].
          i=4: 6 > 5 → BREAK.
        pop 2. current=[1].
      i=3: pick 5. target=2. current=[1,5].
        backtrack(start=4, target=2, current=[1,5])
          i=4: 6 > 2 → BREAK.
        pop 5. current=[1].
      i=4: pick 6. target=1. current=[1,6].
        (no valid continuation) pop 6.
      i=5: pick 7. target=0. current=[1,7].
        → TARGET == 0! ADD [1,7]. ✓
      pop 7. current=[1].
      i=6: 10 > 7 → BREAK.
    pop 1. current=[].
  i=1: candidates[1]=1 == candidates[0]=1 AND i(1) > start(0) → SKIP! ✗
  i=2: pick 2. target=6. current=[2].
    backtrack(start=3, target=6, current=[2])
      i=3: pick 5. target=1. (no continuation)
      i=4: pick 6. target=0. current=[2,6].
        → TARGET == 0! ADD [2,6]. ✓
      pop 6. current=[2].
      i=5: 7 > 6 → BREAK.
    pop 2. current=[].
  i=3: pick 5. target=3. (no valid continuation)
  i=4: pick 6. target=2. (no valid continuation)
  i=5: pick 7. target=1. (no valid continuation)
  i=6: 10 > 8 → BREAK.
```

**Result: [[1,1,6], [1,2,5], [1,7], [2,6]]** ✓

**Key moment:** at i=1 (second `1`), the skip fires:
`candidates[1]==candidates[0]` AND `i > start` → SKIP.
This prevents generating `[1, 2, 5]` twice
(once starting from the first `1`, once from the second `1`).

But at start=1, i=1: `i == start` → NOT skipped.
This allows `[1, 1, 6]` (using both `1`s at different depths).

---

## 🔍 The Three-Way Comparison

```cpp
// Combination Sum I (reuse, distinct):
backtrack(candidates, i, target - candidates[i], current, result);
// No skip needed (all distinct).

// Combination Sum II (use once, duplicates):
if (i > start && candidates[i] == candidates[i-1]) continue;
backtrack(candidates, i + 1, target - candidates[i], current, result);

// Subsets II (use once, duplicates, no target):
if (i > start && nums[i] == nums[i-1]) continue;
backtrack(nums, i + 1, current, result);
// Add every state (not just target==0).
```

The duplicate skip line is IDENTICAL in Subsets II and Combination Sum II.
The only difference: Combo Sum II has a target check + pruning.

---

## 🔍 Why Both 1s Can Appear in [1, 1, 6]

```
sorted: [1, 1, 2, 5, 6, 7, 10]

Level 0 (start=0): pick candidates[0]=1. → current=[1].
  Level 1 (start=1): pick candidates[1]=1. i==start → NOT skipped. → current=[1,1].
    Level 2 (start=2): pick candidates[4]=6. → current=[1,1,6]. Target=0. ✓
```

The second `1` is picked at a DEEPER level (start=1, i=1, so `i == start`).
The skip only fires when `i > start` (same level, not first).

---

### 🧠 Memory of the Unique Offerings Law

-   **Sort + skip duplicates** (same as Subsets II)
-   **`if (i > start && candidates[i] == candidates[i-1]) continue`**
-   **`start = i + 1`** → each element used at most once
-   **`if (candidates[i] > target) break`** → sorted pruning
-   **Add when target == 0** → valid combination
-   **Combines:** Combination Sum I (target + prune) + Subsets II (sort + skip)
-   **Time:** O(2ⁿ × n)
-   **Space:** O(n)
-   **Edge cases:**
    -   All same elements `[2,2,2]`, target=4 → `[[2,2]]`
    -   No valid combination → `[]`
    -   Single element equals target → `[[x]]`

Thus is remembered the saga of **Combination Sum II**,
where the Oracle sorted the candidates to group the twins,
then walked the backtracking tree --
using each element at most once (`i + 1`),
silencing duplicate twins at the same level,
pruning branches that exceeded the target --
until every unique combination summing to the target
was found without repetition. 🔥🚫✨
