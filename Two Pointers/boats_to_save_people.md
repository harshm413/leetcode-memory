## ⛵👥 _The Pairing of Heaviest and Lightest: The Boats to Save People Saga_

> \_"The Oracle was given an array of people's weights and a boat limit.
>
> She was commanded:
>
> **'Each boat can carry at most TWO people,
> and the total weight must not exceed the limit.
> What is the MINIMUM number of boats needed?'**
>
> The Oracle sorted the people by weight.
> Then she used two pointers from opposite ends:
>
> **Pair the LIGHTEST with the HEAVIEST.**
> -   If they fit together → one boat for both. Move both pointers.
> -   If they don't fit → the heaviest goes alone. Move only right.
>
> The heaviest person ALWAYS gets on a boat (alone or paired).
> The lightest person gets paired IF possible.
>
> Greedy + two pointers. Minimize boats."\_

---

This is the saga of **Boats to Save People (LeetCode 881)**.

Given an array `people` (weights) and integer `limit`:
-   Each boat carries at most 2 people with total weight ≤ limit.
-   Return the minimum number of boats.

```
Input:  people = [1, 2], limit = 3
Output: 1   (both fit in one boat: 1+2=3 ≤ 3)

Input:  people = [3, 2, 2, 1], limit = 3
Output: 3   (boat 1: [1,2], boat 2: [2], boat 3: [3])

Input:  people = [3, 5, 3, 4], limit = 5
Output: 4   (everyone goes alone — no pair fits)
```

---

## 🧠 The Greedy Insight — Pair Lightest with Heaviest

After sorting, the heaviest person is at the right end.
They MUST get on a boat — the question is: can anyone join them?

The BEST candidate to pair with the heaviest is the LIGHTEST.
If even the lightest can't fit with the heaviest,
then NO ONE can fit with the heaviest. They go alone.

If the lightest CAN fit → pair them. Both are handled. Move both pointers.
If the lightest CAN'T fit → heaviest goes alone. Move only right.

Either way, `right--` (heaviest is handled).
If paired, also `left++` (lightest is handled).

> _"The heaviest always boards. The question is: who joins them?
> The lightest is the best hope. If even the lightest is too heavy —
> the heaviest sails alone. No one else could have fit either."_

---

## 🧠 Why Pairing Lightest with Heaviest Is Optimal

**Claim:** pairing the lightest with the heaviest (when possible)
never leads to a worse solution than any other pairing.

**Proof sketch:** suppose the lightest (L) could pair with someone
other than the heaviest (H). Say L pairs with M (a middle person).

-   L + H ≤ limit (they fit). So this pairing is valid.
-   If we pair L with M instead, then H must go alone or pair with someone else.
-   But H is the heaviest — fewer people can pair with H than with M.
-   Pairing L with H "uses up" L on the hardest-to-pair person.
-   This frees M to potentially pair with someone else.

Greedy choice: always give the heaviest the best chance of pairing.
The lightest is that best chance.

---

### 📜 The Scroll of the Pairing

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⛵ The Two-Pointer Solution

### Sort the people

```cpp
int numRescueBoats(vector<int>& people, int limit) {
    sort(people.begin(), people.end());
    int left = 0, right = people.size() - 1;
    int boats = 0;
```

Sort by weight. Lightest at left, heaviest at right.

---

### Pair from both ends

```cpp
    while (left <= right) {
```

Continue until everyone is on a boat.
`left <= right` (not `<`) because a single remaining person
still needs a boat.

---

### Check if lightest and heaviest can share

```cpp
        if (people[left] + people[right] <= limit) {
            left++;
        }
```

They fit together! The lightest is paired. Move left forward.

If they DON'T fit — the lightest stays (might pair with someone else later).
Only the heaviest boards.

> _"Can the lightest join the heaviest?
> If yes — both board. left advances.
> If no — only the heaviest boards. left stays for the next round."_

---

### Heaviest always boards

```cpp
        right--;
        boats++;
    }
    return boats;
}
```

Regardless of whether pairing happened, the heaviest person is handled.
`right--` always. One boat used. `boats++` always.

The key: `left++` is CONDITIONAL (only if paired).
`right--` and `boats++` are UNCONDITIONAL (heaviest always leaves).

---

### 🎺 The Trial of the Pairing

```cpp
int main() {
    vector<int> p1 = {1, 2};
    cout << numRescueBoats(p1, 3) << endl; // expected: 1

    vector<int> p2 = {3, 2, 2, 1};
    cout << numRescueBoats(p2, 3) << endl; // expected: 3

    vector<int> p3 = {3, 5, 3, 4};
    cout << numRescueBoats(p3, 5) << endl; // expected: 4

    vector<int> p4 = {2, 4, 2, 3, 5, 1};
    cout << numRescueBoats(p4, 5) << endl; // expected: 3

    return 0;
}
```

---

**Full trace for people = [3, 2, 2, 1], limit = 3:**

After sorting: `[1, 2, 2, 3]`

| left | right | people[l] | people[r] | sum | ≤ limit? | Action | boats |
|------|-------|-----------|-----------|-----|----------|--------|-------|
| 0 | 3 | 1 | 3 | 4 | > 3 ✗ | right-- only | 1 |
| 0 | 2 | 1 | 2 | 3 | ≤ 3 ✓ | left++, right-- | 2 |
| 1 | 1 | 2 | 2 | 4 | > 3 ✗ | right-- only | 3 |
| 1 | 0 | left>right, stop | | | | | 3 |

**Answer: 3** ✓

Boats: [3 alone], [1+2], [2 alone].

---

**Trace for people = [3, 5, 3, 4], limit = 5:**

After sorting: `[3, 3, 4, 5]`

| left | right | people[l] | people[r] | sum | ≤ 5? | Action | boats |
|------|-------|-----------|-----------|-----|------|--------|-------|
| 0 | 3 | 3 | 5 | 8 | ✗ | right-- only | 1 |
| 0 | 2 | 3 | 4 | 7 | ✗ | right-- only | 2 |
| 0 | 1 | 3 | 3 | 6 | ✗ | right-- only | 3 |
| 0 | 0 | 3 | 3 | — | single person | right-- | 4 |

**Answer: 4** ✓ (everyone goes alone — no pair fits within limit 5)

---

**Trace for people = [2, 4, 2, 3, 5, 1], limit = 5:**

After sorting: `[1, 2, 2, 3, 4, 5]`

| left | right | people[l] | people[r] | sum | ≤ 5? | Action | boats |
|------|-------|-----------|-----------|-----|------|--------|-------|
| 0 | 5 | 1 | 5 | 6 | ✗ | right-- only | 1 |
| 0 | 4 | 1 | 4 | 5 | ✓ | left++, right-- | 2 |
| 1 | 3 | 2 | 3 | 5 | ✓ | left++, right-- | 3 |
| 2 | 2 | left>right after right--, stop | | | | | 3 |

Wait: left=2, right=2. `left <= right` is true. Enter loop.
people[2] + people[2] = 2+2 = 4 ≤ 5 ✓. left=3, right=1. boats=3.
Now left > right. Stop.

**Answer: 3** ✓

Boats: [5 alone], [1+4], [2+3]. Optimal pairing.

---

## 🔍 Why At Most 2 People Per Boat?

The problem constrains boats to at most 2 people.
This is what makes the two-pointer approach work perfectly.

If boats could hold 3+ people, we'd need a different approach
(bin packing — NP-hard in general). The 2-person limit
makes it a clean greedy problem.

---

## 🔍 Why Sort First?

Without sorting, we can't efficiently find the best pairing partner.
Sorting puts the lightest and heaviest at opposite ends,
enabling the two-pointer greedy approach.

Sorting: O(N log N). Two-pointer pass: O(N). Total: O(N log N).

---

## 🔍 Edge Cases

**Everyone fits alone:** each person ≤ limit. Boats = N (worst case).
**Everyone pairs:** lightest + heaviest ≤ limit for all pairs. Boats = N/2 (best case).
**Single person:** 1 boat.
**All same weight:** if 2×weight ≤ limit → N/2 boats. Otherwise → N boats.

---

## 🔍 The Greedy Two-Pointer Pattern

| Problem | Sort by | Pair logic | Pointer movement |
|---------|---------|-----------|-----------------|
| **Boats (this)** | Weight | Lightest + heaviest ≤ limit? | Conditional left, always right |
| Two Sum II | Already sorted | Sum == target? | Conditional both |
| Container With Most Water | N/A (indices) | Move shorter | Conditional based on height |

All use two pointers from opposite ends. The "move" logic differs.

---

### 🧠 Memory of the Pairing Law

-   **Sort by weight.** Lightest at left, heaviest at right.
-   **If `people[left] + people[right] ≤ limit`:** pair them. `left++`.
-   **Always:** `right--`, `boats++` (heaviest always boards).
-   **`left++` is conditional.** `right--` and `boats++` are unconditional.
-   **Why lightest with heaviest:** gives the hardest-to-pair person the best chance.
-   **At most 2 per boat** — what makes greedy work.
-   **Time:** O(N log N). **Space:** O(1).

Thus is remembered the saga of **Boats to Save People**,
where the Oracle sorted the people by weight
and paired from opposite ends —
the lightest with the heaviest when possible,
the heaviest alone when not —
always boarding the heaviest, conditionally pairing the lightest —
minimizing boats through greedy pairing
in a single two-pointer sweep. ⛵👥✨
