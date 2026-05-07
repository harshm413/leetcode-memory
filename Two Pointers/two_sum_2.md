## 🎯🔢 _The Sorted Pair: The Two Sum II Saga_

> \_"The Oracle was given a SORTED array and a target.
>
> She was commanded:
>
> **'Find two numbers that add up to the target.
> Return their 1-indexed positions.
> The array is already sorted in non-decreasing order.
> Use only constant extra space.'**
>
> Unlike Two Sum I (unsorted, hashmap, O(N) space),
> the sorted order enabled a cleaner approach:
>
> **Two pointers from opposite ends.**
>
> Left pointer at the smallest. Right pointer at the largest.
> If sum < target → need bigger → move left right.
> If sum > target → need smaller → move right left.
> If sum == target → found it.
>
> No hashmap needed. O(1) space.
> The sorted order gives DIRECTION to the search."\_

---

This is the saga of **Two Sum II - Input Array Is Sorted (LeetCode 167)**.

Given a **1-indexed sorted** array `numbers` and a `target`:
-   Find two numbers that sum to target.
-   Return their **1-indexed** positions `[index1, index2]`.
-   Exactly one solution exists. You may not use the same element twice.

```
Input:  numbers = [2, 7, 11, 15], target = 9
Output: [1, 2]   (2 + 7 = 9)

Input:  numbers = [2, 3, 4], target = 6
Output: [1, 3]   (2 + 4 = 6)

Input:  numbers = [-1, 0], target = -1
Output: [1, 2]   (-1 + 0 = -1)
```

---

## 🧠 Why Two Pointers Work on Sorted Arrays

The array is sorted: `numbers[left] ≤ ... ≤ numbers[right]`.

**Sum too small:** `numbers[left] + numbers[right] < target`.
-   Moving `right` left makes the sum even SMALLER (worse).
-   Moving `left` right makes the sum BIGGER (better).
-   → `left++`.

**Sum too big:** `numbers[left] + numbers[right] > target`.
-   Moving `left` right makes the sum even BIGGER (worse).
-   Moving `right` left makes the sum SMALLER (better).
-   → `right--`.

**Sum equals target:** found the pair. Return.

Each step eliminates one element from consideration.
The pointers converge toward the answer. O(N) total.

> _"The sorted order gives direction.
> Too small? The left pointer knows to move right — toward bigger values.
> Too big? The right pointer knows to move left — toward smaller values.
> No guessing. No hashmap. Just the logic of order."_

---

## 🧠 Why This Is Guaranteed to Find the Answer

**Proof:** suppose the answer is at indices `i` and `j` (i < j).

At some point during the algorithm, either:
-   `left = i` and `right = j` → found it directly.
-   `left < i`: sum is too small (numbers[left] < numbers[i]).
    We move left right. Eventually left reaches i.
-   `right > j`: sum is too big (numbers[right] > numbers[j]).
    We move right left. Eventually right reaches j.

The algorithm never skips past the answer because:
-   We only move left RIGHT (toward i) when sum is too small.
-   We only move right LEFT (toward j) when sum is too big.
-   Neither pointer can overshoot the answer.

---

### 📜 The Scroll of the Sorted Pair

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔢 The Two-Pointer Solution

### Initialize

```cpp
vector<int> twoSum(vector<int>& numbers, int target) {
    int left = 0, right = numbers.size() - 1;
```

Left at the smallest element. Right at the largest.
The widest possible search range.

---

### Converge toward the target

```cpp
    while (left < right) {
        int sum = numbers[left] + numbers[right];
```

Compute the sum of the current pair.

---

### Found the target

```cpp
        if (sum == target) {
            return {left + 1, right + 1};
        }
```

Return 1-indexed positions. `left + 1` and `right + 1`.

---

### Sum too small — need bigger

```cpp
        else if (sum < target) {
            left++;
        }
```

The current sum is below target. We need a larger value on the left side.
Move left toward bigger numbers.

> _"The sum falls short. The left pointer steps right,
> seeking a larger partner to reach the target."_

---

### Sum too big — need smaller

```cpp
        else {
            right--;
        }
    }
    return {};
}
```

The current sum exceeds target. We need a smaller value on the right side.
Move right toward smaller numbers.

> _"The sum overshoots. The right pointer steps left,
> seeking a smaller partner to bring the sum down."_

---

### 🎺 The Trial of the Sorted Pair

```cpp
int main() {
    vector<int> n1 = {2, 7, 11, 15};
    auto r1 = twoSum(n1, 9);
    cout << r1[0] << " " << r1[1] << endl; // expected: 1 2

    vector<int> n2 = {2, 3, 4};
    auto r2 = twoSum(n2, 6);
    cout << r2[0] << " " << r2[1] << endl; // expected: 1 3

    vector<int> n3 = {-1, 0};
    auto r3 = twoSum(n3, -1);
    cout << r3[0] << " " << r3[1] << endl; // expected: 1 2

    vector<int> n4 = {1, 2, 3, 4, 4, 9, 56, 90};
    auto r4 = twoSum(n4, 8);
    cout << r4[0] << " " << r4[1] << endl; // expected: 4 5

    return 0;
}
```

---

**Full trace for numbers = [2, 7, 11, 15], target = 9:**

| left | right | numbers[l] | numbers[r] | sum | vs target | Action |
|------|-------|-----------|-----------|-----|-----------|--------|
| 0 | 3 | 2 | 15 | 17 | > 9 | right-- |
| 0 | 2 | 2 | 11 | 13 | > 9 | right-- |
| 0 | 1 | 2 | 7 | 9 | == 9 | **Found! [1, 2]** |

**Answer: [1, 2]** ✓

---

**Trace for numbers = [2, 3, 4], target = 6:**

| left | right | sum | vs target | Action |
|------|-------|-----|-----------|--------|
| 0 | 2 | 2+4=6 | == 6 | **Found! [1, 3]** |

**Answer: [1, 3]** ✓ (found immediately — the widest pair was the answer)

---

**Trace for numbers = [1, 2, 3, 4, 4, 9, 56, 90], target = 8:**

| left | right | sum | vs target | Action |
|------|-------|-----|-----------|--------|
| 0 | 7 | 1+90=91 | > 8 | right-- |
| 0 | 6 | 1+56=57 | > 8 | right-- |
| 0 | 5 | 1+9=10 | > 8 | right-- |
| 0 | 4 | 1+4=5 | < 8 | left++ |
| 1 | 4 | 2+4=6 | < 8 | left++ |
| 2 | 4 | 3+4=7 | < 8 | left++ |
| 3 | 4 | 4+4=8 | == 8 | **Found! [4, 5]** |

**Answer: [4, 5]** ✓

---

## 🔍 Two Sum I vs Two Sum II — Complete Comparison

| Aspect | Two Sum I (LC 1) | Two Sum II (this, LC 167) |
|--------|-----------------|---------------------------|
| Input | Unsorted | **Sorted** |
| Approach | Hashmap (complement lookup) | **Two pointers (opposite ends)** |
| Time | O(N) | O(N) |
| Space | O(N) | **O(1)** |
| Return | 0-indexed | **1-indexed** |
| Duplicates | Handled by check-before-insert | Handled naturally (pointers skip) |

**When to use which:**
-   Unsorted + need indices → hashmap (Two Sum I).
-   Sorted + O(1) space → two pointers (Two Sum II).
-   Sorted + need indices → two pointers (this) or hashmap (both work).

---

## 🔍 Why Not Binary Search?

For each element, binary search for `target - numbers[i]` in the rest.
O(N log N). Works but slower than two pointers O(N).

Two pointers is optimal for sorted pair-finding.

---

## 🔍 Extension to K-Sum

Two Sum II is the inner loop of 3Sum and 4Sum.
After fixing outer elements, the remaining pair is found
with this exact two-pointer technique on the sorted remainder.

---

### 🧠 Memory of the Sorted Pair Law

-   **Two pointers:** `left = 0`, `right = n-1` (opposite ends)
-   **Sum < target:** `left++` (need bigger)
-   **Sum > target:** `right--` (need smaller)
-   **Sum == target:** return `{left+1, right+1}` (1-indexed)
-   **Guaranteed to find:** pointers converge toward the answer, never overshoot
-   **Sorted order is the key** — gives directional guarantees
-   **Time:** O(N). **Space:** O(1).
-   **Foundation** for 3Sum and 4Sum (inner pair-finding loop).

Thus is remembered the saga of **Two Sum II**,
where the sorted order gave the Oracle direction —
two pointers from opposite ends,
one seeking bigger, one seeking smaller,
converging toward the target sum —
no hashmap, no extra space,
just the elegant logic of sorted order. 🎯🔢✨
