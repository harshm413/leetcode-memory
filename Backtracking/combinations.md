## 🎯📦 _The Fixed-Size Selections: The Combinations Saga_

> \_"The Oracle was given two integers `n` and `k`.
>
> She was commanded:
>
> **'Return all possible combinations of `k` numbers
> chosen from the range `[1, n]`.'**
>
> This was Subsets -- but with a SIZE constraint.
> Instead of collecting ALL subsets,
> only collect subsets of EXACTLY size `k`.
>
> Same backtracking template.
> One extra condition: add to result only when `current.size() == k`.
> One extra pruning: if remaining elements aren't enough to reach size k, stop early."\_

---

This is the saga of **Combinations**.

Given `n` and `k`:

-   Return all combinations of `k` numbers from `[1, n]`.
-   Order doesn't matter (`[1,2]` = `[2,1]`).

```
Input:  n = 4, k = 2
Output: [[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]

Input:  n = 1, k = 1
Output: [[1]]
```

---

## 🧠 The Oracle's Core Insight -- Subsets with Size Constraint

This is the **Subsets template** with two modifications:

| Subsets                           | Combinations                      |
| --------------------------------- | --------------------------------- |
| Add every state to result         | Add only when `current.size() == k` |
| No pruning                        | Prune when not enough elements remain |
| Elements from array               | Elements from range [1, n]        |

```
backtrack(start, current):
  If current.size() == k: add to result. Return.

  For i = start to n:
    current.push(i)
    backtrack(i + 1, current)
    current.pop()
```

**Pruning optimization:** if `n - i + 1 < k - current.size()`,
there aren't enough remaining numbers to fill the combination. Stop early.

```
Time:  O(C(n,k) × k) -- C(n,k) combinations, O(k) to copy each
Space: O(k) -- recursion depth
```

---

### 📜 The Scroll of the Fixed Selections

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📦 The Backtracking Ritual

```cpp
void backtrack(int n, int k, int start,
               vector<int>& current, vector<vector<int>>& result) {
```

---

### 🎯 Base Case -- Combination Complete

```cpp
    if ((int)current.size() == k) {
        result.push_back(current);
        return;
    }
```

The combination has exactly `k` elements. Record it.

---

### 🔁 Try Each Number from `start` to n

```cpp
    for (int i = start; i <= n; i++) {
```

Pick numbers in increasing order (start onward) to avoid duplicates.

---

### ✂️ Pruning -- Not Enough Numbers Remaining (Optional)

```cpp
        // Optional: if remaining numbers < spots to fill, stop
        // if (n - i + 1 < k - (int)current.size()) break;
```

If there are fewer numbers left (`n - i + 1`) than spots to fill
(`k - current.size()`), no valid combination can be formed. Break.

---

### Pick, Recurse, Backtrack

```cpp
        current.push_back(i);
        backtrack(n, k, i + 1, current, result);
        current.pop_back();
    }
}
```

Same as Subsets: pick `i`, recurse with `i + 1`, undo.

---

## 🔮 The Main Function

```cpp
vector<vector<int>> combine(int n, int k) {
    vector<vector<int>> result;
    vector<int> current;
    backtrack(n, k, 1, current, result);
    return result;
}
```

Start from 1 (range is [1, n]).

---

### 🎺 The Trial

```cpp
int main() {
    auto r1 = combine(4, 2);
    for (auto& c : r1) {
        for (int x : c) cout << x << " ";
        cout << endl;
    }
    // expected: 1 2 | 1 3 | 1 4 | 2 3 | 2 4 | 3 4
    return 0;
}
```

---

**Trace for n=4, k=2:**

```
backtrack(start=1, current=[])
  i=1: push 1. current=[1].
    i=2: push 2. current=[1,2]. SIZE==2 → ADD [1,2]. ✓ pop.
    i=3: push 3. current=[1,3]. SIZE==2 → ADD [1,3]. ✓ pop.
    i=4: push 4. current=[1,4]. SIZE==2 → ADD [1,4]. ✓ pop.
  pop 1.
  i=2: push 2. current=[2].
    i=3: push 3. current=[2,3]. SIZE==2 → ADD [2,3]. ✓ pop.
    i=4: push 4. current=[2,4]. SIZE==2 → ADD [2,4]. ✓ pop.
  pop 2.
  i=3: push 3. current=[3].
    i=4: push 4. current=[3,4]. SIZE==2 → ADD [3,4]. ✓ pop.
  pop 3.
  i=4: push 4. current=[4]. (can't reach size 2 from here) → nothing added.
  pop 4.
```

**Result: [[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]** ✓

C(4,2) = 6 combinations.

---

### 🧠 Memory of the Fixed Selections Law

-   **Same as Subsets** but add only when `current.size() == k`
-   **Loop from `start` to `n`** (range [1, n])
-   **Pruning:** break if remaining numbers < spots to fill
-   **Time:** O(C(n,k) × k)
-   **Space:** O(k)

Thus is remembered the saga of **Combinations**,
where the Oracle picked numbers from 1 to n --
always moving forward, never backward --
collecting only when exactly k numbers were chosen. 🎯📦✨
