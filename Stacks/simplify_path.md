## 🗺️⚔️ _The Labyrinth of Slashes: The Simplify Path Saga_

> \*"Within the Ancient Filesystem,
> paths twisted through directories and shadows —
> some forward,
> some backward,
> some meaningless echoes of the present.
>
> The Oracle was commanded:
>
> **‘Simplify this absolute path.
> Remove confusion.
> Resolve every dot and double dot.
> Return the true canonical route.’**
>
> In this labyrinth:
>
> `.` meant _stay where you are_ > `..` meant _step back one chamber_
> multiple `/` meant nothing more than one
>
> The Oracle knew she must walk carefully —
> remembering where she had been,
> stepping back only when allowed."\*

---

This is the epic saga of **Simplify Path**.

You are given a string `path`, representing an **absolute Unix-style file path**.

Your task:

-   Return the **simplified canonical path**
-   Follow these rules:

    -   `"."` → ignore
    -   `".."` → go up one directory (if possible)
    -   Multiple `/` → treated as single `/`
    -   Result must start with `/`
    -   No trailing `/` (unless root)

---

## 🧠 The Oracle’s Core Insight — Stack of Directories

The Oracle realized:

-   Every valid directory entered must be remembered
-   `".."` removes the most recent valid directory
-   `"."` and empty segments must be ignored

This is the perfect use of a **stack**.

---

## ⚔️ The Oracle’s Directory Stack Ritual

```cpp
string simplifyPath(string path) {
    vector<string> st;
    string curr;
```

The stack stored valid directories.

---

### 🌊 Walk Through the Path

```cpp
    for (int i = 0; i <= path.size(); i++) {
        if (i == path.size() || path[i] == '/') {
```

Each slash marked the end of a segment.

---

### 📁 Process Each Segment

```cpp
            if (curr == "..") {
                if (!st.empty()) st.pop_back();
            }
            else if (!curr.empty() && curr != ".") {
                st.push_back(curr);
            }
            curr.clear();
```

Rules applied:

-   `".."` → step back
-   valid name → step forward
-   `"."` or empty → ignore

---

### 🔤 Build Segment Name

```cpp
        } else {
            curr += path[i];
        }
    }
```

Characters between slashes formed directory names.

---

### 🏁 Reconstruct the True Path

```cpp
    string result = "/";
    for (int i = 0; i < st.size(); i++) {
        result += st[i];
        if (i != st.size() - 1) result += "/";
    }
    return result;
}
```

The Oracle rebuilt the path
from the stack of truth.

---

### 🎺 The Trial of the Labyrinth

```cpp
int main() {
    string path = "/home//foo/../bar/./baz/";
    cout << simplifyPath(path) << endl;
    // expected: "/home/bar/baz"
    return 0;
}
```

The Oracle removed:

-   redundant slashes
-   `"."`
-   stepped back for `".."`

And revealed the true path.

---

### 🧠 Memory of the Labyrinth Law

-   Split by `/`
-   Use stack for valid directories
-   `".."` → pop if possible
-   `"."` and empty → ignore
-   Rebuild final path
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of **Simplify Path**,
where the Oracle walks a tangled filesystem,
ignoring illusions of stillness,
stepping back when commanded,
and rebuilding the one true route —
a clean path carved through chaos. 🗺️✨
