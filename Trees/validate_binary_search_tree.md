## ⚖️ _The Law of Ordered Realms: The Validate Binary Search Tree Saga_

> _"In the Realm of the Binary Trees,
> not all forests obey the ancient law.
> A true BST is a kingdom of order —
> where every left descendant is lesser,
> every right descendant is greater,
> and every node guards a sacred interval.
> To judge such a kingdom,
> carry bounds like lanterns,
> and ensure no spirit steps outside its destined range."_

---

In the Forest of Order stood a tree whose rulers claimed it obeyed the ancient rules of the **Binary Search Tree**.
Yet whispers spread: _was the order real, or merely an illusion?_

The Oracle was summoned to deliver judgment.
But she knew the law was deeper than comparing parent and child.
Every node must obey a **global destiny**:

-   All nodes in the left subtree must be **strictly less** than the current node.
-   All nodes in the right subtree must be **strictly greater**.
-   These rules apply not just locally — but throughout every branch.

The only way to judge was to walk the tree with **min and max bounds**,
ensuring each node stayed within its rightful territory.

Thus began the saga of **Validate Binary Search Tree**.

---

### 📜 The Scroll of Tree Spirits

```cpp
#include <iostream>
#include <climits>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int v) : val(v), left(NULL), right(NULL) {}
};
```

Each spirit held a value and two branches,
but only the Law of Ordered Realms could determine if their structure was truly a BST.

---

## 🕯️ The Oracle’s Judgment Ritual

_Carry bounds and test each node_

```cpp
bool validate(TreeNode* node, long minVal, long maxVal) {
    if (!node) return true;
```

An empty tree obeyed the law by default — silence has no disorder.

---

### 🔒 The Bound Check

```cpp
    if (node->val <= minVal || node->val >= maxVal) return false;
```

Every spirit must stay **strictly** within its allowed interval:
`(minVal, maxVal)`

If it stepped outside — or touched the boundary —
the law was broken.

---

### 🌿 Descend with Updated Bounds

```cpp
    return validate(node->left, minVal, node->val) &&
           validate(node->right, node->val, maxVal);
}
```

As the Oracle descended:

-   The **left child** received a tighter _max_ bound: everything must be less than the current node.
-   The **right child** received a tighter _min_ bound: everything must be greater.

Thus every spirit was judged according to its place in the grand order.

---

## ⚖️ The Public Verdict Function

```cpp
bool isValidBST(TreeNode* root) {
    return validate(root, LONG_MIN, LONG_MAX);
}
```

The Oracle invoked the ritual with infinite bounds,
letting the tree prove itself worthy.

---

### 🎺 The Trial of the Ordered Tree

```cpp
int main() {
    //      5
    //     / \
    //    1   7
    //       / \
    //      6   8

    TreeNode* root = new TreeNode(5);
    root->left = new TreeNode(1);
    root->right = new TreeNode(7);
    root->right->left = new TreeNode(6);
    root->right->right = new TreeNode(8);

    cout << (isValidBST(root) ? "true" : "false") << endl; // expected: true
    return 0;
}
```

The Oracle walked through the branches:

-   `1 < 5` → allowed
-   `7 > 5` → allowed
-   `6` lies between `(5,7)` → allowed
-   `8 > 7` → allowed

The kingdom held perfect order.
She pronounced the verdict: **true**.

---

### 🧠 Memory of the Ordered Realms

-   A BST must obey **strict** ordering throughout the entire tree.
-   Use **bounds** (minVal, maxVal) to ensure each node stays in its allowed interval.
-   Left subtree → updates the _max_ bound.
-   Right subtree → updates the _min_ bound.
-   **Time:** O(n) — visit every node.
-   **Space:** O(h) — recursion depth.

Thus is remembered the saga of **Validate Binary Search Tree**,
where the Oracle carries the lanterns of bounds into the forest
to ensure every spirit stands in its rightful place
under the ancient Law of Order. ⚖️🌲✨
