## 🌳 _The Descent into the Green Abyss: The Maximum Depth of Binary Tree Saga_

> _"A tree does not boast by its width,
> but by how far its roots plunge into the earth
> and how high its branches reach toward the sun.
> To measure its true depth,
> walk to the very end of every path
> and return with the greatest distance found."_

---

In the ancient Forest of Nodes, every tree had a story —
a root at its crown, children branching downward,
and deeper still, layers upon layers of descendants.

The Elders asked the Oracle:

> _“What is the **maximum depth** of this tree?
> How many layers of ancestors and heirs lie beneath the root?”_

The Oracle knew the answer lived in a simple truth:
To know a tree’s depth, you must know **the depth of its left child**,
**the depth of its right child**,
and take the greater of the two — then add one for the current node.

Thus began the saga of **Maximum Depth of Binary Tree**.

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

Each tree spirit held a value,
and two branches that either reached deeper
or came to an end like silent roots.

---

## 🌲 The Oracle’s Descent Ritual

_Explore both paths, choose the deeper one_

```cpp
int maxDepth(TreeNode* root) {
    if (!root) return 0;
```

If the root was empty, the forest was silent —
its depth was zero.

---

```cpp
    int leftDepth = maxDepth(root->left);
    int rightDepth = maxDepth(root->right);
```

The Oracle descended down the **left branch**,
exploring every path until she reached the deepest leaf.
She marked that depth as `leftDepth`.

Then she traveled the **right branch**,
seeking its deepest rootlet,
marking it as `rightDepth`.

---

```cpp
    return 1 + max(leftDepth, rightDepth);
}
```

At each node she whispered:

> _“Your depth is one more than the greater of your children’s depths.”_

Thus, from the deepest leaves upward,
the true height of the tree was measured.

---

### 🎺 The Trial of the Ancestral Tree

```cpp
int main() {
    //      3
    //     / \
    //    9  20
    //      /  \
    //     15   7

    TreeNode* root = new TreeNode(3);
    root->left = new TreeNode(9);
    root->right = new TreeNode(20);
    root->right->left = new TreeNode(15);
    root->right->right = new TreeNode(7);

    cout << maxDepth(root) << endl; // expected: 3
    return 0;
}
```

This noble tree’s deepest path
traveled from **3 → 20 → 15** (or 3 → 20 → 7),
a journey of three layers.

The Oracle returned with the answer: **3**.

---

### 🧠 Memory of the Deep Roots

-   **Depth of empty tree:** 0
-   **Depth of node:**
    `1 + max(depth(left), depth(right))`
-   **DFS recursion** naturally explores all paths.
-   **Time:** O(n) — visit every node.
-   **Space:** O(h) — height of tree (recursion stack).

Thus is remembered the saga of the **Maximum Depth of Binary Tree**,
where the Oracle walks into the heart of the forest,
counts every layer beneath the crown,
and brings back the measure of how deeply the tree’s spirit runs. 🌳✨
