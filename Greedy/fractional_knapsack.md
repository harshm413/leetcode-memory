## 🎒✂️ _The Greedy Thief Who Cuts: The Fractional Knapsack Saga_

> \_"In the Vault of Treasures,
> items lay scattered -- each with a weight and a value.
> The thief had a knapsack with a fixed capacity `W`.
>
> But unlike the 0/1 Knapsack,
> the thief could **BREAK items into fractions**.
> Take half an item. Take a quarter. Take any fraction.
>
> The Oracle was commanded:
>
> **'Maximize the total value in the knapsack.
> You may take fractions of items.'**
>
> The Oracle saw that greed was optimal here:
>
> **Sort items by value-per-unit-weight (value/weight) in DESCENDING order.
> Take the most valuable-per-weight item first.
> If it fits entirely -- take it all.
> If it doesn't fit -- take as much as the remaining capacity allows.**
>
> Why does greedy work here (but NOT in 0/1 Knapsack)?
>
> Because we can take FRACTIONS.
> There's never a reason to leave capacity unused --
> we can always fill the remaining space
> with a fraction of the next best item.
>
> In 0/1 Knapsack, taking a heavy item might block
> a better combination of lighter items.
> In Fractional Knapsack, we just take a piece -- no blocking."\_

---

This is the saga of **Fractional Knapsack**.

Given:
-   `n` items, each with `value[i]` and `weight[i]`.
-   Knapsack capacity `W`.
-   You may take **fractions** of items.

Return the **maximum total value** that fits in the knapsack.

```
Input:  W = 50, items = [{value:60, weight:10}, {value:100, weight:20}, {value:120, weight:30}]
Output: 240.0

Input:  W = 10, items = [{value:500, weight:30}]
Output: 166.67  (take 10/30 of the item)
```

---

## 🧠 The Oracle's Core Insight -- Sort by Value/Weight Ratio

The **value density** (value per unit weight) determines priority:

```
ratio[i] = value[i] / weight[i]
```

Sort items by ratio in descending order.
Greedily take items with the highest ratio first.

```
For each item (in sorted order):
  If item fits entirely (weight <= remaining capacity):
    Take it all. Add full value. Reduce capacity.
  Else:
    Take a fraction. Add (remaining capacity / weight) × value.
    Capacity becomes 0. Done.
```

```
Time:  O(n log n) -- sorting dominates
Space: O(1) extra (or O(n) for the ratio array)
```

---

### 📜 The Scroll of the Greedy Thief

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🎒 The Item Structure

```cpp
struct Item {
    int value, weight;
};
```

Each item has a value and a weight.

---

## ✂️ The Greedy Ritual

```cpp
double fractionalKnapsack(int W, vector<Item>& items) {
```

---

### 📊 Sort by Value/Weight Ratio (Descending)

```cpp
    sort(items.begin(), items.end(), [](Item& a, Item& b) {
        return (double)a.value / a.weight > (double)b.value / b.weight;
    });
```

The item with the highest value-per-weight goes first.
This is the greedy choice -- always pick the most "efficient" item.

> _"The thief is not fooled by large values alone.
> A 100-value item weighing 50 is less efficient
> than a 60-value item weighing 10.
> Value per weight is the true measure of worth."_

---

### 🎒 Greedily Fill the Knapsack

```cpp
    double totalValue = 0.0;
    int remainingCapacity = W;
```

---

```cpp
    for (auto& item : items) {
```

Process items in order of decreasing efficiency.

---

### 📦 Item Fits Entirely -- Take It All

```cpp
        if (item.weight <= remainingCapacity) {
            totalValue += item.value;
            remainingCapacity -= item.weight;
        }
```

The item fits completely. Take the whole thing.
Add its full value. Reduce the remaining capacity.

> _"This treasure fits in the bag.
> Take it all. Every last piece."_

---

### ✂️ Item Doesn't Fit -- Take a Fraction

```cpp
        else {
            double fraction = (double)remainingCapacity / item.weight;
            totalValue += fraction * item.value;
            remainingCapacity = 0;
            break;
        }
    }
```

The item is too heavy for the remaining space.
Take only what fits: `remainingCapacity / weight` of the item.
The value gained = that fraction × the item's full value.

After this, the knapsack is full. Break.

> _"The treasure is too large.
> But the thief can cut it.
> Take exactly what fills the remaining space.
> The bag is now full. The heist is complete."_

---

### 📤 The Answer

```cpp
    return totalValue;
}
```

---

### 🎺 The Trial of the Greedy Thief

```cpp
int main() {
    vector<Item> items1 = {{60, 10}, {100, 20}, {120, 30}};
    cout << fractionalKnapsack(50, items1) << endl; // expected: 240

    vector<Item> items2 = {{500, 30}};
    cout << fractionalKnapsack(10, items2) << endl; // expected: 166.67

    return 0;
}
```

---

**Full trace for W=50, items=[{60,10},{100,20},{120,30}]:**

**Step 1 -- Compute ratios and sort:**

| Item | Value | Weight | Ratio (value/weight) |
|------|-------|--------|---------------------|
| A    | 60    | 10     | 6.0                 |
| B    | 100   | 20     | 5.0                 |
| C    | 120   | 30     | 4.0                 |

Sorted order: A (6.0), B (5.0), C (4.0). Already sorted.

**Step 2 -- Greedily fill:**

| Item | Weight | Fits? | Action                    | totalValue | remaining |
|------|--------|-------|---------------------------|------------|-----------|
| A    | 10     | 10 ≤ 50 ✓ | Take all. +60.         | 60         | 40        |
| B    | 20     | 20 ≤ 40 ✓ | Take all. +100.        | 160        | 20        |
| C    | 30     | 30 ≤ 20 ✗ | Take fraction: 20/30 = 2/3. +120×(2/3)=80. | 240 | 0 |

**Answer: 240.0** ✓

Took all of A (60), all of B (100), and 2/3 of C (80). Total = 240.

---

**Trace for W=10, items=[{500,30}]:**

Only one item. Weight 30 > capacity 10.
Take fraction: 10/30 = 1/3. Value = 500 × (1/3) = 166.67.

**Answer: 166.67** ✓

---

**Trace for W=60, items=[{60,10},{100,20},{120,30}]:**

All items fit (total weight = 10+20+30 = 60 = W).
Take everything. Total value = 60+100+120 = 280.

**Answer: 280** ✓ -- no fractions needed.

---

## 🔍 Why Greedy Works for Fractional but NOT for 0/1 Knapsack

**Fractional Knapsack (greedy works ✓):**
-   We can always fill remaining capacity with a fraction.
-   No "wasted space" -- the knapsack is always full.
-   Taking the highest ratio first is always optimal.

**0/1 Knapsack (greedy FAILS ✗):**
-   Items are indivisible. Taking a heavy item might block better combinations.
-   Example: W=10, items=[{value:9, weight:6}, {value:5, weight:4}, {value:5, weight:4}].
    -   Greedy (ratio): take {9,6} first (ratio 1.5). Remaining=4. Take one {5,4}. Total=14.
    -   Optimal: take both {5,4} items. Total=10. Wait, that's less... 
    -   Actually: take {9,6} + {5,4} = 14. Can't fit second {5,4} (6+4+4=14>10).
    -   Greedy gives 14. Optimal is also 14 here. But in general, greedy can fail for 0/1.

The key: in fractional, there's no "all or nothing" decision.
We can always take the optimal fraction. No trade-offs.

---

## 🔍 Edge Cases

-   **All items fit:** take everything. No fractions needed.
-   **Single item too heavy:** take `W/weight` fraction.
-   **W = 0:** can't take anything. Return 0.
-   **Items with weight 0:** infinite ratio. Take them first (free value).

---

### 🧠 Memory of the Greedy Thief Law

-   **Sort by value/weight ratio** (descending)
-   **Greedily take items** in order of efficiency
-   **If item fits:** take all. Reduce capacity.
-   **If item doesn't fit:** take fraction `(remaining/weight) × value`. Done.
-   **Greedy works** because fractions fill any remaining space optimally
-   **Does NOT work for 0/1 Knapsack** (indivisible items need DP)
-   **Time:** O(n log n) -- sorting
-   **Space:** O(1) extra
-   **Edge cases:**
    -   All fit → take everything
    -   W = 0 → return 0
    -   One item too heavy → take fraction

Thus is remembered the saga of **Fractional Knapsack**,
where the greedy thief sorted treasures by their efficiency --
value per unit weight --
and took the most efficient first,
whole if they fit,
cut into fractions if they didn't --
always filling the knapsack to the brim,
never wasting a single unit of capacity,
until the maximum value was secured. 🎒✂️✨
