## 🪨⚔️ _The Arena of the Heaviest Stones: The Last Stone Weight Saga_

> \_"In the Kingdom of Stones,
> the King had collected a great pile of boulders --
> each with a different weight.
>
> He announced a ruthless tournament:
>
> **'In each round, take the two heaviest stones
> and smash them together.
>
> If they weigh the same --
> both are destroyed. Neither survives.
>
> If they weigh differently --
> the lighter one is destroyed.
> The heavier one survives,
> but loses weight equal to the lighter stone's weight.'**
>
> Round after round, the pile shrunk.
>
> The tournament continued until
> at most one stone remained.
>
> The Analyst needed to always find
> the two heaviest stones instantly --
> without sorting the pile from scratch after every collision.
>
> The answer was clear:
>
> **A Max-Heap.**
>
> It always kept the heaviest stone at the top.
> After every collision, the survivor was pushed back in.
> The heap re-balanced itself in O(log N).
>
> No sorting. No scanning.
> The arena ran itself --
> round after round --
> until silence fell."\_

---

This is the saga of **Last Stone Weight**.

Given an array `stones` where `stones[i]` is the weight of the `i`th stone.

Each round:

-   Pick the **two heaviest** stones `y` (heaviest) and `x` (second heaviest).
-   If `x == y` → both destroyed.
-   If `x != y` → both destroyed, but `y - x` re-enters the pile.

Repeat until **at most one stone** remains.

Return the weight of the last stone, or `0` if none remain.

```
Input:  [2, 7, 4, 1, 8, 1]
Output: 1

Input:  [1]
Output: 1

Input:  [2, 2]
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Max-Heap Runs the Arena

The problem demands two operations repeatedly:

```
1. Find and remove the two largest elements.
2. Optionally insert a new element (the survivor).
```

Scanning the array for the maximum every round is O(N) per round --
O(N²) total. Too slow.

The **Max-Heap** is the perfect tool:

```
Build the heap once:              O(N)
Each round:
  pop largest (y):                O(log N)
  pop second largest (x):         O(log N)
  if y != x: push (y - x):       O(log N)
Total: O(N log N)
```

In C++, `priority_queue<int>` is a **max-heap by default** --
the largest element always sits at the top.
No comparator needed. The default serves perfectly.

```
Time:  O(N log N)
Space: O(N)
```

---

### 📜 The Scroll of the Stone Arena

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏟️ The Arena Is Built

```cpp
int lastStoneWeight(vector<int>& stones) {
    priority_queue<int> maxHeap(stones.begin(), stones.end());
```

All stones were hurled into the arena at once.

The `priority_queue` constructor accepted the full vector
and built a valid max-heap in O(N) time --
the heaviest stone immediately rose to the top.

---

## ⚔️ The Tournament Runs -- Round by Round

```cpp
    while (maxHeap.size() > 1) {
```

The tournament continued as long as
**at least two stones** remained to clash.

A single stone standing alone had no opponent.

---

### 🥇 Pull the Two Heaviest Stones

```cpp
        int y = maxHeap.top(); maxHeap.pop();
        int x = maxHeap.top(); maxHeap.pop();
```

The Analyst pulled the two champions --
the heaviest (`y`) first, then the second heaviest (`x`).

Both were removed from the arena.
The heap adjusted in O(log N) for each pop.

---

### 💥 The Collision

```cpp
        if (y != x) {
            maxHeap.push(y - x);
        }
    }
```

**If equal** (`y == x`) --
both completely destroyed. Nothing pushed back.
The arena lost two stones.

**If unequal** (`y != x`) --
the lighter stone was obliterated.
The heavier survived, reduced by the lighter's weight.
`y - x` was thrown back into the heap.
The arena lost one stone net.

> _"Two stones enter. At most one leaves.
> The heap shrinks by one or two each round.
> The tournament always terminates."_

---

## 🏁 The Last Stone Speaks

```cpp
    if (maxHeap.empty()) return 0;
    return maxHeap.top();
}
```

If the heap was **empty** -- the last two stones had equal weight
and annihilated each other. Return `0`.

If the heap had **one stone** -- it was the sole survivor.
Return its weight.

---

### 🎺 The Trial of the Stone Arena

```cpp
int main() {
    vector<int> stones1 = {2, 7, 4, 1, 8, 1};
    cout << lastStoneWeight(stones1) << endl; // expected: 1

    vector<int> stones2 = {1};
    cout << lastStoneWeight(stones2) << endl; // expected: 1

    vector<int> stones3 = {2, 2};
    cout << lastStoneWeight(stones3) << endl; // expected: 0

    vector<int> stones4 = {3, 7, 2};
    cout << lastStoneWeight(stones4) << endl; // expected: 2

    return 0;
}
```

---

**Full trace for `[2, 7, 4, 1, 8, 1]`:**

**Initial heap (max at top):** `{8, 7, 4, 2, 1, 1}`

| Round | y | x | y == x? | Survivor | Heap after           |
| ----- | - | - | ------- | -------- | -------------------- |
| 1     | 8 | 7 | No      | 1        | {4, 2, 1, 1, 1}     |
| 2     | 4 | 2 | No      | 2        | {2, 1, 1, 1}        |
| 3     | 2 | 1 | No      | 1        | {1, 1, 1}           |
| 4     | 1 | 1 | Yes     | none     | {1}                  |

One stone remains -- weight `1`.

**Answer: 1** ✓

Round 1: 8 vs 7 → survivor 1. Five stones remain.
Round 2: 4 vs 2 → survivor 2. Four stones remain.
Round 3: 2 vs 1 → survivor 1. Three stones remain.
Round 4: 1 vs 1 → mutual destruction. One stone remains.

---

**Full trace for `[2, 2]`:**

| Round | y | x | y == x? | Survivor | Heap after |
| ----- | - | - | ------- | -------- | ---------- |
| 1     | 2 | 2 | Yes     | none     | {}         |

Heap empty → **Answer: 0** ✓

Both stones annihilated each other.

---

**Full trace for `[3, 7, 2]`:**

**Initial heap:** `{7, 3, 2}`

| Round | y | x | y == x? | Survivor | Heap after |
| ----- | - | - | ------- | -------- | ---------- |
| 1     | 7 | 3 | No      | 4        | {4, 2}     |
| 2     | 4 | 2 | No      | 2        | {2}        |

**Answer: 2** ✓

---

**Full trace for `[1, 1, 1, 1]`:**

| Round | y | x | y == x? | Survivor | Heap after |
| ----- | - | - | ------- | -------- | ---------- |
| 1     | 1 | 1 | Yes     | none     | {1, 1}     |
| 2     | 1 | 1 | Yes     | none     | {}         |

**Answer: 0** ✓ -- all stones destroyed in pairs.

---

**Full trace for `[1, 1, 1]`:**

| Round | y | x | y == x? | Survivor | Heap after |
| ----- | - | - | ------- | -------- | ---------- |
| 1     | 1 | 1 | Yes     | none     | {1}        |

**Answer: 1** ✓ -- odd number of equal stones, one survives.

---

## 🔍 Why Max-Heap and Not Sorting?

Sorting after every collision: O(N log N) per round × N rounds = O(N² log N).

Max-heap: O(log N) per round × N rounds = O(N log N).

The heap maintains order incrementally.
After removing two elements and inserting one,
the heap re-balances in O(log N) --
far cheaper than re-sorting the entire array.

---

## 🔄 Why Not Min-Heap?

We need the two **largest** stones each round.
A max-heap gives us the largest at the top in O(1).

A min-heap would give us the smallest --
we'd need to drain the entire heap to find the two largest.
That defeats the purpose.

The default `priority_queue<int>` in C++ is a max-heap.
No `greater<int>` needed. No custom comparator.
The simplest possible setup.

---

## ⚠️ Termination Guarantee

Each round removes at least one stone from the heap:
-   Equal stones: two removed, zero added → net -2.
-   Unequal stones: two removed, one added → net -1.

Starting with N stones, the tournament ends in at most N-1 rounds.
The algorithm always terminates.

---

### 🧠 Memory of the Stone Arena Law

-   **Max-Heap** = the arena -- largest stone always at the top
-   `priority_queue<int>` in C++ is a max-heap **by default**
-   **Build heap** from vector using constructor: O(N)
-   **While `heap.size() > 1`:** pop `y`, pop `x`
    -   If `y == x` → both destroyed, nothing pushed
    -   If `y != x` → push `y - x` (survivor)
-   **After loop:** empty → return 0; one stone → return `heap.top()`
-   Each round removes 1 or 2 stones → terminates in at most N-1 rounds
-   **Time:** O(N log N) -- N rounds, O(log N) per round
-   **Space:** O(N) -- the heap
-   **Edge cases:**
    -   Single stone → return it (loop never runs)
    -   Two equal stones → return 0
    -   All equal, even count → return 0
    -   All equal, odd count → return one stone's weight

Thus is remembered the saga of **Last Stone Weight**,
where the Analyst loaded all boulders into a Max-Heap arena --
pulling the two heaviest champions each round,
smashing them together,
returning the survivor if one existed --
and repeating until the arena emptied
or one stone stood alone --
with the heap always knowing instantly
who the two heaviest combatants were,
round after round,
until silence fell. 🪨⚔️✨
