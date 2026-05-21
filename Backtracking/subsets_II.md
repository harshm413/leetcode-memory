## 🧩🚫 _The Tree Without Twins: The Subsets II Saga_

> \_"The Oracle was given an array of integers --
> but this time, the array could contain **DUPLICATES**.
>
> She was commanded:
>
> **'Generate all possible subsets.
> The solution must NOT contain duplicate subsets.'**
>
> With distinct elements, the Subsets template worked perfectly.
> But with duplicates like `[1, 2, 2]`:
> -   Picking the first `2` and skipping the second → `[1, 2]`
> -   Skipping the first `2` and picking the second → `[1, 2]`
> -   Same subset generated twice. Forbidden.
>
> The Oracle devised a simple rule:
>
> **Sort the array first.
> Then at each level of the recursion,
> if the current element equals the previous one
> at the SAME level -- SKIP it.**
>
> This ensured that among duplicate elements,
> only the FIRST occurrence could start a new branch.
> The rest were silenced.
>
> Sort + skip duplicates at the same level.
> The twins were eliminated."\_

---

This is the saga of **Subsets II**.

Given an array `nums` that **may contain duplicates**:

-   Return all possible subsets.
-   The solution must NOT contain duplicate subsets.

```
Input:  nums = [1, 2, 2]
Output: [[], [1], [1,2], [1,2,2], [2], [2,2]]

Input:  nums = [0]
Output: [[], [0]]
```

---

## 🧠 The Oracle's Core Insight -- Sort + Skip Duplicates at Same Level

**Step 1: Sort the array.**
This groups duplicates together: `[2, 1, 2]` → `[1, 2, 2]`.

**Step 2: In the backtracking loop, skip duplicates at the same level.**

```cpp
if (i > start && nums[i] == nums[i - 1]) continue;
```

**Why `i > start`?**
-   `i == start` → this is the FIRST element we're considering at this level. Always allowed.
-   `i > start` → we've already processed `nums[i-1]` at this level.
    If `nums[i] == nums[i-1]`, picking `nums[i]` would generate the same subset
    as picking `nums[i-1]` did. Skip it.

**Why does this work?**
After sorting, duplicates are adjacent.
At any recursion level, the first duplicate is processed normally.
Subsequent duplicates at the SAME level are skipped.
But duplicates at DEEPER levels (inside the recursion) are allowed --
that's how `[2, 2]` is generated.

```
Time:  O(2ⁿ × n)
Space: O(n)
```

---

### 📜 The Scroll of the Unique Subsets

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🚫 The Backtracking Ritual (With Duplicate Skipping)

```cpp
void backtrack(vector<int>& nums, int start,
               vector<int>& current, vector<vector<int>>& result) {
```

Same structure as Subsets I. One extra line for duplicate skipping.

---

### 📝 Every State Is a Valid Subset

```cpp
    result.push_back(current);
```

Same as Subsets I -- add the current state before exploring further.

---

### 🔁 Choose the Next Element

```cpp
    for (int i = start; i < (int)nums.size(); i++) {
```

Loop from `start` onward. Same as Subsets I.

---

### 🚫 Skip Duplicates at the Same Level

```cpp
        if (i > start && nums[i] == nums[i - 1]) continue;
```

**THE critical line.**

If `nums[i] == nums[i-1]` AND `i > start`:
this element is a duplicate of the previous one AT THIS LEVEL.
Picking it would generate the same subtree as the previous one.
Skip it.

**Why `i > start` and not `i > 0`?**

`i > start` means "not the first element at this recursion level."
`i > 0` would be wrong -- it would skip valid deeper duplicates.

Example: `nums = [1, 2, 2]`, start = 1.
-   i=1: pick first `2`. Recurse. (allowed -- first at this level)
-   i=2: `nums[2]==nums[1]` AND `i > start(1)`. **SKIP.** (duplicate at same level)

But inside the recursion from i=1 (start=2):
-   i=2: `i == start(2)`. NOT skipped. Pick second `2`. → generates `[2, 2]`. ✓

> _"The first twin may speak.
> The second twin at the same level is silenced.
> But deeper in the tree, the second twin may still appear --
> because there, it is the first at ITS level."_

---

### 🌿 Include, Recurse, Backtrack

```cpp
        current.push_back(nums[i]);
        backtrack(nums, i + 1, current, result);
        current.pop_back();
    }
}
```

Same as Subsets I: pick element, recurse with `i+1`, undo.

---

## 🧩 The Main Function

```cpp
vector<vector<int>> subsetsWithDup(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> result;
    vector<int> current;
    backtrack(nums, 0, current, result);
    return result;
}
```

**Sort first** -- groups duplicates together.
Without sorting, the skip condition `nums[i] == nums[i-1]` wouldn't work
(duplicates might not be adjacent).

---

### 🎺 The Trial of the Unique Subsets

```cpp
int main() {
    vector<int> nums1 = {1, 2, 2};
    auto r1 = subsetsWithDup(nums1);
    for (auto& s : r1) {
        cout << "[";
        for (int i = 0; i < (int)s.size(); i++)
            cout << s[i] << (i < (int)s.size()-1 ? "," : "");
        cout << "] ";
    }
    cout << endl;
    // expected: [] [1] [1,2] [1,2,2] [2] [2,2]

    return 0;
}
```

---

**Full recursion trace for nums = [1, 2, 2] (after sorting):**

```
backtrack(start=0, current=[])
  → ADD [].
  i=0: nums[0]=1. Push 1. current=[1].
    backtrack(start=1, current=[1])
      → ADD [1].
      i=1: nums[1]=2. Push 2. current=[1,2].
        backtrack(start=2, current=[1,2])
          → ADD [1,2].
          i=2: nums[2]=2. i==start(2) → allowed. Push 2. current=[1,2,2].
            backtrack(start=3, current=[1,2,2])
              → ADD [1,2,2].
            pop 2. current=[1,2].
        pop 2. current=[1].
      i=2: nums[2]=2 == nums[1]=2 AND i(2) > start(1) → SKIP! ✗
    pop 1. current=[].
  i=1: nums[1]=2. Push 2. current=[2].
    backtrack(start=2, current=[2])
      → ADD [2].
      i=2: nums[2]=2. i==start(2) → allowed. Push 2. current=[2,2].
        backtrack(start=3, current=[2,2])
          → ADD [2,2].
        pop 2. current=[2].
    pop 2. current=[].
  i=2: nums[2]=2 == nums[1]=2 AND i(2) > start(0) → SKIP! ✗
```

**Result: [[], [1], [1,2], [1,2,2], [2], [2,2]]** ✓

6 unique subsets. No duplicates.

**Key moments:**
-   At start=1, i=2: `nums[2]==nums[1]` and `i > start` → SKIPPED.
    This prevents generating `[1, 2]` twice (once from first 2, once from second 2).
-   At start=2, i=2: `i == start` → NOT skipped.
    This allows `[1, 2, 2]` and `[2, 2]` to be generated.

---

**Trace for nums = [4, 4, 4, 1, 4] → sorted: [1, 4, 4, 4, 4]:**

At any level, only the FIRST `4` in the remaining sequence is picked.
Subsequent `4`s at the same level are skipped.
But deeper levels can pick additional `4`s.

Result includes: `[], [1], [1,4], [1,4,4], [1,4,4,4], [1,4,4,4,4], [4], [4,4], [4,4,4], [4,4,4,4]`.

---

## 🔍 The One-Line Difference from Subsets I

```cpp
// Subsets I (distinct elements):
for (int i = start; i < nums.size(); i++) {
    current.push_back(nums[i]);
    backtrack(nums, i + 1, current, result);
    current.pop_back();
}

// Subsets II (with duplicates):
for (int i = start; i < nums.size(); i++) {
    if (i > start && nums[i] == nums[i - 1]) continue;  // ← ONLY CHANGE
    current.push_back(nums[i]);
    backtrack(nums, i + 1, current, result);
    current.pop_back();
}
```

Plus `sort(nums)` at the beginning. That's it.

---

## 🔍 The Duplicate Skip Pattern -- Used Everywhere

This exact `if (i > start && nums[i] == nums[i-1]) continue` pattern
appears in multiple problems:

| Problem              | Uses this skip?                    |
| -------------------- | ---------------------------------- |
| Subsets II (this)    | ✅ Yes                             |
| Combination Sum II   | ✅ Yes (same skip + target check)  |
| Permutations II      | Similar (uses `used[]` array instead) |
| 3Sum / 4Sum          | ✅ Yes (skip duplicate pointers)   |

Learn it once, apply it everywhere.

---

### 🧠 Memory of the Unique Subsets Law

-   **Sort the array first** (groups duplicates together)
-   **Same as Subsets I** but add ONE line:
    `if (i > start && nums[i] == nums[i-1]) continue`
-   **`i > start`** = not the first element at this recursion level
-   **First duplicate at a level → allowed. Second+ → skipped.**
-   **Deeper levels can still pick duplicates** (they're "first" at their level)
-   **This pattern reappears** in Combination Sum II, 3Sum, 4Sum
-   **Time:** O(2ⁿ × n)
-   **Space:** O(n)
-   **Edge cases:**
    -   All same elements `[2,2,2]` → `[[], [2], [2,2], [2,2,2]]`
    -   All distinct → same as Subsets I
    -   Single element → `[[], [x]]`

Thus is remembered the saga of **Subsets II**,
where the Oracle sorted the array to group the twins,
then walked the same backtracking tree as before --
but at each level, silenced every twin
that tried to speak after its sibling had already spoken --
ensuring that no duplicate subset was ever born,
while still allowing twins to appear together
in deeper branches of the tree. 🧩🚫✨
