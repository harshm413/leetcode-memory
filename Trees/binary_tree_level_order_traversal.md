## 🌾 _The Song of Rising Levels: The Binary Tree Level Order Traversal Saga_

> _"A tree is not only depth and roots —
> it is a rising hymn, sung layer by layer.
> From ancestor to children,
> from children to grandchildren,
> each level lifts its voice in turn.
> To hear the full chorus,
> walk the tree not downward alone,
> but outward — one ring at a time."_

---

In the Forest of Ancients, the Oracle wished to hear the **Hymn of Levels** —
the order in which the tree’s spirits appear **from top to bottom**,
**left to right**,
layer by layer.

This was no solitary descent;
instead, the Oracle used a **queue**, a long line of waiting spirits,
to visit each level like steps on a staircase.

Thus began the saga of **Binary Tree Level Order Traversal**.

---

### 📜 The Scroll of Tree Spirits

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int v) : val(v), left(NULL), right(NULL) {}
};
```

Each spirit held a number,
and two branches extending to heirs below.

---

## 🎒 The Oracle’s Level-Walking Ritual

_Use a queue to traverse breadth-first_

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> res;
    if (!root) return res;
```

If the tree was empty, no hymn could be sung.

---

```cpp
    queue<TreeNode*> q;
    q.push(root);
```

The Oracle summoned a **queue**,
placing the root at the front —
the first singer of the first level.

---

### 🎶 Visiting Level by Level

```cpp
    while (!q.empty()) {
        int size = q.size();
        vector<int> level;
```

Each iteration, the Oracle measured the number of spirits standing in the queue —
these formed a **single level** of the tree.

---

```cpp
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);
```

She called each spirit of the current level forward,
recording their values in order —
leftmost to rightmost.

---

```cpp
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        res.push_back(level);
    }
```

As each spirit stepped forward,
the Oracle summoned its children to the end of the queue —
ready to sing in the next level.

When all nodes of the current layer had sung,
the level was added to the final hymn.

The ritual continued until all branches had been explored.

---

```cpp
    return res;
}
```

The hymn of the entire tree was returned,
layer by harmonious layer.

---

### 🎺 The Trial of the Singing Tree

```cpp
int main() {
    //      3
    //     / \
    //    9  20
    //       / \
    //      15  7

    TreeNode* root = new TreeNode(3);
    root->left = new TreeNode(9);
    root->right = new TreeNode(20);
    root->right->left = new TreeNode(15);
    root->right->right = new TreeNode(7);

    vector<vector<int>> ans = levelOrder(root);

    for (auto &level : ans) {
        for (int v : level) cout << v << " ";
        cout << endl;
    }
    return 0;
}
```

The tree sang its hymn like this:

-   Level 1: `3`
-   Level 2: `9 20`
-   Level 3: `15 7`

The Oracle heard the rising harmony,
each generation lifting the song higher.

---

### 🧠 Memory of the Rising Hymn

-   Use a **queue** to visit nodes level by level (BFS).
-   Each loop iteration handles **one full level**.
-   Children of current level are enqueued for the next.
-   Result is a list of lists — each list a level.
-   **Time:** O(n) — each node visited once.
-   **Space:** O(n) — queue holds up to one level at a time.

Thus is remembered the saga of **Binary Tree Level Order Traversal**,
where the Oracle listens to the forest sing not in depth,
but in rising tiers of harmony,
one level at a time. 🌾✨
