## 0️⃣1️⃣📏 _The Balanced Binary: The Contiguous Array Saga_

> \_"The Oracle was given a binary array (only 0s and 1s).
>
> She was commanded:
>
> **'Find the longest contiguous subarray
> with an EQUAL number of 0s and 1s.'**
>
> The Oracle made one brilliant transformation:
>
> **Replace every 0 with -1.**
>
> Now 'equal number of 0s and 1s' becomes 'sum equals 0.'
>
> `[0, 1, 0, 1]` → `[-1, 1, -1, 1]`. Sum = 0. Equal count ✓.
> `[1, 1, 0]` → `[1, 1, -1]`. Sum = 1. Not equal ✗.
>
> The problem reduced to:
> **Longest subarray with sum 0.**
>
> And that's a problem she already knew —
> prefix sum + hashmap."\_

---

This is the saga of **Contiguous Array (LeetCode 525)**.

Given a binary array `nums`:
-   Find the maximum length of a contiguous subarray
    with equal number of 0s and 1s.

```
Input:  nums = [0, 1]
Output: 2   (equal: one 0, one 1)

Input:  nums = [0, 1, 0]
Output: 2   ([0,1] or [1,0] — both have equal 0s and 1s)

Input:  nums = [0, 0, 1, 0, 0, 0, 1, 1]
Output: 6   ([0, 1, 0, 0, 1, 1] at indices 1-6 — three 0s, three 1s... 
             let me verify: actually [0,0,1,0,0,0,1,1] → need to check)
```

---

## 🧠 The Transformation — 0 Becomes -1

Replace every 0 with -1. Now:
-   Each 1 contributes +1 to the sum.
-   Each 0 contributes -1 to the sum.

A subarray with equal 0s and 1s has sum = 0 after transformation.
(k ones contribute +k, k zeros contribute -k, total = 0.)

**The problem is now: longest subarray with sum 0.**

---

## 🧠 Prefix Sum + HashMap — Same as Longest Subarray Sum K

Compute a running prefix sum. If `prefixSum[i] == prefixSum[j]`,
then the subarray `[j+1, i]` has sum 0.

Why? `prefixSum[i] - prefixSum[j] = 0` means the elements between
them cancel out — equal positive and negative contributions.

**HashMap stores:** `prefixSum → first index where this sum appeared`.

We store the FIRST occurrence because we want the LONGEST subarray.
The earlier the first occurrence, the longer the subarray.

---

### 📜 The Scroll of the Balanced Binary

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 📏 The Prefix Sum Solution

### Initialize

```cpp
int findMaxLength(vector<int>& nums) {
    unordered_map<int, int> firstSeen;
    firstSeen[0] = -1;
```

`firstSeen` maps each prefix sum to the FIRST index where it appeared.

`firstSeen[0] = -1` — the empty prefix (before index 0) has sum 0.
This handles subarrays starting from index 0.

---

```cpp
    int sum = 0;
    int maxLen = 0;
```

`sum` = running prefix sum (with 0s treated as -1).
`maxLen` = longest balanced subarray found.

---

### Walk the array

```cpp
    for (int i = 0; i < nums.size(); i++) {
        sum += (nums[i] == 1) ? 1 : -1;
```

If `nums[i]` is 1 → add 1. If 0 → add -1.
No need to actually modify the array. Just adjust the sum on the fly.

> _"The Oracle doesn't physically replace 0s with -1s.
> She just adds -1 to the sum when she sees a 0.
> The transformation is in the arithmetic, not the array."_

---

### If this prefix sum was seen before — found a balanced subarray

```cpp
        if (firstSeen.count(sum)) {
            maxLen = max(maxLen, i - firstSeen[sum]);
        }
```

`sum` at index `i` equals `sum` at some earlier index `j`.
The subarray `[j+1, i]` has sum 0 → equal 0s and 1s.
Length = `i - j` = `i - firstSeen[sum]`.

We use the FIRST occurrence of this sum to maximize the length.

---

### If this prefix sum is new — record it

```cpp
        else {
            firstSeen[sum] = i;
        }
    }
    return maxLen;
}
```

Only store the FIRST time we see each sum.
If we see it again later, we DON'T update — the first occurrence
gives the longest possible subarray.

> _"The Oracle records the first time each sum appears.
> When the same sum returns — the distance between them
> is a balanced subarray. The earlier the first sighting,
> the longer the subarray."_

---

### 🎺 The Trial of the Balanced Binary

```cpp
int main() {
    vector<int> n1 = {0, 1};
    cout << findMaxLength(n1) << endl; // expected: 2

    vector<int> n2 = {0, 1, 0};
    cout << findMaxLength(n2) << endl; // expected: 2

    vector<int> n3 = {0, 0, 1, 0, 0, 0, 1, 1};
    cout << findMaxLength(n3) << endl; // expected: 6

    vector<int> n4 = {0, 1, 1, 0, 1, 1, 1, 0, 0, 0};
    cout << findMaxLength(n4) << endl; // expected: 10

    return 0;
}
```

---

**Full trace for nums = [0, 0, 1, 0, 0, 0, 1, 1]:**

| i | nums[i] | +1/-1 | sum | firstSeen[sum]? | Action | maxLen |
|---|---------|-------|-----|-----------------|--------|--------|
| — | —       | —     | 0   | seed: -1        | —      | 0      |
| 0 | 0       | -1    | -1  | Not seen        | store -1→0 | 0 |
| 1 | 0       | -1    | -2  | Not seen        | store -2→1 | 0 |
| 2 | 1       | +1    | -1  | Seen at 0       | len=2-0=2 | **2** |
| 3 | 0       | -1    | -2  | Seen at 1       | len=3-1=2 | 2 |
| 4 | 0       | -1    | -3  | Not seen        | store -3→4 | 2 |
| 5 | 0       | -1    | -4  | Not seen        | store -4→5 | 2 |
| 6 | 1       | +1    | -3  | Seen at 4       | len=6-4=2 | 2 |
| 7 | 1       | +1    | -2  | Seen at 1       | len=7-1=**6** | **6** |

**Answer: 6** ✓

Subarray [1, 7] = `[0, 1, 0, 0, 0, 1, 1]` — three 0s, three 1s. Wait, that's indices 1 to 7 which is 7 elements... Let me recheck.

`firstSeen[-2] = 1`. At i=7, sum=-2. Length = 7 - 1 = 6.
Subarray is indices [2, 7] = `[1, 0, 0, 0, 1, 1]` — three 1s, three 0s. ✓

(The subarray starts at `firstSeen[sum] + 1 = 2`, ends at `i = 7`. Length = 6.)

---

**Trace for nums = [0, 1, 1, 0, 1, 1, 1, 0, 0, 0]:**

```
Sums: -1, 0, 1, 0, 1, 2, 3, 2, 1, 0

sum=0 at i=1: firstSeen[0]=-1. len = 1-(-1) = 2.
sum=0 at i=3: firstSeen[0]=-1. len = 3-(-1) = 4.
sum=0 at i=9: firstSeen[0]=-1. len = 9-(-1) = 10.
```

**Answer: 10** ✓ (entire array — five 0s, five 1s)

---

## 🔍 The Transformation Trick — When to Use It

| Original problem | Transformation | Becomes |
|-----------------|----------------|---------|
| **Equal 0s and 1s** | **0 → -1** | **Longest subarray sum = 0** |
| More 1s than 0s by K | 0 → -1 | Longest subarray sum = K |
| Equal vowels and consonants | consonant → -1, vowel → +1 | Longest subarray sum = 0 |

Any "equal count" problem on binary-like data can be transformed
into a prefix sum problem by mapping one category to -1.

---

## 🔍 Why Store FIRST Occurrence Only?

If we stored the LAST occurrence, we'd get the SHORTEST subarray.
We want the LONGEST → store the first.

The `else` branch ensures we never overwrite an earlier index.
First come, first served.

---

### 🧠 Memory of the Balanced Binary Law

-   **Transform:** treat 0 as -1. Equal 0s and 1s → sum = 0.
-   **Prefix sum + hashmap:** `firstSeen[sum] → first index`
-   **Seed:** `firstSeen[0] = -1` (handles subarrays from index 0)
-   **Same sum seen again:** `length = i - firstSeen[sum]`
-   **Store FIRST occurrence only** — maximizes length
-   **No array modification needed** — just `sum += (nums[i] == 1) ? 1 : -1`
-   **Time:** O(N). **Space:** O(N).

Thus is remembered the saga of **Contiguous Array**,
where the Oracle transformed zeros into negative ones —
turning "equal count" into "sum equals zero" —
then walked the array with a prefix sum and a hashmap,
recording the first time each sum appeared,
and measuring the distance when the same sum returned —
the longest balanced subarray revealed
by the echo of a repeated prefix sum. 0️⃣1️⃣📏✨
