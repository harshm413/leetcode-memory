## ⚖️ _The Balance of Brackets: The Valid Parentheses Saga_

> _"Three clans guard the gates of logic — round, square, and curly.
> When one opens, it must close; when one steps forth,
> its kin must wait their turn. Only perfect balance keeps the gate unbroken."_

---

In the grand city of Syntax, three guardian clans ruled:

-   **The Round** `()` — swift and soft-spoken,
-   **The Square** `[]` — measured and precise,
-   **The Curly** `{}` — bold and structured.

When scrolls of symbols were written, they had to obey the Law of Nesting:
Each opener must one day be closed by its rightful kin, and never out of order.

The Scribe of Balance was tasked to judge whether a scroll upheld the law — whether the sequence of symbols was a **Valid Parentheses** string.

---

### 📜 The Tools of Balance

```cpp
#include <iostream>
#include <string>
#include <stack>
using namespace std;
```

The Scribe carried a **stack**, the ancient Ledger of Open Gates.
Whenever a gate was opened, its symbol was pushed onto the ledger.
When a closing symbol appeared, the Scribe looked atop the stack —
only if the guardian there matched could the gate close in peace.

---

### ⚖️ The Scribe’s Judgment

```cpp
bool isValid(string s) {
    stack<char> st;
```

An empty ledger meant perfect calm — no gates yet opened.

---

```cpp
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        }
```

Each time an opener appeared, the Scribe marked it upon the stack —
a promise waiting to be fulfilled.

---

```cpp
        else {
            if (st.empty()) return false;
            char top = st.top();
            st.pop();
```

But when a closer appeared, the Scribe checked:
If the ledger was empty — a gate trying to close before any were opened —
the scroll was immediately invalid.

Otherwise, the last open gate was read (`top`), and the Scribe inspected the match.

---

```cpp
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{')) {
                return false;
            }
        }
    }
    return st.empty();
}
```

If a mismatch occurred — round against square, curly against round — the scroll was deemed broken.
When the entire scroll was read, only if the ledger stood empty — all gates closed in perfect order — was the balance upheld.

---

### 🎺 The Trial of the Scrolls

```cpp
int main() {
    vector<string> tests = {
        "()",
        "()[]{}",
        "(]",
        "([)]",
        "{[]}",
        "(((",
        ""
    };
    for (auto &t : tests) {
        cout << "\"" << t << "\" -> " << (isValid(t) ? "true" : "false") << endl;
    }
    return 0;
}
```

The Scribe tested many scrolls:

| Scroll     | Verdict                 |
| ---------- | ----------------------- |
| `()`       | true                    |
| `()[]{}`   | true                    |
| `(]`       | false                   |
| `([)]`     | false                   |
| `{[]}`     | true                    |
| `(((`      | false                   |
| `` (empty) | true — peace in silence |

The Law of Nesting stood firm: every open must be closed by its rightful kin, and no closer may appear before its opener.

---

### 🧠 Memory of the Balance

-   **Stack** → the Ledger of open gates, holding expected closers.
-   **Push** when you meet an opener — record the promise to close.
-   **Pop and compare** when you meet a closer — verify the last opened matches.
-   **Empty at end** → all gates closed in harmony.
-   **Invalid cases:**

    -   A closer appears when stack empty.
    -   Closer doesn’t match opener.
    -   Stack nonempty at end (some gates left unclosed).

-   **Time:** O(n), **Space:** O(n).

Thus is remembered the saga of **Valid Parentheses**,
where peace is found only when every guardian opens and closes in perfect order —
and the Ledger of Balance ends empty beneath the moon of logic. ⚖️✨
