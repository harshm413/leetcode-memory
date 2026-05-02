## 🍪👶 _The Fair Distribution: The Assign Cookies Saga_

> \_"In the Kingdom of Children and Cookies,
> each child had a **greed factor** --
> the minimum cookie size that would make them content.
>
> Each cookie had a **size**.
>
> A child was content only if they received
> a cookie whose size was **≥ their greed factor**.
> Each child could receive **at most one cookie**.
> Each cookie could be given to **at most one child**.
>
> The Oracle was commanded:
>
> **'Maximize the number of content children.'**
>
> The Oracle saw this as a **greedy** problem --
> not DP, not graphs, just pure greedy logic.
>
> **Sort both arrays.
> Give the smallest sufficient cookie to the least greedy child.
> Move to the next child and the next cookie.**
>
> Why smallest sufficient?
> Because giving a large cookie to a child
> who would be happy with a small one
> WASTES the large cookie.
> That large cookie might be the ONLY one
> that can satisfy a greedier child later.
>
> **Be frugal. Give each child the smallest cookie
> that makes them happy. Save the big cookies
> for the greedy children.**"\_

---

This is the saga of **Assign Cookies**.

Given:
-   `g[i]` = greed factor of child `i` (minimum cookie size needed).
-   `s[j]` = size of cookie `j`.

Each child gets at most one cookie. Each cookie goes to at most one child.
Maximize the number of **content** children.

```
Input:  g = [1, 2, 3], s = [1, 1]
Output: 1   (only one child (greed=1) can be satisfied)

Input:  g = [1, 2], s = [1, 2, 3]
Output: 2   (child 1 gets cookie 1, child 2 gets cookie 2)

Input:  g = [10, 9, 8, 7], s = [5, 6, 7, 8]
Output: 2   (child greed=7 gets cookie 7, child greed=8 gets cookie 8)
```

---

## 🧠 Building the Intuition -- Why Greedy Works

Imagine the children sorted by greed (least greedy first)
and the cookies sorted by size (smallest first).

Start with the least greedy child.
Find the smallest cookie that satisfies them.
Give it to them. Move to the next child.

**Why the smallest sufficient cookie?**

If child A has greed 2 and you give them cookie size 5,
you've wasted a size-5 cookie.
Child B with greed 5 now has no cookie.

If instead you give child A cookie size 2 (just enough),
cookie size 5 is still available for child B.

**Greedy rule: always use the smallest cookie that works.**
This maximizes the cookies available for greedier children.

**Why sort both?**

Sorting lets us use a two-pointer approach.
The smallest unassigned cookie is always at the front.
The least greedy unsatisfied child is always at the front.
We just walk both arrays forward.

---

## 🧠 The Two-Pointer Algorithm

```
Sort g (children) ascending.
Sort s (cookies) ascending.

i = 0 (child pointer)
j = 0 (cookie pointer)

While both pointers are in bounds:
  If s[j] >= g[i]:
    This cookie satisfies this child.
    i++ (move to next child)
    j++ (this cookie is used up)
  Else:
    This cookie is too small for this child.
    j++ (try the next bigger cookie)

Answer = i (number of children satisfied)
```

**Why does `j` always advance?**

If the cookie is big enough → it's given away. Move both.
If the cookie is too small → it can't satisfy THIS child
or ANY greedier child (since children are sorted).
This cookie is useless. Skip it.

**Why does `i` only advance on success?**

The child stays until they get a cookie.
If the current cookie is too small, the child waits
for a bigger one. Only when satisfied do they leave.

```
Time:  O(N log N + M log M) -- sorting dominates
Space: O(1) -- two pointers (ignoring sort space)
```

---

### 📜 The Scroll of the Fair Distribution

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🍪 The Greedy Ritual

### Sort Both Arrays

```cpp
int findContentChildren(vector<int>& g, vector<int>& s) {
    sort(g.begin(), g.end());
    sort(s.begin(), s.end());
```

Children sorted by greed: least greedy first.
Cookies sorted by size: smallest first.

Why ascending? So the two-pointer approach works --
we match the smallest available cookie
to the least greedy waiting child.

---

### The Two Pointers

```cpp
    int i = 0;
    int j = 0;
```

`i` = the next child to satisfy (least greedy unsatisfied).
`j` = the next cookie to try (smallest unused).

---

### Walk Both Arrays

```cpp
    while (i < (int)g.size() && j < (int)s.size()) {
```

Continue as long as there are children to satisfy
AND cookies to give.

If either runs out → we're done.

---

### Is This Cookie Big Enough?

```cpp
        if (s[j] >= g[i]) {
            i++;
        }
```

**Yes!** This cookie satisfies this child.
The child is content. Move to the next child (`i++`).

Why only `i++` here and `j++` outside?
Because `j` ALWAYS advances (see below).
But `i` only advances when a child is actually satisfied.

> _"The child smiles. They got their cookie.
> They leave the line. The next child steps forward."_

---

### Move to the Next Cookie (Always)

```cpp
        j++;
    }
```

Whether the cookie was given away or was too small --
move to the next cookie.

**If it was given:** it's used up. Can't give it again.
**If it was too small:** it can't help this child or any greedier child.
Either way, this cookie is done.

> _"The cookie is consumed or discarded.
> Either way, it leaves the pile.
> The next cookie steps forward."_

---

### The Answer

```cpp
    return i;
}
```

`i` = the number of children who were satisfied.
Every time `i` advanced, a child got a cookie.

---

### 🎺 The Trial of the Fair Distribution

```cpp
int main() {
    vector<int> g1 = {1, 2, 3}, s1 = {1, 1};
    cout << findContentChildren(g1, s1) << endl; // expected: 1

    vector<int> g2 = {1, 2}, s2 = {1, 2, 3};
    cout << findContentChildren(g2, s2) << endl; // expected: 2

    vector<int> g3 = {10, 9, 8, 7}, s3 = {5, 6, 7, 8};
    cout << findContentChildren(g3, s3) << endl; // expected: 2

    return 0;
}
```

---

**Full trace for g = [1, 2, 3], s = [1, 1]:**

After sorting: g = [1, 2, 3], s = [1, 1].

| Step | i (child) | j (cookie) | g[i] | s[j] | s[j] >= g[i]? | Action          |
| ---- | --------- | ---------- | ---- | ---- | ------------- | --------------- |
| 1    | 0         | 0          | 1    | 1    | 1 >= 1 ✓     | i=1, j=1        |
| 2    | 1         | 1          | 2    | 1    | 1 >= 2 ✗     | j=2             |
| 3    | --        | 2          | --   | --   | j out of bounds | Stop          |

**Answer: i = 1** ✓

Child with greed 1 got cookie size 1.
Child with greed 2 couldn't get cookie size 1 (too small).
No more cookies.

---

**Full trace for g = [1, 2], s = [1, 2, 3]:**

After sorting: g = [1, 2], s = [1, 2, 3].

| Step | i | j | g[i] | s[j] | Match? | Action   |
| ---- | - | - | ---- | ---- | ------ | -------- |
| 1    | 0 | 0 | 1    | 1    | ✓      | i=1, j=1 |
| 2    | 1 | 1 | 2    | 2    | ✓      | i=2, j=2 |
| 3    | 2 | 2 | --   | --   | i out of bounds | Stop |

**Answer: i = 2** ✓

Both children satisfied. Cookie size 3 was unused (not needed).

---

**Full trace for g = [10, 9, 8, 7], s = [5, 6, 7, 8]:**

After sorting: g = [7, 8, 9, 10], s = [5, 6, 7, 8].

| Step | i | j | g[i] | s[j] | Match? | Action   |
| ---- | - | - | ---- | ---- | ------ | -------- |
| 1    | 0 | 0 | 7    | 5    | ✗      | j=1      |
| 2    | 0 | 1 | 7    | 6    | ✗      | j=2      |
| 3    | 0 | 2 | 7    | 7    | ✓      | i=1, j=3 |
| 4    | 1 | 3 | 8    | 8    | ✓      | i=2, j=4 |
| 5    | 2 | 4 | --   | --   | j out of bounds | Stop |

**Answer: i = 2** ✓

Cookies 5 and 6 were too small for anyone.
Cookie 7 went to child with greed 7.
Cookie 8 went to child with greed 8.
Children with greed 9 and 10 got nothing.

---

## 🔍 Why Greedy and Not DP?

**Greedy works when:**
The locally optimal choice (smallest sufficient cookie)
leads to the globally optimal solution.

**Why is this true here?**

Giving a child a bigger cookie than needed
can only HURT future children (fewer big cookies left).
Giving the smallest sufficient cookie
can only HELP or be neutral for future children.

So the greedy choice is always safe. No need to explore alternatives.

**DP would work** but is overkill.
This is a matching problem with a simple greedy solution.

---

## 🔍 Why Not Give the Biggest Cookie to the Greediest Child?

You could also sort descending and match from the top.
That works too -- it's the mirror greedy strategy.

Both approaches give the same answer.
The ascending approach is slightly more intuitive:
"start with the easiest child, use the smallest cookie."

---

### 🧠 Memory of the Fair Distribution Law

-   **Sort both arrays** ascending
-   **Two pointers:** `i` for children, `j` for cookies
-   **If cookie fits** (`s[j] >= g[i]`) → child satisfied, `i++`
-   **Cookie always advances** (`j++`) -- used or too small
-   **Answer:** `i` = number of content children
-   **Greedy rule:** give the smallest sufficient cookie to save big ones
-   **Time:** O(N log N + M log M) -- sorting
-   **Space:** O(1) extra
-   **Edge cases:**
    -   No cookies → 0
    -   No children → 0
    -   All cookies too small → 0
    -   More cookies than children → all children satisfied

Thus is remembered the saga of **Assign Cookies**,
where the Oracle sorted children by greed
and cookies by size --
then walked both lines with two pointers,
giving each child the smallest cookie that made them happy,
saving the big cookies for the greedy ones --
until the cookies ran out
or every child was content. 🍪👶✨
