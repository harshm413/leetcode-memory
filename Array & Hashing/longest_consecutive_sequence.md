## 🏔️⛓️ _The March of Unbroken Steps: The Longest Consecutive Sequence Saga_

> _"Across the highlands, scattered stones bore numbers —
> some isolated, some forming trails
> where each stone differed from its neighbor by exactly one.
>
> Travelers sought the longest unbroken trail
> they could walk by stepping from `x` to `x+1` without gaps.
>
> The Mapmaker was called with a challenge:
>
> **'Find the longest consecutive sequence of numbers.
> But you cannot sort the stones —
> that would take O(N log N) time.
> You must find the answer in O(N).'**
>
> The Mapmaker thought carefully.
>
> Sorting would reveal all sequences easily —
> but the Queen had forbidden it.
>
> Then he realized the key:
>
> **Use a Set to mark which stones exist.
> For each stone, check if it's the START of a sequence
> (no stone at x-1 exists).
> If it is — walk forward (x, x+1, x+2, ...)
> counting steps until the trail breaks.
> Track the longest trail found.**
>
> By only starting at sequence beginnings,
> each stone is visited at most twice —
> once when inserted into the set,
> once when walked as part of a sequence.
>
> O(N) time. No sorting. Pure presence checks."_

---

This is the saga of **Longest Consecutive Sequence**.

You are given an unsorted array of integers `nums`.

Your task:

- Find the length of the **longest consecutive elements sequence**.
- Your algorithm must run in **O(N) time**.

```
Input:  [100, 4, 200, 1, 3, 2]
Output: 4
Explanation: The longest consecutive sequence is [1, 2, 3, 4]

Input:  [0, 3, 7, 2, 5, 8, 4, 6, 0, 1]
Output: 9
Explanation: [0, 1, 2, 3, 4, 5, 6, 7, 8]
```

---

## 🧠 The Oracle's Core Insight — Start Only at Sequence Beginnings

The naive approach:

```
Sort the array → O(N log N)
Walk through and count consecutive runs → O(N)
Total: O(N log N)
```

This works but violates the O(N) requirement.

The key insight:

> **Use a HashSet for O(1) presence checks.
> For each number x, check if x-1 exists.
> If x-1 does NOT exist → x is the START of a sequence.
> Walk forward from x (x+1, x+2, ...) counting steps.
> Track the longest sequence found.**

Why is this O(N)?

```
Each number is inserted into the set once: O(N)
Each number is checked as a potential start: O(N)
Each number is walked at most once (only when part of a sequence starting earlier): O(N)
Total: O(N)
```

The trick: by only starting at sequence beginnings (where x-1 doesn't exist),
we ensure each sequence is counted exactly once,
and each number is visited at most twice total.

---

### 📜 The Map and the Marks

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## 🗺️ The Mapmaker's Ritual

```cpp
int longestConsecutive(vector<int>& nums) {
    unordered_set<int> stones(nums.begin(), nums.end());
    int longest = 0;
```

All stones were stamped into the set —
a quick ledger of presence.

`stones.count(x)` would answer in O(1):
"Does stone `x` exist?"

`longest` tracked the longest trail discovered so far.

---

## 🔎 Seek the Trailheads

```cpp
    for (int x : stones) {
```

The Mapmaker walked through every stone in the set.

---

```cpp
        if (!stones.count(x - 1)) {
```

For each stone `x`, he asked:

> **"Is there a stone at `x-1`?"**

If NO — `x` is a **trailhead** —
the true beginning of a consecutive sequence.

If YES — `x` is in the middle of a sequence
that starts earlier. Skip it.
It will be counted when we process its trailhead.

> _"Only from trailheads do we begin counting,
> so each path is measured once without repetition."_

---

## 🏃 The Walk Along the Path

```cpp
            int current = x;
            int length = 1;
```

The Mapmaker stood at the trailhead `x`.

`current` tracked the current stone being examined.
`length` counted how many consecutive stones existed.

---

```cpp
            while (stones.count(current + 1)) {
                current++;
                length++;
            }
```

The Mapmaker stepped forward:
`x → x+1 → x+2 → ...`

As long as `current + 1` existed in the set,
he advanced and incremented the length.

When `current + 1` did NOT exist,
the trail had ended.

---

```cpp
            longest = max(longest, length);
        }
    }
    return longest;
}
```

After walking the entire sequence starting from `x`,
the Mapmaker compared its length with the longest seen so far.

He continued checking other stones,
but only those that were trailheads would trigger a new walk.

When all stones had been examined,
`longest` held the answer.

---

### 🎺 The Trial of Many Stones

```cpp
int main() {
    vector<int> nums1 = {100, 4, 200, 1, 3, 2};
    cout << longestConsecutive(nums1) << endl; // expected: 4

    vector<int> nums2 = {0, 3, 7, 2, 5, 8, 4, 6, 0, 1};
    cout << longestConsecutive(nums2) << endl; // expected: 9

    return 0;
}
```

---

**Trace for `[100, 4, 200, 1, 3, 2]`:**

**Set after insertion:** `{100, 4, 200, 1, 3, 2}`

| Stone x | x-1 exists? | Trailhead? | If yes, walk sequence | Length | longest |
|---------|-------------|------------|-----------------------|--------|---------|
| 100     | No (99 ✗)   | Yes        | 100 → 101✗ (stop)     | 1      | 1       |
| 4       | No (3 ✓)    | No         | Skip                  | —      | 1       |
| 200     | No (199 ✗)  | Yes        | 200 → 201✗ (stop)     | 1      | 1       |
| 1       | No (0 ✗)    | Yes        | 1→2→3→4→5✗ (stop)     | 4      | 4       |
| 3       | Yes (2 ✓)   | No         | Skip                  | —      | 4       |
| 2       | Yes (1 ✓)   | No         | Skip                  | —      | 4       |

**Answer: 4** ✓

The sequence `[1, 2, 3, 4]` was the longest.

---

**Trace for `[0, 3, 7, 2, 5, 8, 4, 6, 0, 1]`:**

**Set after insertion:** `{0, 1, 2, 3, 4, 5, 6, 7, 8}` (duplicates removed)

| Stone x | x-1 exists? | Trailhead? | If yes, walk sequence          | Length | longest |
|---------|-------------|------------|--------------------------------|--------|---------|
| 0       | No (-1 ✗)   | Yes        | 0→1→2→3→4→5→6→7→8→9✗ (stop)    | 9      | 9       |
| 1       | Yes (0 ✓)   | No         | Skip                           | —      | 9       |
| 2       | Yes (1 ✓)   | No         | Skip                           | —      | 9       |
| ...     | ...         | No         | Skip                           | —      | 9       |

**Answer: 9** ✓

The entire sequence `[0, 1, 2, 3, 4, 5, 6, 7, 8]` was consecutive.

---

## ⚠️ Why Not Start from Every Number?

A common mistake:

```cpp
for (int x : stones) {
    int length = 1;
    while (stones.count(x + 1)) {
        x++;
        length++;
    }
    longest = max(longest, length);
}
```

This looks correct but is inefficient.

For the sequence `[1, 2, 3, 4]`:
- Starting from 1: walk 1→2→3→4 (4 steps)
- Starting from 2: walk 2→3→4 (3 steps)
- Starting from 3: walk 3→4 (2 steps)
- Starting from 4: walk 4 (1 step)

Total: 4+3+2+1 = 10 steps for just 4 numbers.

For a sequence of length N, this becomes O(N²).

By checking `if (!stones.count(x - 1))` first,
we only start from sequence beginnings,
ensuring each number is walked at most once.

---

### 🧠 Memory of the Trail Law

- **Use `unordered_set`** for O(1) presence checks
- **Only start counting at trailheads:** check `if (!stones.count(x - 1))`
- **Walk forward:** `while (stones.count(current + 1))` increment length
- **Track longest:** `longest = max(longest, length)`
- **Time:** O(N) — each number inserted once, checked once, walked at most once
- **Space:** O(N) — the set stores all unique numbers
- **Edge cases:**
  - Empty array → return 0
  - Single element → return 1
  - All duplicates → return 1
  - No consecutive numbers → return 1 (each number is its own sequence)
  - Negative numbers → works fine (x-1 check handles negatives)

Thus is remembered the saga of **Longest Consecutive Sequence**,
where the Mapmaker refused to sort the stones,
instead marking their presence in a set —
seeking only the trailheads where sequences began,
walking forward from each start,
counting unbroken steps —
and discovering the longest march
of neighboring stones
in a single O(N) pass. 🏔️⛓️✨
