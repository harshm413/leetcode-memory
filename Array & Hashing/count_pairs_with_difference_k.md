## 🎯➖ _The Difference Census: The Count Pairs With Difference K Saga_

> \_"The Oracle was given an array and a target difference K.
>
> She was commanded:
>
> **'Count ALL pairs (i, j) where i < j and arr[i] - arr[j] = K.'**
>
> `arr[i] - arr[j] = K` means `arr[i] = arr[j] + K`.
> The earlier element minus the later element equals K.
>
> The Oracle reframed:
>
> **For each element arr[j], how many PREVIOUS elements equal arr[j] + K?**
>
> Same hashmap pattern as Count Pairs With Sum.
> Different complement: `arr[j] + K` instead of `K - arr[j]`."\_

---

This is the saga of **Count Pairs With Difference K**.

Given an array `arr` and integer `K`:
-   Count all pairs `(i, j)` where `i < j` and `arr[i] - arr[j] = K`.

```
Input:  arr = [5, 1, 3, 4, 2], K = 2
Output: 2   (pairs: arr[0]-arr[2]=5-3=2, arr[3]-arr[4]=4-2=2)

Input:  arr = [1, 1, 1, 1], K = 0
Output: 6   (every pair: arr[i]-arr[j]=0 for equal elements, C(4,2)=6)

Input:  arr = [8, 4, 12, 6, 2], K = 2
Output: 2   (8-6=2, 4-2=2)
```

---

## 🧠 The Complement Formula

We want `arr[i] - arr[j] = K` where `i < j`.

Rearranging: `arr[i] = arr[j] + K`.

When we're at index `j`, we look for previous elements with value `arr[j] + K`.

**Complement = `arr[j] + K`.**

> _"At each element, the Oracle asks:
> 'How many previous elements are exactly K more than me?'
> Each one forms a valid pair."_

---

### 📜 The Scroll of the Difference Census

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## ➖ The Counting Solution

```cpp
int countPairsWithDiff(vector<int>& arr, int K) {
    unordered_map<int, int> freq;
    int count = 0;
```

---

```cpp
    for (int j = 0; j < arr.size(); j++) {
        int complement = arr[j] + K;
```

Need previous elements with value `arr[j] + K`.

---

```cpp
        count += freq[complement];
```

Each previous occurrence of the complement = one valid pair.

---

```cpp
        freq[arr[j]]++;
    }
    return count;
}
```

Record current element AFTER counting (ensures i < j).

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> a1 = {5, 1, 3, 4, 2};
    cout << countPairsWithDiff(a1, 2) << endl; // expected: 2

    vector<int> a2 = {1, 1, 1, 1};
    cout << countPairsWithDiff(a2, 0) << endl; // expected: 6

    vector<int> a3 = {8, 4, 12, 6, 2};
    cout << countPairsWithDiff(a3, 2) << endl; // expected: 2

    return 0;
}
```

---

**Full trace for arr = [5, 1, 3, 4, 2], K = 2:**

| j | arr[j] | complement (arr[j]+2) | freq[comp] | count += | freq after |
|---|--------|----------------------|------------|----------|------------|
| 0 | 5      | 7                    | 0          | 0        | {5:1} |
| 1 | 1      | 3                    | 0          | 0        | {5:1, 1:1} |
| 2 | 3      | 5                    | 1          | **1**    | {5:1, 1:1, 3:1} |
| 3 | 4      | 6                    | 0          | 0        | {5:1, 1:1, 3:1, 4:1} |
| 4 | 2      | 4                    | 1          | **1**    | {5:1, 1:1, 3:1, 4:1, 2:1} |

**Total: 2** ✓

Pairs: arr[0]-arr[2] = 5-3 = 2 ✓, arr[3]-arr[4] = 4-2 = 2 ✓.

---

**Trace for arr = [1, 1, 1, 1], K = 0:**

| j | arr[j] | complement (arr[j]+0) | freq[comp] | count += |
|---|--------|----------------------|------------|----------|
| 0 | 1      | 1                    | 0          | 0        |
| 1 | 1      | 1                    | 1          | 1        |
| 2 | 1      | 1                    | 2          | 2        |
| 3 | 1      | 1                    | 3          | 3        |

**Total: 0+1+2+3 = 6 = C(4,2)** ✓

When K=0, complement = arr[j] itself. Counting equal pairs.

---

## 🔍 What If the Problem Asks for |arr[i] - arr[j]| = K?

For ABSOLUTE difference, a pair is valid if `arr[i] - arr[j] = K`
OR `arr[j] - arr[i] = K`. Two directions.

**Solution: look for TWO complements.**

```cpp
int countPairsAbsDiff(vector<int>& arr, int K) {
    unordered_map<int, int> freq;
    int count = 0;
    for (int j = 0; j < arr.size(); j++) {
        count += freq[arr[j] + K];
        if (K != 0) count += freq[arr[j] - K];
        freq[arr[j]]++;
    }
    return count;
}
```

`freq[arr[j] + K]` = previous elements that are K MORE than current.
`freq[arr[j] - K]` = previous elements that are K LESS than current.

**`if (K != 0)`** — when K=0, both complements are the same.
Without this check, we'd double-count.

---

## 🔍 The Pair Counting Family

| Problem | Condition | Complement |
|---------|-----------|------------|
| Count Pairs Sum = K | `arr[i] + arr[j] = K` | `K - arr[j]` |
| **Count Pairs Diff = K** | **`arr[i] - arr[j] = K`** | **`arr[j] + K`** |
| Count Pairs Abs Diff = K | `|arr[i] - arr[j]| = K` | `arr[j] + K` AND `arr[j] - K` |
| Count Pairs XOR = K | `arr[i] ^ arr[j] = K` | `arr[j] ^ K` |

Same hashmap pattern. Different complement formula.

---

## 🔍 Sum vs Difference — The Complement Derivation

**Sum:** `arr[i] + arr[j] = K` → `arr[i] = K - arr[j]` → look for `K - arr[j]`.
**Diff:** `arr[i] - arr[j] = K` → `arr[i] = arr[j] + K` → look for `arr[j] + K`.

The complement is always: isolate `arr[i]` on one side,
the other side is what you look up in the map.

---

### 🧠 Memory of the Difference Census Law

-   **`arr[i] - arr[j] = K`** → complement = `arr[j] + K`
-   **`count += freq[arr[j] + K]`** — count previous elements that are K more
-   **Record AFTER counting** — ensures i < j
-   **K = 0:** counts pairs of equal elements (C(n,2) for n duplicates)
-   **Absolute diff:** check BOTH `arr[j] + K` and `arr[j] - K` (skip second if K=0)
-   **Time:** O(N). **Space:** O(N).

Thus is remembered the saga of **Count Pairs With Difference K**,
where the Oracle walked the array asking at each element:
'How many previous elements are exactly K more than me?' —
the frequency map answering instantly —
each match forming a valid pair —
the same hashmap trick as sum pairs,
just a different complement formula. 🎯➖✨
