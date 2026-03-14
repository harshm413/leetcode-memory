## 🌳📜 _The Law of the Left, the Root, and the Right: The Binary Tree Inorder Traversal Saga_

> \_"In the Kingdom of Trees,
> every noble stood at the center of their domain —
> their left lands stretching to the west,
> their right lands stretching to the east.
>
> The Royal Chronicler was summoned and commanded:
>
> **'Walk the entire kingdom and record every noble —
> but in a precise and sacred order:
> first, visit everything to the LEFT.
> Then, record the noble at the CENTER.
> Then, visit everything to the RIGHT.'**
>
> This was the Law of Inorder —
> Left before self, self before right.
> Always. Without exception.
>
> The Chronicler smiled — for she knew two paths
> to walk this law:
>
> The first path: **Recursion** —
> whisper the law to each noble,
> and trust that every subtree
> would obey the same law for itself.
>
> The second path: **Iteration with a Stack** —
> carry a memory scroll yourself,
> dive as deep left as possible,
> record what you find,
> then surface and turn right.
>
> Both paths visit every noble exactly once.
> Both paths produce the same sacred order.
>
> The Chronicler chose to know both —
> for wisdom lies in having two roads
> to the same destination."\_

---

This is the saga of **Binary Tree Inorder Traversal**.

You are given the `root` of a binary tree.

Your task:

-   Return the **inorder traversal** of its nodes' values.
-   Inorder means: **Left → Root → Right**

```
        1
         \
          2
         /
        3

Inorder: [1, 3, 2]
```

---

## 🧠 The Oracle's Core Insight — Left, Root, Right. Always Recursive in Nature.

Inorder traversal has one sacred rule:

```
visit(node):
    visit(node->left)    ← go left first, all the way down
    record(node->val)    ← then record this node
    visit(node->right)   ← then go right
```

This rule, applied to every node, produces values
in **ascending order for a Binary Search Tree** —
which is why inorder traversal is one of the most important
tree operations in all of computer science.

Both recursive and iterative approaches
honor the exact same rule.
The iterative version simply makes the call stack
**explicit and visible** using its own stack.

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

A noble with no left domain had `left = nullptr`.
A noble with no right domain had `right = nullptr`.
A noble with neither was a **leaf** — the end of their bloodline.

---

## 🌿 Path One — The Recursive Chronicle

_Whisper the law to every noble and trust the recursion_

```cpp
void inorderRecursive(TreeNode* node, vector<int>& result) {
    if (node == nullptr) return;
```

The Chronicler arrived at a domain.
If it was empty — there was nothing to record.
She turned back immediately.

This was the **base case** —
the anchor that stopped the infinite descent.

---

```cpp
    inorderRecursive(node->left, result);
```

Before recording this noble,
the Chronicler dove entirely into the **left domain** —
trusting that the same law would govern everything there.

She would not return until every noble
in the entire left subtree had been recorded.

---

```cpp
    result.push_back(node->val);
```

Only after the entire left domain was chronicled
did the Chronicler finally record **this noble's value**.

The left had spoken first. Now the center spoke.

---

```cpp
    inorderRecursive(node->right, result);
}
```

Finally, the Chronicler turned east —
diving into the **right domain**,
applying the same sacred law recursively
until every noble there was recorded too.

Left. Self. Right.
The law was complete for this noble.

---

## 🗂️ Path Two — The Iterative Chronicle with a Memory Stack

_Carry the call stack yourself — dive left, surface, turn right_

```cpp
vector<int> inorderIterative(TreeNode* root) {
    vector<int> result;
    stack<TreeNode*> stk;
    TreeNode* curr = root;
```

The Chronicler carried her own **Memory Stack** —
a scroll that remembered which nobles she had started visiting
but not yet recorded,
because she had dived deeper left first.

`curr` was her current position in the kingdom.

---

```cpp
    while (curr != nullptr || !stk.empty()) {
```

The march continued as long as either:

-   `curr` pointed to an unvisited domain, **OR**
-   the stack held nobles she had passed but not yet recorded.

Both conditions together mean:
**there is still kingdom left to walk.**

---

### 🏃 Dive Left as Deep as Possible

```cpp
        while (curr != nullptr) {
            stk.push(curr);
            curr = curr->left;
        }
```

The Chronicler dove left without stopping —
pushing every noble she passed onto the Memory Stack.

> _"I have seen you. I have not recorded you yet.
> Wait here on the stack while I check
> if you have any left-domain nobles
> who must be recorded before you."_

She kept diving until she hit `nullptr` —
the leftmost noble in the current subtree.
There was no one further left to visit.

---

### 📝 Surface and Record

```cpp
        curr = stk.top();
        stk.pop();
        result.push_back(curr->val);
```

The Chronicler surfaced —
popping the top noble from the Memory Stack.

This noble was now safe to record:
their entire left domain had already been chronicled.

The noble's value was written into the result.

---

### 🔄 Turn Right and Repeat

```cpp
        curr = curr->right;
    }
    return result;
}
```

`curr` was set to the noble's right child.

If the right child existed — the outer while loop would dive
left into that subtree next.

If the right child was `nullptr` — the inner dive loop
would be skipped, and the stack would surface the next waiting noble.

The cycle of **dive left → surface → record → turn right**
continued until the stack was empty and `curr` was `nullptr`.

Every noble had been recorded. In perfect inorder.

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
    inorderRecursive(root, res1);
    for (int v : res1) cout << v << " ";
    cout << endl; // expected: 1 2 3 4 5 6 7

    // Iterative
    vector<int> res2 = inorderIterative(root);
    for (int v : res2) cout << v << " ";
    cout << endl; // expected: 1 2 3 4 5 6 7

    return 0;
}
```

The Chronicler walked the kingdom both ways:

**Recursive path:**

-   Dive left from `4` → `2` → `1` → nullptr
-   Surface: record `1`, turn right (nullptr)
-   Surface: record `2`, turn right → `3`
-   Dive left from `3` → nullptr
-   Surface: record `3`, turn right (nullptr)
-   Surface: record `4`, turn right → `6`
-   Dive left from `6` → `5` → nullptr
-   Surface: record `5`, turn right (nullptr)
-   Surface: record `6`, turn right → `7`
-   Dive left from `7` → nullptr
-   Surface: record `7`

Result: `1 2 3 4 5 6 7` — ascending order, as if the BST sorted it. ✓

---

### 🧠 Memory of the Left-Root-Right Law

-   **Inorder = Left → Root → Right** — this single rule governs everything
-   For a **BST**, inorder traversal always produces a **sorted ascending sequence**
-   **Recursive:** base case `nullptr` → recurse left → record → recurse right
-   **Iterative:** `curr` + `stack` — dive left pushing all nodes → pop and record → move right → repeat
-   **While condition:** `curr != nullptr || !stk.empty()` — either unvisited nodes exist OR stack has waiting nodes
-   The iterative stack **mirrors** what the recursive call stack does automatically
-   **Time:** O(n) — every node is visited exactly once
-   **Space:** O(h) — where `h` is the height of the tree (stack depth = tree height)
    -   O(log n) for balanced trees
    -   O(n) worst case for a skewed tree (like a linked list)

Thus is remembered the saga of **Binary Tree Inorder Traversal**,
where the Royal Chronicler walked an entire kingdom
in one sacred order —
always honoring the left domain before the noble,
always honoring the noble before the right —
whether she trusted the universe to recurse for her,
or carried her own Memory Stack through the trees
herself. 🌳📜✨
