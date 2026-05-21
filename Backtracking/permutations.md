## 🔀🌿 _Every Possible Arrangement: The Permutations Saga_

> \_"The Oracle was given an array of DISTINCT integers.
>
> She was commanded:
>
> **'Generate ALL possible permutations.'**
>
> A permutation is an arrangement where ORDER matters.
> `[1, 2, 3]` and `[3, 2, 1]` are DIFFERENT permutations.
> For `n` elements, there are `n!` permutations.
>
> This was fundamentally different from Subsets:
>
> **In Subsets:** order doesn't matter. `[1, 2]` = `[2, 1]`.
> We use a `start` index to avoid going backward.
>
> **In Permutations:** order MATTERS. `[1, 2]` ≠ `[2, 1]`.
> We can pick ANY unused element at each position.
> No `start` index. Instead, track which elements are USED.
>
> The Oracle used a `used[]` array:
>
> **At each position, try every element.
> If it's not yet used -- pick it, mark used, recurse.
> When done -- unmark, try the next.**
>
> When the permutation reaches length `n` -- it's complete."\_

---

This is the saga of **Permutations**.

Given an array `nums` of **distinct** integers:

-   Return all possible permutations.

```
Input:  nums = [1, 2, 3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

Input:  nums = [0, 1]
Output: [[0,1],[1,0]]

Input:  nums = [1]
Output: [[1]]
```

---

## 🧠 The Oracle's Core Insight -- Used Array, No Start Index

**Why no `start` index?**

In Subsets/Combinations, we use `start` to enforce order
(never pick an element before the current position).
This prevents `[2, 1]` when `[1, 2]` already exists.

In Permutations, we WANT both `[1, 2]` and `[2, 1]`.
Order matters. Every arrangement is unique.
So we loop from `0` to `n-1` every time --
but skip elements already used in the current permutation.

```
backtrack(current):
  If current.size() == n: add to result. Return.

  For i = 0 to n-1:
    If used[i]: continue.  (already in current permutation)
    used[i] = true.
    current.push(nums[i]).
    backtrack(current).
    current.pop().
    used[i] = false.
```

**`used[i]`** tracks whether `nums[i]` is already in the current permutation.
This prevents using the same element twice in one permutation.

```
Time:  O(n! × n) -- n! permutations, O(n) to copy each
Space: O(n) -- recursion depth + used array
```

---

### 📜 The Scroll of Every Arrangement

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔀 The Backtracking Ritual

```cpp
void backtrack(vector<int>& nums, vector<bool>& used,
               vector<int>& current, vector<vector<int>>& result) {
```

The Oracle carried:
-   `nums` -- the elements to arrange.
-   `used` -- which elements are already in the current permutation.
-   `current` -- the permutation being built.
-   `result` -- all completed permutations.

---

### 🎯 Permutation Complete -- Length Equals n

```cpp
    if ((int)current.size() == (int)nums.size()) {
        result.push_back(current);
        return;
    }
```

The permutation has `n` elements -- it's complete.
Add it to the result.

> _"Every seat is filled.
> Every element has found its place.
> This arrangement is one of the n! possibilities."_

---

### 🔁 Try Every Unused Element

```cpp
    for (int i = 0; i < (int)nums.size(); i++) {
```

Loop from `0` to `n-1`. NOT from `start` -- we consider ALL elements.
This is the key difference from Subsets.

---

### 🚫 Skip If Already Used

```cpp
        if (used[i]) continue;
```

If `nums[i]` is already in the current permutation -- skip it.
Can't use the same element twice in one arrangement.

---

### ✅ Pick This Element

```cpp
        used[i] = true;
        current.push_back(nums[i]);
```

Mark as used. Add to the current permutation.

---

### 🔁 Recurse -- Fill the Next Position

```cpp
        backtrack(nums, used, current, result);
```

Recurse to fill the next position in the permutation.

---

### 🔄 Backtrack -- Undo the Choice

```cpp
        current.pop_back();
        used[i] = false;
    }
}
```

Remove the element. Unmark it as used.
Now it's available for other positions.

> _"Choose, explore, unchoose.
> The element returns to the pool.
> Another position may claim it."_

---

## 🔮 The Main Function

```cpp
vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> current;
    vector<bool> used(nums.size(), false);
    backtrack(nums, used, current, result);
    return result;
}
```

Start with an empty permutation and all elements unused.

---

### 🎺 The Trial of Every Arrangement

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3};
    auto r1 = permute(nums1);
    for (auto& p : r1) {
        for (int x : p) cout << x << " ";
        cout << endl;
    }
    // expected: 1 2 3 | 1 3 2 | 2 1 3 | 2 3 1 | 3 1 2 | 3 2 1

    return 0;
}
```

---

**Full recursion trace for nums = [1, 2, 3]:**

```
backtrack(current=[], used=[F,F,F])
  i=0: pick 1. used=[T,F,F]. current=[1].
    backtrack(current=[1], used=[T,F,F])
      i=0: used[0]=T → skip.
      i=1: pick 2. used=[T,T,F]. current=[1,2].
        backtrack(current=[1,2], used=[T,T,F])
          i=0: skip. i=1: skip.
          i=2: pick 3. current=[1,2,3]. SIZE==3 → ADD [1,2,3]. ✓
          pop 3. used=[T,T,F].
      pop 2. used=[T,F,F].
      i=2: pick 3. used=[T,F,T]. current=[1,3].
        backtrack(current=[1,3], used=[T,F,T])
          i=0: skip. i=2: skip.
          i=1: pick 2. current=[1,3,2]. SIZE==3 → ADD [1,3,2]. ✓
          pop 2. used=[T,F,T].
      pop 3. used=[T,F,F].
    pop 1. used=[F,F,F].
  i=1: pick 2. used=[F,T,F]. current=[2].
    backtrack(current=[2], used=[F,T,F])
      i=0: pick 1. current=[2,1].
        i=2: pick 3. current=[2,1,3]. → ADD [2,1,3]. ✓
      i=2: pick 3. current=[2,3].
        i=0: pick 1. current=[2,3,1]. → ADD [2,3,1]. ✓
    pop 2. used=[F,F,F].
  i=2: pick 3. used=[F,F,T]. current=[3].
    backtrack(current=[3], used=[F,F,T])
      i=0: pick 1. current=[3,1].
        i=1: pick 2. current=[3,1,2]. → ADD [3,1,2]. ✓
      i=1: pick 2. current=[3,2].
        i=0: pick 1. current=[3,2,1]. → ADD [3,2,1]. ✓
```

**Result: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]** ✓

6 permutations = 3! = 6.

---

## 🔄 Swap-Based Approach (Alternative)

Instead of a `used[]` array, swap elements into position:

```cpp
void backtrack(vector<int>& nums, int idx, vector<vector<int>>& result) {
    if (idx == (int)nums.size()) {
        result.push_back(nums);
        return;
    }

    for (int i = idx; i < (int)nums.size(); i++) {
        swap(nums[idx], nums[i]);
        backtrack(nums, idx + 1, result);
        swap(nums[idx], nums[i]);
    }
}
```

At position `idx`, try placing every remaining element (from `idx` onward).
Swap it into position, recurse for the next position, swap back.

**Pros:** no extra `used[]` array. O(1) extra space.
**Cons:** harder to extend to Permutations II (duplicates).

The `used[]` approach is more versatile and recommended for interviews.

---

## 🔍 Subsets vs Permutations -- The Key Differences

| Aspect               | Subsets                    | Permutations               |
| -------------------- | -------------------------- | -------------------------- |
| Order matters?       | No (`[1,2]` = `[2,1]`)    | Yes (`[1,2]` ≠ `[2,1]`)   |
| Loop starts at       | `start` (forward only)     | `0` (any unused element)   |
| Prevents revisiting  | `start` index              | `used[]` array             |
| When to add result   | Every state                | Only when size == n        |
| Count                | 2ⁿ subsets                 | n! permutations            |

---

## 🔍 The Permutations Family

| Problem              | Modification                       |
| -------------------- | ---------------------------------- |
| **Permutations (this)** | Base template with `used[]`     |
| Permutations II      | Sort + skip `if (i>0 && nums[i]==nums[i-1] && !used[i-1])` |
| Next Permutation     | Different algorithm (not backtracking) |

---

### 🧠 Memory of Every Arrangement Law

-   **No `start` index** -- loop from 0 to n-1 every time
-   **`used[i]` array** -- tracks which elements are in the current permutation
-   **Add to result when `current.size() == n`** (permutation complete)
-   **Choose → mark used → recurse → unmark → try next**
-   **Swap alternative:** swap into position, no extra array needed
-   **n! permutations** for n distinct elements
-   **Time:** O(n! × n)
-   **Space:** O(n) -- used array + recursion depth
-   **Edge cases:**
    -   Single element → [[x]]
    -   Two elements → [[a,b], [b,a]]
    -   Empty → [[]]

Thus is remembered the saga of **Permutations**,
where the Oracle at each position in the arrangement
tried every unused element --
marking it as taken, filling the next position,
then releasing it for other futures --
until every seat was filled
and one of the n! arrangements was complete --
then backtracking to discover all the others. 🔀🌿✨
