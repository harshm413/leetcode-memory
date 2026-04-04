## ⚔️🌗 _The Dance of Light and Shadow: The Rearrange Array Elements by Sign Saga_

> \_"In the Realm of Balanced Forces,
> warriors arrived carrying two kinds of energy --
> **positive** and **negative**.
>
> Chaos ruled the formation:
> light clustered with light,
> shadow clung to shadow.
>
> The Oracle was commanded:
>
> **'Rearrange the warriors
> so Light and Shadow march alternately --
> beginning with Light --
> while preserving the order
> within each faction.
> The number of positives equals the number of negatives.
> No warrior may change allegiance.
> No relative order may be broken.'**
>
> The Oracle studied the chaotic line.
>
> She saw two approaches:
>
> **Approach One -- Separate and Weave:**
> Collect all positives into one river.
> Collect all negatives into another.
> Then interleave them one by one.
> Simple. Clean. O(N) time, O(N) space.
>
> **Approach Two -- Direct Placement:**
> Walk the original array once.
> Maintain two pointers into the result --
> one for the next positive slot (0, 2, 4, ...),
> one for the next negative slot (1, 3, 5, ...).
> Place each number directly into its destined position.
> One pass. Same O(N) time and space, but more elegant.
>
> The Oracle chose the direct path --
> for it required no separation,
> no second weaving pass,
> just one clean march through the chaos."\_

---

This is the saga of **Rearrange Array Elements by Sign**.

You are given an integer array `nums` where:

-   The number of **positive** elements equals the number of **negative** elements.
-   You must rearrange so positives and negatives **alternate**, starting with positive.
-   The **relative order** within positives and within negatives must be preserved.

```
Input:  [3, 1, -2, -5, 2, -4]
Output: [3, -2, 1, -5, 2, -4]

Input:  [-1, 1]
Output: [1, -1]
```

---

## 🧠 The Oracle's Core Insight -- Two Pointers into the Result

The result array has a clear pattern:

```
Index:  0    1    2    3    4    5
Sign:   +    -    +    -    +    -
```

Even indices (0, 2, 4, ...) belong to positives.
Odd indices (1, 3, 5, ...) belong to negatives.

So the Oracle maintains two placement pointers:

```
posIdx = 0   (next even slot for a positive)
negIdx = 1   (next odd slot for a negative)
```

Walk the original array once.
When a positive is found -- place it at `posIdx`, advance by 2.
When a negative is found -- place it at `negIdx`, advance by 2.

Relative order is preserved because we encounter elements
left-to-right and place them in order within their respective slots.

```
Time:  O(N) -- single pass
Space: O(N) -- the result array
```

---

### 📜 The Scroll of Dual Energies

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Direct Placement Ritual

```cpp
vector<int> rearrangeArray(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n);
    int posIdx = 0;
    int negIdx = 1;
```

The Oracle prepared the result array -- `n` slots, all empty.

Two pointers were placed:
-   `posIdx = 0` -- the first even slot, awaiting the first positive.
-   `negIdx = 1` -- the first odd slot, awaiting the first negative.

---

## 🧭 Walk the Chaos -- Place Each Warrior Directly

```cpp
    for (int i = 0; i < n; i++) {
```

The Oracle walked the original array from left to right,
inspecting each warrior exactly once.

---

### ☀️ A Positive Warrior Steps Forward

```cpp
        if (nums[i] > 0) {
            result[posIdx] = nums[i];
            posIdx += 2;
        }
```

When a positive was found,
it was placed at the next available even slot.

`posIdx` then jumped forward by 2 --
skipping the odd slot (reserved for negatives)
to land on the next even position.

The first positive went to index 0.
The second to index 2.
The third to index 4.
And so on.

---

### 🌑 A Negative Warrior Steps Forward

```cpp
        else {
            result[negIdx] = nums[i];
            negIdx += 2;
        }
    }
```

When a negative was found,
it was placed at the next available odd slot.

`negIdx` jumped forward by 2 --
skipping the even slot (reserved for positives)
to land on the next odd position.

The first negative went to index 1.
The second to index 3.
The third to index 5.

> _"Light fills the even thrones.
> Shadow fills the odd thrones.
> Neither crosses into the other's domain.
> Order within each faction is preserved
> because the Oracle encounters them left-to-right
> and places them in sequence."_

---

## 🏁 The Balanced Formation Emerges

```cpp
    return result;
}
```

Every slot was filled.
Positives occupied even indices.
Negatives occupied odd indices.
The alternating pattern was perfect.

---

### 🎺 The Trial of Balance

```cpp
int main() {
    vector<int> nums1 = {3, 1, -2, -5, 2, -4};
    vector<int> ans1 = rearrangeArray(nums1);
    for (int x : ans1) cout << x << " ";
    cout << endl; // expected: 3 -2 1 -5 2 -4

    vector<int> nums2 = {-1, 1};
    vector<int> ans2 = rearrangeArray(nums2);
    for (int x : ans2) cout << x << " ";
    cout << endl; // expected: 1 -1

    return 0;
}
```

---

**Full trace for `[3, 1, -2, -5, 2, -4]`:**

| i | nums[i] | Sign | Placed at | posIdx after | negIdx after | result state              |
| - | ------- | ---- | --------- | ------------ | ------------ | ------------------------- |
| 0 | 3       | +    | index 0   | 2            | 1            | [3, _, _, _, _, _]        |
| 1 | 1       | +    | index 2   | 4            | 1            | [3, _, 1, _, _, _]        |
| 2 | -2      | -    | index 1   | 4            | 3            | [3, -2, 1, _, _, _]       |
| 3 | -5      | -    | index 3   | 4            | 5            | [3, -2, 1, -5, _, _]      |
| 4 | 2       | +    | index 4   | 6            | 5            | [3, -2, 1, -5, 2, _]      |
| 5 | -4      | -    | index 5   | 6            | 7            | [3, -2, 1, -5, 2, -4]     |

**Answer: [3, -2, 1, -5, 2, -4]** ✓

Positives in original order: `3, 1, 2` -- placed at indices `0, 2, 4`.
Negatives in original order: `-2, -5, -4` -- placed at indices `1, 3, 5`.
Alternating pattern: `+, -, +, -, +, -` starting with positive.

---

**Full trace for `[-1, 1]`:**

| i | nums[i] | Sign | Placed at | posIdx after | negIdx after | result state |
| - | ------- | ---- | --------- | ------------ | ------------ | ------------ |
| 0 | -1      | -    | index 1   | 0            | 3            | [_, -1]      |
| 1 | 1       | +    | index 0   | 2            | 3            | [1, -1]      |

**Answer: [1, -1]** ✓

Even though the negative came first in the input,
it was placed at index 1 (the first odd slot),
and the positive was placed at index 0 (the first even slot).
The alternating rule was honored regardless of input order.

---

## 🔄 Alternative -- Separate and Weave

A simpler but two-pass approach:

```cpp
vector<int> rearrangeArray(vector<int>& nums) {
    vector<int> pos, neg;
    for (int x : nums) {
        if (x > 0) pos.push_back(x);
        else neg.push_back(x);
    }

    vector<int> result;
    for (int i = 0; i < pos.size(); i++) {
        result.push_back(pos[i]);
        result.push_back(neg[i]);
    }
    return result;
}
```

First pass separates. Second pass weaves.
Same time and space complexity, just two passes instead of one.
Both are valid -- the direct placement is slightly more efficient.

---

### 🧠 Memory of the Balanced Weave Law

-   **Direct Placement:** one pass, two pointers (`posIdx = 0`, `negIdx = 1`)
    -   Positive found: place at `posIdx`, advance by 2
    -   Negative found: place at `negIdx`, advance by 2
    -   Even indices hold positives, odd indices hold negatives
-   **Separate and Weave:** two passes -- collect into two arrays, then interleave
-   Relative order within positives and within negatives is preserved
    because elements are encountered left-to-right and placed in sequence
-   Equal count of positives and negatives is guaranteed by the problem
-   **Time:** O(N) -- single pass (direct) or two passes (separate-weave)
-   **Space:** O(N) -- the result array

Thus is remembered the saga of **Rearrange Array Elements by Sign**,
where the Oracle did not force balance through chaos
but instead honored each faction's order --
placing every positive on an even throne
and every negative on an odd throne --
weaving Light and Shadow together
into a flawless alternating march
in a single, disciplined pass. ⚔️🌗✨
