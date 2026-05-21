## 🔢📋 _The Cipher of Letters (Tabulation): The Decode Ways Saga_

> \_"The Oracle had solved Decode Ways with memoization.
> Now she built the answer bottom-up.
>
> **`dp[i]` = number of ways to decode `s[0..i-1]`
> (the first `i` characters).**
>
> Start at the empty string (1 way -- do nothing).
> Fill position by position.
> At each position, check:
> can the last ONE digit form a letter?
> Can the last TWO digits form a letter?
>
> Pure iteration. No recursion."\_

---

This is the saga of **Decode Ways (Tabulation)**.

Same problem:
-   `'A' = 1, 'B' = 2, ..., 'Z' = 26`
-   Return the number of ways to decode the digit string.

```
Input:  s = "12"   →  Output: 2
Input:  s = "226"  →  Output: 3
Input:  s = "06"   →  Output: 0
```

---

## 🧠 The Bottom-Up Formulation

```
dp[0] = 1                (empty prefix -- one way: do nothing)
dp[1] = 1 if s[0] != '0', else 0

dp[i] = 0
  If s[i-1] != '0':           (last one digit is valid)
    dp[i] += dp[i-1]
  If s[i-2..i-1] forms 10-26: (last two digits are valid)
    dp[i] += dp[i-2]
```

`dp[i]` depends on `dp[i-1]` (took one digit) and `dp[i-2]` (took two digits).
Same as Fibonacci/Climbing Stairs -- but with validity checks.

---

### 📜 The Scroll of the Bottom-Up Cipher

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Array

```cpp
int numDecodings(string s) {
    int n = s.size();
    if (n == 0 || s[0] == '0') return 0;
```

Empty string or starts with '0' → no valid decoding.

---

```cpp
    vector<int> dp(n + 1, 0);
    dp[0] = 1;
    dp[1] = 1;
```

`dp[i]` = ways to decode the first `i` characters.

`dp[0] = 1` -- the empty prefix has one decoding (do nothing).
This is the same "empty path = 1" base case as memoization.

`dp[1] = 1` -- the first character is valid (we checked `s[0] != '0'` above).

---

### 🔁 Fill Position by Position

```cpp
    for (int i = 2; i <= n; i++) {
```

For each position from 2 to n (the first `i` characters):

---

### 🔢 Check Last One Digit

```cpp
        int oneDigit = s[i - 1] - '0';
        if (oneDigit >= 1 && oneDigit <= 9) {
            dp[i] += dp[i - 1];
        }
```

The last character `s[i-1]` as a single digit.
Valid if 1-9 (maps to A-I). NOT valid if 0.

If valid: the number of ways = whatever ways existed
for the prefix WITHOUT this last character (`dp[i-1]`).

> _"If the last digit alone is a valid letter,
> then every decoding of the prefix
> can be extended by this one letter."_

---

### 🔢 Check Last Two Digits

```cpp
        int twoDigit = (s[i - 2] - '0') * 10 + (s[i - 1] - '0');
        if (twoDigit >= 10 && twoDigit <= 26) {
            dp[i] += dp[i - 2];
        }
    }
```

The last two characters `s[i-2..i-1]` as a two-digit number.
Valid if 10-26 (maps to J-Z).

If valid: the number of ways = whatever ways existed
for the prefix WITHOUT these last two characters (`dp[i-2]`).

> _"If the last two digits together form a valid letter,
> then every decoding of the prefix (minus two)
> can be extended by this two-digit letter."_

---

### 📤 The Answer

```cpp
    return dp[n];
}
```

`dp[n]` = number of ways to decode the entire string.

---

### 🎺 The Trial of the Bottom-Up Cipher

```cpp
int main() {
    cout << numDecodings("12") << endl;   // expected: 2
    cout << numDecodings("226") << endl;  // expected: 3
    cout << numDecodings("06") << endl;   // expected: 0
    cout << numDecodings("10") << endl;   // expected: 1
    cout << numDecodings("2101") << endl; // expected: 1
    return 0;
}
```

---

**Full table trace for s = "226":**

```
s = "226", n = 3.
dp = [1, 1, 0, 0]  (dp[0]=1, dp[1]=1, rest to fill)
```

| i | s[i-1] | oneDigit | valid? | twoDigit (s[i-2..i-1]) | valid? | dp[i]         |
|---|--------|----------|--------|------------------------|--------|---------------|
| 2 | '2'    | 2        | ✓      | "22" = 22              | ✓ (10-26) | dp[1] + dp[0] = 1+1 = **2** |
| 3 | '6'    | 6        | ✓      | "26" = 26              | ✓ (10-26) | dp[2] + dp[1] = 2+1 = **3** |

**dp = [1, 1, 2, 3]**

**Answer: dp[3] = 3** ✓

---

**Full table trace for s = "12":**

| i | oneDigit | valid? | twoDigit | valid? | dp[i]         |
|---|----------|--------|----------|--------|---------------|
| 2 | 2        | ✓      | 12       | ✓      | dp[1]+dp[0] = 1+1 = **2** |

**Answer: dp[2] = 2** ✓

---

**Trace for s = "10":**

```
s[0] = '1', not '0' → proceed. dp = [1, 1, 0]
```

| i | oneDigit | valid? | twoDigit | valid? | dp[i]         |
|---|----------|--------|----------|--------|---------------|
| 2 | 0        | ✗ (0!) | 10       | ✓      | 0 + dp[0] = **1** |

**Answer: dp[2] = 1** ✓

The single digit '0' is invalid → dp[i-1] NOT added.
But "10" = 10 is valid → dp[i-2] added. Only one way: "10" → J.

---

**Trace for s = "27":**

| i | oneDigit | valid? | twoDigit | valid? | dp[i]         |
|---|----------|--------|----------|--------|---------------|
| 2 | 7        | ✓      | 27       | ✗ (>26)| dp[1] + 0 = **1** |

**Answer: 1** ✓ — only "2"+"7" = BG. Cannot use "27" (no letter for 27).

---

**Trace for s = "2101":**

```
dp = [1, 1, 0, 0, 0]
```

| i | oneDigit | valid? | twoDigit | valid? | dp[i]         |
|---|----------|--------|----------|--------|---------------|
| 2 | 1        | ✓      | 21       | ✓      | 1 + 1 = **2** |
| 3 | 0        | ✗      | 10       | ✓      | 0 + dp[1] = **1** |
| 4 | 1        | ✓      | 01       | ✗ (<10)| dp[3] + 0 = **1** |

**Answer: 1** ✓ — only "21"+"01"? No! "01" is invalid.
The only valid decoding: "2"+"10"+"1" = BJA. One way.

---

## 🔧 Space Optimization -- O(1) Space

Each `dp[i]` depends only on `dp[i-1]` and `dp[i-2]`.
Two variables suffice.

```cpp
int numDecodings(string s) {
    int n = s.size();
    if (n == 0 || s[0] == '0') return 0;

    int prev2 = 1;  // dp[i-2], starts as dp[0]
    int prev1 = 1;  // dp[i-1], starts as dp[1]

    for (int i = 2; i <= n; i++) {
        int curr = 0;

        int oneDigit = s[i - 1] - '0';
        if (oneDigit >= 1) curr += prev1;

        int twoDigit = (s[i - 2] - '0') * 10 + (s[i - 1] - '0');
        if (twoDigit >= 10 && twoDigit <= 26) curr += prev2;

        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

Same logic. Two variables instead of an array.
Slide the window forward each iteration.

```
Time:  O(n)
Space: O(1)
```

---

## 🔍 The Zero Trap -- All Cases

| String | oneDigit valid? | twoDigit valid? | dp[i]          |
| ------ | --------------- | --------------- | -------------- |
| "11"   | ✓ (1)          | ✓ (11)          | dp[i-1]+dp[i-2]|
| "10"   | ✗ (0)          | ✓ (10)          | dp[i-2] only   |
| "30"   | ✗ (0)          | ✗ (30>26)       | 0 (dead end!)  |
| "01"   | caught at start | --              | return 0       |

When `dp[i] = 0`, it means no valid decoding exists
for the first `i` characters. The string is undecodable.

---

## 🔍 Memoization vs Tabulation

| Memoization (Top-Down)            | Tabulation (Bottom-Up)            |
| --------------------------------- | --------------------------------- |
| `solve(i)` = ways for `s[i..n-1]`| `dp[i]` = ways for `s[0..i-1]`   |
| Starts from position 0, goes right| Fills from position 1 to n        |
| Recursion + cache                 | Pure iteration                    |
| O(n) time, O(n) space            | O(n) time, O(1) space possible   |

Both give the same answer. Tabulation allows O(1) space optimization.

---

### 🧠 Memory of the Cipher (Tabulation) Law

-   **State:** `dp[i]` = ways to decode first `i` characters
-   **Base:** `dp[0] = 1` (empty), `dp[1] = 1` (if s[0] ≠ '0')
-   **Transition:**
    -   Last 1 digit valid (1-9): `dp[i] += dp[i-1]`
    -   Last 2 digits valid (10-26): `dp[i] += dp[i-2]`
-   **Answer:** `dp[n]`
-   **Space optimization:** two variables (`prev1`, `prev2`)
-   **Zero handling:** oneDigit=0 → don't add dp[i-1]. twoDigit<10 or >26 → don't add dp[i-2].
-   **If dp[i] = 0** → dead end, string is undecodable from here
-   **This is Climbing Stairs** with validity constraints on each step
-   **Time:** O(n). **Space:** O(n) or O(1) optimized.

Thus is remembered the saga of **Decode Ways (Tabulation)**,
where the Oracle built the answer from left to right --
at each position checking if the last one digit
or the last two digits formed a valid letter --
adding the ways from one step back or two steps back --
until the final position revealed
how many ways the entire cipher could be read. 🔢📋✨
