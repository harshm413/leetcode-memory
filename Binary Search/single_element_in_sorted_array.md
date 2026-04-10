## 🌗🔍 _The Lone Flame in the Hall of Twins: The Single Element in a Sorted Array Saga_

> \_"In the Hall of Paired Souls,
> every number arrived with a twin --
> two by two they sat in sorted order,
> perfect pairs stretching across the hall.
>
> But one soul had no twin.
> One flame burned alone.
>
> The Oracle was summoned:
>
> **'Find the single element --
> the one number that appears exactly once
> while all others appear exactly twice.
> The array is sorted. You must find it in O(log N).'**
>
> The Oracle could XOR everything in O(N) --
> but the decree demanded logarithmic time.
> The sorted order held a secret she could exploit.
>
> She studied the pairs.
>
> Before the single element,
> pairs sat at (even, odd) index positions:
> `nums[0]==nums[1]`, `nums[2]==nums[3]`, etc.
>
> After the single element,
> the pairing shifted to (odd, even):
> `nums[5]==nums[6]`, `nums[7]==nums[8]`, etc.
>
> The single element was the exact point
> where this pairing pattern broke.
>
> **Binary search for the break point.
> Check if mid's pair is on the expected side.
> If the pattern holds -- the single is to the right.
> If the pattern is broken -- the single is to the left.**
>
> One binary search. O(log N).
> The lone flame found."\_

---

This is the saga of **Single Element in a Sorted Array**.

You are given a sorted array where every element appears **exactly twice**,
except for one element which appears **exactly once**.

Find the single element in **O(log N)** time.

```
Input:  [1,1,2,3,3,4,4,8,8]
Output: 2

Input:  [3,3,7,7,10,11,11]
Output: 10

Input:  [1]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- The Pairing Pattern Breaks at the Single

In a sorted array of pairs, before the single element:

```
Index:  0  1  2  3  4  5  6  7  8
Value:  1  1  2  3  3  4  4  8  8
        ^--^     ^--^  ^--^  ^--^
        pair     pair  pair  pair
              ^ single element (index 2)
```

Before the single: pairs start at **even** indices.
`nums[0]==nums[1]`, `nums[3]==nums[4]` (if single weren't at 2).

After the single: pairs start at **odd** indices.
`nums[5]==nums[6]`, `nums[7]==nums[8]`.

The rule for checking which side the single is on:

```
If mid is EVEN:
  If nums[mid] == nums[mid+1]  →  pair is intact, single is to the RIGHT
  Else                          →  pair is broken, single is at mid or LEFT

If mid is ODD:
  If nums[mid] == nums[mid-1]  →  pair is intact, single is to the RIGHT
  Else                          →  pair is broken, single is at mid or LEFT
```

A cleaner approach: **always work with even indices**.
If `mid` is odd, step back to `mid - 1` so we always check
the start of a potential pair.

```
Time:  O(log N) -- binary search
Space: O(1)
```

---

### 📜 The Scroll of the Paired Hall

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Pair-Breaking Ritual

```cpp
int singleNonDuplicate(vector<int>& nums) {
    int left = 0, right = nums.size() - 1;
```

Two sentinels at the boundaries of the hall.

---

## 🔁 Halve Until One Element Remains

```cpp
    while (left < right) {
        int mid = left + (right - left) / 2;
```

The Oracle split the hall at the middle.

Note: `left < right` (not `<=`) because we converge
`left` and `right` onto the single element.

---

### 🔧 Force Mid to Even -- Always Check Pair Start

```cpp
        if (mid % 2 == 1) mid--;
```

If `mid` landed on an odd index,
the Oracle stepped back one position to make it even.

This ensured she always stood at the **start** of a potential pair.
The pair would be `(mid, mid+1)` if intact.

> _"Always stand at the beginning of a pair.
> From there, the truth is visible."_

---

### ✅ Pair Is Intact -- Single Is to the Right

```cpp
        if (nums[mid] == nums[mid + 1]) {
            left = mid + 2;
        }
```

`nums[mid] == nums[mid+1]` -- the pair at this position was intact.

The pairing pattern had not yet been disrupted.
The single element must be somewhere to the **right**.

`left = mid + 2` -- skip past this complete pair.

---

### ❌ Pair Is Broken -- Single Is Here or to the Left

```cpp
        else {
            right = mid;
        }
    }
```

`nums[mid] != nums[mid+1]` -- the expected pair was broken.

The single element disrupted the pattern at or before `mid`.
The single is at `mid` or somewhere to the **left**.

`right = mid` -- keep `mid` in the search space
(it might be the single element itself).

---

## 🌗 The Lone Flame Is Revealed

```cpp
    return nums[left];
}
```

When `left == right`, the sentinels converged on the single element.

---

### 🎺 The Trial of the Paired Hall

```cpp
int main() {
    vector<int> nums1 = {1,1,2,3,3,4,4,8,8};
    cout << singleNonDuplicate(nums1) << endl; // expected: 2

    vector<int> nums2 = {3,3,7,7,10,11,11};
    cout << singleNonDuplicate(nums2) << endl; // expected: 10

    vector<int> nums3 = {1};
    cout << singleNonDuplicate(nums3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `[1,1,2,3,3,4,4,8,8]`, target single = 2:**

| Step | left | right | mid | mid%2? | Adjusted mid | nums[mid]==nums[mid+1]? | Action       |
| ---- | ---- | ----- | --- | ------ | ------------ | ----------------------- | ------------ |
| 1    | 0    | 8     | 4   | even   | 4            | nums[4]=3, nums[5]=4 → No | right = 4  |
| 2    | 0    | 4     | 2   | even   | 2            | nums[2]=2, nums[3]=3 → No | right = 2  |
| 3    | 0    | 2     | 1   | odd    | 0            | nums[0]=1, nums[1]=1 → Yes | left = 2  |
| 4    | 2    | 2     | --  | --     | --           | left == right → exit    | --           |

**Answer: nums[2] = 2** ✓

Step 1: Pair at index 4 is broken (3≠4) → single is at 4 or left.
Step 2: Pair at index 2 is broken (2≠3) → single is at 2 or left.
Step 3: Mid adjusted to 0. Pair at index 0 is intact (1==1) → single is right. left=2.
Converged at index 2. The lone flame was `2`.

---

**Full trace for `[3,3,7,7,10,11,11]`, target single = 10:**

| Step | left | right | mid | Adjusted mid | nums[mid]==nums[mid+1]? | Action       |
| ---- | ---- | ----- | --- | ------------ | ----------------------- | ------------ |
| 1    | 0    | 6     | 3   | 2            | nums[2]=7, nums[3]=7 → Yes | left = 4  |
| 2    | 4    | 6     | 5   | 4            | nums[4]=10, nums[5]=11 → No | right = 4 |
| 3    | 4    | 4     | --  | --           | left == right → exit    | --           |

**Answer: nums[4] = 10** ✓

Step 1: mid=3 (odd) → adjusted to 2. Pair intact → single is right.
Step 2: mid=5 (odd) → adjusted to 4. Pair broken → single is here or left.
Converged at index 4.

---

**Trace for `[1,1,2,2,3]`, target single = 3 (single at end):**

| Step | left | right | mid | Adjusted mid | nums[mid]==nums[mid+1]? | Action       |
| ---- | ---- | ----- | --- | ------------ | ----------------------- | ------------ |
| 1    | 0    | 4     | 2   | 2            | nums[2]=2, nums[3]=2 → Yes | left = 4  |
| 2    | 4    | 4     | --  | --           | left == right → exit    | --           |

**Answer: nums[4] = 3** ✓ — single at the very end.

---

**Trace for `[1,2,2,3,3]`, target single = 1 (single at start):**

| Step | left | right | mid | Adjusted mid | nums[mid]==nums[mid+1]? | Action       |
| ---- | ---- | ----- | --- | ------------ | ----------------------- | ------------ |
| 1    | 0    | 4     | 2   | 2            | nums[2]=2, nums[3]=3 → No | right = 2 |
| 2    | 0    | 2     | 1   | 0            | nums[0]=1, nums[1]=2 → No | right = 0 |
| 3    | 0    | 0     | --  | --           | left == right → exit    | --           |

**Answer: nums[0] = 1** ✓ — single at the very start.

---

## 🔍 Why Force Mid to Even?

Without the even-index trick, you'd need separate logic
for odd and even `mid` values -- checking `mid-1` or `mid+1`
depending on parity. Error-prone and messy.

By always stepping `mid` back to even:
-   You always check `(mid, mid+1)` as a potential pair.
-   One comparison tells you which side the single is on.
-   The logic is clean, uniform, and impossible to get wrong.

---

### 🧠 Memory of the Lone Flame Law

-   **Sorted array** where every element appears twice except one
-   **Pairing pattern:** before the single → pairs start at even indices; after → shifted
-   **Binary search** with `left < right` -- converge onto the single
-   **Force mid to even:** `if (mid % 2 == 1) mid--`
-   **Check pair:** compare `nums[mid]` with `nums[mid+1]`
    -   If equal → pair intact → single is to the right → `left = mid + 2`
    -   If not equal → pair broken → single is at mid or left → `right = mid`
-   **Return `nums[left]`** when `left == right`
-   **Time:** O(log N) -- true binary search, not XOR
-   **Space:** O(1)
-   **Edge cases:**
    -   Single element array → return it directly
    -   Single at start or end → handled naturally
    -   Array of length 1 → loop never runs, returns nums[0]

Thus is remembered the saga of **Single Element in a Sorted Array**,
where the Oracle did not XOR the crowd into submission
but instead read the pairing pattern of the sorted hall --
always standing at the start of a pair,
checking if the twin was present,
and halving the search space based on
whether the pattern held or had been broken --
until the lone flame that burned without a twin
was found in logarithmic time. 🌗🔍✨
