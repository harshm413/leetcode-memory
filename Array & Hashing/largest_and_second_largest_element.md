## 🏆🥈 _The Two Champions: The Largest and Second Largest Element Saga_

> \_"The Oracle was given an array of numbers.
>
> First, the King asked:
>
> **'What is the largest element?'**
>
> Then he asked:
>
> **'What is the second largest — the runner-up?'**
>
> The Oracle solved both in a single pass.
>
> For the largest — she walked the array once,
> tracking the biggest value seen so far.
>
> For the second largest — she tracked TWO values:
> the champion and the runner-up.
> When a new champion was crowned,
> the old champion became the runner-up.
> When a value beat the runner-up but not the champion,
> only the runner-up was updated.
>
> One pass. Two variables. Both answers."\_

---

This is the saga of **Largest Element and Second Largest Element**.

**Problem 1 — Largest Element:**
Given an array, find the maximum value.

**Problem 2 — Second Largest Element:**
Given an array, find the second largest DISTINCT value.
If no second largest exists (all elements equal), return -1.

```
Input:  arr = [1, 8, 7, 56, 90]
Largest: 90, Second Largest: 56

Input:  arr = [12, 35, 1, 10, 34, 1]
Largest: 35, Second Largest: 34

Input:  arr = [10, 10, 10]
Largest: 10, Second Largest: -1 (no distinct second)
```

---

## 🧠 Part 1 — Finding the Largest Element

Walk the array. Track the maximum.

This is the simplest possible array problem.
One variable, one comparison per element.

```
Time:  O(N) — single pass
Space: O(1) — one variable
```

---

### 📜 The Scroll of the Champion

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

```cpp
int largest(vector<int>& arr) {
    int maxi = INT_MIN;
```

Start with the smallest possible value.
Any element in the array will be ≥ this.

---

```cpp
    for (int i = 0; i < arr.size(); i++) {
        if (arr[i] > maxi) {
            maxi = arr[i];
        }
    }
    return maxi;
}
```

Each element challenges the current champion.
If it's bigger — it takes the crown.

> _"The champion stands. Each challenger steps forward.
> If the challenger is stronger — the crown changes hands.
> By the end, the strongest remains."_

---

## 🧠 Part 2 — Finding the Second Largest Element

This is where it gets interesting.

**Naive approach:** sort the array, return the second-to-last distinct element.
O(N log N). Wasteful.

**Better approach:** two passes. First find the largest.
Then find the largest element that's strictly less than the largest.
O(2N). Two passes.

**Optimal approach:** ONE pass. Track both simultaneously.

Two variables: `first` (largest) and `second` (second largest).

For each element:
-   If it's **greater than first** → old first becomes second, element becomes first.
-   Else if it's **greater than second AND not equal to first** → element becomes second.
-   Else → skip.

The "not equal to first" check ensures we find the second largest
DISTINCT value. Without it, duplicates of the largest would
incorrectly become the second largest.

---

### 📜 The Scroll of the Runner-Up

```cpp
int secondLargest(vector<int>& arr) {
    if (arr.size() < 2) return -1;
```

Need at least 2 elements for a second largest.

---

```cpp
    int first = INT_MIN;
    int second = INT_MIN;
```

Both start at the smallest possible value.
If `second` remains `INT_MIN` after the pass,
no valid second largest exists.

---

```cpp
    for (int i = 0; i < arr.size(); i++) {
```

---

### New champion found

```cpp
        if (arr[i] > first) {
            second = first;
            first = arr[i];
        }
```

This element beats the current champion.

**Order matters here.** First, the old champion steps down
to become the runner-up (`second = first`).
Then the new champion takes the crown (`first = arr[i]`).

If we did it the other way — `first = arr[i]` first —
we'd lose the old champion's value before saving it.

> _"The old champion doesn't vanish.
> He steps down to second place.
> The new champion takes the throne."_

---

### Beats the runner-up but not the champion

```cpp
        else if (arr[i] > second && arr[i] != first) {
            second = arr[i];
        }
```

This element is not the biggest, but it's bigger than the current runner-up.
AND it's not equal to the champion (we want DISTINCT second largest).

Why `arr[i] != first`? Consider `[10, 10, 5]`.
Without this check, the second 10 would become `second = 10`.
But 10 is not the "second largest" — it's a duplicate of the largest.
The true second largest is 5.

> _"The challenger can't beat the champion,
> but it's stronger than the current runner-up.
> And it's not a clone of the champion.
> It earns the silver medal."_

---

```cpp
    }
    return (second == INT_MIN) ? -1 : second;
}
```

If `second` was never updated from `INT_MIN`,
no valid second largest exists (all elements are equal).
Return -1.

---

### 🎺 The Trial of the Two Champions

```cpp
int main() {
    vector<int> a1 = {1, 8, 7, 56, 90};
    cout << "Largest: " << largest(a1) << endl;        // 90
    cout << "Second: " << secondLargest(a1) << endl;   // 56

    vector<int> a2 = {12, 35, 1, 10, 34, 1};
    cout << "Largest: " << largest(a2) << endl;        // 35
    cout << "Second: " << secondLargest(a2) << endl;   // 34

    vector<int> a3 = {10, 10, 10};
    cout << "Largest: " << largest(a3) << endl;        // 10
    cout << "Second: " << secondLargest(a3) << endl;   // -1

    vector<int> a4 = {5};
    cout << "Second: " << secondLargest(a4) << endl;   // -1

    return 0;
}
```

---

**Trace for arr = [1, 8, 7, 56, 90]:**

| i | arr[i] | first | second | Action |
|---|--------|-------|--------|--------|
| 0 | 1      | 1     | MIN    | 1 > MIN → new champion. second = MIN, first = 1. |
| 1 | 8      | 8     | 1      | 8 > 1 → new champion. second = 1, first = 8. |
| 2 | 7      | 8     | 7      | 7 < 8 but 7 > 1 and 7 ≠ 8 → new runner-up. |
| 3 | 56     | 56    | 8      | 56 > 8 → new champion. second = 8, first = 56. |
| 4 | 90     | 90    | 56     | 90 > 56 → new champion. second = 56, first = 90. |

**Largest: 90, Second: 56** ✓

---

**Trace for arr = [10, 10, 10]:**

| i | arr[i] | first | second | Action |
|---|--------|-------|--------|--------|
| 0 | 10     | 10    | MIN    | 10 > MIN → champion. |
| 1 | 10     | 10    | MIN    | 10 == first → skip (not > first, and == first blocks runner-up). |
| 2 | 10     | 10    | MIN    | Same. Skip. |

second == INT_MIN → **return -1** ✓

---

**Trace for arr = [5, 5, 4, 4, 3]:**

| i | arr[i] | first | second |
|---|--------|-------|--------|
| 0 | 5      | 5     | MIN    |
| 1 | 5      | 5     | MIN    | 5 == first → skip |
| 2 | 4      | 5     | 4      | 4 < 5, 4 > MIN, 4 ≠ 5 → runner-up |
| 3 | 4      | 5     | 4      | 4 == second → skip |
| 4 | 3      | 5     | 4      | 3 < 4 → skip |

**Largest: 5, Second: 4** ✓

---

## 🔍 The Three Approaches Compared

| Approach | Time | Space | Passes |
|----------|------|-------|--------|
| Sort + pick | O(N log N) | O(1) or O(N) | 1 (sort) |
| Two passes | O(2N) | O(1) | 2 |
| **One pass (this)** | **O(N)** | **O(1)** | **1** |

The one-pass approach is optimal. No sorting, no extra passes.

---

## 🔍 Extending to Kth Largest

For the 3rd, 4th, or Kth largest:
-   **Small K:** track K variables (messy for K > 3).
-   **Any K:** use a min-heap of size K. O(N log K).
-   **Exact K:** use QuickSelect. O(N) average.

The two-variable approach only works cleanly for 1st and 2nd.

---

### 🧠 Memory of the Two Champions Law

-   **Largest:** single pass, track `maxi`, update when `arr[i] > maxi`
-   **Second Largest:** track `first` and `second`
    -   `arr[i] > first` → second = first, first = arr[i] (demotion + promotion)
    -   `arr[i] > second AND arr[i] != first` → second = arr[i] (runner-up update)
-   **Order matters:** save old first BEFORE overwriting
-   **Distinct check:** `arr[i] != first` prevents duplicates from being second
-   **No valid second:** return -1 (all elements equal or array too small)
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Largest and Second Largest Element**,
where the Oracle crowned two champions in a single pass --
the strongest took the throne,
the old champion stepped down to silver,
and challengers who couldn't beat the king
but outshone the runner-up
earned their rightful place --
all in one walk through the array. 🏆🥈✨
