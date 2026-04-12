## ⚖️🏛️ _The Law of Nesting: The Valid Parentheses Saga_

> \_"In the City of Syntax,
> three guardian clans ruled the gates --
> the Round `()`, the Square `[]`, and the Curly `{}`.
>
> Every gate that opened had to close.
> Every closer had to match its opener.
> And no gate could close out of order --
> the most recently opened gate
> must be the first to close.
>
> The Scribe of Balance was summoned:
>
> **'Judge this scroll of symbols.
> Does it obey the Law of Nesting?
> Is every opener matched by its rightful closer,
> in the correct order,
> with nothing left unclosed?'**
>
> The Scribe carried a stack --
> the Ledger of Open Gates.
>
> Every opener was pushed onto the ledger --
> a promise waiting to be fulfilled.
> Every closer was checked against the top --
> the most recent unfulfilled promise.
>
> If the closer matched -- the promise was honored, pop.
> If it didn't -- the law was broken.
> If the ledger was empty when a closer arrived --
> a gate tried to close before any were opened.
> If the ledger was not empty at the end --
> some gates were left forever open.
>
> Only when the ledger stood empty
> after the last symbol was read
> was the scroll declared valid."\_

---

This is the saga of **Valid Parentheses**.

Given a string `s` containing only `(`, `)`, `{`, `}`, `[`, `]`:

-   Return `true` if the string is valid.
-   A string is valid if:
    -   Every open bracket is closed by the same type.
    -   Brackets are closed in the correct order.
    -   Every close bracket has a corresponding open bracket.

```
Input:  "()"       →  true
Input:  "()[]{}"   →  true
Input:  "(]"       →  false
Input:  "([)]"     →  false
Input:  "{[]}"     →  true
```

---

## 🧠 The Oracle's Core Insight -- LIFO Matching

Bracket matching is inherently a **Last In, First Out** problem.

The most recently opened bracket must be the first to close.
This is exactly what a stack enforces.

```
For each character:
  If opener → push onto stack
  If closer → check if stack top matches
              If yes → pop (promise fulfilled)
              If no  → return false (mismatch)
              If stack empty → return false (no opener to match)

After all characters:
  If stack empty → all openers were matched → true
  If stack not empty → unclosed openers remain → false
```

```
Time:  O(N) -- one pass through the string
Space: O(N) -- stack holds at most N/2 openers
```

---

### 📜 The Scroll of the Three Clans

```cpp
#include <iostream>
#include <string>
#include <stack>
using namespace std;
```

---

## ⚔️ The Scribe's Judgment Ritual

```cpp
bool isValid(string s) {
    stack<char> st;
```

The Scribe opened the Ledger of Open Gates --
a `stack<char>` to track every unfulfilled opener.

---

## 🔁 Walk the Scroll Symbol by Symbol

```cpp
    for (char c : s) {
```

Each symbol was inspected in order.

---

### 🚪 Opener -- Push the Promise

```cpp
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        }
```

An opening bracket was encountered.
The Scribe pushed it onto the stack --
a promise that this gate would be closed
by its rightful partner.

> _"Every opener is a debt.
> The stack remembers all debts,
> with the most recent on top."_

---

### 🔒 Closer -- Check the Match

```cpp
        else {
            if (st.empty()) return false;
```

A closing bracket arrived.

If the stack was empty -- no opener existed to match this closer.
A gate tried to close before any were opened.
The scroll was immediately invalid.

---

```cpp
            char top = st.top();
            st.pop();
```

The Scribe read the most recent unfulfilled opener
and removed it from the ledger.

This was the candidate match -- the last gate opened.

---

```cpp
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{')) {
                return false;
            }
        }
    }
```

The Scribe checked: did the closer match its opener?

-   `)` must match `(`
-   `]` must match `[`
-   `}` must match `{`

If the types clashed -- round against square,
curly against round -- the law was broken.
Return `false`.

If they matched -- the promise was fulfilled.
The gate closed in peace. Continue.

---

## 🏛️ The Final Judgment

```cpp
    return st.empty();
}
```

After every symbol was processed,
the Scribe looked at the ledger one last time.

**If empty** -- every opener found its closer.
Every promise was fulfilled. The scroll was valid.

**If not empty** -- some openers were never closed.
Gates left open. The scroll was invalid.

> _"An empty ledger at the end
> is the only proof of perfect balance."_

---

### 🎺 The Trial of the Scrolls

```cpp
int main() {
    cout << isValid("()")     << endl; // 1 (true)
    cout << isValid("()[]{}")  << endl; // 1 (true)
    cout << isValid("(]")     << endl; // 0 (false)
    cout << isValid("([)]")   << endl; // 0 (false)
    cout << isValid("{[]}")   << endl; // 1 (true)
    cout << isValid("(((" )   << endl; // 0 (false)
    cout << isValid("")       << endl; // 1 (true)
    return 0;
}
```

---

**Full trace for `"{[]}"`:**

| Step | Char | Action                    | Stack after |
| ---- | ---- | ------------------------- | ----------- |
| 1    | `{`  | Opener → push             | [{]         |
| 2    | `[`  | Opener → push             | [{, []      |
| 3    | `]`  | Closer → top=`[`, match ✓ | [{]         |
| 4    | `}`  | Closer → top=`{`, match ✓ | []          |

Stack empty → **true** ✓

Perfect nesting: `[` closed before `{`, innermost first.

---

**Full trace for `"([)]"`:**

| Step | Char | Action                    | Stack after |
| ---- | ---- | ------------------------- | ----------- |
| 1    | `(`  | Opener → push             | [(]         |
| 2    | `[`  | Opener → push             | [(, []      |
| 3    | `)`  | Closer → top=`[`, `)` needs `(` → **mismatch** | -- |

**Return false** ✓

The `)` tried to close `[` -- wrong clan. The interleaving `([)]`
violates the nesting law even though each type appears in pairs.

---

**Full trace for `"()[]{}"`:**

| Step | Char | Action                    | Stack after |
| ---- | ---- | ------------------------- | ----------- |
| 1    | `(`  | Push                      | [(]         |
| 2    | `)`  | top=`(`, match ✓, pop     | []          |
| 3    | `[`  | Push                      | [[]         |
| 4    | `]`  | top=`[`, match ✓, pop     | []          |
| 5    | `{`  | Push                      | [{]         |
| 6    | `}`  | top=`{`, match ✓, pop     | []          |

Stack empty → **true** ✓

Three sequential pairs, each opened and closed before the next.

---

**Trace for `"((("` (unclosed openers):**

| Step | Char | Action | Stack after   |
| ---- | ---- | ------ | ------------- |
| 1    | `(`  | Push   | [(]           |
| 2    | `(`  | Push   | [(, (]        |
| 3    | `(`  | Push   | [(, (, (]     |

End of string. Stack not empty → **false** ✓

Three gates opened, none closed.

---

**Trace for `""` (empty string):**

No characters. Stack stays empty → **true** ✓

Silence is perfect balance.

---

## 🔍 Three Ways the Law Can Break

1. **Mismatch:** closer doesn't match the most recent opener.
   `"(]"` → `)` expected but `]` arrived.

2. **Excess closers:** closer arrives when stack is empty.
   `"())"` → third `)` has no opener.

3. **Excess openers:** string ends with openers still on the stack.
   `"(()"` → one `(` never closed.

All three are caught by the algorithm:
mismatch by the type check, excess closers by the empty check,
excess openers by the final `st.empty()` check.

---

### 🧠 Memory of the Nesting Law

-   **Stack** = Ledger of Open Gates -- tracks unfulfilled openers
-   **Opener** (`(`, `[`, `{`) → push onto stack
-   **Closer** (`)`, `]`, `}`) → check stack:
    -   Stack empty → false (no opener to match)
    -   Top doesn't match → false (wrong clan)
    -   Top matches → pop (promise fulfilled)
-   **After all characters:** `return st.empty()`
    -   Empty → all matched → true
    -   Not empty → unclosed openers → false
-   **Time:** O(N) -- single pass
-   **Space:** O(N) -- stack holds at most N/2 openers
-   **Edge cases:**
    -   Empty string → true
    -   Single character → always false
    -   All openers → false (stack not empty)
    -   All closers → false (stack empty on first closer)

Thus is remembered the saga of **Valid Parentheses**,
where the Scribe walked a scroll of brackets
carrying the Ledger of Open Gates --
pushing every opener as a promise,
matching every closer against the most recent debt --
and declaring the scroll valid
only when the ledger stood empty at the end,
every gate opened and closed in perfect nesting order,
the three clans of Round, Square, and Curly
standing in flawless harmony. ⚖️🏛️✨
