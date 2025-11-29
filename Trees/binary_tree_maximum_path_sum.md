## ⚡ _The Thunder Path: The Binary Tree Maximum Path Sum Saga_

> _"Within the Forest of Storms,
> each node holds a spark — some dim, some blazing.
> A path may twist anywhere in the tree,
> rising from any root, ending at any leaf,
> but when storms align,
> lightning flows through connected ancestors
> and the brightest possible path is revealed."_

---

In the land of binary trees, every spirit carried a value—
some positive, radiating power,
others negative, weighed down by shadows.

The Oracle of Storms was asked:

> _“Among all paths in this tree—
> paths that may start and end **anywhere**,
> as long as they travel through parent-child connections—
> which path yields the **maximum sum of lightning**?”_

This was no simple descent or single-root journey.
The Oracle needed to consider that the best path might:

-   rise from a left branch,
-   pass through a root,
-   descend into a right branch,
-   or even exist entirely in a single subtree.

At each node, she sought two truths:

1. **The maximum _downward_ path carrying lightning upward**
2. **The maximum _full_ path passing _through_ that node**

Each storm spirit contributed its value,
but negative sparks could be abandoned to let brighter paths shine.

Thus began the saga of **Binary Tree Maximum Path Sum**.

---

### 📜 The Scroll of Storm Spirits

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

Each node bears a number—
its storm power—
and branches to left and right children.

---

## ⚡ The Oracle’s Lightning Ritual

_Return downward power, update global maximum_

```cpp
int maxGain(TreeNode* node, int& globalMax) {
    if (!node) return 0;
```

If no spirit exists here,
no lightning flows upward.

---

### 🌩️ Step 1 — Seek Lightning From Children

```cpp
    int leftGain = max(0, maxGain(node->left, globalMax));
    int rightGain = max(0, maxGain(node->right, globalMax));
```

The Oracle gathered lightning from left and right subtrees—
but only if positive.
Negative paths were abandoned, for they dimmed the storm.

`leftGain` = maximum lightning descending from the left
`rightGain` = same for the right

If either was negative, she treated it as zero.

---

### ⚡ Step 2 — Consider a Storm Passing _Through_ This Node

```cpp
    int throughPath = node->val + leftGain + rightGain;
    globalMax = max(globalMax, throughPath);
```

This node could be the **center** of the greatest storm—
a blazing strike combining:

-   left lightning
-   its own spark
-   right lightning

The Oracle updated the **global maximum lightning** seen anywhere.

---

### 🔥 Step 3 — Return the Best Upward Path

```cpp
    return node->val + max(leftGain, rightGain);
}
```

But only **one branch** can continue upward.
Thus, she returned:

`node->val + max(leftGain, rightGain)`

This value flowed upward as a single path of lightning,
ready to connect with ancestors.

---

## 🌪️ The Invocation of the Full Storm

```cpp
int maxPathSum(TreeNode* root) {
    int globalMax = INT_MIN;
    maxGain(root, globalMax);
    return globalMax;
}
```

The Oracle invoked the ritual,
allowing every node to audition as a storm center.
Whichever storm shone brightest became the final answer.

---

### 🎺 The Trial of the Storm-Touched Tree

```cpp
int main() {
    //      -10
    //      /  \
    //     9   20
    //        /  \
    //       15   7

    TreeNode* root = new TreeNode(-10);
    root->left = new TreeNode(9);
    root->right = new TreeNode(20);
    root->right->left = new TreeNode(15);
    root->right->right = new TreeNode(7);

    cout << maxPathSum(root) << endl; // expected: 42 (15 + 20 + 7)
    return 0;
}
```

Lightning raced through the tree.
The greatest storm surged from:

`15 → 20 → 7`

A total of **42**,
the brightest path in the Forest of Storm Spirits.

---

### 🧠 Memory of the Thunder Path

-   Maximum path can start and end anywhere.
-   At each node:

    -   **leftGain = max(0, left subtree)**
    -   **rightGain = max(0, right subtree)**

-   A full storm through the node =
    `node->val + leftGain + rightGain`
-   Update global maximum with this full storm.
-   Return upward lightning =
    `node->val + max(leftGain, rightGain)`
-   **Time:** O(n) — each node visited once.
-   **Space:** O(h) recursion depth.

Thus is remembered the saga of **Binary Tree Maximum Path Sum**,
where lightning flows through roots and branches,
and the Oracle seeks the brightest possible storm
hidden within the ancient forest. ⚡🌲✨
