## 🏠🔄📋 _The Circular Street (Tabulation): The House Robber II Saga_

> \_"The Oracle had broken the circle into two straight streets
> and solved each with memoization.
>
> Now she solved each street bottom-up --
> no recursion, pure iteration,
> with a clear dp array that anyone could trace on a whiteboard.
>
> **A helper function solves House Robber I
> on any subrange of the array.**
>
> Call it twice. Take the max. Done."\_

---

This is the saga of **House Robber II (Tabulation)**.

Same circular problem:
-   Houses in a circle. First and last are adjacent.
-   Cannot rob two adjacent houses.
-   Maximize total gold.

```
Input:  nums = [2, 3, 2]      →  Output: 3
Input:  nums = [1, 2, 3, 1]   →  Output: 4
Input:  nums = [200, 3, 140, 20, 10]  →  Output: 340
```

---

## 🧠 The Strategy -- Same Split, Bottom-Up Execution

The circle-breaking insight is identical to the memo version:

```
Case 1: solve houses 0 to n-2 (may include first, exclude last)
Case 2: solve houses 1 to n-1 (exclude first, may include last)
Answer = max(Case 1, Case 2)
```

Each case is solved with a dp array -- iterative, clear, traceable.

---

### 📜 The Scroll of the Bottom-Up Circular Street

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏠 The Linear Helper -- House Robber I on a Subrange

This solves the rob-or-skip dilemma on houses `[start, end]`
using a dp array.

```cpp
int robLinear(vector<int>& nums, int start, int end) {
```

"What's the most gold from houses `start` through `end`?"

---

### One house? Rob it.

```cpp
    if (start == end) return nums[start];
```

---

### Build the dp array for this subrange

```cpp
    int len = end - start + 1;
    vector<int> dp(len);
```

`dp[j]` = best gold from houses `start` to `start + j`.
The array is sized for the subrange, not the full input.

Why a local array sized `len` instead of `n`?
Because each scenario works on a different subrange.
A clean local array avoids confusion about which indices mean what.

---

### The first house of this street -- no choice

```cpp
    dp[0] = nums[start];
```

One house on this street so far. Rob it.

---

### The second house -- the first real choice

```cpp
    dp[1] = max(nums[start], nums[start + 1]);
```

Two houses. They're neighbors. Can't rob both.
Pick the richer one.

Why `max` and not just `nums[start+1]`?
Because `dp[1]` means "best from the first two houses."
That could be EITHER house -- whichever has more gold.

---

### Every house after -- the recurring dilemma

```cpp
    for (int j = 2; j < len; j++) {
        int houseIdx = start + j;
```

`j` is the position within the subrange.
`houseIdx` is the actual index in the original `nums` array.

---

### Rob or skip

```cpp
        int pick = nums[houseIdx] + dp[j - 2];
```

**"I rob this house."**
I take its gold. House `j-1` is forbidden.
The best I had from two houses back in this subrange is `dp[j-2]`.

---

```cpp
        int skip = dp[j - 1];
```

**"I skip this house."**
The best from one house back carries forward.

---

### Choose the richer future

```cpp
        dp[j] = max(pick, skip);
    }
    return dp[len - 1];
}
```

The last cell holds the answer for this subrange.

---

## 🔄 The Circular Wrapper

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
```

---

### Trivial circles

```cpp
    if (n == 1) return nums[0];
    if (n == 2) return max(nums[0], nums[1]);
```

One house → rob it. Two houses → rob the richer.

---

### Scenario 1 -- "I might rob house 0"

```cpp
    int case1 = robLinear(nums, 0, n - 2);
```

The thief's street runs from house 0 to house n-2.
House n-1 doesn't exist in this world.
He's free to rob house 0 without worrying about the circular neighbor.

---

### Scenario 2 -- "I skip house 0"

```cpp
    int case2 = robLinear(nums, 1, n - 1);
```

The thief's street runs from house 1 to house n-1.
House 0 doesn't exist in this world.
He's free to rob house n-1 without worrying about the circular neighbor.

---

### The richer universe wins

```cpp
    return max(case1, case2);
}
```

---

### 🎺 The Trial of the Bottom-Up Circular Street

```cpp
int main() {
    vector<int> n1 = {2, 3, 2};
    cout << rob(n1) << endl; // expected: 3

    vector<int> n2 = {1, 2, 3, 1};
    cout << rob(n2) << endl; // expected: 4

    vector<int> n3 = {200, 3, 140, 20, 10};
    cout << rob(n3) << endl; // expected: 340

    return 0;
}
```

---

**Full trace for nums = [1, 2, 3, 1]:**

**Case 1: robLinear(0, 2) → subrange [1, 2, 3]**

| j | houseIdx | nums[houseIdx] | pick           | skip    | dp[j]  |
|---|----------|----------------|----------------|---------|--------|
| 0 | 0        | 1              | --             | --      | 1      |
| 1 | 1        | 2              | --             | --      | max(1,2) = 2 |
| 2 | 2        | 3              | 3 + dp[0] = 3+1 = 4 | dp[1] = 2 | 4 |

Case 1 = 4.

**Case 2: robLinear(1, 3) → subrange [2, 3, 1]**

| j | houseIdx | nums[houseIdx] | pick           | skip    | dp[j]  |
|---|----------|----------------|----------------|---------|--------|
| 0 | 1        | 2              | --             | --      | 2      |
| 1 | 2        | 3              | --             | --      | max(2,3) = 3 |
| 2 | 3        | 1              | 1 + dp[0] = 1+2 = 3 | dp[1] = 3 | 3 |

Case 2 = 3.

**Answer: max(4, 3) = 4** ✓

---

**Full trace for nums = [200, 3, 140, 20, 10]:**

**Case 1: robLinear(0, 3) → subrange [200, 3, 140, 20]**

| j | houseIdx | nums | pick          | skip | dp[j] |
|---|----------|------|---------------|------|-------|
| 0 | 0        | 200  | --            | --   | 200   |
| 1 | 1        | 3    | --            | --   | max(200,3) = 200 |
| 2 | 2        | 140  | 140+200 = 340 | 200  | 340   |
| 3 | 3        | 20   | 20+200 = 220  | 340  | 340   |

Case 1 = 340.

**Case 2: robLinear(1, 4) → subrange [3, 140, 20, 10]**

| j | houseIdx | nums | pick          | skip | dp[j] |
|---|----------|------|---------------|------|-------|
| 0 | 1        | 3    | --            | --   | 3     |
| 1 | 2        | 140  | --            | --   | max(3,140) = 140 |
| 2 | 3        | 20   | 20+3 = 23    | 140  | 140   |
| 3 | 4        | 10   | 10+140 = 150 | 140  | 150   |

Case 2 = 150.

**Answer: max(340, 150) = 340** ✓

---

**Trace for nums = [2, 3, 2]:**

**Case 1: robLinear(0, 1) → [2, 3]**

dp[0] = 2. dp[1] = max(2, 3) = 3. Case 1 = 3.

**Case 2: robLinear(1, 2) → [3, 2]**

dp[0] = 3. dp[1] = max(3, 2) = 3. Case 2 = 3.

**Answer: max(3, 3) = 3** ✓

---

## 🔧 Space Optimization -- Mention as Follow-Up

Once the dp array solution is clear and correct,
you can optimize space by noticing that each `dp[j]`
only depends on `dp[j-1]` and `dp[j-2]`.

Replace the entire array with two variables:

```cpp
int robLinear(vector<int>& nums, int start, int end) {
    if (start == end) return nums[start];

    int prev2 = 0;
    int prev1 = nums[start];

    for (int i = start + 1; i <= end; i++) {
        int pick = nums[i] + prev2;
        int skip = prev1;
        int curr = max(pick, skip);
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

`prev2` = best from two back. `prev1` = best from one back.
Slide forward each iteration.

This reduces space from O(N) to O(1).
Present the dp array first. Mention this as an optimization.

---

## 🔍 The Complete Mental Model

```
1. "It's a circle. First and last are neighbors."
2. "I can't rob both. So either first is in my plan or last is."
3. "Two straight streets: [0..n-2] and [1..n-1]."
4. "Each street is House Robber I with a dp array."
5. "Solve both. Take the max."
6. "Follow-up: optimize each to O(1) space with two variables."
```

Present steps 1-5 first. Step 6 is the cherry on top.

---

### 🧠 Memory of the Circular Street (Tabulation) Law

-   **Circle = two straight streets**
    -   `robLinear(0, n-2)` -- may include first, exclude last
    -   `robLinear(1, n-1)` -- exclude first, may include last
-   **Answer = max of both**
-   **robLinear** uses a dp array: `dp[j] = max(nums[j] + dp[j-2], dp[j-1])`
-   **Space optimization (follow-up):** replace dp array with two variables → O(1)
-   **Present dp array first** -- clear, traceable, easy to explain
-   **Edge cases:** n=1 → rob it. n=2 → rob the richer.
-   **Time:** O(N). **Space:** O(N), optimizable to O(1).

Thus is remembered the saga of **House Robber II (Tabulation)**,
where the Oracle broke the circular street
into two straight streets --
solving each with a clear dp array,
traceable on any whiteboard --
and choosing the richer universe,
because in a circle,
the first and last can never both be robbed. 🏠🔄📋✨
