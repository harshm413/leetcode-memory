## 🍬👶 _The Two Passes of Fairness: The Candy Saga_

> \_"In a line of children,
> each child had a rating.
> The Oracle had to distribute candies
> following two rules:
>
> **Rule 1:** Every child gets at least 1 candy.
> **Rule 2:** A child with a HIGHER rating than their neighbor
> must get MORE candies than that neighbor.
>
> The Oracle was commanded:
>
> **'What is the MINIMUM total candies needed?'**
>
> The Oracle could not satisfy both neighbors simultaneously
> in a single pass. A child might need more than its left neighbor
> AND more than its right neighbor.
>
> She devised the **two-pass** strategy:
>
> **Pass 1 (Left to Right):**
> If a child's rating > left neighbor's rating →
> give it one more candy than the left neighbor.
>
> **Pass 2 (Right to Left):**
> If a child's rating > right neighbor's rating →
> give it at least one more candy than the right neighbor.
> (Take the max of current value and the right-based value.)
>
> After both passes, every child satisfies BOTH rules.
> Sum all candies → the answer."\_

---

This is the saga of **Candy**.

Given an array `ratings` of children's ratings:

-   Each child gets at least 1 candy.
-   Higher-rated child gets more candies than their lower-rated neighbor.
-   Return the **minimum** total candies.

```
Input:  ratings = [1, 0, 2]
Output: 5   (candies: [2, 1, 2])

Input:  ratings = [1, 2, 2]
Output: 4   (candies: [1, 2, 1])

Input:  ratings = [1, 3, 2, 2, 1]
Output: 7   (candies: [1, 2, 1, 2, 1])
```

---

## 🧠 The Oracle's Core Insight -- Two Passes

A single pass can't handle both directions.
Consider `[1, 3, 2]`:
-   Left pass: child 1 (rating 3) > child 0 (rating 1) → candy[1] = 2.
-   But child 1 (rating 3) > child 2 (rating 2) → candy[1] must also > candy[2].
-   The right-side constraint isn't visible in a left-to-right pass.

**Solution: two passes.**

**Pass 1 (Left → Right):** satisfy the LEFT neighbor constraint.
If `ratings[i] > ratings[i-1]` → `candy[i] = candy[i-1] + 1`.

**Pass 2 (Right → Left):** satisfy the RIGHT neighbor constraint.
If `ratings[i] > ratings[i+1]` → `candy[i] = max(candy[i], candy[i+1] + 1)`.

The `max` in pass 2 ensures we don't violate the left constraint
established in pass 1.

```
Time:  O(n) -- two passes
Space: O(n) -- candy array
```

---

### 📜 The Scroll of the Two Passes

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🍬 The Two-Pass Ritual

### Initialize -- Everyone Gets 1 Candy

```cpp
int candy(vector<int>& ratings) {
    int n = ratings.size();
    vector<int> candies(n, 1);
```

Every child starts with 1 candy (minimum requirement).

---

### ➡️ Pass 1 -- Left to Right

```cpp
    for (int i = 1; i < n; i++) {
        if (ratings[i] > ratings[i - 1]) {
            candies[i] = candies[i - 1] + 1;
        }
    }
```

If a child has a higher rating than the child to its LEFT →
it must have more candies than the left child.
Give it exactly one more.

After this pass: every child satisfies the left-neighbor rule.
But the right-neighbor rule may be violated.

> _"Walk left to right.
> If you're better than the one before you --
> you deserve one more candy than them."_

---

### ⬅️ Pass 2 -- Right to Left

```cpp
    for (int i = n - 2; i >= 0; i--) {
        if (ratings[i] > ratings[i + 1]) {
            candies[i] = max(candies[i], candies[i + 1] + 1);
        }
    }
```

If a child has a higher rating than the child to its RIGHT →
it must have more candies than the right child.
Take the MAX of the current value (from pass 1) and `right + 1`.

**Why max?** The child might already have enough candies from pass 1.
We can't reduce it (that would violate the left rule).
We can only increase it if the right rule demands more.

> _"Walk right to left.
> If you're better than the one after you --
> you deserve at least one more than them.
> But never less than what the left pass already gave you."_

---

### 📊 Sum All Candies

```cpp
    int total = 0;
    for (int c : candies) total += c;
    return total;
}
```

The minimum total candies = sum of the candy array.

---

### 🎺 The Trial of the Two Passes

```cpp
int main() {
    vector<int> r1 = {1, 0, 2};
    cout << candy(r1) << endl; // expected: 5

    vector<int> r2 = {1, 2, 2};
    cout << candy(r2) << endl; // expected: 4

    vector<int> r3 = {1, 3, 2, 2, 1};
    cout << candy(r3) << endl; // expected: 7

    return 0;
}
```

---

**Full trace for ratings = [1, 0, 2]:**

**Initial:** candies = [1, 1, 1].

**Pass 1 (Left → Right):**
-   i=1: ratings[1]=0 > ratings[0]=1? No. Skip.
-   i=2: ratings[2]=2 > ratings[1]=0? Yes. candies[2] = candies[1]+1 = 2.

After pass 1: candies = [1, 1, 2].

**Pass 2 (Right → Left):**
-   i=1: ratings[1]=0 > ratings[2]=2? No. Skip.
-   i=0: ratings[0]=1 > ratings[1]=0? Yes. candies[0] = max(1, 1+1) = 2.

After pass 2: candies = [2, 1, 2].

**Total = 2 + 1 + 2 = 5** ✓

---

**Full trace for ratings = [1, 3, 2, 2, 1]:**

**Initial:** candies = [1, 1, 1, 1, 1].

**Pass 1 (Left → Right):**
-   i=1: 3 > 1 → candies[1] = 2.
-   i=2: 2 > 3? No.
-   i=3: 2 > 2? No. (equal, not greater)
-   i=4: 1 > 2? No.

After pass 1: candies = [1, 2, 1, 1, 1].

**Pass 2 (Right → Left):**
-   i=3: 2 > 1? Yes. candies[3] = max(1, 1+1) = 2.
-   i=2: 2 > 2? No. (equal)
-   i=1: 3 > 2? Yes. candies[1] = max(2, 1+1) = max(2, 2) = 2. (no change)
-   i=0: 1 > 3? No.

After pass 2: candies = [1, 2, 1, 2, 1].

**Total = 1 + 2 + 1 + 2 + 1 = 7** ✓

---

**Trace for ratings = [1, 2, 2]:**

**Pass 1:** candies = [1, 2, 1]. (i=1: 2>1 → 2. i=2: 2>2? No.)
**Pass 2:** candies = [1, 2, 1]. (i=1: 2>2? No. i=0: 1>2? No.)

**Total = 4** ✓

Note: equal ratings do NOT require more candies.
Child 1 (rating 2) and child 2 (rating 2) can have different candy counts.

---

**Trace for ratings = [5, 4, 3, 2, 1] (strictly decreasing):**

**Pass 1:** No increases left-to-right. candies = [1, 1, 1, 1, 1].
**Pass 2:**
-   i=3: 2>1 → max(1, 2) = 2.
-   i=2: 3>2 → max(1, 3) = 3.
-   i=1: 4>3 → max(1, 4) = 4.
-   i=0: 5>4 → max(1, 5) = 5.

After pass 2: candies = [5, 4, 3, 2, 1].

**Total = 15** ✓ (the "worst case" -- a descending sequence).

---

## 🔍 Why Two Passes Are Needed

**Single left-to-right pass fails:**
`ratings = [1, 3, 2]` → pass 1 gives [1, 2, 1].
But child 1 (rating 3) > child 2 (rating 2), so candy[1] should > candy[2].
candy[1]=2 > candy[2]=1 ✓. Happens to work here.

But `ratings = [3, 2, 1]` → pass 1 gives [1, 1, 1].
Child 0 (rating 3) > child 1 (rating 2) → needs more. NOT satisfied.
Pass 2 fixes it: [3, 2, 1].

**The left pass handles increasing sequences.
The right pass handles decreasing sequences.
Together they handle everything.**

---

## 🔍 Why `max` in Pass 2 (Not Just Assign)

If pass 1 gave `candy[i] = 5` (because of a long increasing sequence to the left),
and pass 2 computes `candy[i+1] + 1 = 3`,
we can't reduce candy[i] to 3 -- that would violate the left rule.

`max(5, 3) = 5` -- keep the larger value. Both rules satisfied.

---

## 🔍 Equal Ratings -- No Constraint

`ratings[i] == ratings[i-1]` does NOT require any relationship.
Equal-rated neighbors can have any candy counts.
Only STRICTLY greater ratings create constraints.

---

### 🧠 Memory of the Two Passes Law

-   **Initialize all to 1** (minimum candy per child)
-   **Pass 1 (Left → Right):** if `ratings[i] > ratings[i-1]` → `candy[i] = candy[i-1] + 1`
-   **Pass 2 (Right → Left):** if `ratings[i] > ratings[i+1]` → `candy[i] = max(candy[i], candy[i+1] + 1)`
-   **Answer:** sum of candy array
-   **Why max in pass 2?** Don't violate the left constraint from pass 1
-   **Equal ratings** → no constraint (can have different candies)
-   **Time:** O(n). **Space:** O(n).
-   **Edge cases:**
    -   All same ratings → n candies (all get 1)
    -   Strictly increasing → 1+2+3+...+n = n(n+1)/2
    -   Strictly decreasing → same as increasing (reversed)
    -   Single child → 1

Thus is remembered the saga of **Candy**,
where the Oracle walked the line of children twice --
once left to right, rewarding those better than their left neighbor,
once right to left, rewarding those better than their right neighbor --
taking the maximum at each child to satisfy both directions --
until every child had the minimum candies
that honored both rules of fairness. 🍬👶✨
