## 📚🔗 _The Longest Balanced Stretch: The Longest Valid Parentheses Saga_

> \_"The Oracle was given a string of only `(` and `)`.
>
> She was commanded:
>
> **'Find the length of the longest substring
> that forms valid (balanced) parentheses.'**
>
> `"(()"` → longest valid = `"()"` = length 2.
> `")()())"` → longest valid = `"()()"` = length 4.
> `"()(()"`→ longest valid = `"()"` = length 2.
>
> The Oracle used a stack of INDICES:
>
> **Push the index of every unmatched character.**
> **When a match is found, the length of the valid substring
> = current index - the index at the top of the stack.**
>
> The stack always holds the boundary —
> the index of the last UNMATCHED character before
> the current valid stretch."\_

---

This is the saga of **Longest Valid Parentheses (LeetCode 32)**.

Given a string `s` containing only `(` and `)`:
-   Find the length of the longest valid (well-formed) parentheses substring.

```
Input:  s = "(()"
Output: 2   ("()" starting at index 1)

Input:  s = ")()())"
Output: 4   ("()()" starting at index 1)

Input:  s = ""
Output: 0
```

---

## 🧠 The Index Stack Insight

Instead of pushing characters, push INDICES.
The stack holds indices of characters that are NOT YET MATCHED.

**When we see `(`:** push its index. It's unmatched (for now).

**When we see `)`:**
-   If the stack top is a `(` index → they match! Pop it.
    The length of the current valid stretch = `i - stack.top()`.
    (`stack.top()` after popping = the boundary before this valid stretch.)
-   If the stack is empty or top is `)` → this `)` is unmatched. Push its index.

**The key:** after popping a matched `(`, the new stack top
is the INDEX of the last unmatched character BEFORE the current valid stretch.
The valid stretch length = `current index - that boundary`.

---

## 🧠 Why Initialize the Stack with -1?

We push `-1` as the initial "boundary."

Without it: if the entire string from index 0 is valid (like `"()()"`),
after popping the matched `(`, the stack would be EMPTY.
We can't compute `i - stack.top()` on an empty stack.

`-1` acts as the boundary BEFORE index 0.
Length of a valid stretch starting at 0 = `i - (-1)` = `i + 1`. Correct.

> _"The -1 is the invisible wall before the string begins.
> It's the boundary that says: 'everything from here onward is valid.'"_

---

### 📜 The Scroll of the Longest Balanced Stretch

```cpp
#include <iostream>
#include <string>
#include <stack>
using namespace std;
```

---

## 🔗 The Index Stack Solution

### Initialize with boundary -1

```cpp
int longestValidParentheses(string s) {
    stack<int> st;
    st.push(-1);
    int maxLen = 0;
```

Push -1 as the initial boundary. `maxLen` tracks the answer.

---

### Process each character

```cpp
    for (int i = 0; i < s.size(); i++) {
```

---

### `(` — push its index (unmatched for now)

```cpp
        if (s[i] == '(') {
            st.push(i);
        }
```

Every `(` is potentially unmatched. Push its index.
If a matching `)` comes later, we'll pop it.

---

### `)` — try to match

```cpp
        else {
            st.pop();
```

Pop the top. This either:
-   Removes a matching `(` (the most recent unmatched `(`).
-   Removes the boundary (if no `(` was available to match).

---

### If stack is empty — this `)` is the new boundary

```cpp
            if (st.empty()) {
                st.push(i);
            }
```

The stack was empty after popping → there was no `(` to match.
This `)` is unmatched. It becomes the new boundary.

Push its index. It marks: "the last unmatched character before
any future valid stretch."

> _"An unmatched `)` becomes a wall.
> Any valid stretch after this point
> will measure its length from this wall."_

---

### If stack is not empty — compute valid length

```cpp
            else {
                maxLen = max(maxLen, i - st.top());
            }
        }
    }
    return maxLen;
}
```

The stack top = the boundary (last unmatched index before this stretch).
Length of the current valid stretch = `i - st.top()`.

Update `maxLen` if this is the longest seen.

> _"The distance from the current position to the boundary
> is the length of the valid stretch ending here.
> The boundary is always the top of the stack."_

---

### 🎺 The Trial of the Longest Balanced Stretch

```cpp
int main() {
    cout << longestValidParentheses("(()") << endl;     // expected: 2
    cout << longestValidParentheses(")()())") << endl;  // expected: 4
    cout << longestValidParentheses("()(()") << endl;   // expected: 2
    cout << longestValidParentheses("(()())") << endl;  // expected: 6
    cout << longestValidParentheses("") << endl;        // expected: 0
    return 0;
}
```

---

**Full trace for s = ")()())":**

| i | char | Action | Stack | maxLen |
|---|------|--------|-------|--------|
| — | — | init | [-1] | 0 |
| 0 | ) | pop -1. Stack empty → push 0. | [0] | 0 |
| 1 | ( | push 1. | [0, 1] | 0 |
| 2 | ) | pop 1. Stack not empty. len = 2-0 = 2. | [0] | **2** |
| 3 | ( | push 3. | [0, 3] | 2 |
| 4 | ) | pop 3. Stack not empty. len = 4-0 = 4. | [0] | **4** |
| 5 | ) | pop 0. Stack empty → push 5. | [5] | 4 |

**Answer: 4** ✓ (substring "()()" at indices 1-4)

Key moments:
-   i=0: unmatched `)` becomes the new boundary (index 0).
-   i=2: matched! Length = 2 - 0 = 2 (from boundary 0 to here).
-   i=4: matched! Length = 4 - 0 = 4 (the stretch grew — "()()" from boundary 0).
-   i=5: unmatched `)` becomes new boundary.

---

**Trace for s = "(()":**

| i | char | Action | Stack | maxLen |
|---|------|--------|-------|--------|
| — | — | init | [-1] | 0 |
| 0 | ( | push 0. | [-1, 0] | 0 |
| 1 | ( | push 1. | [-1, 0, 1] | 0 |
| 2 | ) | pop 1. len = 2-0 = 2. | [-1, 0] | **2** |

**Answer: 2** ✓ (substring "()" at indices 1-2)

The `(` at index 0 remains unmatched. Only indices 1-2 form valid parens.

---

**Trace for s = "(()())":**

| i | char | Action | Stack | maxLen |
|---|------|--------|-------|--------|
| — | — | init | [-1] | 0 |
| 0 | ( | push 0. | [-1, 0] | 0 |
| 1 | ( | push 1. | [-1, 0, 1] | 0 |
| 2 | ) | pop 1. len = 2-0 = 2. | [-1, 0] | 2 |
| 3 | ( | push 3. | [-1, 0, 3] | 2 |
| 4 | ) | pop 3. len = 4-0 = 4. | [-1, 0] | 4 |
| 5 | ) | pop 0. len = 5-(-1) = 6. | [-1] | **6** |

**Answer: 6** ✓ (entire string is valid)

At i=5: the last `)` matches the `(` at index 0.
After popping, stack top = -1 (the initial boundary).
Length = 5 - (-1) = 6. The entire string.

---

## 🔍 Why This Works — The Boundary Concept

The stack always maintains a sequence of "boundaries" —
indices of unmatched characters that separate valid stretches.

```
String: ) ( ) ( ) )
Index:  0 1 2 3 4 5

After processing:
  Index 0 is a boundary (unmatched ')').
  Indices 1-4 form "()()" — valid stretch of length 4.
  Index 5 is a boundary (unmatched ')').
```

The valid stretch length = distance between consecutive boundaries.
The stack tracks these boundaries. `i - st.top()` gives the distance.

---

## 🔍 The DP Alternative

```cpp
int longestValidParentheses(string s) {
    int n = s.size();
    vector<int> dp(n, 0);
    int maxLen = 0;
    for (int i = 1; i < n; i++) {
        if (s[i] == ')') {
            if (s[i-1] == '(') {
                dp[i] = (i >= 2 ? dp[i-2] : 0) + 2;
            } else if (i - dp[i-1] - 1 >= 0 && s[i - dp[i-1] - 1] == '(') {
                dp[i] = dp[i-1] + 2 + (i - dp[i-1] - 2 >= 0 ? dp[i - dp[i-1] - 2] : 0);
            }
        }
        maxLen = max(maxLen, dp[i]);
    }
    return maxLen;
}
```

`dp[i]` = length of longest valid parentheses ENDING at index i.
Works but harder to understand. The stack approach is cleaner.

---

## 🔍 The Two-Pass O(1) Space Alternative

```cpp
int longestValidParentheses(string s) {
    int left = 0, right = 0, maxLen = 0;
    for (int i = 0; i < s.size(); i++) {
        if (s[i] == '(') left++; else right++;
        if (left == right) maxLen = max(maxLen, 2 * right);
        else if (right > left) left = right = 0;
    }
    left = right = 0;
    for (int i = s.size() - 1; i >= 0; i--) {
        if (s[i] == '(') left++; else right++;
        if (left == right) maxLen = max(maxLen, 2 * left);
        else if (left > right) left = right = 0;
    }
    return maxLen;
}
```

Two passes (left-to-right and right-to-left). O(1) space.
But the stack approach is the most intuitive for this problem.

---

### 🧠 Memory of the Longest Balanced Stretch Law

-   **Stack of indices** — holds unmatched characters (boundaries)
-   **Initialize with -1** — boundary before index 0
-   **`(`:** push index (unmatched for now)
-   **`)`:** pop. If stack empty → push i (new boundary). Else → `len = i - st.top()`.
-   **`i - st.top()`** = length of valid stretch ending at i
-   **The stack top after popping = the boundary before the current valid stretch**
-   **Time:** O(N). **Space:** O(N).

Thus is remembered the saga of **Longest Valid Parentheses**,
where the Oracle pushed indices of unmatched characters —
each unmatched character a boundary between valid stretches —
and when a match was found, the distance to the boundary
revealed the length of the valid stretch —
the longest such stretch being the answer,
found in a single pass through the string. 📚🔗✨
