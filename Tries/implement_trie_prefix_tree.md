## 🌿 _The Forest of Prefixes: The Implement Trie (Prefix Tree) Saga_

> \*"In the Whispering Woods of Words,
> countless strings drifted like leaves on the wind.
> The Oracle longed for a way to gather them—
> not as isolated scrolls,
> but as **shared paths**,
> where common beginnings formed branches,
> and endings glowed like sacred runes.
>
> Thus she planted the **Trie**,
> a Tree of Prefixes,
> where each letter is a step on a path
> and every word finds its resting place."\*

---

A **Trie** is no ordinary tree.
It is a forest built from characters themselves.

Each node holds:

-   A set of child paths for possible next letters
-   A marker to show whether a complete word ends at this node

This structure lets the Oracle answer three sacred queries:

1. **insert(word)** → Plant the word into the prefix forest
2. **search(word)** → Does a complete word live here?
3. **startsWith(prefix)** → Does any word begin with this prefix?

Thus began the saga of the **Trie (Prefix Tree)**.

---

### 📜 The Scroll of Prefix Spirits

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Trie {
public:
    struct Node {
        bool end;
        Node* child[26];
        Node() : end(false) {
            for (int i = 0; i < 26; i++) child[i] = NULL;
        }
    };

    Node* root;

    Trie() : root(new Node()) {}
```

Each **Node** of the Trie contained:

-   `end` → a glowing rune marking the end of a word
-   `child[26]` → paths for letters `a` to `z`

The **root** was the silent beginning of all paths.

---

## 🌱 The Oracle Plants a Word

### _insert(word)_

```cpp
    void insert(string word) {
        Node* cur = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!cur->child[idx])
                cur->child[idx] = new Node();
            cur = cur->child[idx];
        }
        cur->end = true;
    }
```

Letter by letter,
the Oracle walked the path:

-   If a branch for this letter didn’t exist,
    she **created a new node**.
-   She descended deeper until all letters were placed.
-   At the final node, she set the **end rune**,
    declaring that a full word rests here.

---

## 🔍 The Oracle Seeks a Full Word

### _search(word)_

```cpp
    bool search(string word) {
        Node* cur = root;
        for (char ch : word) {
            int idx = ch - 'a';
            if (!cur->child[idx]) return false;
            cur = cur->child[idx];
        }
        return cur->end;
    }
```

She walked letter by letter.
If ever a branch was missing,
the word did not exist.

If she reached the end
and the final node held the glowing rune,
the word was truly present.

---

## 🌬️ The Oracle Listens for Any Beginning

### _startsWith(prefix)_

```cpp
    bool startsWith(string prefix) {
        Node* cur = root;
        for (char ch : prefix) {
            int idx = ch - 'a';
            if (!cur->child[idx]) return false;
            cur = cur->child[idx];
        }
        return true;
    }
};
```

For a prefix,
the Oracle only needed to verify the path existed.
She did **not** require the end rune —
only the correct journey through letters.

If the prefix’s trail existed,
at least one word began with it.

---

### 🎺 The Trial of the Whispering Woods

```cpp
int main() {
    Trie trie;
    trie.insert("apple");
    cout << trie.search("apple") << endl;    // true
    cout << trie.search("app") << endl;      // false
    cout << trie.startsWith("app") << endl;  // true
    trie.insert("app");
    cout << trie.search("app") << endl;      // true
    return 0;
}
```

The Oracle planted “apple,”
and the Prefix Tree unfurled:

-   `search("apple")` → true
-   `search("app")` → false
-   `startsWith("app")` → true
-   After planting “app,”
    `search("app")` → true

The forest of prefixes echoed her every step.

---

### 🧠 Memory of the Prefix Forest

-   **Each node stores up to 26 children** (for letters a–z).
-   **Insertion** grows branches character by character.
-   **Search** confirms an exact full word.
-   **startsWith** checks only the existence of a prefix path.
-   **Time:** O(L) per operation,
    where L = length of the word/prefix.
-   **Space:** O(total characters inserted).

Thus is remembered the saga of **Implement Trie (Prefix Tree)**,
where the Oracle plants words like seeds,
paths intertwine into living branches,
and every prefix becomes a guide through
the Whispering Woods of Letters. 🌿✨
