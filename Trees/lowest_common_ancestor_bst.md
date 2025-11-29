## 🦉 _The Fork in the Ancestral Road: The Lowest Common Ancestor of a BST Saga_

> _"In the Realm of Ordered Branches,
> every spirit knows its place —
> smaller ones flow left, greater ones right.
> When two descendants call for guidance,
> the Ancient Guardian seeks the node
> where their paths last met
> before diverging into separate destinies."_

---

In the sacred grove of **Binary Search Trees**, every value obeys the Law of Order:

-   Left child < parent
-   Right child > parent

This law shaped every branch, every fork.

One day, two young spirits — **p** and **q** — sought the Oracle:

> _“Who is our Lowest Common Ancestor?
> The deepest node that is ancestor to both of us?”_

The Oracle understood that in a BST, the answer lies in the direction of values themselves.

Thus began the saga of **Lowest Common Ancestor of a Binary Search Tree (BST)**.

---

### 📜 The Scroll of Ordered Spirits

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

Each spirit carried a number and obeyed the BST’s ancient ordering.

---

## 🦉 The Oracle’s Path-Finding Ritual

_Follow values until the paths of p and q diverge_

```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    TreeNode* curr = root;
```

The Oracle began at the tree’s crown,
for every ancestor’s path starts from the root.

---

### 🌄 Step 1 — Both Spirits Lie Left?

```cpp
    while (curr) {
        if (p->val < curr->val && q->val < curr->val) {
            curr = curr->left;
        }
```

If both spirits’ values were smaller than the current node,
the Oracle knew they dwelled **together in the left forest**.
She descended peacefully down the left branch.

---

### 🌅 Step 2 — Both Spirits Lie Right?

```cpp
        else if (p->val > curr->val && q->val > curr->val) {
            curr = curr->right;
        }
```

If both spirits were greater,
their roots lay **together in the right forest**.
The Oracle walked that path instead.

---

### 🌿 Step 3 — Their Paths Split Here

```cpp
        else {
            return curr;
        }
    }
    return NULL;
}
```

If neither condition held —
one spirit was on the left and the other on the right,
or one was exactly the current node —

then **curr** was the fork where their destinies diverged.
This node was their **Lowest Common Ancestor**.

---

### 🎺 The Trial of the Ordered Tree

```cpp
int main() {
    //      6
    //     / \
    //    2   8
    //   / \ / \
    //  0  4 7  9
    //    / \
    //   3   5

    TreeNode* root = new TreeNode(6);
    root->left = new TreeNode(2);
    root->right = new TreeNode(8);
    root->left->left = new TreeNode(0);
    root->left->right = new TreeNode(4);
    root->left->right->left = new TreeNode(3);
    root->left->right->right = new TreeNode(5);
    root->right->left = new TreeNode(7);
    root->right->right = new TreeNode(9);

    TreeNode* p = root->left;        // 2
    TreeNode* q = root->left->right; // 4

    TreeNode* lca = lowestCommonAncestor(root, p, q);
    cout << lca->val << endl; // expected: 2
    return 0;
}
```

In this grove, the spirits **2** and **4**
shared a common ancestor at node **2** —
the point where both their paths still walked together
before one went deeper left and the other deeper right.

---

### 🧠 Memory of the Forked Path

-   In a BST, left < parent < right.
-   If **both p and q < curr** → move left.
-   If **both p and q > curr** → move right.
-   Otherwise → curr is the LCA (their paths diverge here).
-   **Time:** O(h) — tree height.
-   **Space:** O(1) — purely iterative.

Thus is remembered the saga of the **Lowest Common Ancestor of a BST**,
where the Oracle walks the ordered branches,
discovering the final node
where two spirits shared the same ancient path
before parting into their destined subtrees. 🌳🦉✨
