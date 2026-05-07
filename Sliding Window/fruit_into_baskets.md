## 🪟🍎🍊 _The Two Baskets: The Fruit Into Baskets Saga_

> \_"The Oracle walked along a row of fruit trees.
> Each tree bore one type of fruit.
>
> She had exactly TWO baskets.
> Each basket could hold only ONE type of fruit.
>
> She was commanded:
>
> **'Start at any tree. Pick fruits moving RIGHT.
> You must stop when you encounter a THIRD type of fruit.
> What is the maximum number of fruits you can collect?'**
>
> The Oracle stripped away the story:
>
> **'Find the longest subarray with at most 2 distinct values.'**
>
> Same sliding window pattern as Max Consecutive Ones III.
> Instead of counting zeros ≤ k, count distinct types ≤ 2.
> Use a hashmap to track frequencies of each type in the window."\_

---

This is the saga of **Fruit Into Baskets (LeetCode 904)**.

Given an array `fruits` where `fruits[i]` is the type of fruit at tree `i`:
-   You have 2 baskets, each holding one type.
-   Find the **maximum** number of fruits you can collect
    from a contiguous subarray with at most 2 distinct types.

```
Input:  fruits = [1, 2, 1]
Output: 3   (all three — only 2 types)

Input:  fruits = [0, 1, 2, 2]
Output: 3   ([1, 2, 2] — types 1 and 2)

Input:  fruits = [1, 2, 3, 2, 2]
Output: 4   ([2, 3, 2, 2] — types 2 and 3)

Input:  fruits = [3, 3, 3, 1, 2, 1, 1, 2, 3, 3, 4]
Output: 5   ([1, 2, 1, 1, 2] — types 1 and 2)
```

---

## 🧠 The Reframing

Strip the fruit/basket story. The actual problem:

**Longest subarray with at most 2 distinct elements.**

This is "Longest Substring with At Most K Distinct Characters" where K=2,
but on an integer array instead of a string.

---

## 🧠 The Sliding Window Mechanics

**HashMap:** `type → count` (frequency of each fruit type in the window).
**Expand:** move `right` forward. Add `fruits[right]` to the map.
**Shrink:** when `map.size() > 2` (more than 2 types), shrink from left.
Remove `fruits[left]` from the map. If its count hits 0, erase it.
**Track:** maximum window size.

---

### 📜 The Scroll of the Two Baskets

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🍎 The Sliding Window Solution

### Initialize

```cpp
int totalFruit(vector<int>& fruits) {
    unordered_map<int, int> basket;
    int left = 0;
    int maxLen = 0;
```

`basket` maps each fruit type to its count in the current window.
`basket.size()` = number of distinct types in the window.

---

### Expand with right pointer

```cpp
    for (int right = 0; right < fruits.size(); right++) {
        basket[fruits[right]]++;
```

Add the new fruit to the basket. Its count increases.
If it's a new type, `basket.size()` increases by 1.

---

### Too many types — shrink from left

```cpp
        while (basket.size() > 2) {
            basket[fruits[left]]--;
            if (basket[fruits[left]] == 0) {
                basket.erase(fruits[left]);
            }
            left++;
        }
```

More than 2 distinct types in the window. Shrink.

Decrement the count of the leftmost fruit.
If its count drops to 0 — that type is completely gone from the window.
Erase it from the map. `basket.size()` decreases.

Keep shrinking until we're back to ≤ 2 types.

> _"A third type of fruit entered the window.
> The Oracle shrinks from the left, removing fruits one by one,
> until one type is completely gone
> and only two remain."_

---

### Update the maximum

```cpp
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

---

### 🎺 The Trial of the Two Baskets

```cpp
int main() {
    vector<int> f1 = {1, 2, 1};
    cout << totalFruit(f1) << endl; // expected: 3

    vector<int> f2 = {0, 1, 2, 2};
    cout << totalFruit(f2) << endl; // expected: 3

    vector<int> f3 = {1, 2, 3, 2, 2};
    cout << totalFruit(f3) << endl; // expected: 4

    vector<int> f4 = {3, 3, 3, 1, 2, 1, 1, 2, 3, 3, 4};
    cout << totalFruit(f4) << endl; // expected: 5

    return 0;
}
```

---

**Full trace for fruits = [1, 2, 3, 2, 2]:**

| right | fruit | basket | size | size>2? | left | window | maxLen |
|-------|-------|--------|------|---------|------|--------|--------|
| 0 | 1 | {1:1} | 1 | No | 0 | [1] | 1 |
| 1 | 2 | {1:1, 2:1} | 2 | No | 0 | [1,2] | 2 |
| 2 | 3 | {1:1, 2:1, 3:1} | **3** | **Yes** | | | |
|   |   | remove fruits[0]=1: {1:0}→erase. {2:1, 3:1} | 2 | No | 1 | [2,3] | 2 |
| 3 | 2 | {2:2, 3:1} | 2 | No | 1 | [2,3,2] | 3 |
| 4 | 2 | {2:3, 3:1} | 2 | No | 1 | [2,3,2,2] | **4** |

**Answer: 4** ✓ (window [2,3,2,2] — types 2 and 3)

---

**Trace for fruits = [3,3,3,1,2,1,1,2,3,3,4]:**

| right | fruit | basket | size>2? | left | window size | maxLen |
|-------|-------|--------|---------|------|-------------|--------|
| 0 | 3 | {3:1} | No | 0 | 1 | 1 |
| 1 | 3 | {3:2} | No | 0 | 2 | 2 |
| 2 | 3 | {3:3} | No | 0 | 3 | 3 |
| 3 | 1 | {3:3, 1:1} | No | 0 | 4 | 4 |
| 4 | 2 | {3:3, 1:1, 2:1} | **Yes** | shrink→ | | |
|   |   | remove 3,3,3,1: left=4. {2:1} | No | 4 | 1 | 4 |
| 5 | 1 | {2:1, 1:1} | No | 4 | 2 | 4 |
| 6 | 1 | {2:1, 1:2} | No | 4 | 3 | 4 |
| 7 | 2 | {2:2, 1:2} | No | 4 | 4 | 4 |
| 8 | 3 | {2:2, 1:2, 3:1} | **Yes** | shrink→ | | |
|   |   | remove 2: {1:2, 2:1, 3:1}→{1:2, 3:1}... | | | | |

Actually let me recount at index 4 more carefully:

At right=4 (fruit=2), basket = {3:3, 1:1, 2:1}, size=3.
Shrink: left=0 (fruit=3), basket[3]=2, left=1.
left=1 (fruit=3), basket[3]=1, left=2.
left=2 (fruit=3), basket[3]=0→erase. basket={1:1, 2:1}. size=2. Stop.
left=3. Window = [1,2], size=2. maxLen stays 4.

Wait — left=3 means window is [fruits[3], fruits[4]] = [1, 2]. Size 2.

Continuing: right=5(1), right=6(1), right=7(2): basket={1:3, 2:2}. Window [1,2,1,1,2]. Size 5. **maxLen=5**.

right=8(3): basket={1:3, 2:2, 3:1}. Size 3. Shrink.
Remove fruits[3]=1: basket[1]=2. Still size 3.
Remove fruits[4]=2: basket[2]=1. Still size 3.
Remove fruits[5]=1: basket[1]=1. Still size 3.
Remove fruits[6]=1: basket[1]=0→erase. basket={2:1, 3:1}. Size 2. left=7.
Window = [2,3]. Size 2.

right=9(3): basket={2:1, 3:2}. Size 2. Window size 3.
right=10(4): basket={2:1, 3:2, 4:1}. Size 3. Shrink.
Remove fruits[7]=2: basket[2]=0→erase. basket={3:2, 4:1}. left=8. Size 3.

**Answer: 5** ✓ (window [1,2,1,1,2] at indices 3-7)

---

## 🔍 The "At Most K Distinct" Pattern

| Problem | K | What's in the map |
|---------|---|-------------------|
| **Fruit Into Baskets (this)** | **2** | Fruit type → count |
| Longest Substring K Distinct | K (given) | Char → count |
| Subarrays with K Different | K (exact) | Num → count (use atMost trick) |

All use the same template: hashmap + shrink when `map.size() > K`.

For **exactly K distinct**, use: `atMost(K) - atMost(K-1)`.

---

## 🔍 Why Erase When Count Hits 0?

```cpp
if (basket[fruits[left]] == 0) {
    basket.erase(fruits[left]);
}
```

Without erasing, `basket.size()` would still count that key
even though its value is 0. The size check `> 2` would be wrong.

The map must only contain types that are ACTUALLY in the window.
Zero-count entries must be removed.

---

### 🧠 Memory of the Two Baskets Law

-   **Reframe:** "2 baskets" = "longest subarray with ≤ 2 distinct types"
-   **HashMap:** `type → count`. `map.size()` = distinct types in window.
-   **Expand right:** `basket[fruits[right]]++`
-   **Shrink left:** while `size > 2`, decrement and erase if count hits 0
-   **Erase at 0** — critical for correct `map.size()`
-   **Time:** O(N). **Space:** O(1) — map has at most 3 entries.

Thus is remembered the saga of **Fruit Into Baskets**,
where the Oracle walked the orchard with two baskets —
expanding her collection rightward,
shrinking leftward when a third type appeared —
the hashmap tracking what filled each basket,
erasing types that left the window entirely —
until the longest stretch of two-type fruit
was found in a single sweep. 🪟🍎🍊✨
