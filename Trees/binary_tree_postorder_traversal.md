## 🌳🕊️ _The Noble Who Speaks Last: The Binary Tree Postorder Traversal Saga_

> \_"The Chronicler had now learned two laws of the Kingdom of Trees.
>
> The Law of Inorder — Left, then Self, then Right.
> The Law of Preorder — Self first, then Left, then Right.
>
> Now the eldest noble of the kingdom
> summoned the Chronicler and spoke:
>
> **'There is a third law. The most humble law.
> The law of those who wait.'**
>
> **'Before I am recorded —
> my entire left domain must speak.
> My entire right domain must speak.
> Only when both my lands have been fully chronicled
> may I finally announce myself.'**
>
> This was the Law of Postorder —
> Left before right, right before self.
> The noble speaks LAST.
>
> The Chronicler understood immediately
> why this law existed:
>
> When you delete a tree —
> you must delete the children before the parent.
> When you compute the size of a directory —
> you must sum all files inside before counting the folder.
> The parent cannot be processed
> until everything it owns has been settled.
>
> The recursive path was familiar —
> just record AFTER both children, not before or in between.
>
> But the iterative path had a beautiful trick:
>
> **Postorder is the reverse of a modified preorder.**
>
> If preorder is Root → Left → Right,
> then Root → Right → Left, reversed,
> gives Left → Right → Root — which is postorder.
>
> Push root, pop and add to front, push left then right.
> The result builds itself in reverse —
> no complex two-stack gymnastics needed.
>
> The last noble finally got to speak."\_

---

This is the saga of **Binary Tree Postorder Traversal**.

You are given the `root` of a binary tree.

Your task:

-   Return the **postorder traversal** of its nodes' values.
-   Postorder means: **Left → Right → Root**

```
        1
         \
          2
         /
        3

Postorder: [3, 2, 1]
```

---

## 🧠 The Oracle's Core Insight — The Patient Noble and the Reversal Trick

Postorder traversal has one sacred rule:

```
visit(node):
    visit(node->left)    ← entire left kingdom first
    visit(node->right)   ← entire right kingdom second
    record(node->val)    ← only then, record this noble
```

This is the traversal of **completion** —
a node is only recorded after all its descendants are done.

It is used for:

-   Deleting a tree (children must be freed before parent)
-   Evaluating expression trees (operands before operator)
-   Computing subtree sizes or heights (children before parent)

**The Reversal Trick for Iterative:**

Notice:

```
Preorder           =  Root → Left  → Right
Modified Preorder  =  Root → Right → Left   (swap push order)
Reverse of above   =  Left → Right → Root   = POSTORDER ✓
```

So instead of a complex two-pointer iterative approach —
run a modified preorder (push left before right),
collect results, then reverse at the end.

---

### 📜 The Scroll of the Tree Kingdom

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <algorithm>
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

Each noble held a value (`val`),
a path to their **left domain** (`left`),
and a path to their **right domain** (`right`).

A leaf noble — with no children — waited the most patiently of all.
They had nothing beneath them to settle first.
They were recorded the moment their domain was entered.

---

## 🌿 Path One — The Recursive Chronicle

_Dive both sides completely before recording the noble_

```cpp
void postorderRecursive(TreeNode* node, vector<int>& result) {
    if (node == nullptr) return;
```

The Chronicler arrived at a domain.
If it was empty — there was nothing here.
She turned back without a word.

Base case. The silence at the leaves and beyond.

---

```cpp
    postorderRecursive(node->left, result);
```

The Chronicler dove entirely into the **left domain** first —
not returning until every single noble
in the entire left subtree had spoken.

---

```cpp
    postorderRecursive(node->right, result);
```

Then — and only then — she dove into the **right domain**,
not returning until every noble there
had also spoken and been recorded.

---

```cpp
    result.push_back(node->val);
}
```

**Only after both children's kingdoms were fully chronicled
did this noble finally speak.**

This single line — placed at the very end —
is the entire difference between postorder and the other two.

In preorder it came first.
In inorder it came in the middle.
Here it comes **last**.

The humble noble waited. And then announced themselves.

---

## 🗂️ Path Two — The Iterative Chronicle via the Reversal Trick

_Modified preorder: push left first so right is processed first — then reverse_

```cpp
vector<int> postorderIterative(TreeNode* root) {
    vector<int> result;
    if (root == nullptr) return result;

    stack<TreeNode*> stk;
    stk.push(root);
```

The Chronicler loaded the stack with the root —
identical opening to the iterative preorder.

But what followed would be subtly different.

---

```cpp
    while (!stk.empty()) {
        TreeNode* node = stk.top();
        stk.pop();
        result.push_back(node->val);
```

The Chronicler popped the top noble
and recorded their value — same as preorder.

But the result being built here is **not the final answer yet**.
It is being assembled in **Root → Right → Left** order —
the mirror image of what we want.

---

```cpp
        if (node->left)  stk.push(node->left);
        if (node->right) stk.push(node->right);
    }
```

The push order is now **opposite to preorder**:
left is pushed BEFORE right.

This means right sits on top of the stack —
right is popped and recorded before left.

The traversal runs as: Root → Right → Left.

Compare with preorder's push order (right before left)
which runs as: Root → Left → Right.

One simple swap. The mirror is created.

---

```cpp
    reverse(result.begin(), result.end());
    return result;
}
```

Now the Chronicler reversed the entire result.

**Root → Right → Left**, reversed, becomes **Left → Right → Root**.

That is postorder. Exactly.

> _"The patient noble who was recorded first in the mirror
> finds themselves last in the true chronicle —
> which is exactly where they belong."_

No second stack needed.
No complex state tracking.
One stack, one push-order swap, one final reverse.

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
    postorderRecursive(root, res1);
    for (int v : res1) cout << v << " ";
    cout << endl; // expected: 1 3 2 5 7 6 4

    // Iterative
    vector<int> res2 = postorderIterative(root);
    for (int v : res2) cout << v << " ";
    cout << endl; // expected: 1 3 2 5 7 6 4

    return 0;
}
```

The Chronicler traced the iterative path:

**Modified preorder (Root → Right → Left) recorded as:**

| Step                             | Stack (top→bottom) | Record (before reverse) |
| -------------------------------- | ------------------ | ----------------------- |
| Start                            | [4]                | —                       |
| Pop 4, push left 2, push right 6 | [6, 2]             | **4**                   |
| Pop 6, push left 5, push right 7 | [7, 5, 2]          | **6**                   |
| Pop 7, no children               | [5, 2]             | **7**                   |
| Pop 5, no children               | [2]                | **5**                   |
| Pop 2, push left 1, push right 3 | [3, 1]             | **2**                   |
| Pop 3, no children               | [1]                | **3**                   |
| Pop 1, no children               | []                 | **1**                   |

Before reverse: `4 6 7 5 2 3 1`
After reverse: `1 3 2 5 7 6 4` ✓

The leaf nobles `1`, `3`, `5`, `7` spoke first —
they had no children to settle before themselves.
The root `4` spoke last — it owned everything.

---

### 🧠 Memory of the Patient Noble's Law

-   **Postorder = Left → Right → Root** — the noble always speaks last
-   **Recursive:** base case → recurse left → recurse right → **record last**
-   **Iterative trick:** modified preorder with push order swapped (push left BEFORE right) → collect Root→Right→Left → **reverse** → get Left→Right→Root
-   **Push order comparison:**
    -   Preorder iterative: push **right** first, then **left** → processes Left before Right ✓
    -   Postorder iterative: push **left** first, then **right** → processes Right before Left → reversal fixes it ✓
-   One line separates all three traversals in the recursive version — **where** `result.push_back` is placed
-   Postorder is used when children must be processed before their parent — deletion, evaluation, sizing
-   **Time:** O(n) — every node visited exactly once
-   **Space:** O(h) for the stack + O(n) for the result vector
    -   O(log n) stack space for balanced trees
    -   O(n) worst case for skewed trees

Thus is remembered the saga of **Binary Tree Postorder Traversal**,
where the most humble noble in the kingdom
waited patiently for every soul in their left lands
and every soul in their right lands
to speak before them —
and where the Chronicler, too impatient to implement a complex iterative solution,
discovered the beautiful mirror trick:
run the preorder in reverse push order,
collect the reflected result,
flip it — and the patient noble
finally gets to speak last,
exactly as they always deserved. 🌳🕊️✨
