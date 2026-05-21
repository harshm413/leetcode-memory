## 🔢🧠 _The Cipher of Letters (Memoization): The Decode Ways Saga_

> \_"In the Kingdom of Encoded Messages,
> letters were mapped to numbers:
>
> `'A' → 1, 'B' → 2, ..., 'Z' → 26`
>
> A message was encoded into a string of digits.
> But decoding was ambiguous --
> the string `"12"` could be decoded as:
> -   `"1" + "2"` → `"AB"`
> -   `"12"` → `"L"`
>
> The Oracle was commanded:
>
> **'How many ways can this digit string be decoded?'**
>
> The Oracle stood at each position and asked:
>
> **'Can I take ONE digit here? Can I take TWO digits here?'**
>
> Taking one digit: if it's `1-9` (not `0`), it maps to a letter.
> Taking two digits: if they form `10-26`, it maps to a letter.
>
> At each position, she had at most two choices.
> The total ways = sum of ways from both choices.
>
> This was Climbing Stairs in disguise --
> but with VALIDITY constraints on each step."\_

---

This is the saga of **Decode Ways (Memoization)**.

Given a string `s` containing only digits:

-   `'A' = 1, 'B' = 2, ..., 'Z' = 26`
-   Return the **number of ways** to decode it.
-   Leading zeros are invalid (`"06"` is not valid, but `"6"` is).

```
Input:  s = "12"
Output: 2   ("AB" or "L")

Input:  s = "226"
Output: 3   ("BZ", "VF", "BBF")

Input:  s = "06"
Output: 0   (leading zero — no valid decoding)
```

---

## 🧠 The Recurrence

`solve(i)` = number of ways to decode `s[i..n-1]` (from position `i` to the end).

At position `i`, two choices:

**Choice 1 -- Take ONE digit `s[i]`:**
Valid if `s[i] != '0'` (digits 1-9 map to A-I).
If valid: `solve(i + 1)` ways for the rest.

**Choice 2 -- Take TWO digits `s[i..i+1]`:**
Valid if `i + 1 < n` AND the two-digit number is between 10 and 26.
If valid: `solve(i + 2)` ways for the rest.

```
solve(i):
  If i == n: return 1  (reached the end — one valid decoding found)
  If s[i] == '0': return 0  (leading zero — no valid decoding from here)

  ways = solve(i + 1)  (take one digit)

  If i+1 < n AND s[i..i+1] forms 10-26:
    ways += solve(i + 2)  (take two digits)

  return ways
```

**Base case:** `i == n` → we've consumed the entire string.
That's one complete valid decoding. Return 1.

**Dead end:** `s[i] == '0'` → a digit string cannot start with 0.
No letter maps to 0. Return 0.

---

### 📜 The Scroll of the Cipher

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
```

---

## 🔢 Setting Up the Cache

```cpp
int numDecodings(string s) {
    int n = s.size();
    vector<int> memo(n, -1);
```

`memo[i]` = number of ways to decode `s[i..n-1]`.
`-1` = not yet computed.

---

```cpp
    return solve(0, s, memo);
}
```

Start from position 0 -- how many ways to decode the entire string?

---

## 🔮 The Recursive Thinking

```cpp
int solve(int i, string& s, vector<int>& memo) {
```

"Starting from position `i`, how many ways can the rest be decoded?"

---

### 🎯 Base Case -- Reached the End

```cpp
    if (i == (int)s.size()) return 1;
```

The entire string has been consumed by valid decodings.
This path represents one complete valid decoding. Return 1.

> _"I have consumed every digit.
> The path I took was valid.
> Count this as one way."_

---

### 🛑 Dead End -- Leading Zero

```cpp
    if (s[i] == '0') return 0;
```

No letter maps to `0`. A substring starting with `0` is invalid.
This path is a dead end. Return 0.

> _"Zero leads nowhere.
> No letter begins with nothing.
> This path dies here."_

---

### 📖 Cache Check

```cpp
    if (memo[i] != -1) return memo[i];
```

Already solved this position → return cached answer.

---

### 🔢 Choice 1 -- Take One Digit

```cpp
    int ways = solve(i + 1, s, memo);
```

Take `s[i]` as a single digit (1-9 → A-I).
We already checked `s[i] != '0'`, so this is always valid here.
Count the ways to decode the rest from position `i + 1`.

---

### 🔢 Choice 2 -- Take Two Digits (If Valid)

```cpp
    if (i + 1 < (int)s.size()) {
        int twoDigit = (s[i] - '0') * 10 + (s[i + 1] - '0');
        if (twoDigit >= 10 && twoDigit <= 26) {
            ways += solve(i + 2, s, memo);
        }
    }
```

Take `s[i]` and `s[i+1]` together as a two-digit number.

**Validity checks:**
-   `i + 1 < n` -- must have a second digit available.
-   `twoDigit >= 10 && twoDigit <= 26` -- must map to a letter (J-Z).

Why `>= 10`? Because `"06"` is NOT a valid two-digit encoding.
Only `10-26` are valid two-digit codes.

If valid: add the ways to decode from position `i + 2`.

> _"If the next two digits form a number between 10 and 26,
> I can treat them as one letter.
> That opens a second path -- skip two positions ahead."_

---

### 📝 Cache and Return

```cpp
    memo[i] = ways;
    return ways;
}
```

---

### 🎺 The Trial of the Cipher

```cpp
int main() {
    cout << numDecodings("12") << endl;   // expected: 2
    cout << numDecodings("226") << endl;  // expected: 3
    cout << numDecodings("06") << endl;   // expected: 0
    cout << numDecodings("10") << endl;   // expected: 1
    cout << numDecodings("27") << endl;   // expected: 1
    return 0;
}
```

---

**Full trace for s = "226":**

```
solve(0): s[0]='2', not '0'.
  Choice 1: take "2" → solve(1)
    solve(1): s[1]='2', not '0'.
      Choice 1: take "2" → solve(2)
        solve(2): s[2]='6', not '0'.
          Choice 1: take "6" → solve(3)
            solve(3): i == n → return 1.
          Choice 2: i+1 = 3 >= n. No second digit. Skip.
        memo[2] = 1. Return 1.
      Choice 2: "22" = 22, valid (10-26) → solve(3)
        solve(3): i == n → return 1.
    memo[1] = 1 + 1 = 2. Return 2.
  Choice 2: "22" = 22, valid → solve(2)
    solve(2): memo[2] = 1 (cached!). Return 1.
memo[0] = 2 + 1 = 3. Return 3.
```

**Answer: 3** ✓

The 3 decodings: "2"+"2"+"6" = BBF, "2"+"26" = BZ, "22"+"6" = VF.

---

**Trace for s = "12":**

```
solve(0): take "1" → solve(1) = 1. take "12"=12 valid → solve(2) = 1.
memo[0] = 1 + 1 = 2.
```

**Answer: 2** ✓ — "AB" or "L".

---

**Trace for s = "06":**

```
solve(0): s[0] = '0' → return 0.
```

**Answer: 0** ✓ — leading zero, no valid decoding.

---

**Trace for s = "10":**

```
solve(0): s[0]='1', not '0'.
  Choice 1: take "1" → solve(1)
    solve(1): s[1]='0' → return 0. (dead end!)
  Choice 2: "10" = 10, valid → solve(2)
    solve(2): i == n → return 1.
memo[0] = 0 + 1 = 1.
```

**Answer: 1** ✓ — only "10" → J. Cannot split as "1"+"0" because "0" is invalid.

---

## 🔍 Why This Is Climbing Stairs with Constraints

| Climbing Stairs                   | Decode Ways                       |
| --------------------------------- | --------------------------------- |
| At each step: take 1 or 2 steps  | At each position: take 1 or 2 digits |
| Always valid                      | Validity constraints (no leading 0, ≤ 26) |
| `ways(n) = ways(n-1) + ways(n-2)`| `ways(i) = ways(i+1) + ways(i+2)` (if valid) |
| No dead ends                      | `'0'` creates dead ends           |

The structure is identical. The only difference:
Decode Ways has conditions on when each choice is allowed.

---

## 🔍 The Zero Problem

`'0'` is the tricky part of this problem:

-   `"0"` alone → invalid. No letter maps to 0.
-   `"10"` → valid as two digits (J). Invalid as "1"+"0".
-   `"20"` → valid as two digits (T). Invalid as "2"+"0".
-   `"30"` → INVALID even as two digits (30 > 26). Dead end.
-   `"100"` → "10"+"0" invalid. "1"+"00" invalid. → 0 ways.

The `s[i] == '0'` check handles all these cases:
when we land on a `0`, that path dies immediately.
The only way `0` survives is as the second digit of `10-20`.

---

### 🧠 Memory of the Cipher (Memoization) Law

-   **State:** `solve(i)` = ways to decode `s[i..n-1]`
-   **Base:** `i == n` → 1 (valid decoding complete)
-   **Dead end:** `s[i] == '0'` → 0 (no letter for zero)
-   **Choice 1:** take one digit (always valid if not '0') → `solve(i+1)`
-   **Choice 2:** take two digits (valid if 10-26) → `solve(i+2)`
-   **Total:** `ways = choice1 + choice2`
-   **This is Climbing Stairs with validity constraints**
-   **The zero trap:** `'0'` kills single-digit paths; only survives as part of 10-20
-   **Time:** O(n) -- each position solved once
-   **Space:** O(n) -- memo array + recursion stack

Thus is remembered the saga of **Decode Ways (Memoization)**,
where the Oracle stood at each digit and faced two futures --
take one digit and step forward,
or take two digits and leap ahead --
but only if the digits formed a valid letter,
and never if a zero stood alone --
counting every valid path through the cipher
until the total number of decodings was known. 🔢🧠✨
