## 🕳️⚖️ _The Weight of Every Valley: The Sum of Subarray Minimums Saga_

> \_"In the Kingdom of Subarrays,
> every contiguous stretch of numbers
> had a smallest member -- its valley, its minimum.
>
> The King commanded:
>
> **'Sum the minimums of ALL possible subarrays.'**
>
> The naive approach would enumerate every subarray
> and find its minimum. O(N²) at best.
>
> The Oracle flipped the question:
>
> **Instead of asking 'what is the minimum of each subarray?',
> ask 'for each element, how many subarrays
> is it the minimum of?'**
>
> If element `nums[i]` is the minimum of `C` subarrays,
> its total contribution to the answer is `nums[i] × C`.
>
> To find `C`, the Oracle needed two boundaries:
>
> **PSE[i]** -- Previous Smaller Element (left boundary).
> **NSE[i]** -- Next Smaller or Equal Element (right boundary).
>
> The number of subarrays where `nums[i]` is the minimum:
> `C = (i - PSE[i]) × (NSE[i] - i)`
>
> Both boundaries found with monotonic stacks in O(N).
> The answer computed in one final pass."\_

---

This is the saga of **Sum of Subarray Minimums**.

Given an integer array `arr`:

-   For every subarray, find its minimum.
-   Return the **sum of all minimums**, modulo `10⁹ + 7`.

```
Input:  [3, 1, 2, 4]
Output: 17
Subarrays: [3]=3, [1]=1, [2]=2, [4]=4,
           [3,1]=1, [1,2]=1, [2,4]=2,
           [3,1,2]=1, [1,2,4]=1,
           [3,1,2,4]=1
Sum = 3+1+2+4+1+1+2+1+1+1 = 17

Input:  [11, 81, 94, 43, 3]
Output: 444
```

---

## 🧠 The Oracle's Core Insight -- Contribution of Each Element

Instead of iterating over subarrays, iterate over elements.

For each `arr[i]`, count how many subarrays have `arr[i]` as their minimum.

`arr[i]` is the minimum of a subarray `[L..R]` if and only if:
-   `arr[i]` is the smallest element in `[L..R]`.
-   The subarray includes index `i`.

This means `L` can range from `PSE[i] + 1` to `i`,
and `R` can range from `i` to `NSE[i] - 1`.

```
leftChoices  = i - PSE[i]       (number of valid left boundaries)
rightChoices = NSE[i] - i       (number of valid right boundaries)
count        = leftChoices × rightChoices
contribution = arr[i] × count
```

Sum all contributions = the answer.

---

## 🔍 Why PSE and NSE? -- The Boundaries of Dominance

**PSE[i]** = index of the nearest element to the LEFT
that is **strictly smaller** than `arr[i]`.
If none → PSE[i] = -1.

Everything between PSE[i]+1 and i has value >= arr[i],
so arr[i] can be the minimum of subarrays starting anywhere in that range.

**NSE[i]** = index of the nearest element to the RIGHT
that is **smaller or equal** to `arr[i]`.
If none → NSE[i] = n.

Everything between i and NSE[i]-1 has value >= arr[i],
so arr[i] can be the minimum of subarrays ending anywhere in that range.

The asymmetry (strict left `>=`, non-strict right `>`)
prevents double-counting when duplicates exist.

```
Time:  O(N) -- two stack passes + one summation pass
Space: O(N)
```

---

### 📜 The Scroll of the Valley Weights

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⬅️ Pass One -- Previous Smaller Element (PSE)

```cpp
int sumSubarrayMins(vector<int>& arr) {
    int n = arr.size();
    const int MOD = 1e9 + 7;
    vector<int> pse(n), nse(n);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] >= arr[i]) {
            st.pop();
        }
        pse[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }
```

Walk left to right with a monotonic increasing stack.

Pop while the top's value is `>=` current (strict smaller survives).
The surviving top is the nearest strictly smaller element to the left.
If stack empty → no smaller element exists, PSE = -1.

> _"Pop everything that is not strictly smaller.
> Whatever survives is the left wall
> that blocks this element's dominance from extending further."_

---

## ➡️ Pass Two -- Next Smaller or Equal Element (NSE)

```cpp
    while (!st.empty()) st.pop();

    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && arr[st.top()] > arr[i]) {
            st.pop();
        }
        nse[i] = st.empty() ? n : st.top();
        st.push(i);
    }
```

Walk right to left with a monotonic increasing stack.

Pop while the top's value is `>` current (smaller **or equal** survives).
The surviving top is the nearest smaller-or-equal element to the right.
If stack empty → no such element, NSE = n.

**Why `>=` on the left but `>` on the right?**

Consider duplicates like `[3, 1, 1, 2]`.
Both `1`s at indices 1 and 2 are minimums of some subarrays.

Without asymmetry, the subarray `[1, 1]` would have its minimum
counted by BOTH indices -- double-counted.

With asymmetry:
-   Index 1: PSE=-1, NSE=2 (the second `1` blocks it on the right).
-   Index 2: PSE=-1 (the first `1` is equal, not strictly smaller, so it's popped), NSE=4.

Wait -- actually PSE for index 2: we pop index 1 because `arr[1]=1 >= arr[2]=1`.
So PSE[2] = -1? No -- after popping index 1, the stack might have something else.

The key: strict on one side, non-strict on the other ensures
each subarray's minimum is attributed to exactly one index.

---

## 📐 Pass Three -- Compute Contributions

```cpp
    long long sum = 0;
    for (int i = 0; i < n; i++) {
        long long left = i - pse[i];
        long long right = nse[i] - i;
        sum = (sum + (long long)arr[i] % MOD * (left % MOD) % MOD * (right % MOD) % MOD) % MOD;
    }
    return (int)sum;
}
```

For each element:
-   `left` = number of valid starting positions = `i - PSE[i]`.
-   `right` = number of valid ending positions = `NSE[i] - i`.
-   `count = left × right` = total subarrays where this element is the minimum.
-   `contribution = arr[i] × count`.

Sum all contributions modulo `10⁹ + 7`.

Use `long long` throughout to prevent overflow.

---

### 🎺 The Trial of the Valley Weights

```cpp
int main() {
    vector<int> arr1 = {3, 1, 2, 4};
    cout << sumSubarrayMins(arr1) << endl; // expected: 17

    vector<int> arr2 = {11, 81, 94, 43, 3};
    cout << sumSubarrayMins(arr2) << endl; // expected: 444

    return 0;
}
```

---

**Full trace for `[3, 1, 2, 4]`:**

**PSE (Previous Smaller Element):**

| i | arr[i] | Stack before | Pops          | PSE[i] | Stack after |
|---|--------|-------------|---------------|--------|-------------|
| 0 | 3      | []          | --            | -1     | [0]         |
| 1 | 1      | [0]         | pop 0 (3>=1)  | -1     | [1]         |
| 2 | 2      | [1]         | --            | 1      | [1, 2]      |
| 3 | 4      | [1, 2]      | --            | 2      | [1, 2, 3]   |

PSE = [-1, -1, 1, 2]

**NSE (Next Smaller or Equal):**

| i | arr[i] | Stack before | Pops          | NSE[i] | Stack after |
|---|--------|-------------|---------------|--------|-------------|
| 3 | 4      | []          | --            | 4      | [3]         |
| 2 | 2      | [3]         | pop 3 (4>2)   | 4      | [2]         |
| 1 | 1      | [2]         | pop 2 (2>1)   | 4      | [1]         |
| 0 | 3      | [1]         | --            | 1      | [1, 0]      |

NSE = [1, 4, 4, 4]

**Contribution table:**

| i | arr[i] | PSE | NSE | left=i-PSE | right=NSE-i | count | contribution |
|---|--------|-----|-----|------------|-------------|-------|-------------|
| 0 | 3      | -1  | 1   | 1          | 1           | 1     | 3           |
| 1 | 1      | -1  | 4   | 2          | 3           | 6     | 6           |
| 2 | 2      | 1   | 4   | 1          | 2           | 2     | 4           |
| 3 | 4      | 2   | 4   | 1          | 1           | 1     | 4           |

**Sum = 3 + 6 + 4 + 4 = 17** ✓

Element `1` (index 1) is the minimum of 6 subarrays:
[3,1], [1], [1,2], [1,2,4], [3,1,2], [3,1,2,4].
Contribution = 1 × 6 = 6.

---

**Trace for `[11, 81, 94, 43, 3]`:**

**PSE:** [-1, 0, 1, 0, -1]
**NSE:** [4, 3, 3, 4, 5]

| i | arr[i] | left | right | count | contribution |
|---|--------|------|-------|-------|-------------|
| 0 | 11     | 1    | 4     | 4     | 44          |
| 1 | 81     | 1    | 2     | 2     | 162         |
| 2 | 94     | 1    | 1     | 1     | 94          |
| 3 | 43     | 3    | 1     | 3     | 129         |
| 4 | 3      | 5    | 1     | 5     | 15          |

**Sum = 44 + 162 + 94 + 129 + 15 = 444** ✓

---

**Trace for `[1, 1, 1]` (all duplicates):**

**PSE** (pop on `>=`): [-1, -1, -1]
**NSE** (pop on `>`): [1, 2, 3]

| i | left | right | count | contribution |
|---|------|-------|-------|-------------|
| 0 | 1    | 1     | 1     | 1           |
| 1 | 2    | 1     | 2     | 2           |
| 2 | 3    | 1     | 3     | 3           |

Sum = 1 + 2 + 3 = **6** ✓

Subarrays: [1]=1, [1]=1, [1]=1, [1,1]=1, [1,1]=1, [1,1,1]=1 → sum=6.
No double-counting thanks to the asymmetric comparisons.

---

## 🔍 The Asymmetry Rule -- Preventing Double Counts

| Side  | PSE (left)         | NSE (right)           |
|-------|--------------------|-----------------------|
| Pop   | `>=` (strict <)    | `>` (allows <=)       |
| Keeps | Strictly smaller   | Smaller or equal      |

This means for equal elements, the **rightmost** one "owns"
the subarrays that span across the duplicates.
The leftmost one's right boundary is blocked by the equal neighbor.

You could also flip it (strict right, non-strict left) --
as long as one side is strict and the other isn't.

---

### 🧠 Memory of the Valley Weight Law

-   **Flip the question:** instead of "min of each subarray",
    ask "how many subarrays is each element the min of?"
-   **PSE[i]:** nearest strictly smaller to the left (pop on `>=`)
-   **NSE[i]:** nearest smaller-or-equal to the right (pop on `>`)
-   **Contribution:** `arr[i] × (i - PSE[i]) × (NSE[i] - i)`
-   **Asymmetric comparisons** prevent double-counting duplicates
-   **Modulo** `10⁹ + 7` -- use `long long` for intermediate products
-   **Time:** O(N) -- two stack passes + one summation
-   **Space:** O(N) -- PSE/NSE arrays + stack
-   **Edge cases:**
    -   Single element → answer = that element
    -   All equal → each element's right boundary is the next index
    -   Strictly increasing → each element is min of subarrays starting at it
    -   Strictly decreasing → first element dominates most subarrays

Thus is remembered the saga of **Sum of Subarray Minimums**,
where the Oracle did not enumerate every subarray
but instead asked each element how many valleys it ruled --
finding the left and right walls of its dominance
with two monotonic stack passes --
multiplying its value by the count of subarrays it governed --
and summing every valley's weight
into one grand total. 🕳️⚖️✨
