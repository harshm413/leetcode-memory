## 📚🧮 _The Expression Evaluator: The Basic Calculator I/II/III Saga_

> \_"The Oracle was given mathematical expressions as strings.
>
> Three levels of difficulty:
>
> **Calculator I (LC 224):** `+`, `-`, parentheses `()`. No `*` or `/`.
> **Calculator II (LC 227):** `+`, `-`, `*`, `/`. No parentheses.
> **Calculator III (LC 772):** `+`, `-`, `*`, `/`, AND parentheses.
>
> Each level added one more challenge.
> The Oracle built her solution progressively —
> each level extending the previous one.
>
> The core technique: **a stack that respects operator precedence.**"\_

---

## ═══════════════════════════════════════════
## Part 1 — Basic Calculator II (LC 227)
## `+`, `-`, `*`, `/` — No Parentheses
## ═══════════════════════════════════════════

> _"Start with Calculator II — it's the foundation.
> Calculator I is actually a special case.
> Calculator III combines both."_

```
Input:  "3+2*2"    → Output: 7
Input:  " 3/2 "   → Output: 1
Input:  " 3+5 / 2 " → Output: 5
```

---

### 🧠 The Precedence Insight

`*` and `/` have HIGHER precedence than `+` and `-`.
`3 + 2 * 2` = `3 + 4` = 7, NOT `5 * 2` = 10.

**Strategy:** process `*` and `/` IMMEDIATELY.
Defer `+` and `-` to the end (push to stack, sum later).

**The algorithm:**
-   Track the PREVIOUS operator (`prevOp`). Start with `+`.
-   When we finish reading a number:
    -   If `prevOp` is `+` → push `+num` to stack.
    -   If `prevOp` is `-` → push `-num` to stack.
    -   If `prevOp` is `*` → pop top, multiply, push result.
    -   If `prevOp` is `/` → pop top, divide, push result.
-   After all tokens: sum everything in the stack.

`+` and `-` just push (deferred). `*` and `/` resolve immediately.

---

### 📜 Calculator II Code

```cpp
#include <iostream>
#include <string>
#include <stack>
using namespace std;
```

---

```cpp
int calculateII(string s) {
    stack<int> st;
    int num = 0;
    char prevOp = '+';
```

`num` builds the current number digit by digit.
`prevOp` = the operator BEFORE the current number. Starts as `+`
(the implicit `+` before the first number: `+3+2*2`).

---

```cpp
    for (int i = 0; i <= s.size(); i++) {
        char c = (i < s.size()) ? s[i] : '+';
```

Process one extra iteration at the end (with a dummy `+`)
to flush the last number. Without this, the last number
would never be processed.

---

### Build the number digit by digit

```cpp
        if (isdigit(c)) {
            num = num * 10 + (c - '0');
        }
```

Multi-digit numbers: `num = num * 10 + digit`.

---

### Skip spaces

```cpp
        else if (c == ' ') {
            continue;
        }
```

---

### Hit an operator (or end) — process the previous number

```cpp
        else {
            if (prevOp == '+') st.push(num);
            else if (prevOp == '-') st.push(-num);
            else if (prevOp == '*') { int top = st.top(); st.pop(); st.push(top * num); }
            else if (prevOp == '/') { int top = st.top(); st.pop(); st.push(top / num); }
```

**`+`:** push the number (positive).
**`-`:** push the NEGATIVE of the number.
**`*`:** pop the previous value, multiply with current, push result.
**`/`:** pop the previous value, divide by current, push result.

`*` and `/` resolve immediately — they grab the top of the stack
(the most recent operand) and combine with the current number.
`+` and `-` just push — they'll be summed at the end.

> _"Multiplication and division are impatient — they resolve NOW.
> Addition and subtraction are patient — they wait on the stack."_

---

### Reset for the next number

```cpp
            num = 0;
            prevOp = c;
        }
    }
```

---

### Sum everything in the stack

```cpp
    int result = 0;
    while (!st.empty()) {
        result += st.top();
        st.pop();
    }
    return result;
}
```

The stack contains terms (positive or negative) that need to be summed.
`*` and `/` already resolved. Only `+` and `-` terms remain.

---

### Trace for "3+2*2":

```
i=0: '3' → num=3.
i=1: '+' → prevOp='+', push 3. Stack=[3]. num=0, prevOp='+'.
i=2: '2' → num=2.
i=3: '*' → prevOp='+', push 2. Stack=[3,2]. num=0, prevOp='*'.
i=4: '2' → num=2.
i=5: (end, dummy '+') → prevOp='*', pop 2, push 2*2=4. Stack=[3,4].

Sum: 3+4 = 7. ✓
```

---

## ═══════════════════════════════════════════
## Part 2 — Basic Calculator I (LC 224)
## `+`, `-`, `()` — No `*` or `/`
## ═══════════════════════════════════════════

```
Input:  "1 + 1"        → Output: 2
Input:  " 2-1 + 2 "   → Output: 3
Input:  "(1+(4+5+2)-3)+(6+8)" → Output: 23
```

---

### 🧠 The Parentheses Insight

Without `*`/`/`, there's no precedence issue. Just `+` and `-`.
The only challenge: **parentheses change the sign.**

`-(3+2)` = `-3 + (-2)` = -5. The `-` before `(` flips signs inside.

**Strategy:** track a running `sign` (1 or -1).
When we hit `(`: push the current result and sign onto the stack.
When we hit `)`: pop the saved result and sign, combine.

---

### 📜 Calculator I Code

```cpp
int calculateI(string s) {
    stack<int> st;
    int result = 0;
    int num = 0;
    int sign = 1;
```

`result` = running total. `sign` = current sign (1 or -1).

---

```cpp
    for (int i = 0; i < s.size(); i++) {
        char c = s[i];

        if (isdigit(c)) {
            num = num * 10 + (c - '0');
        }
```

---

### `+` or `-` — apply the previous number, update sign

```cpp
        else if (c == '+' || c == '-') {
            result += sign * num;
            num = 0;
            sign = (c == '+') ? 1 : -1;
        }
```

Add the previous number (with its sign) to result.
Update sign for the NEXT number.

---

### `(` — save current state, start fresh

```cpp
        else if (c == '(') {
            st.push(result);
            st.push(sign);
            result = 0;
            sign = 1;
        }
```

Push the result-so-far and the sign-before-parenthesis.
Start fresh inside the parentheses (result=0, sign=1).

> _"The Oracle saves her progress before entering the parentheses.
> Inside, she starts a new calculation from scratch.
> When she exits, she'll combine with the saved state."_

---

### `)` — combine with saved state

```cpp
        else if (c == ')') {
            result += sign * num;
            num = 0;
            int savedSign = st.top(); st.pop();
            int savedResult = st.top(); st.pop();
            result = savedResult + savedSign * result;
        }
    }
```

Finish the inner calculation. Pop the saved sign and result.
Combine: `savedResult + savedSign * innerResult`.

The `savedSign` handles cases like `-(3+2)` where the sign
before the parenthesis was negative.

---

```cpp
    result += sign * num;
    return result;
}
```

Don't forget the last number (no trailing operator to trigger it).

---

### Trace for "(1+(4+5+2)-3)+(6+8)":

```
'(' → push result=0, sign=1. Fresh start.
'1' → num=1. '+' → result=1, sign=+1.
'(' → push result=1, sign=1. Fresh start.
'4' → num=4. '+' → result=4. '5' → result=9. '+' → '2' → result=11.
')' → inner=11. Pop sign=1, saved=1. result = 1 + 1*11 = 12.
'-' → sign=-1. '3' → num=3.
')' → result = 12 + (-1)*3 = 9. Pop sign=1, saved=0. result = 0 + 1*9 = 9.
'+' → sign=+1.
'(' → push result=9, sign=1. Fresh start.
'6' → result=6. '+' → '8' → result=14.
')' → inner=14. Pop sign=1, saved=9. result = 9 + 1*14 = 23.

Answer: 23. ✓
```

---

## ═══════════════════════════════════════════
## Part 3 — Basic Calculator III (LC 772)
## `+`, `-`, `*`, `/`, AND `()` — Everything
## ═══════════════════════════════════════════

```
Input:  "2*(5+5*2)/3+(6/2+8)"  → Output: 21
Input:  "(2+6* 3+5- (3*14/7+2)*5)+3" → Output: -12
```

---

### 🧠 Combining Both Techniques

Calculator III = Calculator II + parentheses.

**Strategy:** use Calculator II's logic (stack + prevOp).
When we hit `(`: recursively evaluate the sub-expression inside.
When we hit `)`: return the result of the sub-expression.

The cleanest approach: **recursive descent.**
Use an index `i` passed by reference. When we see `(`, recurse.
When we see `)`, return.

---

### 📜 Calculator III Code

```cpp
int calculateIII(string s) {
    int i = 0;
    return helper(s, i);
}
```

---

```cpp
int helper(string& s, int& i) {
    stack<int> st;
    int num = 0;
    char prevOp = '+';
```

Same as Calculator II. But now we handle `(` and `)`.

---

```cpp
    while (i < s.size()) {
        char c = s[i];

        if (isdigit(c)) {
            num = num * 10 + (c - '0');
        }
```

---

### `(` — recurse to evaluate the sub-expression

```cpp
        else if (c == '(') {
            i++;
            num = helper(s, i);
        }
```

The sub-expression inside `()` becomes a single NUMBER.
Recurse. The recursive call returns the value of the inner expression.
That value becomes `num` — treated like any other number.

> _"Parentheses are portals. The Oracle steps inside,
> evaluates everything within, and returns with a single number.
> That number takes the place of the entire parenthesized expression."_

---

### `)` — end of sub-expression, break out

```cpp
        else if (c == ')') {
            // Process the last number before returning
            if (prevOp == '+') st.push(num);
            else if (prevOp == '-') st.push(-num);
            else if (prevOp == '*') { int t = st.top(); st.pop(); st.push(t * num); }
            else if (prevOp == '/') { int t = st.top(); st.pop(); st.push(t / num); }

            int result = 0;
            while (!st.empty()) { result += st.top(); st.pop(); }
            return result;
        }
```

Flush the last number. Sum the stack. Return to the caller.

---

### Operator — same as Calculator II

```cpp
        else if (c != ' ') {
            if (prevOp == '+') st.push(num);
            else if (prevOp == '-') st.push(-num);
            else if (prevOp == '*') { int t = st.top(); st.pop(); st.push(t * num); }
            else if (prevOp == '/') { int t = st.top(); st.pop(); st.push(t / num); }
            num = 0;
            prevOp = c;
        }
        i++;
    }
```

---

### End of string — flush and sum

```cpp
    if (prevOp == '+') st.push(num);
    else if (prevOp == '-') st.push(-num);
    else if (prevOp == '*') { int t = st.top(); st.pop(); st.push(t * num); }
    else if (prevOp == '/') { int t = st.top(); st.pop(); st.push(t / num); }

    int result = 0;
    while (!st.empty()) { result += st.top(); st.pop(); }
    return result;
}
```

---

## 🔍 The Three Calculators — Summary

| Problem | Operators | Parentheses | Key technique |
|---------|-----------|-------------|---------------|
| **Calc I (224)** | `+`, `-` | Yes | Stack saves state at `(`, restores at `)` |
| **Calc II (227)** | `+`, `-`, `*`, `/` | No | Stack + prevOp. `*/` resolve immediately. |
| **Calc III (772)** | `+`, `-`, `*`, `/` | Yes | Calc II + recursive descent for `()` |

**Progression:**
-   Calc II handles precedence (the harder part).
-   Calc I handles parentheses (simpler — no precedence without `*/`).
-   Calc III combines both.

---

## 🔍 Why prevOp and Not Current Operator?

We process the number WHEN WE SEE THE NEXT OPERATOR.
At that point, we know the operator BEFORE the number (`prevOp`)
and the number itself. We can decide what to do.

If we tried to process at the current operator, we wouldn't know
the number yet (it hasn't been fully read).

---

## 🔍 The Dummy Operator Trick

In Calculator II, we iterate to `i <= s.size()` with a dummy `+` at the end.
This flushes the last number without special-casing.

In Calculator III, the `)` and end-of-string both flush.
The recursive structure handles it naturally.

---

### 🧠 Memory of the Expression Evaluator Law

-   **Calc II:** stack + prevOp. `+/-` push. `*/` resolve immediately. Sum at end.
-   **Calc I:** stack saves (result, sign) at `(`. Restores at `)`.
-   **Calc III:** Calc II + recurse at `(`, return at `)`.
-   **prevOp starts as `+`** — implicit `+` before the first number.
-   **Multi-digit:** `num = num * 10 + digit`.
-   **Flush last number:** dummy operator at end, or explicit handling.
-   **Time:** O(N). **Space:** O(N) for stack/recursion.

Thus is remembered the saga of **Basic Calculator I/II/III**,
where the Oracle built an expression evaluator in three stages —
first handling precedence with a stack and prevOp,
then handling parentheses by saving and restoring state,
finally combining both with recursive descent —
each level building on the last,
until any expression with `+`, `-`, `*`, `/`, and `()`
could be evaluated in a single pass. 📚🧮✨
