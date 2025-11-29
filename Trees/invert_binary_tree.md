## 🌲 _The Mirror Forest: The Invert Binary Tree Saga_

> _"In the forest of ancestors, each tree leans a certain way —
> left child, right child, branches growing in quiet symmetry.
> But the Oracle of Mirrors proclaimed:
> ‘Let every tree face its reflection.
> What was left shall be right,
> and what was right shall be left.’"_

---

Deep within the kingdom stood ancient binary trees.
Each node had two children — left and right — forming a branching history.
But when the Oracle walked through the woods, she cast a spell:

> _“Invert the forest.
> Swap each node’s children.
> Let the entire world be mirrored.”_

To achieve this, she used a simple and beautiful ritual:
at each node, she **swapped its left and right child**,
then descended into each subtree to repeat the spell.

Thus began the saga of **Invert Binary Tree**.

---

### 📜 The Scroll of Nodes

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

Every tree spirit held a value and two branches.
Reversing them required only a swift twist of fate.

---

## 🔄 The Oracle’s Mirror Ritual

_Swap children, then recurse_

```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;
```

If the tree was empty, the Oracle moved on.
Otherwise, she prepared to turn the branches.

---

```cpp
    // swap the children
    TreeNode* temp = root->left;
    root->left = root->right;
    root->right = temp;
```

With a gentle gesture, the left child became the right,
and the right child became the left.

The mirror magic began.

---

```cpp
    // recurse on both subtrees
    invertTree(root->left);
    invertTree(root->right);
```

The Oracle then descended to the newly swapped children,
ensuring every level of the forest reflected perfectly —
from tallest ancestor to smallest leaf.

---

```cpp
    return root;
}
```

At her touch, the entire tree became a mirrored twin of its former self.

---

### 🎺 The Trial of the Ancestral Tree

```cpp
int main() {
    // Construct tree:
    //      4
    //     / \
    //    2   7
    //   / \ / \
    //  1  3 6  9

    TreeNode* root = new TreeNode(4);
    root->left = new TreeNode(2);
    root->right = new TreeNode(7);
    root->left->left = new TreeNode(1);
    root->left->right = new TreeNode(3);
    root->right->left = new TreeNode(6);
    root->right->right = new TreeNode(9);

    invertTree(root);

    // expected mirrored:
    //      4
    //     / \
    //    7   2
    //   / \ / \
    //  9  6 3  1

    cout << root->val << " ";
    cout << root->left->val << " " << root->right->val << " ";
    cout << root->left->left->val << " " << root->left->right->val << " ";
    cout << root->right->left->val << " " << root->right->right->val << endl;

    return 0;
}
```

The proud tree stood one way —
`4 / 2,7 / 1,3,6,9` —

and after the Mirror Spell,
it stood in perfect reflection:
`4 / 7,2 / 9,6,3,1`.

The forest shimmered with symmetry.

---

### 🧠 Memory of the Mirror Forest

-   **swap left and right** at every node.
-   **recursive process** — invert subtrees after swapping.
-   **base case:** if node is null, return.
-   **Time:** O(n) — each node touched once.
-   **Space:** O(h) — recursion stack (height of tree).

Thus is remembered the saga of **Invert Binary Tree**,
where every branch is reversed,
every child swapped,
and the forest learns to face its own reflection. 🌲🔄✨
