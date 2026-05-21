## 🧱🔗 _The Balanced Brackets: The Generate Parentheses Saga_

> \_"The Oracle was given a number `n`.
>
> She was commanded:
>
> **'Generate all combinations of `n` pairs
> of well-formed parentheses.'**
>
> For n=3: `"((()))"`, `"(()())"`, `"(())()"`, `"()(())"`, `"()()()"`.
>
> The Oracle saw this as a constrained backtracking problem.
>
> At each position, she could place either `(` or `)`.
> But not freely -- two rules governed her choices:
>
> **Rule 1:** She could place `(` only if she hadn't used all `n` opens yet.
> (open count < n)
>
> **Rule 2:** She could place `)` only if there was an unmatched `(` before it.
> (close count < open count)
>
> These two rules ensured every generated string was valid.
> No invalid string was ever explored.
>
> No `start` index. No `used[]` array.
> Just two counters: `open` and `close`.
> The simplest backtracking of all."\_

---

This is the saga of **Generate Parentheses**.

Given `n` pairs of parentheses:

-   Generate all combinations of well-formed parentheses.

```
Input:  n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]

Input:  n = 1
Output: ["()"]

Input:  n = 2
Output: ["(())","()()"]
```

---

## 🧠 The Oracle's Core Insight -- Two Counters, Two Rules

Track how many `(` and `)` have been placed so far:
-   `open` = number of `(` placed.
-   `close` = number of `)` placed.

**Rule 1 -- Can place `(`:** only if `open < n`.
(Haven't used all n opening brackets yet.)

**Rule 2 -- Can place `)`:** only if `close < open`.
(There's at least one unmatched `(` to close.)

**Base case:** when `open == n && close == n` → string has `2n` characters.
It's complete and valid. Add to result.

```
backtrack(current, open, close):
  If open == n AND close == n: add current. Return.

  If open < n:
    backtrack(current + "(", open + 1, close)

  If close < open:
    backtrack(current + ")", open, close + 1)
```

**Why this always produces valid strings:**
-   Rule 1 ensures we never have more than `n` opens.
-   Rule 2 ensures we never close without a matching open.
-   Together, they guarantee every string is well-formed.

**No pruning needed. No invalid states are ever reached.**

```
Time:  O(4ⁿ / √n) -- the nth Catalan number of valid strings
Space: O(n) -- recursion depth
```

---

### 📜 The Scroll of the Balanced Brackets

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🧱 The Backtracking Ritual

```cpp
void backtrack(int n, int open, int close,
               string& current, vector<string>& result) {
```

The Oracle carried:
-   `n` -- total pairs needed.
-   `open` -- how many `(` placed so far.
-   `close` -- how many `)` placed so far.
-   `current` -- the string being built.
-   `result` -- all valid strings found.

---

### 🎯 Base Case -- String Complete

```cpp
    if (open == n && close == n) {
        result.push_back(current);
        return;
    }
```

Both counters reached `n`. The string has `2n` characters.
It's a valid combination. Record it.

> _"Every opening has found its closing.
> The brackets are balanced.
> This string is complete."_

---

### 🔓 Choice 1 -- Place an Opening Bracket

```cpp
    if (open < n) {
        current.push_back('(');
        backtrack(n, open + 1, close, current, result);
        current.pop_back();
    }
```

If we haven't used all `n` opening brackets → place one.
Recurse with `open + 1`.
Backtrack (remove it) to try the other choice.

> _"There are still openings to give.
> Place one. The door opens.
> Something must close it later."_

---

### 🔒 Choice 2 -- Place a Closing Bracket

```cpp
    if (close < open) {
        current.push_back(')');
        backtrack(n, open, close + 1, current, result);
        current.pop_back();
    }
}
```

If there are unmatched opens (`close < open`) → place a close.
Recurse with `close + 1`.
Backtrack.

> _"There is an open door waiting to be closed.
> Place the closing bracket.
> Balance is restored by one."_

**Why `close < open` and not `close < n`?**

`close < n` would allow placing `)` even when there's no unmatched `(`.
That would create invalid strings like `)(`.
`close < open` ensures every `)` has a matching `(` before it.

---

## 🔮 The Main Function

```cpp
vector<string> generateParenthesis(int n) {
    vector<string> result;
    string current;
    backtrack(n, 0, 0, current, result);
    return result;
}
```

Start with 0 opens and 0 closes. Build from empty string.

---

### 🎺 The Trial of the Balanced Brackets

```cpp
int main() {
    auto r1 = generateParenthesis(3);
    for (auto& s : r1) cout << s << " ";
    cout << endl;
    // expected: ((()))  (()())  (())()  ()(())  ()()()

    auto r2 = generateParenthesis(2);
    for (auto& s : r2) cout << s << " ";
    cout << endl;
    // expected: (())  ()()

    auto r3 = generateParenthesis(1);
    for (auto& s : r3) cout << s << " ";
    cout << endl;
    // expected: ()

    return 0;
}
```

---

**Full recursion trace for n = 2:**

```
backtrack(open=0, close=0, current="")
  open < 2 → place '('. current="(".
    backtrack(open=1, close=0, current="(")
      open < 2 → place '('. current="((".
        backtrack(open=2, close=0, current="((")
          open == 2, can't place '('.
          close < open (0 < 2) → place ')'. current="(()".
            backtrack(open=2, close=1, current="(()")
              open == 2, can't place '('.
              close < open (1 < 2) → place ')'. current="(())".
                backtrack(open=2, close=2, current="(())")
                  → COMPLETE! ADD "(())". ✓
                pop ')'.
            pop ')'.
        pop '('.
      close < open (0 < 1) → place ')'. current="()".
        backtrack(open=1, close=1, current="()")
          open < 2 → place '('. current="()(".
            backtrack(open=2, close=1, current="()(")
              open == 2, can't place '('.
              close < open (1 < 2) → place ')'. current="()()".
                backtrack(open=2, close=2, current="()()")
                  → COMPLETE! ADD "()()". ✓
                pop ')'.
            pop '('.
          close < open? (1 < 1) → NO. Can't place ')'.
        pop ')'.
    pop '('.
```

**Result: ["(())", "()()"]** ✓

2 valid strings for n=2. The Catalan number C(2) = 2.

---

**Trace for n = 3 (showing the tree structure):**

```
""
├── "("
│   ├── "(("
│   │   ├── "((("
│   │   │   └── "((()" → "((())" → "((()))" ✓
│   │   └── "(()" 
│   │       ├── "(()(" → "(()())" ... wait let me simplify
│   │       └── "(())" → "(())()" ✓ ... 
│   └── "()"
│       ├── "()("
│       │   ├── "()((" → "()(())" ✓
│       │   └── "()()" → "()()()" ✓... 
│       └── (close < open? 1<1 NO)
```

5 valid strings for n=3. Catalan number C(3) = 5.

---

## 🔍 Why This Is NOT the Subsets/Permutations Pattern

| Subsets/Permutations              | Generate Parentheses              |
| --------------------------------- | --------------------------------- |
| Choose from an array of elements  | Choose between `(` and `)`        |
| `start` index or `used[]` array  | `open` and `close` counters       |
| Variable-length results           | Fixed length `2n`                 |
| Pruning optional                  | Rules ARE the pruning (no invalid states) |

Generate Parentheses is a **binary decision tree** --
at each position, exactly two options (open or close),
constrained by the two rules.

---

## 🔍 The Catalan Number

The number of valid parentheses strings of `n` pairs = the nth Catalan number:

```
C(n) = (2n)! / ((n+1)! × n!)

C(1) = 1:  ()
C(2) = 2:  (()), ()()
C(3) = 5:  ((())), (()()), (())(), ()(()), ()()()
C(4) = 14
C(5) = 42
```

The growth is approximately `4ⁿ / (n√n)`.

---

## 🔍 Why No Invalid Strings Are Generated

The two rules form a **tight constraint**:
-   `open < n` → at most `n` opens.
-   `close < open` → never more closes than opens at any point.

These are the EXACT conditions for a valid parentheses string.
By enforcing them at every step, we never enter an invalid state.
No backtracking from invalid states is ever needed --
every path in the recursion tree leads to a valid result.

> _"The Oracle never makes a mistake.
> She only places a bracket when the rules allow it.
> Every path she walks leads to a valid string.
> There is no wasted exploration."_

---

### 🧠 Memory of the Balanced Brackets Law

-   **Two counters:** `open` (number of `(` placed), `close` (number of `)` placed)
-   **Place `(`:** if `open < n`
-   **Place `)`:** if `close < open`
-   **Base case:** `open == n && close == n` → string complete, add to result
-   **No invalid states** — rules prevent them entirely
-   **Binary decision tree** — at each position, at most 2 choices
-   **Result count** = Catalan number C(n)
-   **Time:** O(4ⁿ / √n) — Catalan number of valid strings × O(n) to copy
-   **Space:** O(n) — recursion depth = 2n
-   **Edge cases:**
    -   n = 0 → [""]
    -   n = 1 → ["()"]

Thus is remembered the saga of **Generate Parentheses**,
where the Oracle at each position faced two choices --
open a new bracket or close an existing one --
governed by two simple rules:
never open more than `n`,
never close more than you've opened --
and through these constraints alone,
every path led to a valid string,
every valid string was discovered,
and the Catalan number of balanced brackets
was generated without a single wasted step. 🧱🔗✨
