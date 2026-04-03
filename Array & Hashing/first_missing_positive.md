## 🧭⚔️ _The Lost Throne of the Smallest King: The First Missing Positive Saga_

> _"In the Kingdom of Integers,
> numbers wandered freely across the land —
> positives mingled with negatives,
> zeros hid in shadows,
> duplicates caused confusion.
>
> The Oracle was summoned before the Queen
> and given a sharp command:
>
> **'Find the smallest positive number
> that does NOT exist in this realm.
> The first missing king —
> the one whose throne stands empty.'**
>
> The Oracle could not build a new palace.
> No extra ledgers were allowed.
> The decree was absolute:
>
> **O(N) time. O(1) extra space.**
>
> She studied the chaotic array before her.
>
> Then a revelation struck:
>
> **If the array has length N,
> the answer MUST lie between 1 and N+1.**
>
> Why? Because if the array perfectly contained [1, 2, 3, ..., N],
> the answer would be N+1.
> If even one number from 1 to N was missing,
> that missing number IS the answer.
>
> So the Oracle devised a bold strategy:
>
> **Turn the array itself into a throne room.
> Place every rightful number onto its destined throne —
> number 1 at index 0,
> number 2 at index 1,
> number X at index X-1.
>
> Then walk the thrones in order.
> The first throne where the rightful king is missing
> reveals the answer.'**
>
> No extra memory. No sorting.
> Just cyclic swapping until the realm stabilizes."_

---

This is the saga of **First Missing Positive**.

You are given an unsorted integer array `nums`.

Your task:

- Find the **smallest missing positive integer**.
- **Time:** O(N)
- **Space:** O(1) extra space (in-place modification allowed)

```
Input:  [1, 2, 0]
Output: 3

Input:  [3, 4, -1, 1]
Output: 2

Input:  [7, 8, 9, 11, 12]
Output: 1
```

---

## 🧠 The Oracle's Core Insight — The Array IS the Throne Room

The key insight that unlocks O(1) space:

> **For an array of length N,
> the answer is guaranteed to be in the range [1, N+1].**

So we only care about numbers in the range `[1, N]`.
Everything else — negatives, zeros, numbers > N — is irrelevant.

The Oracle's strategy:

```
Step 1: Place every valid number (1 to N) at its "home" index.
        Number 1 belongs at index 0.
        Number X belongs at index X-1.

Step 2: Walk the array from index 0 to N-1.
        The first index i where nums[i] != i+1
        means the number (i+1) is missing.

Step 3: If all indices have their rightful numbers,
        return N+1.
```

This is called **Cyclic Sort** — using the array indices themselves
as a hash table to mark which numbers exist.

---

### 📜 The Scroll of the Throne Room

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ Phase 1 — The Cyclic Placement Ritual

_Place every number on its rightful throne_

```cpp
int firstMissingPositive(vector<int>& nums) {
    int n = nums.size();
```

The Oracle surveyed the realm — `n` thrones existed.

---

```cpp
    for (int i = 0; i < n; i++) {
        while (nums[i] > 0 && nums[i] <= n
               && nums[nums[i] - 1] != nums[i]) {
            swap(nums[i], nums[nums[i] - 1]);
        }
    }
```

At position `i`, the Oracle asked three questions:

**Is `nums[i]` a valid positive number?**
```
nums[i] > 0
```
Negatives and zeros are irrelevant.

**Is `nums[i]` within the valid range [1, N]?**
```
nums[i] <= n
```
Numbers larger than `n` can never be the answer.

**Is `nums[i]` already at its rightful throne?**
```
nums[nums[i] - 1] != nums[i]
```
If `nums[i] = 5`, its rightful throne is index `4`.
Check: is `nums[4]` already `5`?
If yes — stop swapping (duplicate or already placed).
If no — swap `nums[i]` into its rightful place.

The while loop continued until position `i` was stable —
either holding a correctly placed number
or holding garbage that could not be placed.

---

## � Phase 2 — Find the First Empty Throne

```cpp
    for (int i = 0; i < n; i++) {
        if (nums[i] != i + 1) {
            return i + 1;
        }
    }
```

After the cyclic placement, the Oracle walked the throne room.

At each index `i`, she checked:
**Is the rightful king sitting here?**

If `nums[i] != i+1` — the throne was empty.
The missing king was `i+1`.

---

## 🌟 Phase 3 — All Thrones Filled

```cpp
    return n + 1;
}
```

If the loop completed without finding an empty throne,
the array perfectly contained `[1, 2, 3, ..., n]`.

The smallest missing positive was `n+1`.

---

### 🎺 The Trial of the Disordered Realm

```cpp
int main() {
    vector<int> nums1 = {3, 4, -1, 1};
    cout << firstMissingPositive(nums1) << endl; // expected: 2

    vector<int> nums2 = {1, 2, 0};
    cout << firstMissingPositive(nums2) << endl; // expected: 3

    vector<int> nums3 = {7, 8, 9, 11, 12};
    cout << firstMissingPositive(nums3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `[3, 4, -1, 1]` (n=4):**

**Phase 1 — Cyclic Placement:**

| i | nums[i] | Valid? | Target | Swap? | Array After      |
|---|---------|--------|--------|-------|------------------|
| 0 | 3       | Yes    | 2      | Yes   | [-1, 4, 3, 1]    |
| 0 | -1      | No     | —      | No    | [-1, 4, 3, 1]    |
| 1 | 4       | Yes    | 3      | Yes   | [-1, 1, 3, 4]    |
| 1 | 1       | Yes    | 0      | Yes   | [1, -1, 3, 4]    |
| 1 | -1      | No     | —      | No    | [1, -1, 3, 4]    |
| 2 | 3       | Yes    | 2      | No    | [1, -1, 3, 4]    |
| 3 | 4       | Yes    | 3      | No    | [1, -1, 3, 4]    |

After Phase 1: `[1, -1, 3, 4]`

**Phase 2 — Find Empty Throne:**

| i | nums[i] | Expected | Match? | Action      |
|---|---------|----------|--------|-------------|
| 0 | 1       | 1        | Yes    | continue    |
| 1 | -1      | 2        | No     | return **2** |

**Answer: 2** ✓

---

**Full trace for `[1, 2, 0]` (n=3):**

**Phase 1:** All numbers already at correct positions or invalid.

After Phase 1: `[1, 2, 0]`

**Phase 2:**

| i | nums[i] | Expected | Match? | Action      |
|---|---------|----------|--------|-------------|
| 0 | 1       | 1        | Yes    | continue    |
| 1 | 2       | 2        | Yes    | continue    |
| 2 | 0       | 3        | No     | return **3** |

**Answer: 3** ✓

---

**Full trace for `[7, 8, 9, 11, 12]` (n=5):**

**Phase 1:** All numbers > 5, so all invalid. No swaps.

After Phase 1: `[7, 8, 9, 11, 12]` (unchanged)

**Phase 2:**

| i | nums[i] | Expected | Match? | Action      |
|---|---------|----------|--------|-------------|
| 0 | 7       | 1        | No     | return **1** |

**Answer: 1** ✓

---

## ⚠️ Why the While Loop, Not Just One Swap?

A common mistake is to write:

```cpp
if (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
    swap(nums[i], nums[nums[i] - 1]);
}
```

This only swaps once per position.

But after swapping, the NEW number at position `i`
might also need to be placed elsewhere.

Example: `[3, 1, 2]`

```
i=0: nums[0]=3 → swap with nums[2] → [2, 1, 3]
     Now nums[0]=2 → needs another swap → [1, 2, 3]
```

Without the while loop, `2` would stay at index `0` incorrectly.

The while loop ensures position `i` is fully stabilized.

---

### 🧠 Memory of the Lost Throne Law

- **Key insight:** For array of length N, answer is in range [1, N+1]
- **Phase 1 — Cyclic Placement:**
  - For each position `i`, while `nums[i]` is valid (1 to N) and not at correct position:
    - Swap `nums[i]` with `nums[nums[i] - 1]`
  - Valid means: `nums[i] > 0 && nums[i] <= n && nums[nums[i]-1] != nums[i]`
  - While loop ensures position stabilizes before moving forward
- **Phase 2 — Find Empty Throne:**
  - Walk indices 0 to n-1
  - First index `i` where `nums[i] != i+1` → return `i+1`
- **Phase 3 — All Thrones Filled:**
  - If loop completes → return `N+1`
- **Why O(N) time despite nested while?**
  - Each number swapped at most once to its correct position
  - Total swaps ≤ N, amortized O(1) per element
- **Space:** O(1) — only in-place swaps
- **Edge cases:**
  - All negatives/zeros → Phase 2 returns 1
  - All numbers > N → Phase 2 returns 1
  - Perfect [1,2,...,N] → return N+1

Thus is remembered the saga of **First Missing Positive**,
where the Oracle transformed the chaotic array
into a throne room of destiny —
placing every rightful king upon their numbered throne
through cyclic swapping,
until the realm stabilized
and the first empty throne revealed
the smallest missing king. 🧭⚔️✨
