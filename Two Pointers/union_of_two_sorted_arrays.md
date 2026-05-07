## 🤝🔗 _The Merging of Two Kingdoms: The Union of Two Sorted Arrays Saga_

> \_"Two sorted arrays stood before the Oracle —
> each already in ascending order,
> each possibly containing duplicates within itself.
>
> She was commanded:
>
> **'Merge them into a single sorted array
> containing every DISTINCT element from both.
> No duplicates in the result.'**
>
> The Oracle placed one pointer at the start of each array.
> She compared the two elements they pointed to:
>
> **If equal:** take one copy, advance both.
> **If left is smaller:** take it, advance left.
> **If right is smaller:** take it, advance right.
>
> But before taking any element,
> she checked: is it the same as the last element added?
> If so — skip it. No duplicates allowed.
>
> When one array was exhausted,
> she drained the remaining elements from the other,
> still skipping duplicates.
>
> Two pointers. One pass through each array. Done."\_

---

This is the saga of **Union of Two Sorted Arrays**.

Given two sorted arrays `a` and `b`:
-   Return a sorted array containing all **distinct** elements from both.

```
Input:  a = [1, 2, 3, 4, 5], b = [1, 2, 3, 6, 7]
Output: [1, 2, 3, 4, 5, 6, 7]

Input:  a = [2, 2, 3, 4, 5], b = [1, 1, 2, 3, 4]
Output: [1, 2, 3, 4, 5]

Input:  a = [1, 1, 1], b = [2, 2, 2]
Output: [1, 2]
```

---

## 🧠 Building the Intuition -- Merge with Deduplication

This is a modified merge step from merge sort.
Standard merge combines two sorted arrays into one sorted array.
Here we add one rule: **skip duplicates**.

Both arrays are sorted → duplicates are adjacent.
We only need to check the LAST element added to the result.

Two pointers `i` and `j` walk through `a` and `b`.
At each step, the smaller element is a candidate.
If it's different from the last added element — take it.
If it's the same — skip it.

---

## 🧠 The Three Comparison Cases

**Case 1: `a[i] < b[j]`**
`a[i]` is smaller. It's the next candidate.
Add it (if not a duplicate). Advance `i`.

**Case 2: `a[i] > b[j]`**
`b[j]` is smaller. It's the next candidate.
Add it (if not a duplicate). Advance `j`.

**Case 3: `a[i] == b[j]`**
Both are equal. Take one copy. Advance BOTH.
This avoids adding the same value twice.

---

### 📜 The Scroll of the Merging Kingdoms

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🤝 The Two-Pointer Union

### Initialize

```cpp
vector<int> unionArray(vector<int>& a, vector<int>& b) {
    int i = 0, j = 0;
    int n = a.size(), m = b.size();
    vector<int> result;
```

Two pointers: `i` for array `a`, `j` for array `b`.
`result` collects the union.

---

### Main merge loop

```cpp
    while (i < n && j < m) {
```

Continue while both arrays have elements remaining.

---

### Case 1: a[i] < b[j] — take from a

```cpp
        if (a[i] < b[j]) {
            if (result.empty() || result.back() != a[i]) {
                result.push_back(a[i]);
            }
            i++;
        }
```

`a[i]` is smaller — it comes next in sorted order.
But before adding, check: is it the same as the last element in result?
If yes — it's a duplicate within `a` itself. Skip it.
If no — add it.

Advance `i` regardless (whether we added or skipped).

> _"The left kingdom offers its smallest warrior.
> If this warrior is a new face — welcome to the union.
> If a clone of the last — turned away."_

---

### Case 2: a[i] > b[j] — take from b

```cpp
        else if (a[i] > b[j]) {
            if (result.empty() || result.back() != b[j]) {
                result.push_back(b[j]);
            }
            j++;
        }
```

Same logic, but from array `b`.

---

### Case 3: a[i] == b[j] — take one, advance both

```cpp
        else {
            if (result.empty() || result.back() != a[i]) {
                result.push_back(a[i]);
            }
            i++;
            j++;
        }
    }
```

Both arrays offer the same value. Take one copy.
Advance BOTH pointers — the value is consumed from both sides.

> _"Both kingdoms offer the same warrior.
> Only one copy enters the union.
> Both kingdoms move on."_

---

### Drain remaining elements from a

```cpp
    while (i < n) {
        if (result.empty() || result.back() != a[i]) {
            result.push_back(a[i]);
        }
        i++;
    }
```

Array `b` is exhausted. Drain `a`, skipping duplicates.

---

### Drain remaining elements from b

```cpp
    while (j < m) {
        if (result.empty() || result.back() != b[j]) {
            result.push_back(b[j]);
        }
        j++;
    }
```

Array `a` is exhausted. Drain `b`, skipping duplicates.

---

```cpp
    return result;
}
```

---

### 🎺 The Trial of the Merging Kingdoms

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 4, 5}, b1 = {1, 2, 3, 6, 7};
    auto r1 = unionArray(a1, b1);
    for (int x : r1) cout << x << " ";
    cout << endl; // expected: 1 2 3 4 5 6 7

    vector<int> a2 = {2, 2, 3, 4, 5}, b2 = {1, 1, 2, 3, 4};
    auto r2 = unionArray(a2, b2);
    for (int x : r2) cout << x << " ";
    cout << endl; // expected: 1 2 3 4 5

    vector<int> a3 = {1, 1, 1}, b3 = {2, 2, 2};
    auto r3 = unionArray(a3, b3);
    for (int x : r3) cout << x << " ";
    cout << endl; // expected: 1 2

    return 0;
}
```

---

**Trace for a = [2,2,3,4,5], b = [1,1,2,3,4]:**

| Step | i | j | a[i] | b[j] | Case | Action | result |
|------|---|---|------|------|------|--------|--------|
| 1 | 0 | 0 | 2 | 1 | b<a | add 1, j++ | [1] |
| 2 | 0 | 1 | 2 | 1 | b<a | 1==back, skip, j++ | [1] |
| 3 | 0 | 2 | 2 | 2 | equal | add 2, i++, j++ | [1,2] |
| 4 | 1 | 3 | 2 | 3 | a<b | 2==back, skip, i++ | [1,2] |
| 5 | 2 | 3 | 3 | 3 | equal | add 3, i++, j++ | [1,2,3] |
| 6 | 3 | 4 | 4 | 4 | equal | add 4, i++, j++ | [1,2,3,4] |
| 7 | 4 | 5 | 5 | — | drain a | add 5 | [1,2,3,4,5] |

**Result: [1, 2, 3, 4, 5]** ✓

---

**Trace for a = [1,1,1], b = [2,2,2]:**

| Step | a[i] | b[j] | Case | Action | result |
|------|------|------|------|--------|--------|
| 1 | 1 | 2 | a<b | add 1, i++ | [1] |
| 2 | 1 | 2 | a<b | 1==back, skip, i++ | [1] |
| 3 | 1 | 2 | a<b | 1==back, skip, i++ | [1] |
| 4 | — | 2 | drain b | add 2, j++ | [1,2] |
| 5 | — | 2 | drain b | 2==back, skip, j++ | [1,2] |
| 6 | — | 2 | drain b | 2==back, skip, j++ | [1,2] |

**Result: [1, 2]** ✓

---

## 🔍 Union vs Intersection

| Operation | What to collect | When both equal |
|-----------|----------------|-----------------|
| **Union (this)** | ALL distinct elements from both | Take one, advance both |
| Intersection | ONLY elements in both | Take one, advance both |

For intersection: only add in the `a[i] == b[j]` case.
Skip the `<` and `>` cases entirely (just advance the smaller pointer).

---

## 🔍 Why Not Use a Set?

```cpp
set<int> s(a.begin(), a.end());
s.insert(b.begin(), b.end());
```

This works but is O((N+M) log(N+M)) due to set insertions.
The two-pointer approach is O(N+M) — faster.

Also, the set approach doesn't exploit the fact that both arrays
are already sorted. The two-pointer approach does.

---

## 🔍 The Duplicate Check Pattern

```cpp
if (result.empty() || result.back() != value) {
    result.push_back(value);
}
```

This one-liner handles deduplication cleanly:
-   `result.empty()` — first element, always add.
-   `result.back() != value` — different from last added, add.
-   Otherwise — duplicate, skip.

Works because the input is sorted → duplicates are adjacent →
checking only the last added element is sufficient.

---

### 🧠 Memory of the Merging Kingdoms Law

-   **Two pointers:** `i` on array `a`, `j` on array `b`
-   **Smaller goes first:** `a[i] < b[j]` → take from a. `b[j] < a[i]` → take from b.
-   **Equal:** take one copy, advance BOTH
-   **Duplicate check:** `result.back() != value` before adding
-   **Drain remaining** from whichever array isn't exhausted
-   **Time:** O(N + M). **Space:** O(N + M) for the result.

Thus is remembered the saga of **Union of Two Sorted Arrays**,
where two sorted kingdoms merged into one --
the smaller warrior always stepped forward first,
equals were welcomed once and both sides moved on,
and duplicates were turned away at the gate --
until every distinct element from both kingdoms
stood together in a single sorted line. 🤝🔗✨
