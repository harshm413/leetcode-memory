## ⚡🔢 _The Halving of Power: The Pow(x, n) Saga_

> \_"The Oracle was given a base `x` and an exponent `n`.
>
> She was commanded:
>
> **'Compute x raised to the power n.'**
>
> The naive approach: multiply `x` by itself `n` times.
> O(n) -- too slow for n = 2 billion.
>
> The Oracle used **binary exponentiation** (fast power):
>
> **If n is even:** `x^n = (x^(n/2))^2`
> **If n is odd:** `x^n = x × x^(n-1)`
>
> Each step halves the exponent.
> O(log n) multiplications instead of O(n).
>
> The key insight: squaring halves the problem.
> `x^16 = (x^8)^2 = ((x^4)^2)^2 = ...`
> Only 4 multiplications for n=16 (not 16)."\_

---

This is the saga of **Pow(x, n)**.

Compute `x^n` (x raised to the power n).

```
Input:  x = 2.0, n = 10
Output: 1024.0

Input:  x = 2.1, n = 3
Output: 9.261

Input:  x = 2.0, n = -2
Output: 0.25  (1 / 2^2 = 1/4)
```

---

## 🧠 The Oracle's Core Insight -- Binary Exponentiation

```
x^n:
  If n == 0: return 1.
  If n is even: return (x^(n/2))^2.
  If n is odd: return x × x^(n-1).
```

Each recursive call halves `n` → O(log n) depth.

**Handling negative n:**
`x^(-n) = 1 / x^n`. Convert to positive, compute, invert.

**Edge case:** `n = INT_MIN` → `-INT_MIN` overflows.
Handle by converting to `long long`.

```
Time:  O(log n)
Space: O(log n) recursion stack (or O(1) iterative)
```

---

### 📜 The Scroll of the Halving Power

```cpp
#include <iostream>
using namespace std;
```

---

## ⚡ The Recursive Approach

```cpp
double myPow(double x, int n) {
    long long power = n;

    if (power < 0) {
        x = 1 / x;
        power = -power;
    }

    return fastPow(x, power);
}
```

If `n` is negative: invert `x` and make `power` positive.
Use `long long` to handle `n = INT_MIN` safely.

---

```cpp
double fastPow(double x, long long n) {
    if (n == 0) return 1.0;

    double half = fastPow(x, n / 2);

    if (n % 2 == 0) {
        return half * half;
    } else {
        return half * half * x;
    }
}
```

**Base case:** `x^0 = 1`.

**Even:** compute `x^(n/2)` once, square it. `(x^4)^2 = x^8`.

**Odd:** compute `x^(n/2)` (integer division), square it, multiply by `x` once.
`x^7 = (x^3)^2 × x`.

> _"Compute the half-power ONCE.
> Square it to get the full power.
> If odd -- multiply by x one extra time.
> Each step halves the problem."_

---

## 🔁 The Iterative Approach

```cpp
double myPow(double x, int n) {
    long long power = n;
    if (power < 0) {
        x = 1 / x;
        power = -power;
    }

    double result = 1.0;

    while (power > 0) {
        if (power % 2 == 1) {
            result *= x;
        }
        x *= x;
        power /= 2;
    }
    return result;
}
```

**Idea:** process the binary representation of `n` bit by bit.

Each iteration:
-   If current bit is 1 → multiply result by current `x`.
-   Square `x` (preparing for the next bit).
-   Halve `power`.

Same O(log n) time. O(1) space (no recursion).

---

### 🎺 The Trial

```cpp
int main() {
    cout << myPow(2.0, 10) << endl;   // expected: 1024
    cout << myPow(2.1, 3) << endl;    // expected: 9.261
    cout << myPow(2.0, -2) << endl;   // expected: 0.25
    return 0;
}
```

---

**Trace for x=2, n=10:**

```
Recursive:
  fastPow(2, 10): half = fastPow(2, 5).
    fastPow(2, 5): half = fastPow(2, 2).
      fastPow(2, 2): half = fastPow(2, 1).
        fastPow(2, 1): half = fastPow(2, 0) = 1.
          return 1 * 1 * 2 = 2.  (odd)
        return 2 * 2 = 4.  (even)
      return 4 * 4 * 2 = 32.  (odd)
    return 32 * 32 = 1024.  (even)

Answer: 1024. ✓ (4 recursive calls for n=10)
```

**Iterative trace for x=2, n=10 (binary: 1010):**

| power | power%2 | result    | x after squaring |
|-------|---------|-----------|------------------|
| 10    | 0       | 1         | 4                |
| 5     | 1       | 1×4=4     | 16               |
| 2     | 0       | 4         | 256              |
| 1     | 1       | 4×256=1024| 65536            |
| 0     | done    | **1024**  |                  |

---

## 🔍 Why Not Just Multiply n Times?

| Naive O(n)                        | Binary Exponentiation O(log n)    |
| --------------------------------- | --------------------------------- |
| n = 10 → 10 multiplications      | n = 10 → 4 multiplications        |
| n = 10⁹ → 10⁹ operations (TLE)  | n = 10⁹ → 30 operations ✓        |

For large `n`, the difference is astronomical.

---

### 🧠 Memory of the Halving Power Law

-   **Even:** `x^n = (x^(n/2))^2` — compute half once, square it
-   **Odd:** `x^n = (x^(n/2))^2 × x` — square + one extra multiply
-   **Base:** `x^0 = 1`
-   **Negative n:** invert x, make n positive
-   **Use `long long`** for n to handle INT_MIN
-   **Iterative:** process bits of n, square x each step, multiply result when bit=1
-   **Time:** O(log n). **Space:** O(log n) recursive, O(1) iterative.

Thus is remembered the saga of **Pow(x, n)**,
where the Oracle did not multiply x by itself n times
but instead halved the exponent at every step --
squaring the half-result to get the full result --
reducing billions of multiplications
to a mere handful of halvings. ⚡🔢✨
