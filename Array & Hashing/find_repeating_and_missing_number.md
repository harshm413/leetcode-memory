## 🔁❓ _The Imposter and the Ghost: The Find Repeating and Missing Number Saga_

> \_"The Oracle was given an array of N numbers.
> The array should contain every number from 1 to N exactly once.
>
> But something went wrong:
> **One number appears TWICE (the repeating number).**
> **One number is MISSING (the missing number).**
>
> She was commanded:
>
> **'Find both — the repeating and the missing.'**
>
> The Oracle knew three approaches:
>
> **Hashing:** count frequencies. O(N) time, O(N) space.
> **Math:** use sum and sum-of-squares equations. O(N) time, O(1) space.
> **XOR:** split into two groups using bit manipulation. O(N) time, O(1) space.
>
> The math approach was the most elegant —
> two equations, two unknowns, solved in one pass."\_

---

This is the saga of **Find the Repeating and Missing Number**.

Given an array of N integers containing numbers from 1 to N:
-   One number appears twice (repeating = R).
-   One number is missing (missing = M).
-   Find both R and M.

```
Input:  arr = [3, 1, 2, 5, 3]   (N=5)
Output: Repeating = 3, Missing = 4

Input:  arr = [1, 1]             (N=2)
Output: Repeating = 1, Missing = 2

Input:  arr = [2, 2]             (N=2)
Output: Repeating = 2, Missing = 1
```

---

## 💡 Approach 1 — Hashing (Quick Mention)

```cpp
vector<int> findRepeatingMissing(vector<int>& arr) {
    int n = arr.size();
    vector<int> count(n + 1, 0);
    for (int x : arr) count[x]++;
    int repeating = -1, missing = -1;
    for (int i = 1; i <= n; i++) {
        if (count[i] == 2) repeating = i;
        if (count[i] == 0) missing = i;
    }
    return {repeating, missing};
}
```

Count frequencies. The one with count 2 = repeating.
The one with count 0 = missing.
O(N) time, O(N) space. Simple but uses extra space.

---

## 💡 Approach 2 — Math (Optimal)

### 🧠 The Two Equations

The array has numbers 1 to N, but R appears twice and M is missing.

**Equation 1 — Sum difference:**

```
actualSum = sum of array elements
expectedSum = N × (N+1) / 2

actualSum - expectedSum = R - M     ... (i)
```

Why? The array has R once extra (+R) and M once less (-M)
compared to the perfect 1..N sequence.

**Equation 2 — Sum of squares difference:**

```
actualSqSum = sum of squares of array elements
expectedSqSum = N × (N+1) × (2N+1) / 6

actualSqSum - expectedSqSum = R² - M²  = (R+M)(R-M)    ... (ii)
```

From (i): `R - M = diff1`.
From (ii): `(R + M) × diff1 = diff2` → `R + M = diff2 / diff1`.

Now we have `R - M` and `R + M`. Solve:

```
R = (diff1 + diff2/diff1) / 2
M = R - diff1
```

---

### 📜 The Scroll of the Imposter and the Ghost

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

### Compute the differences

```cpp
vector<int> findRepeatingMissing(vector<int>& arr) {
    long long n = arr.size();
```

Use `long long` to prevent overflow. Sum of squares of N numbers
can be very large.

---

```cpp
    long long expectedSum = n * (n + 1) / 2;
    long long expectedSqSum = n * (n + 1) * (2 * n + 1) / 6;
```

The perfect sums for 1 to N.

---

```cpp
    long long actualSum = 0, actualSqSum = 0;
    for (int x : arr) {
        actualSum += x;
        actualSqSum += (long long)x * x;
    }
```

Compute actual sum and sum of squares from the array.
Cast to `long long` before squaring to prevent overflow.

---

### Derive R - M and R + M

```cpp
    long long diff1 = actualSum - expectedSum;
```

`diff1 = R - M`. Could be positive or negative.

---

```cpp
    long long diff2 = actualSqSum - expectedSqSum;
```

`diff2 = R² - M² = (R+M)(R-M) = (R+M) × diff1`.

---

```cpp
    long long sumRM = diff2 / diff1;
```

`R + M = diff2 / diff1`. This division is always exact (no remainder).

---

### Solve for R and M

```cpp
    int repeating = (diff1 + sumRM) / 2;
    int missing = (sumRM - diff1) / 2;
```

From `R - M = diff1` and `R + M = sumRM`:
-   `R = (diff1 + sumRM) / 2`
-   `M = (sumRM - diff1) / 2`

Standard "sum and difference" system of equations.

> _"Two equations. Two unknowns.
> The sum tells us the difference between R and M.
> The sum of squares tells us their sum.
> Together — both are revealed."_

---

```cpp
    return {repeating, missing};
}
```

---

### 🎺 The Trial of the Imposter and the Ghost

```cpp
int main() {
    vector<int> a1 = {3, 1, 2, 5, 3};
    auto r1 = findRepeatingMissing(a1);
    cout << "Repeating: " << r1[0] << ", Missing: " << r1[1] << endl;
    // expected: Repeating: 3, Missing: 4

    vector<int> a2 = {1, 1};
    auto r2 = findRepeatingMissing(a2);
    cout << "Repeating: " << r2[0] << ", Missing: " << r2[1] << endl;
    // expected: Repeating: 1, Missing: 2

    vector<int> a3 = {2, 2};
    auto r3 = findRepeatingMissing(a3);
    cout << "Repeating: " << r3[0] << ", Missing: " << r3[1] << endl;
    // expected: Repeating: 2, Missing: 1

    return 0;
}
```

---

**Trace for arr = [3, 1, 2, 5, 3], N = 5:**

```
expectedSum = 5×6/2 = 15
expectedSqSum = 5×6×11/6 = 55

actualSum = 3+1+2+5+3 = 14
actualSqSum = 9+1+4+25+9 = 48

diff1 = 14 - 15 = -1        → R - M = -1
diff2 = 48 - 55 = -7        → R² - M² = -7

sumRM = -7 / -1 = 7         → R + M = 7

R = (-1 + 7) / 2 = 3
M = (7 - (-1)) / 2 = 4
```

**Repeating: 3, Missing: 4** ✓

---

**Trace for arr = [1, 1], N = 2:**

```
expectedSum = 3, actualSum = 2.  diff1 = -1.  R - M = -1.
expectedSqSum = 5, actualSqSum = 2.  diff2 = -3.
sumRM = -3 / -1 = 3.  R + M = 3.

R = (-1 + 3) / 2 = 1.  M = (3 + 1) / 2 = 2.
```

**Repeating: 1, Missing: 2** ✓

---

**Trace for arr = [2, 2], N = 2:**

```
expectedSum = 3, actualSum = 4.  diff1 = 1.  R - M = 1.
expectedSqSum = 5, actualSqSum = 8.  diff2 = 3.
sumRM = 3 / 1 = 3.  R + M = 3.

R = (1 + 3) / 2 = 2.  M = (3 - 1) / 2 = 1.
```

**Repeating: 2, Missing: 1** ✓

---

## 🔍 The Three Approaches Compared

| Approach | Time | Space | Concept |
|----------|------|-------|---------|
| Hashing | O(N) | O(N) | Count frequencies |
| **Math** | **O(N)** | **O(1)** | Sum + sum of squares |
| **XOR** | **O(N)** | **O(1)** | XOR all + split by differentiating bit |

Math is the cleanest. XOR avoids overflow entirely (no large sums).

---

## 💡 Approach 3 — XOR (No Overflow Risk)

### 🧠 The Insight

XOR all array elements with all numbers 1 to N:

```cpp
    int xorAll = 0;
    for (int x : arr) xorAll ^= x;
    for (int i = 1; i <= n; i++) xorAll ^= i;
```

Every number that appears once in both (the correct numbers)
cancels out (A ^ A = 0). What remains is `R ^ M`.

`xorAll = R ^ M`. We know R and M differ — so `xorAll ≠ 0`.

### Find a differentiating bit

```cpp
    int diffBit = xorAll & (-xorAll);
```

`xorAll & (-xorAll)` isolates the LOWEST set bit.
This bit is 1 in R and 0 in M (or vice versa).
It's a bit where R and M DIFFER.

### Split into two groups

```cpp
    int group0 = 0, group1 = 0;
    for (int x : arr) {
        if (x & diffBit) group1 ^= x;
        else group0 ^= x;
    }
    for (int i = 1; i <= n; i++) {
        if (i & diffBit) group1 ^= i;
        else group0 ^= i;
    }
```

Split ALL numbers (array + 1..N) into two groups
based on whether the differentiating bit is set.

R and M land in DIFFERENT groups (they differ at this bit).
All other numbers appear in both the array and 1..N → cancel out.

After XOR within each group:
`group0` = one of {R, M}. `group1` = the other.

### Identify which is repeating, which is missing

```cpp
    int repeating, missing;
    bool found = false;
    for (int x : arr) {
        if (x == group0) { found = true; break; }
    }
    if (found) { repeating = group0; missing = group1; }
    else { repeating = group1; missing = group0; }
```

Check which of the two values actually appears in the array.
The one that appears = repeating. The other = missing.

**Time:** O(N). **Space:** O(1). **No overflow** — XOR stays within int range.

The XOR approach is the same technique used in "Find Two Numbers
Appearing Once" (Single Number III). Same split-by-bit trick.

---

## 🔍 Why Sum Alone Isn't Enough

Sum gives us `R - M = diff1`. That's ONE equation with TWO unknowns.
Infinite solutions: R=5,M=4 and R=6,M=5 both give diff1=1.

We need a SECOND equation. Sum of squares provides it.
`R² - M²` combined with `R - M` gives us `R + M`.
Two equations → unique solution.

---

## 🔍 Overflow Considerations

For N up to 10⁵:
-   Sum ≈ N²/2 ≈ 5×10⁹ → exceeds `int` range. Use `long long`.
-   Sum of squares ≈ N³/3 ≈ 3.3×10¹⁴ → fits in `long long`.

Always use `long long` for the sums. Cast before squaring.

---

### 🧠 Memory of the Imposter and the Ghost Law

-   **Two equations:** `R - M = actualSum - expectedSum`, `R + M = (actualSqSum - expectedSqSum) / (R - M)`
-   **Solve:** `R = (diff + sum) / 2`, `M = (sum - diff) / 2`
-   **Use `long long`** — sums and squares overflow int
-   **Expected formulas:** sum = `N(N+1)/2`, sqSum = `N(N+1)(2N+1)/6`
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Find Repeating and Missing Number**,
where the Oracle used two mathematical equations —
the sum revealed the difference between imposter and ghost,
the sum of squares revealed their sum —
and from difference and sum,
both the repeating imposter and the missing ghost
were unmasked in a single pass. 🔁❓✨
