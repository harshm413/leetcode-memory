## 🕯️⚡ _The Lone Soldier in the Army of Pairs: The Single Number Saga_

> \_"In the great Army of Numbers,
> every soldier had a twin —
> a perfect duplicate standing beside them.
>
> Every soldier, except one.
>
> One soldier stood alone —
> no twin, no partner, no echo.
>
> The Oracle was commanded:
>
> **'Find the lone soldier.
> Every other number appears exactly twice.
> One number appears exactly once.
> Find it.'**
>
> The naive Keeper opened a HashMap —
> counting every soldier's appearances,
> then scanning for the one with count of one.
> Correct — but it used O(N) extra memory.
>
> A wiser Keeper sorted the army —
> then walked through, checking every pair.
> Correct — but O(N log N) time.
>
> But the Wisest Keeper knew an ancient magic —
> a single operation so elegant
> it required no memory,
> no sorting,
> no counting:
>
> **XOR.**
>
> 'A number XOR'd with itself becomes zero.
> A number XOR'd with zero stays itself.
> XOR the entire army together —
> all the twins cancel to zero —
> and what remains is the lone soldier.'
>
> One pass. No memory. Pure bitwise truth.
> The lone soldier revealed itself
> through the silence of cancelled pairs."\_

---

This is the saga of **Single Number**.

You are given a non-empty array `nums` where every element
appears **exactly twice** except for one element which appears **exactly once**.

Your task:

-   Find and return the element that appears only once.

```
Input:  [2, 2, 1]         → Output: 1
Input:  [4, 1, 2, 1, 2]   → Output: 4
Input:  [1]               → Output: 1
```

**Constraints:** Linear time O(N). Constant space O(1).

---

## 🧠 The Oracle's Core Insight — XOR: The Magic That Cancels Pairs

Before the approaches, understand the three laws of XOR:

```
Law 1:  a ^ a = 0       (a number XOR'd with itself = 0)
Law 2:  a ^ 0 = a       (a number XOR'd with zero = itself)
Law 3:  XOR is commutative and associative
        (order doesn't matter)
```

Apply these laws to any array where everything appears twice:

```
[4, 1, 2, 1, 2]
= 4 ^ 1 ^ 2 ^ 1 ^ 2
= 4 ^ (1^1) ^ (2^2)      ← reorder (Law 3)
= 4 ^ 0 ^ 0              ← Law 1
= 4                       ← Law 2
```

All paired soldiers XOR'd with their twins → cancelled to `0`.
The lone soldier XOR'd with all those zeros → survived unchanged.

This is the entire algorithm in one operation.
One variable. One loop. No memory. O(N) time. O(1) space.

---

### 📜 The Scroll of All Approaches

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>
using namespace std;
```

---

## ⚡ Approach 1 — XOR: The Wisest Path (Optimal)

_One pass, no memory, pure bitwise magic_

```cpp
int singleNumber_XOR(vector<int>& nums) {
    int result = 0;
    for (int num : nums) {
        result ^= num;
    }
    return result;
}
```

`result` started at `0` — the identity for XOR (Law 2).

Every number was XOR'd into `result` one by one.

Paired soldiers cancelled each other to `0`.
The lone soldier, with no twin to cancel it,
survived in `result` at the end.

**Why `result = 0` at start?**
Because `0 ^ a = a` — starting at zero means
the first number XOR'd with zero just becomes itself.
The chain could begin cleanly.

> _"The lone soldier did not fight its battles.
> It simply outlasted all the cancellations around it."_

-   **Time:** O(N)
-   **Space:** O(1)

---

## 📚 Approach 2 — HashMap: The Honest Counter

_Count appearances, find the one with count 1_

```cpp
int singleNumber_HashMap(vector<int>& nums) {
    unordered_map<int, int> count;
    for (int num : nums) {
        count[num]++;
    }
    for (auto& [num, cnt] : count) {
        if (cnt == 1) return num;
    }
    return -1;
}
```

Every soldier was counted in the HashMap.
After the full march, the Keeper scanned the ledger —
the soldier with count `1` was the lone one.

Clear. Correct. Easy to understand and explain.
Used when you are not aware of the XOR trick —
or when the interviewer asks for the naive approach first.

-   **Time:** O(N)
-   **Space:** O(N) — the HashMap grows with unique elements

---

## 🎯 Approach 3 — HashSet: The Set Math Path

_Use the identity: 2 × (sum of unique) − sum of all = lone number_

```cpp
int singleNumber_Set(vector<int>& nums) {
    unordered_set<int> unique(nums.begin(), nums.end());
    long long sumSet = 0, sumAll = 0;
    for (int n : unique) sumSet += n;
    for (int n : nums)   sumAll += n;
    return (long long)2 * sumSet - sumAll;
}
```

The mathematical identity behind this approach:

```
If all pairs cancel and one is lone:
2 × (sum of distinct values) − (sum of all values) = lone value

Example: [4, 1, 2, 1, 2]
distinct values: {4, 1, 2} → sumSet = 7
sumAll = 4+1+2+1+2 = 10
answer = 2×7 − 10 = 14 − 10 = 4 ✓
```

Elegant math — but uses O(N) memory for the set.
Useful to know as an alternative insight.

-   **Time:** O(N)
-   **Space:** O(N) — the set holds all distinct elements

---

## 🗂️ Approach 4 — Sorting: The Visual Path

_Sort, then check pairs — if a number doesn't match its neighbor, it's alone_

```cpp
int singleNumber_Sort(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    for (int i = 0; i < (int)nums.size() - 1; i += 2) {
        if (nums[i] != nums[i + 1]) return nums[i];
    }
    return nums[nums.size() - 1];
}
```

After sorting, every pair sat side by side.
The Keeper walked through the army in steps of 2:

-   If a soldier and their immediate neighbor were different —
    the soldier at position `i` was the lone one. Return it.
-   If they matched — this was a valid pair. Skip both and continue.
-   If the loop completed without finding a mismatch —
    the lone soldier was the **last element** (all pairs ahead of it matched).

> _"In a sorted world, the lone soldier has no neighbor who matches.
> The pair beside them belongs to someone else."_

-   **Time:** O(N log N) — dominated by sorting
-   **Space:** O(1) — in-place sort (or O(log N) for recursive sort stack)

---

### 🎺 The Trial of the Army

```cpp
int main() {
    vector<int> test1 = {2, 2, 1};
    vector<int> test2 = {4, 1, 2, 1, 2};
    vector<int> test3 = {1};

    cout << "XOR:     " << singleNumber_XOR(test1) << endl;     // 1
    cout << "HashMap: " << singleNumber_HashMap(test2) << endl;  // 4
    cout << "Set:     " << singleNumber_Set(test2) << endl;      // 4
    cout << "Sort:    " << singleNumber_Sort(test2) << endl;     // 4
    cout << "Single:  " << singleNumber_XOR(test3) << endl;      // 1
    return 0;
}
```

**XOR trace for `[4, 1, 2, 1, 2]`:**

| Step | num | result (XOR so far) |
| ---- | --- | ------------------- |
| init | —   | `0`                 |
| 1    | 4   | `0 ^ 4 = 4`         |
| 2    | 1   | `4 ^ 1 = 5`         |
| 3    | 2   | `5 ^ 2 = 7`         |
| 4    | 1   | `7 ^ 1 = 6`         |
| 5    | 2   | `6 ^ 2 = 4`         |

Result: `4` ✓ — The lone soldier survived through all the cancellations.

**Sort trace for `[4, 1, 2, 1, 2]`:**

Sorted: `[1, 1, 2, 2, 4]`

| Index | nums[i] | nums[i+1] | Match?    |
| ----- | ------- | --------- | --------- |
| 0     | 1       | 1         | ✓ skip    |
| 2     | 2       | 2         | ✓ skip    |
| 4     | 4       | (last)    | loop ends |

Return last element: `4` ✓

---

### 🧠 Memory of All Four Approaches

| Approach | Time       | Space | Key Idea                                         |
| -------- | ---------- | ----- | ------------------------------------------------ |
| **XOR**  | O(N)       | O(1)  | `a^a=0`, `a^0=a` — pairs cancel, lone survives   |
| HashMap  | O(N)       | O(N)  | Count frequencies, find count == 1               |
| Set+Math | O(N)       | O(N)  | `2×sumUnique − sumAll = lone`                    |
| Sort     | O(N log N) | O(1)  | Sort then walk pairs, first mismatch is the lone |

**The interview answer order:**

1. Mention the naive HashMap approach first (shows you can solve it).
2. Then reveal the XOR trick (shows you know bit manipulation).
3. If asked for alternatives — the sort approach demonstrates another O(1) space path.

**XOR laws to memorize forever:**

```
a ^ a = 0
a ^ 0 = a
XOR is commutative and associative
```

These three laws unlock XOR-based solutions across many problems.

Thus is remembered the saga of **Single Number**,
where the lone soldier survived not through strength —
but through the silence of cancelled pairs —
revealed by the ancient XOR magic
that reduced an entire army of duplicates to zero
and let the unpaired one speak its name alone. 🕯️⚡✨
