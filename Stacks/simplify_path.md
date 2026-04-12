## 📂🧭 _The Pilgrim's True Path: The Simplify Path Saga_

> \_"In the Kingdom of Directories,
> paths wound through nested chambers --
> forward into subdirectories,
> backward with `..` to the parent,
> and sometimes nowhere at all with `.` or empty steps.
>
> Travelers wrote their journeys as Unix paths --
> long strings of slashes and names,
> riddled with redundant slashes,
> pointless current-directory dots,
> and parent-directory retreats.
>
> The Oracle was commanded:
>
> **'Given an absolute Unix path,
> simplify it to its canonical form --
> no trailing slashes, no double slashes,
> no `.` or `..` cluttering the way.
> Just the true path from root to destination.'**
>
> The Oracle reached for a stack.
>
> She would split the path by `/`,
> process each component one by one:
>
> **A real directory name → push it deeper.**
> **`..` → go back one level (pop).**
> **`.` or empty → ignore, stay where you are.**
>
> When the walk was done,
> the stack held the true path --
> each entry a directory from root to destination.
> Join them with `/` and prepend the root slash."\_

---

This is the saga of **Simplify Path**.

Given an **absolute Unix path** string `path`,
return the simplified **canonical path**.

Rules of canonical form:
-   Starts with a single `/`.
-   Directories separated by a single `/`.
-   No trailing `/` (unless it's the root `/`).
-   No `.` (current directory) or `..` (parent) in the result.
-   `..` moves up one directory (if possible -- root has no parent).

```
Input:  "/home/"
Output: "/home"

Input:  "/home//foo/"
Output: "/home/foo"

Input:  "/home/user/Documents/../Pictures"
Output: "/home/user/Pictures"

Input:  "/../"
Output: "/"

Input:  "/home/../../.."
Output: "/"
```

---

## 🧠 The Oracle's Core Insight -- Split, Filter, Stack

The path is a sequence of components separated by `/`.

Split by `/` and process each component:

```
""   (empty, from double slashes)  → ignore
"."  (current directory)           → ignore
".." (parent directory)            → pop from stack (go up one level)
anything else                      → push onto stack (enter directory)
```

After processing all components, the stack holds
the directories from root to destination in order.

Join them with `/` and prepend `/` for the root.

```
Time:  O(N) -- split and process each component once
Space: O(N) -- the stack and the split tokens
```

---

### 📜 The Scroll of the Directory Kingdom

```cpp
#include <iostream>
#include <string>
#include <stack>
#include <sstream>
using namespace std;
```

`sstream` provides `getline` with a delimiter --
the cleanest way to split a string by `/` in C++.

---

## ⚔️ The Oracle's Path-Simplifying Ritual

```cpp
string simplifyPath(string path) {
    stack<string> st;
    stringstream ss(path);
    string token;
```

The Oracle prepared:
-   `st` -- a stack of directory names (the true path).
-   `ss` -- a string stream wrapping the input path.
-   `token` -- each component extracted by splitting on `/`.

---

## 🔁 Split by `/` and Process Each Token

```cpp
    while (getline(ss, token, '/')) {
```

`getline(ss, token, '/')` split the path at every `/`.

For `"/home//foo/"`, the tokens would be:
`""`, `"home"`, `""`, `"foo"`, `""`.

Empty tokens came from leading `/`, trailing `/`, and double `//`.
All were harmless -- the Oracle simply ignored them.

---

### 🚫 Empty or `.` -- Ignore

```cpp
        if (token == "" || token == ".") {
            continue;
        }
```

Empty strings (from extra slashes) and `.` (current directory)
meant "stay where you are." No action needed.

---

### ⬆️ `..` -- Go Up One Level

```cpp
        else if (token == "..") {
            if (!st.empty()) {
                st.pop();
            }
        }
```

`..` meant "move to the parent directory."

If the stack had entries -- pop the top (leave the current directory).
If the stack was empty -- we were already at root.
Root has no parent. Do nothing.

> _"You cannot climb above the sky.
> At the root, `..` is silence."_

---

### 📁 Directory Name -- Go Deeper

```cpp
        else {
            st.push(token);
        }
    }
```

Any other token was a real directory name.
Push it onto the stack -- the pilgrim descended one level deeper.

---

## 🏁 Build the Canonical Path

```cpp
    string result = "";
    while (!st.empty()) {
        result = "/" + st.top() + result;
        st.pop();
    }
    return result.empty() ? "/" : result;
}
```

The Oracle drained the stack to build the final path.

Since a stack gives elements in reverse order (LIFO),
each popped directory was prepended to the result:
`"/" + top + result`.

This built the path from deepest directory back to root.

If the result was empty -- the pilgrim ended at root.
Return `"/"`.

---

### 🎺 The Trial of the Winding Paths

```cpp
int main() {
    cout << simplifyPath("/home/")                        << endl; // /home
    cout << simplifyPath("/home//foo/")                   << endl; // /home/foo
    cout << simplifyPath("/home/user/Documents/../Pictures") << endl; // /home/user/Pictures
    cout << simplifyPath("/../")                          << endl; // /
    cout << simplifyPath("/home/../../..")                << endl; // /
    cout << simplifyPath("/a/./b/../../c/")               << endl; // /c
    return 0;
}
```

---

**Full trace for `"/home/user/Documents/../Pictures"`:**

**Split tokens:** `""`, `"home"`, `"user"`, `"Documents"`, `".."`, `"Pictures"`

| Token       | Action                | Stack after                    |
| ----------- | --------------------- | ------------------------------ |
| `""`        | Ignore (empty)        | []                             |
| `"home"`    | Push                  | [home]                         |
| `"user"`    | Push                  | [home, user]                   |
| `"Documents"` | Push                | [home, user, Documents]        |
| `".."`      | Pop (Documents)       | [home, user]                   |
| `"Pictures"` | Push                 | [home, user, Pictures]         |

**Build:** `/home/user/Pictures` ✓

The `..` after `Documents` retreated one level,
then `Pictures` entered as a sibling of `Documents`.

---

**Full trace for `"/home/../../.."`:**

**Split tokens:** `""`, `"home"`, `".."`, `".."`, `".."`

| Token    | Action              | Stack after |
| -------- | ------------------- | ----------- |
| `""`     | Ignore              | []          |
| `"home"` | Push                | [home]      |
| `".."`   | Pop (home)          | []          |
| `".."`   | Stack empty → skip  | []          |
| `".."`   | Stack empty → skip  | []          |

**Build:** result is empty → return `"/"` ✓

Three `..` but only one directory to leave.
The extra `..` at root were silently ignored.

---

**Full trace for `"/a/./b/../../c/"`:**

**Split tokens:** `""`, `"a"`, `"."`, `"b"`, `".."`, `".."`, `"c"`, `""`

| Token  | Action         | Stack after |
| ------ | -------------- | ----------- |
| `""`   | Ignore         | []          |
| `"a"`  | Push           | [a]         |
| `"."`  | Ignore (stay)  | [a]         |
| `"b"`  | Push           | [a, b]      |
| `".."` | Pop (b)        | [a]         |
| `".."` | Pop (a)        | []          |
| `"c"`  | Push           | [c]         |
| `""`   | Ignore         | [c]         |

**Build:** `/c` ✓

---

**Trace for `"/home//foo/"`:**

**Split tokens:** `""`, `"home"`, `""`, `"foo"`, `""`

| Token    | Action | Stack after   |
| -------- | ------ | ------------- |
| `""`     | Ignore | []            |
| `"home"` | Push   | [home]        |
| `""`     | Ignore | [home]        |
| `"foo"`  | Push   | [home, foo]   |
| `""`     | Ignore | [home, foo]   |

**Build:** `/home/foo` ✓

Double slashes produced empty tokens -- all ignored.

---

## 🔍 Edge Case: `..` as a Directory Name?

In this problem, `..` always means "parent directory."
But in real Unix, a directory could theoretically be named `...` (three dots).

The problem guarantees only `.` and `..` are special.
Everything else -- including `...`, `....`, or names with dots --
is treated as a regular directory name and pushed onto the stack.

---

### 🧠 Memory of the Pilgrim's Path Law

-   **Split** the path by `/` using `getline(ss, token, '/')`
-   **Process each token:**
    -   `""` or `"."` → ignore (stay in place)
    -   `".."` → pop from stack if not empty (go up one level)
    -   Anything else → push onto stack (enter directory)
-   **Build result:** drain stack, prepend `"/" + top` to result
-   **Empty result** → return `"/"` (root)
-   `..` at root is silently ignored (can't go above root)
-   **Time:** O(N) -- split and process once
-   **Space:** O(N) -- stack and tokens
-   **Edge cases:**
    -   Multiple `//` → empty tokens, ignored
    -   Trailing `/` → empty token, ignored
    -   `..` beyond root → ignored
    -   `"/"` alone → empty stack → return `"/"`

Thus is remembered the saga of **Simplify Path**,
where the Oracle split a winding Unix path into tokens,
walked them one by one with a stack of directories --
pushing real names deeper,
popping on `..` to retreat,
ignoring `.` and empty noise --
until the stack held the true canonical path
from root to destination,
free of all redundancy. 📂🧭✨
