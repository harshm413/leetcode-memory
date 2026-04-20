## 🔄🔢 _The Mirror of Digits: The Reverse Integer Saga_

> \_"In the Kingdom of Integers,
> every number wore its digits proudly --
> ones, tens, hundreds, thousands --
> each in its rightful place.
>
> The Oracle was commanded:
>
> **'Reverse the digits of this integer.
> 123 becomes 321.
> -456 becomes -654.
> But beware -- if the reversed number
> overflows a 32-bit signed integer,
> return 0.'**
>
> The Oracle devised a simple ritual:
>
> **Extract the last digit with `x % 10`.
> Build the reversed number with `result * 10 + digit`.
> Chop the last digit with `x / 10`.
> Repeat until x is 0.**
>
> But the overflow trap was deadly.
> Before multiplying by 10 and adding,
> the Oracle had to check:
> would this operation exceed INT_MAX or fall below INT_MIN?
>
> One careless multiplication
> and the number would wrap around silently --
> producing a wrong answer without warning.
>
> Extract. Check. Build. Chop. Repeat."\_

---

This is the saga of **Reverse Integer**.

Given a 32-bit signed integer `x`:

-   Reverse its digits.
-   If the result overflows 32-bit signed range, return `0`.

```
Input:  123
Output: 321

Input:  -123
Output: -321

Input:  120
Output: 21

Input:  1534236469
Output: 0  (reversed = 9646324351, overflows)
```

---

## 🧠 The Oracle's Core Insight -- Extract, Build, Check Overflow

The digit extraction loop:

```
While x != 0:
  digit  = x % 10      (last digit, preserves sign)
  x      = x / 10      (chop last digit)
  result = result * 10 + digit   (build reversed number)
```

`%` and `/` in C++ preserve sign for negative numbers:
`-123 % 10 = -3`, `-123 / 10 = -12`.
So the algorithm works identically for positives and negatives.

**The overflow check** must happen BEFORE `result * 10 + digit`:

```
If result > INT_MAX / 10                          → overflow
If result == INT_MAX / 10 AND digit > INT_MAX % 10 → overflow
If result < INT_MIN / 10                          → underflow
If result == INT_MIN / 10 AND digit < INT_MIN % 10 → underflow
```

If any of these trigger → return 0.

```
Time:  O(log₁₀(x)) -- number of digits
Space: O(1)
```

---

### 📜 The Scroll of the Digit Mirror

```cpp
#include <iostream>
#include <climits>
using namespace std;
```

---

## ⚔️ The Oracle's Digit-Reversal Ritual

```cpp
int reverse(int x) {
    int result = 0;
```

The reversed number, built digit by digit.

---

## 🔁 Extract and Build

```cpp
    while (x != 0) {
        int digit = x % 10;
```

The last digit was extracted.

For positive `x`: `123 % 10 = 3`.
For negative `x`: `-123 % 10 = -3`.

The sign is preserved automatically.

---

### ⚠️ Overflow Check -- Before It's Too Late

```cpp
        if (result > INT_MAX / 10 || 
            (result == INT_MAX / 10 && digit > 7)) {
            return 0;
        }
        if (result < INT_MIN / 10 || 
            (result == INT_MIN / 10 && digit < -8)) {
            return 0;
        }
```

**Why 7 and -8?**

```
INT_MAX = 2147483647  →  last digit is 7
INT_MIN = -2147483648 →  last digit is -8
```

If `result` is already `INT_MAX / 10 = 214748364`,
the next digit can be at most 7 before overflow.

If `result` is already `INT_MIN / 10 = -214748364`,
the next digit can be at most -8 before underflow.

This check happens BEFORE the multiplication --
preventing the overflow from ever occurring.

> _"Check before you leap.
> Once the multiplication overflows,
> the damage is done and undetectable."_

---

### 🔨 Build the Reversed Number

```cpp
        result = result * 10 + digit;
        x /= 10;
    }
    return result;
}
```

`result * 10` shifted all existing digits left by one decimal place.
`+ digit` placed the extracted digit at the ones position.

`x /= 10` chopped the last digit from the original number.

The loop continued until `x` became 0 -- all digits extracted.

---

### 🎺 The Trial of the Digit Mirror

```cpp
int main() {
    cout << reverse(123)        << endl; // expected: 321
    cout << reverse(-123)       << endl; // expected: -321
    cout << reverse(120)        << endl; // expected: 21
    cout << reverse(0)          << endl; // expected: 0
    cout << reverse(1534236469) << endl; // expected: 0 (overflow)
    return 0;
}
```

---

**Full trace for x = 123:**

| Iteration | x   | digit = x%10 | result (before) | result = result*10 + digit | x = x/10 |
| --------- | --- | ------------- | --------------- | -------------------------- | -------- |
| 1         | 123 | 3             | 0               | 0*10 + 3 = 3              | 12       |
| 2         | 12  | 2             | 3               | 3*10 + 2 = 32             | 1        |
| 3         | 1   | 1             | 32              | 32*10 + 1 = 321           | 0        |

x = 0 → exit loop.

**Answer: 321** ✓

---

**Full trace for x = -123:**

| Iteration | x    | digit = x%10 | result | result*10 + digit | x = x/10 |
| --------- | ---- | ------------- | ------ | ----------------- | -------- |
| 1         | -123 | -3            | 0      | 0 + (-3) = -3    | -12      |
| 2         | -12  | -2            | -3     | -30 + (-2) = -32 | -1       |
| 3         | -1   | -1            | -32    | -320 + (-1) = -321| 0       |

**Answer: -321** ✓

The sign was preserved naturally through `%` and `/`.
No special handling for negatives needed.

---

**Full trace for x = 120:**

| Iteration | x   | digit | result | result*10 + digit | x/10 |
| --------- | --- | ----- | ------ | ----------------- | ---- |
| 1         | 120 | 0     | 0      | 0                 | 12   |
| 2         | 12  | 2     | 0      | 2                 | 1    |
| 3         | 1   | 1     | 2      | 21                | 0    |

**Answer: 21** ✓

The trailing zero in `120` became a leading zero in `021` = `21`.
Leading zeros vanish naturally in integer representation.

---

**Trace for x = 1534236469:**

Reversed digits would give `9646324351`.
`INT_MAX = 2147483647`.
`9646324351 > 2147483647` → **overflow**.

The overflow check catches this before it happens.

**Answer: 0** ✓

---

**Trace for x = 0:**

Loop never runs (x is already 0). **Answer: 0** ✓

---

## 🔍 Why Not Just Use `long long`?

```cpp
long long result = 0;
// ... build result ...
if (result > INT_MAX || result < INT_MIN) return 0;
return (int)result;
```

This works and is simpler. But the problem is designed
to test overflow detection within 32-bit constraints.

The `long long` approach sidesteps the challenge.
The check-before-multiply approach demonstrates
understanding of how overflow works at the hardware level.

In interviews, showing the overflow check is the expected answer.

---

## 🔄 Connection to Other Problems

| Problem         | Similarity                              |
| --------------- | --------------------------------------- |
| Reverse Integer | Extract last digit, build reversed      |
| Palindrome Number | Extract digits, compare with reverse  |
| Add Binary      | Process digits right to left with carry |
| String to Integer (atoi) | Build number digit by digit with overflow check |

The `result = result * 10 + digit` pattern with overflow checking
is a fundamental building block for digit-processing problems.

---

### 🧠 Memory of the Digit Mirror Law

-   **Extract last digit:** `digit = x % 10` (preserves sign)
-   **Chop last digit:** `x = x / 10`
-   **Build reversed:** `result = result * 10 + digit`
-   **Overflow check BEFORE building:**
    -   `result > INT_MAX/10` or `(result == INT_MAX/10 && digit > 7)` → return 0
    -   `result < INT_MIN/10` or `(result == INT_MIN/10 && digit < -8)` → return 0
-   **Negatives handled automatically** -- `%` and `/` preserve sign in C++
-   **Trailing zeros** become leading zeros → vanish naturally
-   **Time:** O(log₁₀(x)) -- number of digits
-   **Space:** O(1)
-   **Edge cases:**
    -   x = 0 → 0
    -   Trailing zeros (120 → 21)
    -   Negative numbers (-123 → -321)
    -   Overflow (1534236469 → 0)
    -   INT_MIN (-2147483648) → reversed overflows → 0

Thus is remembered the saga of **Reverse Integer**,
where the Oracle extracted digits one by one
from the tail of the number --
building the mirror image digit by digit,
always checking for overflow before each step --
until the original was consumed
and the reversed number stood complete,
or the overflow trap was sprung
and zero was returned in silence. 🔄🔢✨
