## 📱🔤 _The Dial of Letters: The Letter Combinations of a Phone Number Saga_

> \_"On an old telephone keypad,
> each digit mapped to a set of letters:
>
> `2 → abc, 3 → def, 4 → ghi, 5 → jkl,
>  6 → mno, 7 → pqrs, 8 → tuv, 9 → wxyz`
>
> The Oracle was given a string of digits.
>
> She was commanded:
>
> **'Return all possible letter combinations
> that the number could represent.'**
>
> For `"23"`:
> digit `2` → {a, b, c}
> digit `3` → {d, e, f}
> Combinations: ad, ae, af, bd, be, bf, cd, ce, cf.
>
> The Oracle saw this as the simplest backtracking:
>
> **At each digit position, try every letter that digit maps to.
> Move to the next digit. Repeat.
> When all digits are processed -- one combination is complete.**
>
> No pruning. No constraints. No duplicates.
> Just a mapping and a simple recursion."\_

---

This is the saga of **Letter Combinations of a Phone Number**.

Given a string `digits` (containing digits 2-9):

-   Return all possible letter combinations.
-   The answer can be in any order.

```
Input:  digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]

Input:  digits = ""
Output: []

Input:  digits = "2"
Output: ["a","b","c"]
```

---

## 🧠 The Oracle's Core Insight -- Map + Simple Recursion

Each digit maps to 3-4 letters. At each position in the digit string,
try every letter for that digit. Recurse to the next position.

```
backtrack(index, current):
  If index == digits.size(): add current to result. Return.

  letters = mapping[digits[index]]
  For each letter in letters:
    current.push(letter)
    backtrack(index + 1, current)
    current.pop()
```

No `start` index (each position has its own independent set of choices).
No `used[]` array (different positions, different letter pools).
No pruning (every combination is valid).

This is the **Cartesian product** of the letter sets.

```
Time:  O(4ⁿ × n) where n = digits.length (at most 4 letters per digit)
Space: O(n) -- recursion depth
```

---

### 📜 The Scroll of the Telephone Dial

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 📱 The Digit-to-Letter Mapping

```cpp
vector<string> letterCombinations(string digits) {
    if (digits.empty()) return {};

    vector<string> mapping = {
        "",     // 0
        "",     // 1
        "abc",  // 2
        "def",  // 3
        "ghi",  // 4
        "jkl",  // 5
        "mno",  // 6
        "pqrs", // 7
        "tuv",  // 8
        "wxyz"  // 9
    };
```

Index 0 and 1 are empty (no letters on those keys).
Index 2-9 hold the corresponding letters.

---

```cpp
    vector<string> result;
    string current;
    backtrack(digits, 0, mapping, current, result);
    return result;
}
```

Start from digit index 0 with an empty string.

---

## 🔤 The Backtracking Ritual

```cpp
void backtrack(string& digits, int index, vector<string>& mapping,
               string& current, vector<string>& result) {
```

The Oracle carried:
-   `digits` -- the input digit string.
-   `index` -- which digit we're currently processing.
-   `mapping` -- digit → letters lookup.
-   `current` -- the combination being built.
-   `result` -- all completed combinations.

---

### 🎯 Base Case -- All Digits Processed

```cpp
    if (index == (int)digits.size()) {
        result.push_back(current);
        return;
    }
```

Every digit has contributed one letter.
The combination is complete. Record it.

> _"Every dial has been turned.
> Every digit has chosen its letter.
> This combination is one of the possibilities."_

---

### 🔤 Get the Letters for the Current Digit

```cpp
    string letters = mapping[digits[index] - '0'];
```

Convert the character digit to an integer index.
Look up the corresponding letters.

`digits[index] = '2'` → `'2' - '0' = 2` → `mapping[2] = "abc"`.

---

### 🔁 Try Every Letter for This Digit

```cpp
    for (char letter : letters) {
        current.push_back(letter);
        backtrack(digits, index + 1, current, result);
        current.pop_back();
    }
}
```

For each letter that this digit can produce:
-   Add it to the current combination.
-   Recurse to the next digit (`index + 1`).
-   Backtrack (remove it, try the next letter).

> _"This digit offers three (or four) letters.
> Each one leads to a different future.
> Try them all. Backtrack after each."_

---

### 🎺 The Trial of the Telephone Dial

```cpp
int main() {
    auto r1 = letterCombinations("23");
    for (auto& s : r1) cout << s << " ";
    cout << endl;
    // expected: ad ae af bd be bf cd ce cf

    auto r2 = letterCombinations("7");
    for (auto& s : r2) cout << s << " ";
    cout << endl;
    // expected: p q r s

    auto r3 = letterCombinations("");
    cout << r3.size() << endl;
    // expected: 0

    return 0;
}
```

---

**Full recursion trace for digits = "23":**

```
mapping: '2' → "abc", '3' → "def"

backtrack(index=0, current="")
  digit='2', letters="abc".
  letter='a': current="a".
    backtrack(index=1, current="a")
      digit='3', letters="def".
      letter='d': current="ad". index+1=2==size → ADD "ad". ✓ pop.
      letter='e': current="ae". → ADD "ae". ✓ pop.
      letter='f': current="af". → ADD "af". ✓ pop.
    pop 'a'.
  letter='b': current="b".
    backtrack(index=1, current="b")
      letter='d': → ADD "bd". ✓
      letter='e': → ADD "be". ✓
      letter='f': → ADD "bf". ✓
    pop 'b'.
  letter='c': current="c".
    backtrack(index=1, current="c")
      letter='d': → ADD "cd". ✓
      letter='e': → ADD "ce". ✓
      letter='f': → ADD "cf". ✓
    pop 'c'.
```

**Result: ["ad","ae","af","bd","be","bf","cd","ce","cf"]** ✓

3 letters × 3 letters = 9 combinations.

---

**Trace for digits = "79":**

```
'7' → "pqrs" (4 letters)
'9' → "wxyz" (4 letters)

4 × 4 = 16 combinations.
pw, px, py, pz, qw, qx, qy, qz, rw, rx, ry, rz, sw, sx, sy, sz.
```

---

## 🔍 Why This Is the Simplest Backtracking

| Aspect               | This problem                       |
| -------------------- | ---------------------------------- |
| Choices at each step | Letters for the current digit (3-4)|
| Constraints          | None (every combination is valid)  |
| Pruning              | None needed                        |
| Duplicates           | Impossible (each digit is independent) |
| Result condition     | When all digits are processed      |

No sorting. No skipping. No target. No used array.
Just: for each digit, try each letter, recurse, backtrack.

---

## 🔍 Why This Is a Cartesian Product

The result is the **Cartesian product** of the letter sets:

```
digits = "234"
→ {a,b,c} × {d,e,f} × {g,h,i}
→ 3 × 3 × 3 = 27 combinations
```

Each digit contributes independently.
The total count = product of letter counts for each digit.

---

## 🔍 Iterative Alternative

Build combinations iteratively, digit by digit:

```cpp
vector<string> letterCombinations(string digits) {
    if (digits.empty()) return {};

    vector<string> mapping = {"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
    vector<string> result = {""};

    for (char digit : digits) {
        vector<string> next;
        string letters = mapping[digit - '0'];
        for (auto& existing : result) {
            for (char letter : letters) {
                next.push_back(existing + letter);
            }
        }
        result = next;
    }
    return result;
}
```

Start with `[""]`. For each digit, expand every existing string
by appending each possible letter. Same result, no recursion.

---

### 🧠 Memory of the Telephone Dial Law

-   **Mapping:** digit → letters (`2→abc, 3→def, ..., 9→wxyz`)
-   **At each digit:** try every letter it maps to
-   **Recurse to next digit** (`index + 1`)
-   **Base case:** `index == digits.size()` → combination complete
-   **No pruning, no constraints, no duplicates** -- simplest backtracking
-   **Result = Cartesian product** of letter sets
-   **Iterative alternative:** expand existing strings digit by digit
-   **Time:** O(4ⁿ × n) where n = number of digits
-   **Space:** O(n) recursion depth
-   **Edge cases:**
    -   Empty string → []
    -   Single digit → just its letters
    -   Digits with 4 letters (7, 9) → more combinations

Thus is remembered the saga of **Letter Combinations of a Phone Number**,
where the Oracle turned each digit on the dial --
each digit offering its letters as choices --
trying every letter, advancing to the next digit,
building the combination one character at a time --
until every digit had spoken
and the full Cartesian product of letters
was revealed. 📱🔤✨
