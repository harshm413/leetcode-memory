## ➕🔢 _The March of Binary Addition: The Add Binary Saga_

> \_"Two scrolls arrived at the Oracle's chamber --
> each carrying a binary number as a string of 0s and 1s.
>
> The Oracle was commanded:
>
> **'Add these two binary numbers together.
> Return the sum as a binary string.'**
>
> The Oracle could not convert them to integers --
> the numbers could be astronomically large,
> far beyond what any 32-bit or 64-bit integer could hold.
>
> She had to add them the way a child adds on paper --
> **digit by digit, right to left, carrying the overflow.**
>
> In decimal, a column overflows at 10.
> In binary, a column overflows at 2.
>
> `1 + 1 = 10` in binary (0 with carry 1).
> `1 + 1 + 1 = 11` in binary (1 with carry 1).
>
> Walk both strings from the rightmost digit.
> Add the two digits plus the carry.
> Record `sum % 2` as the current digit.
> Pass `sum / 2` as the carry to the next column.
>
> When both strings are exhausted and carry is 0 -- done.
> Reverse the result (built right to left)."\_

---

This is the saga of **Add Binary**.

Given two binary strings `a` and `b`:

-   Return their **sum as a binary string**.
-   Strings can be very long (no integer conversion).

```
Input:  a = "11", b = "1"
Output: "100"

Input:  a = "1010", b = "1011"
Output: "10101"
```

---

## 🧠 The Oracle's Core Insight -- Binary Long Addition

Binary addition follows the same rules as decimal addition,
but the base is 2 instead of 10.

```
Binary addition table:
  0 + 0 = 0,  carry 0
  0 + 1 = 1,  carry 0
  1 + 0 = 1,  carry 0
  1 + 1 = 0,  carry 1    (like 5+5=10 in decimal)

With carry:
  1 + 1 + 1 = 1, carry 1  (three 1s = 3 = binary 11)
```

At each column:

```
sum   = digitA + digitB + carry
digit = sum % 2     (the bit to record)
carry = sum / 2     (0 or 1, passed to next column)
```

Walk both strings from the end (least significant bit).
If one string is shorter, treat missing digits as 0.
After both strings are exhausted, if carry remains -- add one more digit.

```
Time:  O(max(m, n)) -- walk the longer string
Space: O(max(m, n)) -- the result string
```

---

### 📜 The Scroll of Binary Arithmetic

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle's Binary Addition Ritual

```cpp
string addBinary(string a, string b) {
    string result = "";
    int i = a.size() - 1;
    int j = b.size() - 1;
    int carry = 0;
```

The Oracle prepared:
-   `result` -- the sum, built right to left.
-   `i` and `j` -- pointers at the rightmost digit of each string.
-   `carry` -- the overflow from the previous column, starting at 0.

---

## 🔁 Walk Both Strings Right to Left

```cpp
    while (i >= 0 || j >= 0 || carry) {
```

The loop continued as long as:
-   `a` still had digits (`i >= 0`), OR
-   `b` still had digits (`j >= 0`), OR
-   A carry was still burning.

This single condition handled all three cases --
unequal lengths and a final carry -- gracefully.

---

### 🔢 Gather the Digits

```cpp
        int sum = carry;
        if (i >= 0) sum += a[i--] - '0';
        if (j >= 0) sum += b[j--] - '0';
```

The Oracle gathered three values:
-   The carry from the previous column.
-   The current digit of `a` (or 0 if exhausted).
-   The current digit of `b` (or 0 if exhausted).

`a[i] - '0'` converted the character `'0'` or `'1'` to integer 0 or 1.
The pointer decremented after reading (`i--`, `j--`).

`sum` could be 0, 1, 2, or 3 (at most `1 + 1 + 1`).

---

### ✍️ Record the Digit and Carry

```cpp
        result += (sum % 2) ? '1' : '0';
        carry = sum / 2;
    }
```

`sum % 2` = the bit to record at this position (0 or 1).
`sum / 2` = the carry to pass forward (0 or 1).

```
sum = 0 → digit 0, carry 0
sum = 1 → digit 1, carry 0
sum = 2 → digit 0, carry 1
sum = 3 → digit 1, carry 1
```

The digit was appended to `result`.
Since we're walking right to left but appending left to right,
the result is built in **reverse order**.

---

## 🔄 Reverse the Result

```cpp
    reverse(result.begin(), result.end());
    return result;
}
```

The result was built least-significant-bit first.
Reversing it gave the correct most-significant-bit-first order.

---

### 🎺 The Trial of Binary Addition

```cpp
int main() {
    cout << addBinary("11", "1")      << endl; // expected: 100
    cout << addBinary("1010", "1011")  << endl; // expected: 10101
    cout << addBinary("0", "0")        << endl; // expected: 0
    cout << addBinary("1", "1")        << endl; // expected: 10
    return 0;
}
```

---

**Full trace for a = "11", b = "1":**

```
    1 1
  +   1
  -----
```

| Column | i | j | a[i] | b[j] | carry in | sum | digit | carry out | result so far |
| ------ | - | - | ---- | ---- | -------- | --- | ----- | --------- | ------------- |
| 1      | 1 | 0 | 1    | 1    | 0        | 2   | 0     | 1         | "0"           |
| 2      | 0 | -1| 1    | 0    | 1        | 2   | 0     | 1         | "00"          |
| 3      | -1| -1| 0    | 0    | 1        | 1   | 1     | 0         | "001"         |

Reverse "001" → **"100"** ✓

`11 + 1 = 100` in binary (3 + 1 = 4).

---

**Full trace for a = "1010", b = "1011":**

```
    1 0 1 0
  + 1 0 1 1
  ---------
```

| Column | a[i] | b[j] | carry in | sum | digit | carry out | result so far |
| ------ | ---- | ---- | -------- | --- | ----- | --------- | ------------- |
| 1      | 0    | 1    | 0        | 1   | 1     | 0         | "1"           |
| 2      | 1    | 1    | 0        | 2   | 0     | 1         | "10"          |
| 3      | 0    | 0    | 1        | 1   | 1     | 0         | "101"         |
| 4      | 1    | 1    | 0        | 2   | 0     | 1         | "1010"        |
| 5      | --   | --   | 1        | 1   | 1     | 0         | "10101"       |

Reverse "10101" → **"10101"** ✓ (palindrome, same reversed)

`1010 + 1011 = 10101` in binary (10 + 11 = 21).

---

**Trace for a = "1", b = "1":**

| Column | a[i] | b[j] | carry in | sum | digit | carry out |
| ------ | ---- | ---- | -------- | --- | ----- | --------- |
| 1      | 1    | 1    | 0        | 2   | 0     | 1         |
| 2      | --   | --   | 1        | 1   | 1     | 0         |

Result: "01" → reverse → **"10"** ✓ (1 + 1 = 2)

---

**Trace for a = "0", b = "0":**

| Column | a[i] | b[j] | carry in | sum | digit | carry out |
| ------ | ---- | ---- | -------- | --- | ----- | --------- |
| 1      | 0    | 0    | 0        | 0   | 0     | 0         |

Result: "0" → reverse → **"0"** ✓

---

## 🔍 Why Not Convert to Integer and Add?

```cpp
// WRONG for large inputs:
int num = stoi(a, nullptr, 2) + stoi(b, nullptr, 2);
```

Binary strings can be hundreds of digits long.
A 100-digit binary number is ~2^100 -- far beyond `long long` range.

String-based addition handles arbitrarily large numbers.
No overflow. No precision loss.

---

## 🔄 Connection to Add Two Numbers (Linked List)

This problem is structurally identical to **Add Two Numbers** (linked list):

| Add Binary                | Add Two Numbers             |
| ------------------------- | --------------------------- |
| Binary strings            | Linked lists of digits      |
| Walk right to left        | Walk left to right (reversed)|
| `sum % 2`, `sum / 2`     | `sum % 10`, `sum / 10`     |
| Base 2                    | Base 10                     |
| Reverse result at end     | Build list forward          |

Same algorithm, different base and data structure.

---

### 🧠 Memory of the Binary Addition Law

-   **Walk both strings right to left** with two pointers `i` and `j`
-   **Loop condition:** `i >= 0 || j >= 0 || carry`
-   **At each column:** `sum = carry + digitA + digitB`
    -   `digit = sum % 2` (the bit to record)
    -   `carry = sum / 2` (0 or 1, passed forward)
-   **Exhausted string** → treat missing digits as 0
-   **Remaining carry** → generates one extra digit
-   **Reverse** the result at the end (built in reverse order)
-   **No integer conversion** -- handles arbitrarily large binary numbers
-   **Time:** O(max(m, n))
-   **Space:** O(max(m, n))
-   **Edge cases:**
    -   Both "0" → "0"
    -   One empty → return the other
    -   Unequal lengths → shorter string padded with 0s
    -   All 1s + 1 → carry propagates through entire number

Thus is remembered the saga of **Add Binary**,
where the Oracle added two binary scrolls
digit by digit, right to left --
summing each column with its carry,
recording the remainder,
passing the overflow forward --
until both scrolls were spent
and the carry went dark,
and the sum stood revealed
in a single reversed string. ➕🔢✨
