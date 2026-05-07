## 🔢✂️ _The Unique Survivors: The Remove Duplicates from Sorted Array Saga_

> \_"The Oracle was given a sorted array.
>
> Duplicates lurked within — repeated values
> standing side by side in the sorted order.
>
> She was commanded:
>
> **'Remove the duplicates IN-PLACE.
> Return the number of unique elements.
> The first K elements of the array must hold
> the unique values in their original order.
> What lies beyond K does not matter.'**
>
> The Oracle could not create a new array.
> She had to work within the existing one.
>
> She placed two pointers:
>
> **`i` — the writer.** It marked the position
> where the next unique element should be placed.
>
> **`j` — the reader.** It scanned ahead,
> searching for the next value that was different.
>
> When the reader found something new,
> the writer advanced and placed it.
> When the reader found a duplicate,
> it simply moved on.
>
> By the end, everything before the writer
> was unique and in order."\_

---

This is the saga of **Remove Duplicates from Sorted Array (LeetCode 26)**.

Given a sorted array `nums`:
-   Remove duplicates **in-place**.
-   Return `k` = the number of unique elements.
-   The first `k` elements of `nums` must contain the unique values in order.

```
Input:  nums = [1,1,2]
Output: k = 2, nums = [1,2,_]

Input:  nums = [0,0,1,1,1,2,2,3,3,4]
Output: k = 5, nums = [0,1,2,3,4,_,_,_,_,_]

Input:  nums = [1,2,3]
Output: k = 3, nums = [1,2,3]  (no duplicates)
```

---

## 🧠 Building the Intuition -- Two Pointers on a Sorted Array

The array is SORTED. This is the key.
Duplicates are always ADJACENT in a sorted array.

If `nums = [1,1,1,2,2,3]`, all the 1s are together,
all the 2s are together, all the 3s are together.

So to find the next unique element, we just need to find
the next element that's DIFFERENT from the current one.

**Writer pointer `i`:** points to the last unique element placed.
Everything at or before `i` is the "clean" unique section.

**Reader pointer `j`:** scans forward looking for new values.
When it finds one, it tells the writer.

The writer only advances when a genuinely new value arrives.
The reader advances every step regardless.

---

## 🧠 Why Start i at 0 and j at 1?

The first element is always unique — there's nothing before it
to be a duplicate of. So `i = 0` (first unique is already placed).

The reader starts at `j = 1` — scanning from the second element onward,
looking for values different from `nums[i]`.

---

### 📜 The Scroll of the Unique Survivors

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ✂️ The Two-Pointer Solution

### Initialize

```cpp
int removeDuplicates(vector<int>& nums) {
    if (nums.empty()) return 0;
    int i = 0;
```

`i` = writer pointer. Starts at 0.
The first element is always part of the unique set.

---

### Scan with the reader

```cpp
    for (int j = 1; j < nums.size(); j++) {
```

`j` = reader pointer. Starts at 1. Scans every element.

---

### Found a new unique value

```cpp
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
```

`nums[j] != nums[i]` — the reader found something DIFFERENT
from the last unique value.

**Step 1:** advance the writer (`i++`).
The next position is now ready to receive a new unique value.

**Step 2:** place the new value (`nums[i] = nums[j]`).
The unique section grows by one.

If `nums[j] == nums[i]` — it's a duplicate. The reader moves on.
The writer stays put. Nothing is written.

> _"The reader scans ahead relentlessly.
> When it finds a new face — someone different from the last —
> the writer steps forward and records it.
> Duplicates are simply ignored. The reader walks past them."_

---

### Return the count

```cpp
    return i + 1;
}
```

`i` is 0-indexed. The number of unique elements = `i + 1`.

---

### 🎺 The Trial of the Unique Survivors

```cpp
int main() {
    vector<int> n1 = {1, 1, 2};
    int k1 = removeDuplicates(n1);
    cout << k1 << ": ";
    for (int x = 0; x < k1; x++) cout << n1[x] << " ";
    cout << endl; // expected: 2: 1 2

    vector<int> n2 = {0, 0, 1, 1, 1, 2, 2, 3, 3, 4};
    int k2 = removeDuplicates(n2);
    cout << k2 << ": ";
    for (int x = 0; x < k2; x++) cout << n2[x] << " ";
    cout << endl; // expected: 5: 0 1 2 3 4

    vector<int> n3 = {1, 2, 3};
    int k3 = removeDuplicates(n3);
    cout << k3 << ": ";
    for (int x = 0; x < k3; x++) cout << n3[x] << " ";
    cout << endl; // expected: 3: 1 2 3

    return 0;
}
```

---

**Trace for nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]:**

| j | nums[j] | nums[i] | Different? | Action | i | Array state (first i+1) |
|---|---------|---------|------------|--------|---|------------------------|
| 1 | 0       | 0       | No         | Skip   | 0 | [0] |
| 2 | 1       | 0       | **Yes**    | i++, write | 1 | [0, 1] |
| 3 | 1       | 1       | No         | Skip   | 1 | [0, 1] |
| 4 | 1       | 1       | No         | Skip   | 1 | [0, 1] |
| 5 | 2       | 1       | **Yes**    | i++, write | 2 | [0, 1, 2] |
| 6 | 2       | 2       | No         | Skip   | 2 | [0, 1, 2] |
| 7 | 3       | 2       | **Yes**    | i++, write | 3 | [0, 1, 2, 3] |
| 8 | 3       | 3       | No         | Skip   | 3 | [0, 1, 2, 3] |
| 9 | 4       | 3       | **Yes**    | i++, write | 4 | [0, 1, 2, 3, 4] |

**Return: i + 1 = 5** ✓
**Array: [0, 1, 2, 3, 4, 2, 2, 3, 3, 4]** — first 5 elements are unique.

---

**Trace for nums = [1, 1, 2]:**

| j | nums[j] | nums[i] | Different? | i |
|---|---------|---------|------------|---|
| 1 | 1       | 1       | No         | 0 |
| 2 | 2       | 1       | **Yes**    | 1 |

**Return: 2** ✓ → [1, 2, _]

---

**Trace for nums = [1, 2, 3] (no duplicates):**

| j | nums[j] | nums[i] | Different? | i |
|---|---------|---------|------------|---|
| 1 | 2       | 1       | **Yes**    | 1 |
| 2 | 3       | 2       | **Yes**    | 2 |

**Return: 3** ✓ → [1, 2, 3] — every element was unique, writer advanced every time.

---

## 🔍 Why This Works Only on Sorted Arrays

In a sorted array, duplicates are ADJACENT.
So comparing `nums[j]` with `nums[i]` (the last unique) is sufficient.

In an unsorted array like `[1, 3, 1, 2, 3]`, duplicates are scattered.
This approach would fail — it would keep both 1s because they're
not adjacent. You'd need a hash set for unsorted arrays.

---

## 🔍 The In-Place Overwrite Pattern

This "reader-writer" two-pointer pattern appears in many problems:

| Problem | Reader looks for | Writer places |
|---------|-----------------|---------------|
| **Remove Duplicates (this)** | Different from last unique | Unique values |
| Remove Element | Not equal to target | Non-target values |
| Move Zeros | Non-zero values | Non-zeros at front |
| Remove Duplicates II | At most 2 of each | Values with ≤ 2 copies |

Same skeleton: reader scans, writer places, gap between them = removed elements.

---

## 🔍 What About Remove Duplicates II (Allow 2)?

LeetCode 80. Allow at most 2 copies of each element.
Change the condition: compare `nums[j]` with `nums[i-1]` (two back).

```cpp
if (i < 1 || nums[j] != nums[i - 1]) {
    i++;
    nums[i] = nums[j];
}
```

Same pattern. Different comparison distance.

---

### 🧠 Memory of the Unique Survivors Law

-   **Two pointers:** `i` (writer, starts at 0), `j` (reader, starts at 1)
-   **If `nums[j] != nums[i]`:** advance writer, place new value
-   **If equal:** skip (reader advances, writer stays)
-   **Return:** `i + 1` (number of unique elements)
-   **Works because:** array is sorted → duplicates are adjacent
-   **In-place:** no extra array needed, O(1) space
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Remove Duplicates from Sorted Array**,
where the writer stood guard over the unique section
while the reader scouted ahead --
every new face was welcomed and placed,
every duplicate was walked past in silence --
until the front of the array held only
the unique survivors, in their original order. 🔢✂️✨
