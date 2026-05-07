## 🎯🔢 _The Pair Census: The Count Pairs With Sum K Saga_

> \_"The Oracle was given an array and a target sum K.
>
> She was commanded:
>
> **'Count ALL pairs (i, j) where i < j and arr[i] + arr[j] = K.'**
>
> Not find ONE pair like Two Sum.
> COUNT every valid pair.
>
> The Oracle used the same hashmap trick as Two Sum,
> but instead of returning indices on the first match,
> she COUNTED how many complements existed so far:
>
> **For each element, ask: 'How many times has K - arr[i]
> appeared BEFORE me?'**
>
> Each previous occurrence of the complement = one valid pair.
> Accumulate the count. Record the current element. Move on."\_

---

This is the saga of **Count Pairs With Sum K**.

Given an array `arr` and integer `K`:
-   Count all pairs `(i, j)` where `i < j` and `arr[i] + arr[j] = K`.

```
Input:  arr = [1, 5, 7, -1, 5], K = 6
Output: 3   (pairs: (1,5), (1,5), (7,-1) → indices (0,1), (0,4), (2,3))

Input:  arr = [1, 1, 1, 1], K = 2
Output: 6   (every pair of 1s: C(4,2) = 6)

Input:  arr = [1, 2, 3, 4], K = 10
Output: 0   (no pair sums to 10)
```

---

## 🧠 The Insight — Two Sum But Count Instead of Return

In Two Sum, when we find the complement in the map, we return immediately.
Here, we DON'T return. We count how many times the complement has appeared
and add that to our running total.

Why does `freq[complement]` give the count?

If `K - arr[i]` has appeared 3 times before index `i`,
then there are 3 different indices `j < i` where `arr[j] = K - arr[i]`.
Each forms a valid pair with `i`. That's 3 pairs.

---

## 🧠 Why This Handles Duplicates Correctly

```
arr = [1, 1, 1, 1], K = 2

i=0: complement = 1. freq[1] = 0. count += 0. freq = {1:1}.
i=1: complement = 1. freq[1] = 1. count += 1. freq = {1:2}.
i=2: complement = 1. freq[1] = 2. count += 2. freq = {1:3}.
i=3: complement = 1. freq[1] = 3. count += 3. freq = {1:4}.

Total: 0 + 1 + 2 + 3 = 6 = C(4,2). ✓
```

Each new 1 pairs with ALL previous 1s. The frequency map
naturally accumulates the count of available partners.

---

### 📜 The Scroll of the Pair Census

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🎯 The Counting Solution

### Initialize

```cpp
int countPairsWithSum(vector<int>& arr, int K) {
    unordered_map<int, int> freq;
    int count = 0;
```

`freq` maps each value to how many times it has appeared so far.
`count` = total number of valid pairs found.

---

### Walk the array

```cpp
    for (int i = 0; i < arr.size(); i++) {
        int complement = K - arr[i];
```

For each element, compute what it NEEDS to form a pair summing to K.

---

### Count how many complements exist before this index

```cpp
        count += freq[complement];
```

`freq[complement]` = how many times the complement has appeared
at indices BEFORE `i`. Each occurrence = one valid pair with `i`.

If the complement hasn't appeared → `freq[complement] = 0`. Adds nothing.

> _"The Oracle asks: 'How many partners are waiting for me?'
> The frequency map answers instantly.
> Each waiting partner forms a pair."_

---

### Record the current element

```cpp
        freq[arr[i]]++;
    }
    return count;
}
```

Add the current element to the frequency map.
Future elements might need it as their complement.

**Order matters:** count BEFORE recording.
This ensures `i < j` — we only count elements that appeared BEFORE.

---

### 🎺 The Trial of the Pair Census

```cpp
int main() {
    vector<int> a1 = {1, 5, 7, -1, 5};
    cout << countPairsWithSum(a1, 6) << endl; // expected: 3

    vector<int> a2 = {1, 1, 1, 1};
    cout << countPairsWithSum(a2, 2) << endl; // expected: 6

    vector<int> a3 = {1, 2, 3, 4};
    cout << countPairsWithSum(a3, 10) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for arr = [1, 5, 7, -1, 5], K = 6:**

| i | arr[i] | complement | freq[comp] | count += | freq after |
|---|--------|-----------|------------|----------|------------|
| 0 | 1      | 5         | 0          | 0        | {1:1} |
| 1 | 5      | 1         | 1          | **1**    | {1:1, 5:1} |
| 2 | 7      | -1        | 0          | 0        | {1:1, 5:1, 7:1} |
| 3 | -1     | 7         | 1          | **1**    | {1:1, 5:1, 7:1, -1:1} |
| 4 | 5      | 1         | 1          | **1**    | {1:1, 5:2, 7:1, -1:1} |

**Total count: 0 + 1 + 0 + 1 + 1 = 3** ✓

Pairs: (1,5) at indices (0,1), (7,-1) at indices (2,3), (1,5) at indices (0,4).

---

**Trace for arr = [1, 1, 1, 1], K = 2:**

| i | arr[i] | complement | freq[comp] | count += | Running count |
|---|--------|-----------|------------|----------|---------------|
| 0 | 1      | 1         | 0          | 0        | 0 |
| 1 | 1      | 1         | 1          | 1        | 1 |
| 2 | 1      | 1         | 2          | 2        | 3 |
| 3 | 1      | 1         | 3          | 3        | **6** |

**Total: 6 = C(4,2)** ✓

Each new 1 pairs with all previous 1s: 0+1+2+3 = 6.

---

## 🔍 Two Sum vs Count Pairs — What Changed

| Aspect | Two Sum | Count Pairs (this) |
|--------|---------|-------------------|
| Goal | Find ONE pair | Count ALL pairs |
| On match | `return {j, i}` | `count += freq[complement]` |
| After match | Stop | Continue scanning |
| Map stores | `value → index` | `value → frequency` |
| Time | O(N) | O(N) |
| Space | O(N) | O(N) |

Same hashmap pattern. Different action on match.

---

## 🔍 What About the Sorting + Two Pointer Approach?

```cpp
int countPairsWithSum(vector<int>& arr, int K) {
    sort(arr.begin(), arr.end());
    int left = 0, right = arr.size() - 1, count = 0;
    while (left < right) {
        int sum = arr[left] + arr[right];
        if (sum < K) left++;
        else if (sum > K) right--;
        else {
            // Handle duplicates
            if (arr[left] == arr[right]) {
                int n = right - left + 1;
                count += n * (n - 1) / 2;
                break;
            }
            int leftCount = 1, rightCount = 1;
            while (left + 1 < right && arr[left] == arr[left + 1]) { left++; leftCount++; }
            while (right - 1 > left && arr[right] == arr[right - 1]) { right--; rightCount++; }
            count += leftCount * rightCount;
            left++; right--;
        }
    }
    return count;
}
```

O(N log N) time, O(1) space. Handles duplicates with counting.
More complex code but saves space. The hashmap approach is simpler.

---

### 🧠 Memory of the Pair Census Law

-   **Same as Two Sum** but count instead of return
-   **`count += freq[complement]`** — each previous complement = one pair
-   **Record AFTER counting** — ensures `i < j` ordering
-   **Handles duplicates naturally** — frequency accumulates
-   **All-same array:** count = C(N,2) = N×(N-1)/2
-   **Time:** O(N). **Space:** O(N).

Thus is remembered the saga of **Count Pairs With Sum K**,
where the Oracle walked the array with a frequency map —
at each element asking how many partners waited —
each waiting partner forming a valid pair —
the count growing with every match found —
until every pair summing to K was tallied
in a single pass through the array. 🎯🔢✨
