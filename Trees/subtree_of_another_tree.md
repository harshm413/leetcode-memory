## 🌳 _The Whisper of the Smaller Spirit: The Subtree of Another Tree Saga_

> _"Within the Great Tree of Ages,
> countless branches weave their tangled stories.
> Yet sometimes a smaller spirit wanders the forest,
> seeking its reflection —
> a place within the vast tree
> where its shape, its leaves, its very soul
> are mirrored perfectly.
> To find this hidden echo,
> inspect every node as a possible root
> and compare spirits branch for branch."_

---

In the kingdom of binary forests, there stood a **Great Tree** called **root** —
immense, ancient, its branches broad and many.

A smaller tree, **subRoot**, arrived at the edge of the grove.
It whispered:

> _“Am I part of the Great Tree?
> Does a copy of my structure and spirit
> lie hidden somewhere in its depths?”_

The Oracle knew this required two spells:

1. **isSameTree** — to check if two trees match perfectly
2. **isSubtree** — to walk the Great Tree and test each node

Thus began the saga of **Subtree of Another Tree**.

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

Each tree spirit held a value and two children —
forming the structure in which reflections might be found.

---

## 🌿 Spell 1 — The Mirror of Sameness

_Check if two trees are identical_

```cpp
bool isSameTree(TreeNode* a, TreeNode* b) {
    if (!a && !b) return true;
    if (!a || !b) return false;
    if (a->val != b->val) return false;
    return isSameTree(a->left, b->left) &&
           isSameTree(a->right, b->right);
}
```

Two spirits are the same if:

-   both vanish together,
-   or both exist with identical values,
-   and their left and right children match in perfect unity.

---

## 🌲 Spell 2 — The Quest for the Hidden Echo

_Search every node of root to find a matching subtree_

```cpp
bool isSubtree(TreeNode* root, TreeNode* subRoot) {
    if (!root) return false;
    if (isSameTree(root, subRoot)) return true;
    return isSubtree(root->left, subRoot) ||
           isSubtree(root->right, subRoot);
}
```

At each node of the Great Tree, the Oracle asks:

> _“Does the smaller spirit match here?”_

If not, she descends left…
and if not there, she descends right…

Until every branch is explored,
and either truth is found or denied.

---

### 🎺 The Trial of the Forest

```cpp
int main() {
    // Great Tree:
    //      3
    //     / \
    //    4   5
    //   / \
    //  1   2

    TreeNode* root = new TreeNode(3);
    root->left = new TreeNode(4);
    root->right = new TreeNode(5);
    root->left->left = new TreeNode(1);
    root->left->right = new TreeNode(2);

    // Smaller Tree:
    //    4
    //   / \
    //  1   2

    TreeNode* subRoot = new TreeNode(4);
    subRoot->left = new TreeNode(1);
    subRoot->right = new TreeNode(2);

    cout << (isSubtree(root, subRoot) ? "true" : "false") << endl; // expected: true
    return 0;
}
```

Within the Great Tree’s left branch,
the Oracle found a perfect copy of the smaller spirit —
value for value, branch for branch.

The forest murmured in recognition: **true** —
the smaller tree lived hidden inside the greater.

---

### 🧠 Memory of the Forest’s Echo

-   **isSameTree** checks for perfect structural + value equality.
-   **isSubtree** walks every node of root, looking for a matching root.
-   If subRoot matches exactly anywhere within root → true.
-   **Edge cases:**

    -   subRoot cannot be found in an empty root.
    -   subRoot empty is always considered a subtree (though problem constraints often avoid this).

-   **Time:** O(n · m) worst case — check subRoot at many nodes.
-   **Space:** O(h) recursion depth.

Thus is remembered the saga of **Subtree of Another Tree**,
where the Oracle walks the vast branches in search of a hidden echo,
and only when a perfect reflection is found
does the forest speak the truth. 🌳✨
