## 📚🧮 _The Postfix Calculator: The Evaluate Reverse Polish Notation Saga_

> \_"The Oracle was given a list of tokens in Reverse Polish Notation (RPN).
>
> In RPN (postfix notation), operators come AFTER their operands:
> `"3 4 +"` means `3 + 4 = 7`.
> `"5 2 * 3 +"` means `(5 * 2) + 3 = 13`.
>
> She was commanded:
>
> **'Evaluate the expression and return the result.'**
>
> The Oracle used a stack:
>
> **If the token is a number → push it.**
> **If the token is an operator → pop two, apply, push result.**
>
> The second popped value is the LEFT operand.
> The first popped value is the RIGHT operand.
> Order matters for subtraction and division.
>
> After processing all tokens, the stack has exactly one element:
> the final answer."\_

---

This is the saga of **Evaluate Reverse Polish Notation (LeetCode 150)**.

Given an array of tokens representing an RPN expression:
-   Evaluate and return the integer result.
-   Valid operators: `+`, `-`, `*`, `/`.
-   Division truncates toward zero.

```
Input:  tokens = ["2","1","+","3","*"]
Output: 9   ((2 + 1) * 3 = 9)

Input:  tokens = ["4","13","5","/","+"]
Output: 6   (4 + (13 / 5) = 4 + 2 = 6)

Input:  tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
Output: 22
```

---

## 🧠 What Is Reverse Polish Notation?

In standard (infix) notation: `3 + 4 * 2` — need parentheses and precedence rules.
In RPN (postfix): `3 4 2 * +` — no parentheses needed. Unambiguous.

**RPN rule:** when you see an operator, it applies to the two most recent numbers.

```
Infix:   (2 + 1) * 3
RPN:     2 1 + 3 *

Reading left to right:
  2 → push 2.
  1 → push 1.
  + → pop 1 and 2. Compute 2+1=3. Push 3.
  3 → push 3.
  * → pop 3 and 3. Compute 3*3=9. Push 9.
  Answer: 9.
```

---

## 🧠 Why a Stack?

Numbers wait on the stack until an operator needs them.
Operators always consume the two MOST RECENT numbers.
"Most recent" = top of stack. Perfect for LIFO.

The stack naturally handles nested expressions:
inner operations resolve first (their results stay on the stack
for outer operations to consume).

---

## 🧠 The Operand Order — Why It Matters

When we pop two values for an operator:
-   **First pop** = RIGHT operand (the one that came later).
-   **Second pop** = LEFT operand (the one that came earlier).

For `+` and `*`: order doesn't matter (commutative).
For `-` and `/`: order MATTERS.

```
Tokens: ["6", "3", "-"]
Stack after pushing: [6, 3] (top = 3)
Pop 3 (right), pop 6 (left). Compute 6 - 3 = 3. Not 3 - 6.
```

> _"The first to leave the stack is the right operand.
> The second is the left. Left OP right. Always."_

---

### 📜 The Scroll of the Postfix Calculator

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <stack>
using namespace std;
```

---

## 🧮 The Stack-Based Evaluator

### Initialize

```cpp
int evalRPN(vector<string>& tokens) {
    stack<int> st;
```

The stack holds intermediate results and pending operands.

---

### Process each token

```cpp
    for (string& token : tokens) {
```

---

### If it's an operator — pop two, compute, push result

```cpp
        if (token == "+" || token == "-" || token == "*" || token == "/") {
            int right = st.top(); st.pop();
            int left = st.top(); st.pop();
```

Pop the right operand first (top of stack = most recent).
Pop the left operand second.

---

### Apply the operator

```cpp
            int result;
            if (token == "+") result = left + right;
            else if (token == "-") result = left - right;
            else if (token == "*") result = left * right;
            else result = left / right;
```

Division truncates toward zero (C++ default for integer division).

---

### Push the result back

```cpp
            st.push(result);
        }
```

The result becomes an operand for future operators.

> _"The operator consumes two. Produces one.
> The result returns to the stack,
> waiting to be consumed by the next operator."_

---

### If it's a number — push it

```cpp
        else {
            st.push(stoi(token));
        }
    }
```

`stoi` converts the string to an integer.
Handles negatives like `"-11"` correctly.

---

### The final answer

```cpp
    return st.top();
}
```

After all tokens are processed, exactly one value remains: the answer.

---

### 🎺 The Trial of the Postfix Calculator

```cpp
int main() {
    vector<string> t1 = {"2", "1", "+", "3", "*"};
    cout << evalRPN(t1) << endl; // expected: 9

    vector<string> t2 = {"4", "13", "5", "/", "+"};
    cout << evalRPN(t2) << endl; // expected: 6

    vector<string> t3 = {"10", "6", "9", "3", "+", "-11", "*", "/", "*", "17", "+", "5", "+"};
    cout << evalRPN(t3) << endl; // expected: 22

    return 0;
}
```

---

**Full trace for tokens = ["2", "1", "+", "3", "*"]:**

| Token | Action | Stack |
|-------|--------|-------|
| "2" | push 2 | [2] |
| "1" | push 1 | [2, 1] |
| "+" | pop 1 (right), pop 2 (left). 2+1=3. push 3. | [3] |
| "3" | push 3 | [3, 3] |
| "*" | pop 3 (right), pop 3 (left). 3*3=9. push 9. | [9] |

**Answer: 9** ✓ — equivalent to `(2 + 1) * 3`.

---

**Trace for tokens = ["4", "13", "5", "/", "+"]:**

| Token | Action | Stack |
|-------|--------|-------|
| "4" | push 4 | [4] |
| "13" | push 13 | [4, 13] |
| "5" | push 5 | [4, 13, 5] |
| "/" | pop 5 (right), pop 13 (left). 13/5=2. push 2. | [4, 2] |
| "+" | pop 2 (right), pop 4 (left). 4+2=6. push 6. | [6] |

**Answer: 6** ✓ — equivalent to `4 + (13 / 5)`.

---

**Trace for the complex expression:**

```
tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]

Push 10, 6, 9, 3.                    Stack: [10, 6, 9, 3]
"+": 9+3=12.                         Stack: [10, 6, 12]
Push -11.                             Stack: [10, 6, 12, -11]
"*": 12*(-11)=-132.                   Stack: [10, 6, -132]
"/": 6/(-132)=0 (truncates to 0).     Stack: [10, 0]
"*": 10*0=0.                          Stack: [0]
Push 17.                              Stack: [0, 17]
"+": 0+17=17.                         Stack: [17]
Push 5.                               Stack: [17, 5]
"+": 17+5=22.                         Stack: [22]
```

**Answer: 22** ✓

---

## 🔍 Why RPN Doesn't Need Parentheses

In infix: `3 + 4 * 2` is ambiguous without precedence rules.
Is it `(3 + 4) * 2 = 14` or `3 + (4 * 2) = 11`?

In RPN: the order of operators IS the precedence.
-   `3 4 2 * +` = `3 + (4 * 2)` = 11.
-   `3 4 + 2 *` = `(3 + 4) * 2` = 14.

No ambiguity. No parentheses. The stack handles nesting naturally.

---

## 🔍 Division Truncation Toward Zero

C++ integer division truncates toward zero:
-   `7 / 2 = 3` (not 3.5)
-   `-7 / 2 = -3` (not -4, truncates toward zero)
-   `6 / (-132) = 0`

This matches the problem's requirement. No special handling needed in C++.

---

## 🔍 How to Detect If a Token Is a Number

A token is an operator if it's exactly `"+"`, `"-"`, `"*"`, or `"/"`.
Everything else is a number (including negative numbers like `"-11"`).

Why not check `isdigit(token[0])`? Because negative numbers start with `-`.
Checking the token against the four operators is simpler and correct.

---

## 🔍 Connection to Expression Conversion

| Notation | Example | Evaluation |
|----------|---------|-----------|
| Infix | `3 + 4 * 2` | Needs precedence rules |
| **Postfix (RPN)** | **`3 4 2 * +`** | **Stack-based, left to right** |
| Prefix | `+ 3 * 4 2` | Stack-based, right to left |

Compilers convert infix → postfix (using the Shunting Yard algorithm),
then evaluate postfix with a stack. That's how calculators work internally.

---

### 🧠 Memory of the Postfix Calculator Law

-   **Number → push.** Operator → pop two, compute, push result.
-   **First pop = RIGHT operand.** Second pop = LEFT operand.
-   **Order matters** for `-` and `/` (not for `+` and `*`)
-   **After all tokens:** stack has exactly one element = the answer
-   **Division truncates toward zero** (C++ default)
-   **No parentheses needed** — operator position encodes precedence
-   **Time:** O(N). **Space:** O(N) for the stack.

Thus is remembered the saga of **Evaluate Reverse Polish Notation**,
where the Oracle processed tokens left to right —
numbers waited on the stack,
operators consumed the two most recent,
computed the result, and pushed it back —
until one final value remained:
the answer to the expression,
evaluated without a single parenthesis. 📚🧮✨
