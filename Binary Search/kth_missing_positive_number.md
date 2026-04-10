## 🕳️🔢 _The Ghost in the Counting Line: The Kth Missing Positive Number Saga_

> \_"In the Kingdom of Positive Integers,
> numbers were supposed to march in order:
> 1, 2, 3, 4, 5, 6, 7, ...
>
> But some had vanished -- gaps in the line,
> ghosts where numbers should have stood.
>
> The Oracle was given a sorted array
> of the numbers that DID survive,
> and a single command:
>
> **'Find the Kth missing positive number --
> the Kth ghost in the counting line.'**
>
> The naive approach would walk the number line
> one by one, counting gaps -- O(N + K).
>
> But the Oracle saw a faster path.
>
> She realized that at any index `i` in the array,
> the number of missing positives BEFORE `arr[i]`
> could be computed instantly:
>
> **missing = arr[i] - (i + 1)**
>
> If the array were perfect (no gaps),
> `arr[i]` would equal `i + 1`.
> The difference tells exactly how many ghosts
> lurk before this position.
>
> **Binary search for the position
> where the missing count crosses K.**
>
> O(log N). The ghost found."\_

---

This is the saga of **Kth Missing Positive Number**.

Given a sorted array `arr` of positive integers (strictly increasing)
and an integer `k`:

-   Find the **Kth missing positive integer**.

```
Input:  arr = [2,3,4,7,11], k = 5
Output: 9
Missing: [1, 5, 6, 8, 9, 10, ...] → 5th missing = 9

Input:  arr = [1,2,3,4], k = 2
Output: 6
Missing: [5, 6, 7, ...] → 2nd missing = 6
```

---

## 🧠 The Oracle's Core Insight -- Missing Count at Any Index

In a perfect sequence starting from 1:
`arr[0]=1, arr[1]=2, arr[2]=3, ...` → `arr[i] = i + 1`.

In the actual array, `arr[i]` might be larger than `i + 1`.
The difference is exactly the number of missing positives before `arr[i]`:

```
missing_before(i) = arr[i] - (i + 1)
```

Examples for `arr = [2, 3, 4, 7, 11]`:

```
i=0: arr[0]=2, expected=1, missing = 2-1 = 1   (missing: 1)
i=1: arr[1]=3, expected=2, missing = 3-2 = 1   (missing: 1)
i=2: arr[2]=4, expected=3, missing = 4-3 = 1   (missing: 1)
i=3: arr[3]=7, expected=4, missing = 7-4 = 3   (missing: 1,5,6)
i=4: arr[4]=11, expected=5, missing = 11-5 = 6 (missing: 1,5,6,8,9,10)
```

The missing count is **non-decreasing** as `i` increases.
This monotonicity enables binary search.

**Goal:** find the largest index where `missing_before(i) < k`.
The Kth missing number lives just after that position.

After finding this index, the answer is:

```
answer = arr[index] + (k - missing_before(index))
```

Or equivalently, if the binary search ends with `left`:

```
answer = left + k
```

---

### 📜 The Scroll of the Ghost Line

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Ghost-Finding Ritual

```cpp
int findKthPositive(vector<int>& arr, int k) {
    int left = 0, right = arr.size() - 1;
```

Two sentinels at the boundaries of the array.

---

## 🔁 Binary Search for the Crossing Point

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int missing = arr[mid] - (mid + 1);
```

At index `mid`, the Oracle computed how many positive integers
were missing before `arr[mid]`.

---

### ✅ Not Enough Missing Yet -- Search Right

```cpp
        if (missing < k) {
            left = mid + 1;
        }
```

Fewer than `k` numbers were missing before `arr[mid]`.
The Kth ghost must be further right (or beyond the array).

---

### ❌ Enough or More Missing -- Search Left

```cpp
        else {
            right = mid - 1;
        }
    }
```

At least `k` numbers were missing before `arr[mid]`.
The Kth ghost is at or before this position.
Search left to find the tightest boundary.

---

## 🕳️ The Ghost Is Revealed

```cpp
    return left + k;
}
```

When the loop ends, `left` is the index where the missing count
first reaches or exceeds `k`.

The answer is simply `left + k`.

**Why `left + k`?**

Think of it this way: if no elements were missing,
the Kth positive number would just be `k`.
Each element in `arr` that is `<= left + k` "pushes" the answer
one step further. There are exactly `left` such elements
(all elements at indices `0` to `left - 1`).
So the answer shifts from `k` to `k + left`.

> _"Every present number before the ghost
> pushes the ghost one step further down the line.
> `left` counts those present numbers.
> `k` counts the gap.
> Together they reveal the ghost's true position."_

---

### 🎺 The Trial of the Ghost Line

```cpp
int main() {
    vector<int> arr1 = {2, 3, 4, 7, 11};
    cout << findKthPositive(arr1, 5) << endl; // expected: 9

    vector<int> arr2 = {1, 2, 3, 4};
    cout << findKthPositive(arr2, 2) << endl; // expected: 6

    vector<int> arr3 = {5, 6, 7, 8, 9};
    cout << findKthPositive(arr3, 4) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for `[2, 3, 4, 7, 11]`, k = 5:**

Missing counts: `[1, 1, 1, 3, 6]`

| Step | left | right | mid | arr[mid] | missing | missing < 5? | Action      |
| ---- | ---- | ----- | --- | -------- | ------- | ------------ | ----------- |
| 1    | 0    | 4     | 2   | 4        | 1       | Yes          | left = 3    |
| 2    | 3    | 4     | 3   | 7        | 3       | Yes          | left = 4    |
| 3    | 4    | 4     | 4   | 11       | 6       | No           | right = 3   |
| 4    | 4    | 3     | --  | --       | --      | left > right | Exit        |

**Answer: left + k = 4 + 5 = 9** ✓

Missing numbers: `[1, 5, 6, 8, 9, 10, ...]`. The 5th is 9.

At index 3 (value 7), only 3 numbers were missing -- not enough.
At index 4 (value 11), 6 numbers were missing -- too many.
The 5th ghost lives between arr[3]=7 and arr[4]=11.
`left=4`, answer = 4 + 5 = 9.

---

**Full trace for `[1, 2, 3, 4]`, k = 2:**

Missing counts: `[0, 0, 0, 0]`

| Step | left | right | mid | arr[mid] | missing | missing < 2? | Action      |
| ---- | ---- | ----- | --- | -------- | ------- | ------------ | ----------- |
| 1    | 0    | 3     | 1   | 2        | 0       | Yes          | left = 2    |
| 2    | 2    | 3     | 2   | 3        | 0       | Yes          | left = 3    |
| 3    | 3    | 3     | 3   | 4        | 0       | Yes          | left = 4    |
| 4    | 4    | 3     | --  | --       | --      | left > right | Exit        |

**Answer: left + k = 4 + 2 = 6** ✓

No numbers are missing within the array.
All missing numbers are beyond it: `[5, 6, 7, ...]`. The 2nd is 6.

---

**Full trace for `[5, 6, 7, 8, 9]`, k = 4:**

Missing counts: `[4, 4, 4, 4, 4]`

| Step | left | right | mid | arr[mid] | missing | missing < 4? | Action      |
| ---- | ---- | ----- | --- | -------- | ------- | ------------ | ----------- |
| 1    | 0    | 4     | 2   | 7        | 4       | No           | right = 1   |
| 2    | 0    | 1     | 0   | 5        | 4       | No           | right = -1  |
| 3    | 0    | -1    | --  | --       | --      | left > right | Exit        |

**Answer: left + k = 0 + 4 = 4** ✓

All missing numbers are before the array: `[1, 2, 3, 4]`. The 4th is 4.
`left` stayed at 0 -- no array element was passed. Answer = 0 + 4 = 4.

---

## 🔍 Why `left + k` Works -- The Intuition

Imagine the positive number line: `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...`

Some of these are "present" (in `arr`), others are "missing" (ghosts).

The Kth missing number is at position `k` in the ghost-only sequence.
But each present number that comes before it shifts it forward by 1.

`left` = how many present numbers come before the Kth ghost.
`k` = the ghost's rank among missing numbers.

So the ghost's actual value = `k + left`.

This is why the formula works regardless of where the missing numbers are --
before the array, within it, or after it.

---

### 🧠 Memory of the Ghost Line Law

-   **Missing count at index i:** `arr[i] - (i + 1)` -- how many positives are missing before arr[i]
-   **Monotonic:** missing count never decreases → binary search works
-   **Binary search:** find where missing count first reaches k
    -   `missing < k` → `left = mid + 1` (not enough ghosts yet)
    -   `missing >= k` → `right = mid - 1` (enough or too many)
-   **Answer:** `left + k` -- k ghosts plus `left` present numbers before them
-   **Time:** O(log N) -- binary search on the array
-   **Space:** O(1)
-   **Edge cases:**
    -   All missing before array → `left = 0`, answer = k
    -   All missing after array → `left = n`, answer = n + k
    -   k = 1, arr = [2] → answer = 1

Thus is remembered the saga of **Kth Missing Positive Number**,
where the Oracle did not walk the number line counting gaps
but instead computed the missing count at each position --
`arr[i] - (i + 1)` -- the elegant formula
that revealed how many ghosts lurked before any point --
and binary searched for the crossing point
where the ghost count reached k,
revealing the Kth missing number
as simply `left + k` --
the ghost's rank plus the present numbers before it. 🕳️🔢✨
