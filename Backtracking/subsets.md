## 🧩🌿 _The Tree of Include and Exclude: The Subsets Saga_

> \_"The Oracle was given an array of DISTINCT integers.
>
> She was commanded:
>
> **'Generate ALL possible subsets (the power set).'**
>
> A subset is any selection of elements --
> including the empty set and the full array.
> For `n` elements, there are `2ⁿ` subsets.
>
> The Oracle saw a binary tree of choices:
>
> **At each element, two branches:
> INCLUDE it in the current subset, or EXCLUDE it.**
>
> Starting from index 0, she walked the array.
> At each index, she forked into two futures --
> one where the element was taken,
> one where it was left behind.
>
> When she reached the end of the array,
> whatever she had collected was ONE valid subset.
>
> This was the **include/exclude** pattern --
> the foundation of ALL subsequence/subset problems."\_

---

This is the saga of **Subsets (Power Set)**.

Given an array `nums` of **distinct** integers:

-   Return all possible subsets.
-   The solution must NOT contain duplicate subsets.

```
Input:  nums = [1, 2, 3]
Output: [[], [1], [2], [3], [1,2], [1,3], [2,3], [1,2,3]]

Input:  nums = [0]
Output: [[], [0]]
```

---

## 🧠 The Oracle's Core Insight -- Include or Exclude at Each Index

At each index `i`, two choices:
1. **Include** `nums[i]` in the current subset → move to `i + 1`.
2. **Exclude** `nums[i]` from the current subset → move to `i + 1`.

When `i == n` (past the last element), the current subset is complete.
Add it to the result.

This generates exactly `2ⁿ` subsets (each element is either in or out).

```
                        []
                 /              \
            [1]                  []
          /      \            /      \
      [1,2]     [1]       [2]       []
      /   \    /   \     /   \    /   \
  [1,2,3][1,2][1,3][1] [2,3][2] [3]  []
```

Every leaf is a valid subset.

---

## 🧠 Alternative View -- "Start Index" Pattern

Instead of include/exclude, use the **"pick from index onward"** pattern:

```
backtrack(start, current):
  Add current to result (every state is a valid subset).
  For i = start to n-1:
    current.push(nums[i])
    backtrack(i + 1, current)
    current.pop()  (undo the choice)
```

At each level, we choose which element to add NEXT.
By starting from `start`, we avoid generating duplicates
(we never pick an element before the current position).

Both approaches generate the same subsets. The "start index" pattern
is more commonly used because it extends naturally to
Combination Sum, Subsets II, and Permutations.

```
Time:  O(2ⁿ × n) -- 2ⁿ subsets, each takes O(n) to copy
Space: O(n) -- recursion depth + current subset
```

---

### 📜 The Scroll of the Power Set

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🌿 The Backtracking Ritual (Start Index Pattern)

```cpp
void backtrack(vector<int>& nums, int start,
               vector<int>& current, vector<vector<int>>& result) {
```

The Oracle carried:
-   `nums` -- the array of choices.
-   `start` -- the index from which she may pick next.
-   `current` -- the subset being built.
-   `result` -- all completed subsets.

---

### 📝 Every State Is a Valid Subset

```cpp
    result.push_back(current);
```

**This is the key difference from other backtracking problems.**

In Combination Sum, we only add when target == 0.
In Permutations, we only add when current.size() == n.
In Subsets, **EVERY state is valid** -- including the empty set.

We add the current subset to the result BEFORE exploring further.

> _"Every moment in the journey is a valid destination.
> The empty set. A single element. The full array.
> All are subsets. All are recorded."_

---

### 🔁 Choose the Next Element to Include

```cpp
    for (int i = start; i < (int)nums.size(); i++) {
```

Try adding each element from `start` onward.
Starting from `start` (not 0) ensures:
-   No element is used before its turn.
-   No duplicate subsets are generated.
-   Elements appear in the same relative order as the input.

---

### 🌿 Include This Element

```cpp
        current.push_back(nums[i]);
```

Add `nums[i]` to the current subset.
The subset grows by one element.

---

### 🔁 Recurse -- Explore All Subsets That Include This Element

```cpp
        backtrack(nums, i + 1, current, result);
```

Move to `i + 1` -- only elements AFTER the current one
can be added next. This prevents duplicates and maintains order.

---

### 🔄 Backtrack -- Undo the Choice

```cpp
        current.pop_back();
    }
}
```

Remove the element. Try the next option in the loop.

> _"The Oracle takes the element, explores all futures with it,
> then puts it back and tries the next.
> This is the heartbeat of backtracking:
> choose, explore, unchoose."_

---

## 🧩 The Main Function

```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> current;
    backtrack(nums, 0, current, result);
    return result;
}
```

Start from index 0 with an empty subset.
The backtracking generates all `2ⁿ` subsets.

---

### 🎺 The Trial of the Power Set

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3};
    auto r1 = subsets(nums1);
    for (auto& s : r1) {
        cout << "[";
        for (int i = 0; i < (int)s.size(); i++) {
            cout << s[i] << (i < (int)s.size()-1 ? "," : "");
        }
        cout << "] ";
    }
    cout << endl;
    // expected: [] [1] [1,2] [1,2,3] [1,3] [2] [2,3] [3]

    return 0;
}
```

---

**Full recursion trace for nums = [1, 2, 3]:**

```
backtrack(start=0, current=[])
  → ADD [] to result.
  i=0: push 1. current=[1].
    backtrack(start=1, current=[1])
      → ADD [1] to result.
      i=1: push 2. current=[1,2].
        backtrack(start=2, current=[1,2])
          → ADD [1,2] to result.
          i=2: push 3. current=[1,2,3].
            backtrack(start=3, current=[1,2,3])
              → ADD [1,2,3] to result.
              (start=3 >= n, loop doesn't run)
            pop 3. current=[1,2].
        pop 2. current=[1].
      i=2: push 3. current=[1,3].
        backtrack(start=3, current=[1,3])
          → ADD [1,3] to result.
        pop 3. current=[1].
    pop 1. current=[].
  i=1: push 2. current=[2].
    backtrack(start=2, current=[2])
      → ADD [2] to result.
      i=2: push 3. current=[2,3].
        backtrack(start=3, current=[2,3])
          → ADD [2,3] to result.
        pop 3. current=[2].
    pop 2. current=[].
  i=2: push 3. current=[3].
    backtrack(start=3, current=[3])
      → ADD [3] to result.
    pop 3. current=[].
```

**Result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]** ✓

8 subsets = 2³. Every possible combination of include/exclude.

---

## 🔄 Include/Exclude Approach (Alternative)

```cpp
void solve(vector<int>& nums, int index,
           vector<int>& current, vector<vector<int>>& result) {
    if (index == (int)nums.size()) {
        result.push_back(current);
        return;
    }

    // INCLUDE nums[index]
    current.push_back(nums[index]);
    solve(nums, index + 1, current, result);
    current.pop_back();

    // EXCLUDE nums[index]
    solve(nums, index + 1, current, result);
}
```

At each index: two recursive calls.
One with the element included, one without.
Subsets are collected only at the leaves (when `index == n`).

Both approaches produce the same result.
The "start index" pattern is preferred because it generalizes better.

---

## 🔍 Why `start = i + 1` and Not `start = i`?

`start = i + 1` → each element used AT MOST ONCE.
This is correct for subsets (no repetition).

If we used `start = i` → the same element could be picked again.
That would be the **Combination Sum** pattern (unlimited reuse).

| Problem              | Next recursion starts at |
| -------------------- | ------------------------ |
| Subsets (this)       | `i + 1` (use once)       |
| Combination Sum      | `i` (reuse allowed)      |
| Combination Sum II   | `i + 1` (use once + skip duplicates) |

---

## 🔍 Why No Duplicate Subsets?

Since all elements are DISTINCT and we always pick
from `start` onward (never going backward),
each subset is generated exactly once in a canonical order.

`[1, 3]` is generated but `[3, 1]` is never generated
because 3 comes after 1 -- we never pick 1 after picking 3.

---

## 🌐 The Subsets Pattern Family

| Problem              | Modification from Subsets          |
| -------------------- | ---------------------------------- |
| **Subsets (this)**   | Base template                      |
| Subsets II           | Sort + skip duplicates             |
| Combination Sum      | `start = i` (reuse) + target check |
| Combination Sum II   | `start = i+1` + skip duplicates + target |
| Permutations         | No `start` — use `used[]` array    |

Master Subsets → the rest are variations.

---

### 🧠 Memory of the Power Set Law

-   **Every state is a valid subset** — add to result BEFORE the loop
-   **Loop from `start` to n-1:** pick each element, recurse with `i+1`, backtrack
-   **`start = i + 1`** ensures each element used at most once
-   **No sorting needed** (all elements are distinct)
-   **2ⁿ subsets** for n elements (each element: in or out)
-   **Include/Exclude alternative:** two recursive calls per index, collect at leaves
-   **Time:** O(2ⁿ × n) — 2ⁿ subsets, O(n) to copy each
-   **Space:** O(n) — recursion depth
-   **Edge cases:**
    -   Empty array → [[]]
    -   Single element → [[], [x]]
    -   n elements → 2ⁿ subsets

Thus is remembered the saga of **Subsets**,
where the Oracle walked the array element by element,
at each step choosing to include or exclude --
recording every state as a valid subset --
until the tree of choices was fully explored
and all 2ⁿ subsets were gathered
in the power set. 🧩🌿✨
