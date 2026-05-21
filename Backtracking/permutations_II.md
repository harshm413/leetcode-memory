## 🔀🚫 _Arrangements Without Twins: The Permutations II Saga_

> \_"The Oracle was given an array that could contain **DUPLICATES**.
>
> She was commanded:
>
> **'Generate all UNIQUE permutations.'**
>
> With `[1, 1, 2]`:
> -   `[1, 1, 2]` ✓
> -   `[1, 2, 1]` ✓
> -   `[2, 1, 1]` ✓
> -   But NOT `[1, 1, 2]` twice (from swapping the two 1s).
>
> The Oracle combined Permutations + duplicate skipping:
>
> **Sort the array.
> Use the `used[]` array (same as Permutations I).
> Add one skip condition:
> if `nums[i] == nums[i-1]` AND `used[i-1] == false` → skip.**
>
> This ensured that among duplicate elements,
> they were always used in LEFT-TO-RIGHT order.
> If the previous duplicate wasn't used yet,
> the current one can't be used either."\_

---

This is the saga of **Permutations II**.

Given an array `nums` that **may contain duplicates**:

-   Return all unique permutations.

```
Input:  nums = [1, 1, 2]
Output: [[1,1,2],[1,2,1],[2,1,1]]

Input:  nums = [1, 2, 3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

---

## 🧠 The Oracle's Core Insight -- Sort + Skip Unused Previous Duplicate

**Same as Permutations I** (used[] array, loop from 0 to n-1).
**Plus:** sort the array + one skip condition.

```cpp
if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;
```

**Why `!used[i-1]`?**

Among duplicates, enforce that they are picked in LEFT-TO-RIGHT order.
If `nums[i-1]` (the previous duplicate) is NOT yet used in the current permutation,
then `nums[i]` (the current duplicate) should NOT be used either --
because that would mean we're picking the second copy before the first.

This ensures each unique permutation is generated exactly once.

> _"If my twin before me hasn't been chosen yet,
> I cannot be chosen either.
> Twins must be picked in order -- first before second.
> This prevents the same arrangement from appearing twice."_

---

### 📜 The Scroll of the Unique Arrangements

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🚫 The Backtracking Ritual

```cpp
void backtrack(vector<int>& nums, vector<bool>& used,
               vector<int>& current, vector<vector<int>>& result) {
```

---

### 🎯 Permutation Complete

```cpp
    if ((int)current.size() == (int)nums.size()) {
        result.push_back(current);
        return;
    }
```

---

### 🔁 Try Every Element

```cpp
    for (int i = 0; i < (int)nums.size(); i++) {
```

Loop from 0 (not `start` -- order matters in permutations).

---

### 🚫 Skip If Already Used

```cpp
        if (used[i]) continue;
```

Same as Permutations I -- can't reuse an element in one permutation.

---

### 🚫 Skip Duplicate -- Previous Twin Not Yet Used

```cpp
        if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;
```

**THE critical line.**

If `nums[i] == nums[i-1]` (duplicate) AND `used[i-1] == false`
(the previous copy isn't in the current permutation yet):
skip this one.

This forces duplicates to be used in left-to-right order.

---

### Pick, Recurse, Backtrack

```cpp
        used[i] = true;
        current.push_back(nums[i]);
        backtrack(nums, used, current, result);
        current.pop_back();
        used[i] = false;
    }
}
```

Same as Permutations I.

---

## 🔮 The Main Function

```cpp
vector<vector<int>> permuteUnique(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> result;
    vector<int> current;
    vector<bool> used(nums.size(), false);
    backtrack(nums, used, current, result);
    return result;
}
```

**Sort first** -- groups duplicates together (required for the skip to work).

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> nums1 = {1, 1, 2};
    auto r1 = permuteUnique(nums1);
    for (auto& p : r1) {
        for (int x : p) cout << x << " ";
        cout << endl;
    }
    // expected: 1 1 2 | 1 2 1 | 2 1 1
    return 0;
}
```

---

**Trace for nums = [1, 1, 2] (sorted):**

```
backtrack(current=[], used=[F,F,F])
  i=0: nums[0]=1. used[0]=F. No prev duplicate. Pick.
    used=[T,F,F], current=[1].
    backtrack:
      i=0: used → skip.
      i=1: nums[1]=1. nums[1]==nums[0] AND !used[0]? used[0]=T → condition FALSE. Pick!
        used=[T,T,F], current=[1,1].
        backtrack:
          i=2: pick 2. current=[1,1,2]. ADD ✓.
      i=2: pick 2. current=[1,2].
        backtrack:
          i=1: nums[1]==nums[0] AND !used[0]? used[0]=T → FALSE. Pick.
            current=[1,2,1]. ADD ✓.
    pop 1. used=[F,F,F].
  i=1: nums[1]=1. nums[1]==nums[0]=1 AND !used[0]=true → SKIP! ✗
  i=2: nums[2]=2. Pick.
    used=[F,F,T], current=[2].
    backtrack:
      i=0: pick 1. current=[2,1].
        i=1: nums[1]==nums[0] AND !used[0]? used[0]=T → FALSE. Pick.
          current=[2,1,1]. ADD ✓.
      i=1: nums[1]==nums[0] AND !used[0]=true → SKIP!
```

**Result: [[1,1,2], [1,2,1], [2,1,1]]** ✓

3 unique permutations (not 6, because two 1s are identical).

---

## 🔍 The One-Line Difference from Permutations I

```cpp
// Permutations I (distinct):
if (used[i]) continue;

// Permutations II (duplicates):
if (used[i]) continue;
if (i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue;  // ← ADDED
```

Plus `sort(nums)` at the beginning.

---

## 🔍 Why `!used[i-1]` (Not `used[i-1]`)

Both `!used[i-1]` and `used[i-1]` produce correct results,
but they prune differently:

-   `!used[i-1]` → "skip if previous twin is NOT used yet" → forces left-to-right order.
-   `used[i-1]` → "skip if previous twin IS used" → forces right-to-left order.

`!used[i-1]` prunes MORE aggressively (earlier in the tree) → faster.
Both are correct. `!used[i-1]` is the standard choice.

---

### 🧠 Memory of the Unique Arrangements Law

-   **Sort the array** (groups duplicates)
-   **Same as Permutations I** (`used[]` array, loop from 0)
-   **Add one skip:** `if (i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue`
-   **Forces duplicates to be used in left-to-right order**
-   **`!used[i-1]`** = "don't pick me if my left twin hasn't been picked yet"
-   **Time:** O(n! / (d1! × d2! × ...)) unique permutations × O(n)
-   **Space:** O(n)

Thus is remembered the saga of **Permutations II**,
where the Oracle sorted the array to line up the twins,
then built permutations with the used[] array --
but silenced any twin that tried to be chosen
before its elder sibling --
ensuring every unique arrangement appeared exactly once. 🔀🚫✨
