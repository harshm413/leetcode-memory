## 📈🔗📋 _The Rising Chain (Tabulation): The Longest Increasing Subsequence Saga_

> \_"The Oracle had found the LIS with memoization.
> Now she built the answer bottom-up.
>
> But she used a DIFFERENT formulation --
> the classic O(N²) DP that is cleaner for tabulation:
>
> **`dp[i]` = length of the longest increasing subsequence
> ENDING at index `i`.**
>
> For each element, look at ALL elements before it.
> If any earlier element is smaller, the chain can extend.
> `dp[i] = max(dp[j] + 1)` for all valid `j < i`.
>
> The answer = the maximum value in the entire dp array.
> Not at a fixed cell — the LIS could end anywhere."\_

---

This is the saga of **Longest Increasing Subsequence (Tabulation)**.

Same problem:
-   Find the length of the longest strictly increasing subsequence.

```
Input:  nums = [10,9,2,5,3,7,101,18]  →  Output: 4
Input:  nums = [0,1,0,3,2,3]           →  Output: 4
Input:  nums = [7,7,7,7,7]             →  Output: 1
```

---

## 🧠 The Classic O(N²) Formulation

```
dp[i] = length of LIS ending at index i.
```

Every element alone is an LIS of length 1.
So `dp[i]` starts at 1 for all i.

For each `i`, look at every `j < i`:
-   If `nums[j] < nums[i]`: element `j` can come before `i` in the chain.
    `dp[i] = max(dp[i], dp[j] + 1)`.

The answer = `max(dp[0], dp[1], ..., dp[n-1])`.

> _"Each element asks every earlier element:
> 'Are you smaller than me?
> If so, I can extend your chain by one.
> Give me your best chain length, and I'll add myself.'"_

---

## 🧠 Why "Ending At" Not "Up To"

If `dp[i]` meant "best LIS in nums[0..i]", we'd need to track
what the last element was — leading back to the 2D state.

By defining `dp[i]` as "LIS ending AT i", the last element is known:
it's `nums[i]`. The comparison `nums[j] < nums[i]` is straightforward.

The tradeoff: the answer isn't at `dp[n-1]` — it's the max over all dp values.

---

### 📜 The Scroll of the Bottom-Up Rising Chain

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation

### Initialize — every element is an LIS of length 1

```cpp
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp(n, 1);
```

Every element alone forms a valid increasing subsequence of length 1.
This is the base case — no element before it, just itself.

---

### 🔁 For each element, check all previous elements

```cpp
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
```

For each `i`, scan every `j` from 0 to `i-1`.
"Can element `j` come before element `i` in an increasing chain?"

---

### ✅ If nums[j] < nums[i] — extend the chain

```cpp
            if (nums[j] < nums[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
            }
```

`nums[j]` is smaller than `nums[i]` — valid predecessor.
The chain ending at `j` has length `dp[j]`.
Extending it with `nums[i]` gives length `dp[j] + 1`.

We take the max because multiple `j`'s might be valid predecessors.
We want the one that gives the longest chain.

> _"Element i asks every earlier element:
> 'How long is your best chain?'
> It picks the longest valid chain and adds itself."_

---

```cpp
        }
    }
```

---

### 🏆 The answer — max over all dp values

```cpp
    return *max_element(dp.begin(), dp.end());
}
```

The LIS could end at ANY index. The longest one wins.

---

### 🎺 The Trial

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

**Full dp trace for nums = [10,9,2,5,3,7,101,18]:**

| i | nums[i] | Check j < i | dp[i] | Reasoning |
|---|---------|-------------|-------|-----------|
| 0 | 10      | —           | 1     | Base: just [10] |
| 1 | 9       | j=0: 10>9 ✗ | 1     | No valid predecessor. Just [9] |
| 2 | 2       | j=0: 10>2 ✗, j=1: 9>2 ✗ | 1 | No valid predecessor. Just [2] |
| 3 | 5       | j=0: 10>5 ✗, j=1: 9>5 ✗, j=2: 2<5 ✓ dp[2]+1=2 | **2** | Chain: [2, 5] |
| 4 | 3       | j=2: 2<3 ✓ dp[2]+1=2 | **2** | Chain: [2, 3] |
| 5 | 7       | j=2: 2<7 ✓ dp=2, j=3: 5<7 ✓ dp=3, j=4: 3<7 ✓ dp=3 | **3** | Chain: [2, 5, 7] or [2, 3, 7] |
| 6 | 101     | j=5: 7<101 ✓ dp=4 (best) | **4** | Chain: [2, 5, 7, 101] |
| 7 | 18      | j=5: 7<18 ✓ dp=4 (best) | **4** | Chain: [2, 5, 7, 18] |

**dp = [1, 1, 1, 2, 2, 3, 4, 4]**
**Answer: max = 4** ✓

---

**dp trace for nums = [0,1,0,3,2,3]:**

| i | nums[i] | dp[i] | Best chain ending here |
|---|---------|-------|----------------------|
| 0 | 0       | 1     | [0] |
| 1 | 1       | 2     | [0, 1] |
| 2 | 0       | 1     | [0] (can't extend — 0 is not > 0 or 1) |
| 3 | 3       | 3     | [0, 1, 3] |
| 4 | 2       | 2     | [0, 2] |
| 5 | 3       | 4     | [0, 1, 2, 3] ← j=4: 2<3, dp[4]+1=3. But j=1: 1<3, dp=3. j=3: 3==3 ✗. Actually j=4 gives dp[4]+1=3, j=1 gives dp[1]+1=3. Hmm... |

Let me recompute index 5 carefully:
```
i=5, nums[5]=3:
  j=0: nums[0]=0 < 3 ✓ → dp[0]+1 = 2
  j=1: nums[1]=1 < 3 ✓ → dp[1]+1 = 3
  j=2: nums[2]=0 < 3 ✓ → dp[2]+1 = 2
  j=3: nums[3]=3 == 3 ✗ (not strictly less)
  j=4: nums[4]=2 < 3 ✓ → dp[4]+1 = 3
dp[5] = max(1, 2, 3, 2, 3) = 3
```

Hmm, dp[5] = 3, not 4. But the expected answer is 4.

Wait — let me recompute dp[4]:
```
i=4, nums[4]=2:
  j=0: 0 < 2 ✓ → dp[0]+1 = 2
  j=1: 1 < 2 ✓ → dp[1]+1 = 3
  j=2: 0 < 2 ✓ → dp[2]+1 = 2
  j=3: 3 > 2 ✗
dp[4] = 3
```

Now dp[5]:
```
  j=4: dp[4]+1 = 3+1 = 4 ✓
dp[5] = 4
```

**dp = [1, 2, 1, 3, 3, 4]**
**Answer: max = 4** ✓ (LIS = [0, 1, 2, 3])

---

**dp trace for nums = [7,7,7,7,7]:**

Every comparison: `nums[j] == nums[i]` (7 == 7). Not strictly less.
No chain extends. Every dp[i] = 1.

**Answer: 1** ✓

---

## 🔍 Printing the Actual LIS

To recover the subsequence, track which `j` gave the best `dp[i]`:

```cpp
vector<int> parent(n, -1);

// Inside the inner loop:
if (nums[j] < nums[i] && dp[j] + 1 > dp[i]) {
    dp[i] = dp[j] + 1;
    parent[i] = j;
}

// After finding the max dp value at index maxIdx:
vector<int> lis;
for (int i = maxIdx; i != -1; i = parent[i]) {
    lis.push_back(nums[i]);
}
reverse(lis.begin(), lis.end());
```

Follow the parent chain backward from the best ending index.
This is covered in detail in the Print LIS saga.

---

## 🔍 O(N²) vs O(N log N)

This O(N²) approach is the foundation. It's clear, easy to explain,
and supports printing the LIS via parent tracking.

The O(N log N) approach uses binary search (patience sorting)
and is covered in a separate saga (DP-43).

For most problems, O(N²) is sufficient (N ≤ 2500 on LeetCode).
For N up to 10⁵, you need the O(N log N) version.

---

## 🔍 Memo vs Tab Formulation Comparison

| Aspect | Memoization | Tabulation (this) |
|--------|-------------|-------------------|
| State | `(index, prevIndex)` — 2D | `dp[i]` — 1D |
| Meaning | LIS from index onward | LIS ending at index i |
| Answer | `solve(0, -1)` | `max(dp[0..n-1])` |
| Space | O(N²) | O(N) |
| Printing | Harder | Easy (parent array) |

The tabulation formulation is simpler and more space-efficient.
It's the standard way to present LIS.

---

### 🧠 Memory of the Rising Chain (Tabulation) Law

-   **`dp[i]`** = length of LIS ending at index `i`
-   **Initialize:** all `dp[i] = 1` (every element alone is length 1)
-   **Transition:** for each `j < i`, if `nums[j] < nums[i]`: `dp[i] = max(dp[i], dp[j]+1)`
-   **Answer:** `max` over entire dp array (LIS can end anywhere)
-   **Printing:** track `parent[i]` = which `j` gave the best extension
-   **Time:** O(N²). **Space:** O(N).
-   **O(N log N) exists** using binary search — separate saga.

Thus is remembered the saga of **Longest Increasing Subsequence (Tabulation)**,
where each element asked every predecessor:
"Are you smaller than me? How long is your chain?"
and extended the longest valid chain by one --
building the answer not at a single cell
but as the maximum across all ending positions --
the longest rising chain through the array. 📈🔗📋✨
