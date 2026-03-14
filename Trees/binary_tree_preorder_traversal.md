## 🌳👑 _The King Who Speaks Before All Others: The Binary Tree Preorder Traversal Saga_

> \_"In the Kingdom of Trees,
> the Chronicler had already learned the Law of Inorder —
> where the left domain spoke first,
> then the noble at the center,
> then the right domain.
>
> But the new King was not a patient man.
>
> He issued a different decree:
>
> **'I speak first.
> Before you visit my left lands.
> Before you visit my right lands.
> You record ME — and then you go.'**
>
> This was the Law of Preorder —
> Root before left, root before right.
> The king announces himself
> before his kingdom is explored.
>
> The Chronicler nodded —
> for the recursive path was almost identical
> to the inorder law,
> only the moment of recording had shifted:
> from AFTER the left dive
> to BEFORE the left dive.
>
> And the iterative path?
>
> Instead of a stack that dove left endlessly,
> the Chronicler would use a stack differently —
> push the root first,
> pop and record it,
> then push right child first, left child second —
> so left is always processed before right.
>
> One shift in timing.
> Two completely different traversal personalities.
> The same kingdom — explored in a new order."\_

---

This is the saga of **Binary Tree Preorder Traversal**.

You are given the `root` of a binary tree.

Your task:

-   Return the **preorder traversal** of its nodes' values.
-   Preorder means: **Root → Left → Right**

```
        1
         \
          2
         /
        3

Preorder: [1, 2, 3]
```

---

## 🧠 The Oracle's Core Insight — Root First, Always

Preorder traversal has one sacred rule:

```
visit(node):
    record(node->val)    ← record THIS node FIRST
    visit(node->left)    ← then explore the entire left
    visit(node->right)   ← then explore the entire right
```

This is the traversal of **exploration** —
the root is always seen before any of its descendants.

It is used for:

-   Copying a tree (you must create the parent before children)
-   Serializing a tree (root first gives you reconstruction order)
-   Printing directory structures (folder before its files)

The key difference from inorder:
**recording happens BEFORE the left dive, not after.**

Both recursive and iterative paths honor the same rule.

---

### 📜 The Scroll of the Tree Kingdom

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

### 🧱 The Structure of a Noble's Domain

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

Each noble (`TreeNode`) held a value (`val`),
a path to their **left domain** (`left`),
and a path to their **right domain** (`right`).

A noble with no descendants on either side was a **leaf** —
the end of their bloodline.

---

## 🌿 Path One — The Recursive Chronicle

_Record the noble BEFORE diving into their lands_

```cpp
void preorderRecursive(TreeNode* node, vector<int>& result) {
    if (node == nullptr) return;
```

The Chronicler arrived at a domain.
If it was empty — nothing to record, nothing to explore.
She turned back immediately.

The base case. The anchor. The silence at the edge of the kingdom.

---

```cpp
    result.push_back(node->val);
```

**This single line is what separates preorder from inorder.**

In inorder, recording happened AFTER the left dive.
Here, the noble speaks **immediately upon arrival** —
before the Chronicler has taken a single step into their lands.

The king announces himself at the door.

---

```cpp
    preorderRecursive(node->left, result);
```

Only after recording this noble
did the Chronicler dive into the **left domain** —
trusting the same law would govern everything there.

She would not return until every noble
in the entire left subtree had been recorded.

---

```cpp
    preorderRecursive(node->right, result);
}
```

Finally, the Chronicler turned east
into the **right domain** —
applying the same law recursively
until every noble there was recorded too.

Root. Left. Right.
The king always spoke first.

---

## 🗂️ Path Two — The Iterative Chronicle with a Stack

_Push root, pop and record, push right then left — left always wins_

```cpp
vector<int> preorderIterative(TreeNode* root) {
    vector<int> result;
    if (root == nullptr) return result;

    stack<TreeNode*> stk;
    stk.push(root);
```

The Chronicler loaded the Memory Stack
with the `root` node — ready to begin.

The root was the first to be pushed
because it is the first to be recorded.

---

```cpp
    while (!stk.empty()) {
```

The march continued as long as the stack held
any noble waiting to be processed.

---

### 📝 Pop and Record Immediately

```cpp
        TreeNode* node = stk.top();
        stk.pop();
        result.push_back(node->val);
```

The Chronicler popped the top noble from the stack
and **immediately recorded their value**.

This is the soul of preorder —
the moment a node is popped, it is recorded.
No waiting. No checking left first.
The king speaks the instant he is reached.

---

### 📌 Push Right First, Then Left

```cpp
        if (node->right) stk.push(node->right);
        if (node->left)  stk.push(node->left);
    }
    return result;
}
```

After recording, the Chronicler pushed the noble's children
onto the stack — but in a **deliberate order**:

**Right is pushed FIRST. Left is pushed SECOND.**

Why? Because the stack is LIFO — last in, first out.

The left child, pushed last, sits on top of the stack —
so it will be popped and processed **before** the right child.

This ensures the traversal honors **Left before Right**
in the recursive spirit —
even though we process things iteratively.

> _"To make left come first in a stack,
> you must push it last.
> The stack reverses the order of pushing.
> Push right, then left — left emerges first."_

This is the critical insight that makes
the iterative preorder different from the iterative inorder.
No inner dive loop needed.
Just push, pop, record, push children.

---

### 🎺 The Trial of the Kingdom

```cpp
int main() {
    //       4
    //      / \
    //     2   6
    //    / \ / \
    //   1  3 5  7

    TreeNode* root        = new TreeNode(4);
    root->left            = new TreeNode(2);
    root->right           = new TreeNode(6);
    root->left->left      = new TreeNode(1);
    root->left->right     = new TreeNode(3);
    root->right->left     = new TreeNode(5);
    root->right->right    = new TreeNode(7);

    // Recursive
    vector<int> res1;
    preorderRecursive(root, res1);
    for (int v : res1) cout << v << " ";
    cout << endl; // expected: 4 2 1 3 6 5 7

    // Iterative
    vector<int> res2 = preorderIterative(root);
    for (int v : res2) cout << v << " ";
    cout << endl; // expected: 4 2 1 3 6 5 7

    return 0;
}
```

The Chronicler walked the kingdom:

**Iterative stack trace:**

| Step                  | Stack (top→bottom) | Record |
| --------------------- | ------------------ | ------ |
| Start                 | [4]                | —      |
| Pop 4, push 6, push 2 | [2, 6]             | **4**  |
| Pop 2, push 3, push 1 | [1, 3, 6]          | **2**  |
| Pop 1, no children    | [3, 6]             | **1**  |
| Pop 3, no children    | [6]                | **3**  |
| Pop 6, push 7, push 5 | [5, 7]             | **6**  |
| Pop 5, no children    | [7]                | **5**  |
| Pop 7, no children    | []                 | **7**  |

Result: `4 2 1 3 6 5 7` ✓

The root `4` spoke first, as the king commanded.
Then the entire left kingdom (`2, 1, 3`),
then the entire right kingdom (`6, 5, 7`).

---

### 🧠 Memory of the Root-First Law

-   **Preorder = Root → Left → Right** — record the node the instant you arrive
-   **Recursive:** base case `nullptr` → **record first** → recurse left → recurse right
-   **Iterative:** push root → while stack not empty: pop & record → push right child → push left child
-   **The stack reversal trick:** push right BEFORE left so left sits on top and is processed first
-   **Key contrast with inorder:** inorder dives left before recording; preorder records before diving
-   **Key contrast with iterative inorder:** no inner dive loop — just pop, record, push children
-   Used for tree **copying**, **serialization**, and **directory-style printing**
-   **Time:** O(n) — every node visited exactly once
-   **Space:** O(h) — stack holds at most one full path from root to leaf at any time
    -   O(log n) for balanced trees
    -   O(n) worst case for a skewed tree

Thus is remembered the saga of **Binary Tree Preorder Traversal**,
where the king spoke before all others —
recording himself the moment the Chronicler arrived,
before a single step was taken into his lands —
and where the iterative stack honored this impatient decree
by pushing right before left,
letting the stack's natural reversal
ensure the left kingdom was always explored first. 🌳👑✨
