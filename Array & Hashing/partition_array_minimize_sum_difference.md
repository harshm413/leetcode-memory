## ⚖️✂️ _The Perfect Half-Split: The Partition Array Into Two Arrays to Minimize Sum Difference Saga_

> \_"The Oracle was given an array of `2n` elements.
>
> She was commanded:
>
> **'Split this array into TWO arrays of EXACTLY n elements each.
> Minimize the absolute difference between their sums.'**
>
> This was NOT the same as Minimum Subset Sum Difference.
> That problem allowed subsets of ANY size.
> This one demanded EXACTLY n elements in each half.
>
> With `2n` elements and the constraint of exactly `n` per side,
> the standard Subset Sum DP was too slow --
> `n` could be up to 15, making `2n = 30` elements.
> The sum could be up to 30 × 10⁷ = 3 × 10⁸.
> A DP table of size 30 × 3×10⁸ was impossible.
>
> The Oracle needed a different weapon:
>
> **Meet in the Middle.**
>
> Split the array into two halves of size `n` each.
> Enumerate ALL possible subset sums for each half
> (grouped by how many elements were picked).
> Then combine: for each way to pick `k` elements from the left half,
> find the best way to pick `n - k` elements from the right half
> such that the total sum is as close to `totalSum / 2` as possible.
>
> Enumeration per half: 2ⁿ subsets (n ≤ 15 → 32768).
> Combining: binary search.
> Total: O(2ⁿ × n) -- feasible for n ≤ 15."\_

---

This is the saga of **Partition Array Into Two Arrays to Minimize Sum Difference** (LeetCode 2035).

Given an array `nums` of size `2n`:
-   Split into two arrays of **exactly n elements** each.
-   Minimize `|sum of array 1 - sum of array 2|`.

```
Input:  nums = [3,9,7,3]
Output: 2   (split: [3,9] and [7,3] → |12-10|=2)

Input:  nums = [-36,36]
Output: 72  (split: [-36] and [36] → |-36-36|=72)

Input:  nums = [2,-1,0,4,-2,-9]
Output: 0   (split: [2,4,-9] and [-1,0,-2] → |-3-(-3)|=0)
```

---

## 🧠 Why Standard DP Fails Here

In Minimum Subset Sum Difference (the earlier saga),
we used Subset Sum DP with target = total/2.
That works when the sum is small (≤ ~10⁵).

Here, `n ≤ 15`, so `2n ≤ 30` elements.
Each element can be up to 10⁷.
Total sum can be up to 3 × 10⁸.

A DP table of size `30 × 3×10⁸` = 9 billion cells. Impossible.

But `2n ≤ 30` means each half has ≤ 15 elements.
2¹⁵ = 32768 subsets per half. That's tiny.

**Meet in the Middle** exploits this:
enumerate subsets of each half separately,
then combine smartly.

---

## 🧠 The Meet in the Middle Strategy

### Step 1 -- Split the Array

```
Left half:  nums[0..n-1]     (n elements)
Right half: nums[n..2n-1]    (n elements)
```

### Step 2 -- Enumerate All Subset Sums Per Half

For each half, enumerate all 2ⁿ subsets.
Group them by **how many elements were picked**.

```
left[k] = sorted list of all possible sums when picking exactly k elements from the left half.
right[k] = sorted list of all possible sums when picking exactly k elements from the right half.
```

### Step 3 -- Combine with Binary Search

We need exactly `n` elements total in one array.
If we pick `k` from the left, we pick `n - k` from the right.

For each sum `s` in `left[k]`:
-   The other array's left-half contribution = `leftTotal - s`.
-   We need `n - k` elements from the right half.
-   The ideal right-half sum = `total/2 - s` (to make both halves equal).
-   Binary search in `right[n - k]` for the closest value to `total/2 - s`.

The minimum difference across all combinations is the answer.

```
Time:  O(2ⁿ × n) -- enumeration + sorting + binary search
Space: O(2ⁿ)
```

---

### 📜 The Scroll of the Half-Split

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
#include <cmath>
using namespace std;
```

---

## ✂️ Step 1 -- Split the Array

```cpp
int minimumDifference(vector<int>& nums) {
    int n = nums.size() / 2;
    int total = 0;
    for (int x : nums) total += x;
```

`n` = half the array size. Each output array has exactly `n` elements.
`total` = sum of all elements.

---

## 📦 Step 2 -- Enumerate Subset Sums for Each Half

### Prepare Storage

```cpp
    vector<vector<int>> leftSums(n + 1);
    vector<vector<int>> rightSums(n + 1);
```

`leftSums[k]` = all possible sums when picking exactly `k` elements
from the left half.

`rightSums[k]` = same for the right half.

Index `k` ranges from 0 to n.

---

### Enumerate Left Half Subsets

```cpp
    for (int mask = 0; mask < (1 << n); mask++) {
        int sum = 0;
        int count = 0;
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) {
                sum += nums[i];
                count++;
            }
        }
        leftSums[count].push_back(sum);
    }
```

`mask` is a bitmask representing which elements are picked.
Bit `i` set → element `i` is picked.

For each subset: compute the sum and count how many elements.
Store the sum in `leftSums[count]`.

Why bitmask? Because `n ≤ 15`, so `2ⁿ ≤ 32768` subsets.
Bitmask enumeration is the standard way to iterate all subsets.

> _"Every possible selection of elements from the left half
> is tried. Each sum is filed by how many elements it used."_

---

### Enumerate Right Half Subsets

```cpp
    for (int mask = 0; mask < (1 << n); mask++) {
        int sum = 0;
        int count = 0;
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) {
                sum += nums[n + i];
                count++;
            }
        }
        rightSums[count].push_back(sum);
    }
```

Same logic for the right half.
Note: `nums[n + i]` accesses the right half of the array.

---

### Sort the Right Half Sums (for Binary Search)

```cpp
    for (int k = 0; k <= n; k++) {
        sort(rightSums[k].begin(), rightSums[k].end());
    }
```

We'll binary search in `rightSums[n - k]` later.
Sorting enables this.

---

## 🔍 Step 3 -- Combine with Binary Search

```cpp
    int minDiff = INT_MAX;
    int halfTotal = total / 2;
```

We want one array's sum as close to `total / 2` as possible.
(If one array sums to `total/2`, the other does too. Diff = 0.)

Note: `total` might be odd. `halfTotal` is integer division.
We'll check both `floor` and `ceil` via binary search.

---

### For Each Split of n Elements Between Left and Right

```cpp
    for (int k = 0; k <= n; k++) {
```

Pick `k` elements from the left half, `n - k` from the right half.
Together: exactly `n` elements in one array.

---

### For Each Left-Half Sum with k Elements

```cpp
        for (int leftSum : leftSums[k]) {
```

`leftSum` = sum of the `k` elements picked from the left.

---

### What Right-Half Sum Do We Want?

```cpp
            int target = halfTotal - leftSum;
```

If the left contributes `leftSum`,
the right should contribute `target = total/2 - leftSum`
to make the total as close to `total/2` as possible.

---

### Binary Search for the Closest Right-Half Sum

```cpp
            auto& rv = rightSums[n - k];
            auto it = lower_bound(rv.begin(), rv.end(), target);
```

`lower_bound` finds the first element ≥ `target`.
This is the closest value from above.

---

### Check the Closest Values (Above and Below)

```cpp
            if (it != rv.end()) {
                int arraySum = leftSum + *it;
                int diff = abs(total - 2 * arraySum);
                minDiff = min(minDiff, diff);
            }
```

`*it` is the closest right-half sum ≥ target.
`arraySum = leftSum + *it` = total sum of one array.
`diff = |total - 2 * arraySum|` = difference between the two arrays.

---

```cpp
            if (it != rv.begin()) {
                --it;
                int arraySum = leftSum + *it;
                int diff = abs(total - 2 * arraySum);
                minDiff = min(minDiff, diff);
            }
        }
    }
```

Also check the element BEFORE `lower_bound` --
the closest right-half sum < target.
It might give a smaller difference.

Why check both? Because `lower_bound` gives ≥ target.
The actual closest might be the element just below.

> _"The Oracle checks both neighbors of the target --
> the one just above and the one just below.
> The closer one wins."_

---

### The Answer

```cpp
    return minDiff;
}
```

---

### 🎺 The Trial of the Perfect Half-Split

```cpp
int main() {
    vector<int> n1 = {3, 9, 7, 3};
    cout << minimumDifference(n1) << endl; // expected: 2

    vector<int> n2 = {-36, 36};
    cout << minimumDifference(n2) << endl; // expected: 72

    vector<int> n3 = {2, -1, 0, 4, -2, -9};
    cout << minimumDifference(n3) << endl; // expected: 0

    return 0;
}
```

---

**Trace for nums = [3, 9, 7, 3], n = 2:**

Left half: [3, 9]. Right half: [7, 3].

**Left subsets (grouped by count):**
-   k=0: {sum=0}
-   k=1: {3, 9}
-   k=2: {12}

**Right subsets (grouped by count):**
-   k=0: {0}
-   k=1: {7, 3} → sorted: {3, 7}
-   k=2: {10}

**Combining (need n=2 elements total in one array):**

k=0 from left, n-k=2 from right:
-   leftSum=0, target=11-0=11. rightSums[2]={10}. Closest=10. arraySum=10. diff=|22-20|=2.

k=1 from left, n-k=1 from right:
-   leftSum=3, target=11-3=8. rightSums[1]={3,7}. lower_bound(8)=end. Check before: 7. arraySum=3+7=10. diff=2.
-   leftSum=9, target=11-9=2. rightSums[1]={3,7}. lower_bound(2)→3. arraySum=9+3=12. diff=|22-24|=2. Check before: begin, skip.

k=2 from left, n-k=0 from right:
-   leftSum=12, target=11-12=-1. rightSums[0]={0}. lower_bound(-1)→0. arraySum=12+0=12. diff=2.

**minDiff = 2** ✓

Split: [3,7] sum=10 and [9,3] sum=12. Diff=2.

---

**Trace for nums = [2, -1, 0, 4, -2, -9], n = 3:**

total = -6. halfTotal = -3.

The enumeration finds a split where one array sums to -3
and the other sums to -3. Diff = 0.

**Answer: 0** ✓

---

## 🔍 Why Meet in the Middle and Not DP?

| Approach | Constraint | Time |
|----------|-----------|------|
| Subset Sum DP | sum ≤ ~10⁵ | O(n × sum) |
| Meet in the Middle | n ≤ 15 (elements ≤ 30) | O(2ⁿ × n) |

When `n` is small but sums are huge → Meet in the Middle.
When sums are small but `n` is large → Subset Sum DP.

This problem: `n ≤ 15`, sums up to 3×10⁸. DP impossible. MITM perfect.

---

## 🔍 The Meet in the Middle Pattern

```
1. Split the input into two halves.
2. Enumerate all possibilities for each half separately.
3. Combine the two halves smartly (sorting + binary search).
```

This pattern appears in:
-   **Partition Array (this problem)** -- subset sums + binary search.
-   **4Sum Count** -- two halves of pairs, binary search for complement.
-   **Closest Subsequence Sum** -- same MITM structure.

The key insight: 2³⁰ is too many, but 2 × 2¹⁵ is fine.

---

## 🔍 Why Group by Count?

We need EXACTLY `n` elements in each array.
If we pick `k` from the left, we MUST pick `n - k` from the right.

Without grouping by count, we'd mix subsets of different sizes
and violate the "exactly n" constraint.

Grouping ensures every combination has exactly `n` elements total.

---

### 🧠 Memory of the Perfect Half-Split Law

-   **Meet in the Middle:** split array into two halves of size n
-   **Enumerate** all 2ⁿ subset sums per half, grouped by element count
-   **Sort** right half sums for binary search
-   **Combine:** for each `k` from 0 to n:
    -   For each leftSum in leftSums[k]:
    -   Binary search rightSums[n-k] for closest to `total/2 - leftSum`
    -   Check both neighbors (above and below target)
-   **Difference:** `|total - 2 * arraySum|`
-   **Why MITM?** n ≤ 15 but sums huge → DP impossible, bitmask feasible
-   **Time:** O(2ⁿ × n). **Space:** O(2ⁿ).

Thus is remembered the saga of **Partition Array to Minimize Sum Difference**,
where the Oracle split the array in half,
enumerated every possible selection from each side,
grouped them by count,
then combined them with binary search --
finding the split of exactly n elements per array
that brought the two sums as close together as possible. ⚖️✂️✨
