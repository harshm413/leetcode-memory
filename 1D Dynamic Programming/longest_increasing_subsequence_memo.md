## 📈🔗🧠 _The Rising Chain (Memoization): The Longest Increasing Subsequence Saga_

> \_"The Oracle was given an array of numbers.
>
> She was commanded:
>
> **'Find the length of the longest subsequence
> where every element is STRICTLY greater than the previous.'**
>
> A subsequence can skip elements but must preserve order.
> It must be strictly increasing — no equal, no decreasing.
>
> The Oracle stood at each element and asked:
>
> **'If I include this element in my chain,
> what's the longest rising chain I can build from here?'**
>
> At each position, she looked at every element AFTER her.
> If it was larger — she could extend the chain.
> She tried every valid extension and picked the longest.
>
> This was the birth of the LIS recurrence."\_

---

This is the saga of **Longest Increasing Subsequence (LeetCode 300)**.

Given an integer array `nums`:
-   Find the length of the **longest strictly increasing subsequence**.

```
Input:  nums = [10,9,2,5,3,7,101,18]
Output: 4   (LIS = [2,3,7,101] or [2,3,7,18] or [2,5,7,101]...)

Input:  nums = [0,1,0,3,2,3]
Output: 4   (LIS = [0,1,2,3])

Input:  nums = [7,7,7,7,7]
Output: 1   (strictly increasing — no equal allowed)
```

---

## 🧠 Building the Intuition -- The "Previous Index" State

The key question at each element: **"Should I include this element?"**

That depends on what came BEFORE it in the subsequence.
If the previous element was 5, I can only include elements > 5.
If the previous element was 3, I can include elements > 3.

So the state needs TWO things:
-   **Current index:** which element am I considering?
-   **Previous index:** what was the last element I included?

`solve(index, prevIndex)` = length of the longest increasing subsequence
starting from `index`, given that the last included element was at `prevIndex`.

---

## 🧠 The Recurrence

At each `index`, two choices:

**SKIP this element:**
Don't include `nums[index]`. Move to `index + 1`.
Previous stays the same.
`solve(index + 1, prevIndex)`

**INCLUDE this element (if valid):**
Only if `prevIndex == -1` (nothing picked yet)
OR `nums[index] > nums[prevIndex]` (strictly increasing).
Include it. Move to `index + 1`. Previous becomes `index`.
`1 + solve(index + 1, index)`

Take the max.

```
solve(index, prevIndex):
  If index == n: return 0

  skip = solve(index + 1, prevIndex)

  include = 0
  if prevIndex == -1 OR nums[index] > nums[prevIndex]:
    include = 1 + solve(index + 1, index)

  return max(skip, include)
```

---

## 🧠 The prevIndex Shift Trick

`prevIndex` starts at -1 (nothing picked). But we can't use -1
as an array index. So we shift: store `prevIndex + 1` in the memo.

`prevIndex = -1` → stored as `0`.
`prevIndex = 0` → stored as `1`.
`prevIndex = i` → stored as `i + 1`.

Memo size: `n × (n + 1)`. The +1 accounts for the -1 state.

---

### 📜 The Scroll of the Rising Chain

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📈 Setting Up the Cache

```cpp
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<vector<int>> memo(n, vector<int>(n + 1, -1));
```

`n × (n+1)` cache. Second dimension is `prevIndex + 1` (shifted by 1).

---

```cpp
    return solve(0, -1, nums, memo);
}
```

Start from index 0, no previous element picked.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int index, int prevIndex, vector<int>& nums,
          vector<vector<int>>& memo) {
```

"Starting from `index`, with last picked element at `prevIndex`,
what's the longest increasing subsequence I can build?"

---

### 🛑 Past the array

```cpp
    if (index == nums.size()) return 0;
```

No more elements to consider. Chain length = 0 from here.

---

### 📖 Cache check (with shift)

```cpp
    if (memo[index][prevIndex + 1] != -1) return memo[index][prevIndex + 1];
```

`prevIndex + 1` because prevIndex can be -1, and we need a valid array index.

---

### 🚶 SKIP this element

```cpp
    int skip = solve(index + 1, prevIndex, nums, memo);
```

Don't include `nums[index]`. Move forward. Previous unchanged.

> _"The element doesn't fit the chain — or maybe a better one comes later.
> Skip it. The chain stays as it was."_

---

### ✅ INCLUDE this element (if valid)

```cpp
    int include = 0;
    if (prevIndex == -1 || nums[index] > nums[prevIndex]) {
        include = 1 + solve(index + 1, index, nums, memo);
    }
```

Two conditions allow inclusion:
-   `prevIndex == -1`: nothing picked yet. Any element can start the chain.
-   `nums[index] > nums[prevIndex]`: strictly greater than the last picked.

If valid: count this element (+1), move forward, update previous to `index`.

> _"This element rises above the last.
> It extends the chain by one.
> The chain grows, and this element becomes the new benchmark."_

---

### ⚖️ Take the longer chain

```cpp
    memo[index][prevIndex + 1] = max(skip, include);
    return memo[index][prevIndex + 1];
}
```

---

### 🎺 The Trial of the Rising Chain

```cpp
int main() {
    vector<int> n1 = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << lengthOfLIS(n1) << endl; // expected: 4

    vector<int> n2 = {0, 1, 0, 3, 2, 3};
    cout << lengthOfLIS(n2) << endl; // expected: 4

    vector<int> n3 = {7, 7, 7, 7, 7};
    cout << lengthOfLIS(n3) << endl; // expected: 1

    return 0;
}
```

---

**Trace for nums = [10,9,2,5,3,7,101,18]:**

```
solve(0, -1): nums[0]=10
  skip: solve(1, -1)
  include: 1 + solve(1, 0)  ← 10 starts a chain

  ... deep recursion ...

One optimal path:
  index 2: include 2 (prev=-1 or prev was skipped, 2 starts fresh)
  index 3: include 5 (5 > 2) → chain [2, 5]
  index 5: include 7 (7 > 5) → chain [2, 5, 7]
  index 6: include 101 (101 > 7) → chain [2, 5, 7, 101]
  Length = 4
```

**Answer: 4** ✓

---

**Trace for nums = [7,7,7,7,7]:**

```
At every index: nums[index] == nums[prevIndex] (7 == 7).
Strictly increasing requires >, not >=.
No element can extend any chain.
Each element alone = chain of length 1.
```

**Answer: 1** ✓

---

## 🔍 Why Two Parameters Are Needed

Unlike House Robber where `solve(i)` was enough,
LIS needs to know WHAT was picked last — not just WHERE we are.

Two elements at the same index can have different valid extensions
depending on what came before. `solve(5, prev=3)` and `solve(5, prev=8)`
have different answers because different elements are valid continuations.

---

## 🔍 The LIS Family

| Problem                          | Twist                              |
| -------------------------------- | ---------------------------------- |
| **LIS (this)**                   | Foundation — longest rising chain  |
| Print LIS                        | Backtrack to recover actual sequence|
| LIS with Binary Search (DP-43)   | O(N log N) using patience sorting  |
| Largest Divisible Subset          | Divisibility instead of <          |
| Longest String Chain              | String insertion instead of <      |
| Longest Bitonic Subsequence       | LIS + LDS combined                |
| Number of LIS                    | Count all LIS, not just length     |

---

### 🧠 Memory of the Rising Chain (Memoization) Law

-   **State:** `(index, prevIndex)` — where we are + what we last picked
-   **Skip:** `solve(index+1, prevIndex)` — don't include, move on
-   **Include:** `1 + solve(index+1, index)` — if `nums[index] > nums[prevIndex]`
-   **prevIndex shift:** store `prevIndex + 1` in memo (handles -1)
-   **Base:** `index == n → 0`
-   **Time:** O(N²). **Space:** O(N²).

Thus is remembered the saga of **Longest Increasing Subsequence (Memoization)**,
where the Oracle at each element faced two futures --
skip and preserve the chain as it was,
or include and extend the rising sequence by one --
always checking that the new element rose above the last,
building the longest ascending chain
through the landscape of numbers. 📈🔗🧠✨
