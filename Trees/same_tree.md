## 🌿 _The Twin Grove: The Same Tree Saga_

> _"In the Grove of Reflections stand two ancient trees.
> They appear alike from afar,
> but the Druids whisper that true sameness
> lies not in the bark nor the branches alone —
> but in every leaf, every fork, every silent root.
> To know if two trees are one in spirit,
> compare them node for node, depth for depth,
> until all differences fade or truth is revealed."_

---

In the Realm of Trees, two great beings stood side by side — Tree **P** and Tree **Q**.
The Elders asked the Oracle:

> _“Are these two trees **exactly the same**?
> Not merely similar — but identical in structure and value?”_

The Oracle knew that sameness could not be judged by the surface.
It must be discovered through **recursion**, comparing:

1. The current nodes’ **values**
2. The **left subtrees**
3. The **right subtrees**

Only if all three matched perfectly could the trees be called the same.

Thus began the saga of **Same Tree**.

---

### 📜 The Scroll of Tree Spirits

```cpp
#include <iostream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int v) : val(v), left(NULL), right(NULL) {}
};
```

Each spirit carried a number and pointed to two children —
one left branch, one right branch —
forming the sacred structure of the forest.

---

## 🌱 The Oracle’s Ritual of Reflection

_Check value, left mirror, right mirror_

```cpp
bool isSameTree(TreeNode* p, TreeNode* q) {
```

Two trees stood before her: `p` and `q`.

---

### 🌬️ Step 1 — Both Roots Vanished?

```cpp
    if (!p && !q) return true;
```

If both nodes were absent,
the Oracle declared them equal in emptiness.

---

### 🌩️ Step 2 — One Exists, One Does Not?

```cpp
    if (!p || !q) return false;
```

If one tree held a branch where the other held none,
their destinies diverged — they could not be the same.

---

### 🌟 Step 3 — Values Must Match

```cpp
    if (p->val != q->val) return false;
```

If two spirits met but carried different values,
the illusion of sameness shattered.

---

### 🌿 Step 4 — Descend into Left and Right Subtrees

```cpp
    return isSameTree(p->left, q->left) &&
           isSameTree(p->right, q->right);
}
```

Only if:

-   left mirrors left
-   right mirrors right

did the Oracle bless the two as true twins.

Thus the judgment was complete.

---

### 🎺 The Trial of the Twin Trees

```cpp
int main() {
    // Tree P:
    //      1
    //     / \
    //    2   3
    TreeNode* p = new TreeNode(1);
    p->left = new TreeNode(2);
    p->right = new TreeNode(3);

    // Tree Q:
    //      1
    //     / \
    //    2   3
    TreeNode* q = new TreeNode(1);
    q->left = new TreeNode(2);
    q->right = new TreeNode(3);

    cout << (isSameTree(p, q) ? "true" : "false") << endl; // expected: true
    return 0;
}
```

Two trees stood in the grove —
each with root `1`,
each branching to `2` and `3`.

The Oracle compared their spirits and pronounced: **true** —
they were mirror-twins of structure and value.

---

### 🧠 Memory of the Twin Grove

-   If both nodes are null → they match.
-   If one null and one not → mismatch.
-   If values differ → mismatch.
-   Recurse on left children and right children.
-   **Same Tree** = identical structure AND identical values.
-   **Time:** O(n) — touch every node.
-   **Space:** O(h) — recursion stack of tree height.

Thus is remembered the saga of **Same Tree**,
where two trees stand before the Oracle,
and only perfect symmetry of structure and spirit
reveals whether they are indeed one and the same. 🌿✨
