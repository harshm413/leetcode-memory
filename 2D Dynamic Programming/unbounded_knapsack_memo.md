## 🎒♾️🧠 _The Infinite Supply (Memoization): The Unbounded Knapsack Saga_

> \_"In the Kingdom of Treasures,
> a thief stood before a collection of items.
> Each item had a **weight** and a **value**.
>
> The thief carried a knapsack with a **weight capacity**.
>
> In the 0/1 Knapsack, each item existed only once.
> Pick it or leave it. No second chances.
>
> But now the rules changed:
>
> **Every item has UNLIMITED copies.**
>
> The thief could take the same item
> as many times as he wanted --
> as long as the total weight didn't exceed the capacity.
>
> The Oracle was commanded:
>
> **'What is the maximum total value
> the thief can carry?'**
>
> The Oracle saw this as the generalization
> of everything she had learned:
>
> Coin Change I was unbounded knapsack with MINIMIZE.
> Coin Change II was unbounded knapsack with COUNT.
> Now she faced the pure form: unbounded knapsack with MAXIMIZE.
>
> Same structure. Different operation.
> The skeleton was always the same:
> **use this item (stay) or skip to the next (move on).**"\_

---

This is the saga of **Unbounded Knapsack (Memoization)**.

Given:
-   `n` items, each with `weight[i]` and `value[i]`.
-   A knapsack with capacity `W`.
-   Each item can be used **unlimited** times.
-   Maximize the **total value** without exceeding capacity.

```
Input:  weight = [2, 4, 6], value = [5, 11, 13], W = 10
Output: 27  (item 0 twice + item 1 once: 2+2+4=8 weight, 5+5+11=21... 
             hmm, let me recalculate.
             item 1 twice: 4+4=8 weight, 11+11=22.
             item 1 twice + item 0 once: 4+4+2=10 weight, 11+11+5=27.)
Output: 27 ✓

Input:  weight = [1, 3, 4, 5], value = [1, 4, 5, 7], W = 8
Output: 11  (item 1 twice + item 0 twice: 3+3+1+1=8, 4+4+1+1=10... 
             or item 3 + item 1: 5+3=8, 7+4=11.)
Output: 11 ✓
```

---

## 🧠 Building the Intuition -- 0/1 vs Unbounded

In **0/1 Knapsack**, after including an item, you move to the next:
```
include: value[i] + solve(i + 1, remaining - weight[i])
```

In **Unbounded Knapsack**, after including an item, you STAY:
```
include: value[i] + solve(i, remaining - weight[i])
```

**That one change -- `i + 1` becomes `i` -- is the entire difference.**

Why stay? Because the item has infinite copies.
Using it once doesn't exhaust it.
You might want to use it again.

The skip choice is the same in both:
```
skip: solve(i + 1, remaining)
```

Move to the next item. Never come back to this one.

---

## 🧠 The Recurrence

```
solve(index, capacity) = max(
    value[index] + solve(index, capacity - weight[index]),   ← USE (stay)
    solve(index + 1, capacity)                                ← SKIP (move on)
)
```

**USE:** take this item's value. Reduce capacity. Stay at same index.
**SKIP:** move to next item. Capacity unchanged.
**Take the max** -- we want maximum value.

**Base cases:**

**`index == n`:** no more items. Value = 0.

**`capacity == 0`:** no more room. Value = 0.

---

### 📜 The Scroll of the Infinite Supply

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🎒 Setting Up the Cache

```cpp
int unboundedKnapsack(vector<int>& weight, vector<int>& value, int W) {
    int n = weight.size();
    vector<vector<int>> memo(n, vector<int>(W + 1, -1));
```

2D cache: `memo[index][capacity]`.
`-1` = not yet computed.

---

```cpp
    return solve(0, W, weight, value, memo);
}
```

Start at item 0 with full capacity.

---

## 🔮 The Recursive Thinking

```cpp
int solve(int index, int cap, vector<int>& weight, vector<int>& value,
          vector<vector<int>>& memo) {
```

"What's the maximum value using items `index` to `n-1`
with remaining capacity `cap`?"

---

### 🛑 No more items or no more room

```cpp
    if (index == (int)weight.size() || cap == 0) return 0;
```

No items left → can't add value.
No capacity left → can't carry anything.
Either way: 0.

---

### 📖 Cache check

```cpp
    if (memo[index][cap] != -1) return memo[index][cap];
```

---

### 🎒 Choice 1 -- USE This Item (Stay)

```cpp
    int use = 0;
    if (weight[index] <= cap) {
        use = value[index] + solve(index, cap - weight[index], weight, value, memo);
    }
```

**Can I carry this item?** Only if it fits (`weight[index] <= cap`).

**If I take it:**
I gain `value[index]`.
My remaining capacity shrinks by `weight[index]`.
I STAY at the same index -- I can take this item again.

Why `solve(index, ...)` and not `solve(index + 1, ...)`?

Because the item has unlimited copies.
Taking it once doesn't remove it from the shelf.
I might want to fill my entire knapsack with copies of this one item.

> _"The thief takes the golden idol from the shelf.
> But the shelf magically refills.
> Another identical idol appears.
> He can take it again. And again.
> Until his bag is full."_

---

### 🚶 Choice 2 -- SKIP This Item (Move On)

```cpp
    int skip = solve(index + 1, cap, weight, value, memo);
```

Done with this item type forever.
Move to the next item. Capacity unchanged.

Maybe a different item gives better value-per-weight.

> _"The thief walks past this item.
> He will never return to it.
> His eyes are on the next shelf."_

---

### ⚖️ Take the Maximum

```cpp
    memo[index][cap] = max(use, skip);
    return memo[index][cap];
}
```

The thief picks whichever choice gives more value.

---

### 🎺 The Trial of the Infinite Supply

```cpp
int main() {
    vector<int> w1 = {2, 4, 6}, v1 = {5, 11, 13};
    cout << unboundedKnapsack(w1, v1, 10) << endl; // expected: 27

    vector<int> w2 = {1, 3, 4, 5}, v2 = {1, 4, 5, 7};
    cout << unboundedKnapsack(w2, v2, 8) << endl; // expected: 11

    return 0;
}
```

---

**Full trace for weight=[2,4,6], value=[5,11,13], W=10:**

The optimal: item 1 (w=4, v=11) twice + item 0 (w=2, v=5) once.
Total weight: 4+4+2 = 10. Total value: 11+11+5 = 27.

```
solve(0, 10):
  USE item 0 (w=2, v=5): 5 + solve(0, 8)
    USE item 0: 5 + solve(0, 6)
      USE item 0: 5 + solve(0, 4)
        USE item 0: 5 + solve(0, 2)
          USE item 0: 5 + solve(0, 0) → 0
        solve(0, 2) via use = 5. via skip = solve(1, 2) → 0 (item 1 w=4 > 2).
        memo[0][2] = 5.
      solve(0, 4) via use = 5+5=10. via skip = solve(1, 4) = 11.
      memo[0][4] = max(10, 11) = 11.
    ... (continues exploring)

  SKIP item 0: solve(1, 10)
    USE item 1 (w=4, v=11): 11 + solve(1, 6)
      USE item 1: 11 + solve(1, 2) → 0.
      SKIP: solve(2, 6) = 13.
    solve(1, 6) = max(11, 13) = 13.
    solve(1, 10) via use = 11 + 13 = 24... 
    
    USE item 1 twice: 11 + solve(1, 6) = 11 + 13 = 24.
    Hmm, but the optimal is 27 which uses items 0 and 1 together.
```

The recursion explores all combinations. The memoization
ensures each (index, capacity) is solved once.
The final answer: **27** ✓.

---

**Trace for weight=[1,3,4,5], value=[1,4,5,7], W=8:**

Optimal: item 3 (w=5, v=7) + item 1 (w=3, v=4) = weight 8, value 11.

Or: item 1 (w=3, v=4) twice + item 0 (w=1, v=1) twice = weight 8, value 10. Worse.

**Answer: 11** ✓

---

## 🔍 The Knapsack Family -- Complete Picture

| Problem              | Items used | After include | Operation | What it finds     |
| -------------------- | ---------- | ------------- | --------- | ----------------- |
| 0/1 Knapsack         | Once       | `index + 1`   | max       | Max value         |
| Subset Sum           | Once       | `index + 1`   | OR        | Can hit target?   |
| Count Subsets        | Once       | `index + 1`   | +         | Ways to hit target|
| **Unbounded Knapsack** | **Unlimited** | **`index` (stay)** | **max** | **Max value** |
| Coin Change I        | Unlimited  | `index` (stay)| min       | Fewest coins      |
| Coin Change II       | Unlimited  | `index` (stay)| +         | Ways to make amount|

**The ONLY difference between 0/1 and unbounded:**
`index + 1` (move on) vs `index` (stay).

Everything else -- the structure, the choices, the base cases --
is identical.

---

## 🔍 Why Not Just Try All Items at Every Capacity?

Like Coin Change I's 1D approach:

```cpp
for (int c = 1; c <= W; c++)
    for (int i = 0; i < n; i++)
        if (weight[i] <= c)
            dp[c] = max(dp[c], value[i] + dp[c - weight[i]]);
```

This works for MAXIMIZATION (no ordering issue).
But the 2D index-based approach is more general
and connects to the knapsack framework.

Both give the same answer. The 2D approach is better
for understanding the pattern across all knapsack variants.

---

### 🧠 Memory of the Infinite Supply (Memoization) Law

-   **State:** `(index, capacity)` -- which item, how much room
-   **USE item:** `value[i] + solve(index, cap - weight[i])` -- **STAY** (unlimited)
-   **SKIP item:** `solve(index + 1, cap)` -- move on
-   **Combine:** `max(use, skip)` -- maximize value
-   **Base:** `index == n || cap == 0` → 0
-   **The ONE difference from 0/1:** `index` instead of `index + 1` after include
-   **Time:** O(n × W). **Space:** O(n × W).

Thus is remembered the saga of **Unbounded Knapsack (Memoization)**,
where the thief faced shelves of infinite items --
taking what he wanted, staying to take more,
or moving on to the next shelf --
always choosing the option that filled his knapsack
with the most value. 🎒♾️🧠✨
