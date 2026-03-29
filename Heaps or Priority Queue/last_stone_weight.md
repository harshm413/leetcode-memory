## 🪨⚔️ _The Arena of the Heaviest Stones: The Last Stone Weight Saga_

> \_"In the Kingdom of Stones,
> the King had collected a great pile of boulders —
> each with a different weight.
>
> He announced a ruthless tournament:
>
> \*\*'In each round, take the two heaviest stones
> and smash them together.
>
> If they weigh the same —
> both are destroyed. Neither survives.
>
> If they weigh differently —
> the lighter one is destroyed.
> The heavier one survives,
> but loses weight equal to the lighter stone's weight.'\*\*
>
> Round after round, the pile shrunk.
>
> The tournament continued until
> at most one stone remained.
>
> Return the weight of the last surviving stone.
> If no stone survived — return zero.
>
> The Analyst stood before the pile.
>
> She needed to always find the two heaviest stones instantly —
> without sorting the pile from scratch after every collision.
>
> The answer was clear:
>
> **A Max-Heap.**
>
> It always kept the heaviest stone at the top.
> After every collision, she pushed the survivor back in.
> The heap re-balanced itself in O(log N).
>
> No sorting. No scanning. No waste.
> The arena ran itself —
> round after round —
> until silence fell
> and at most one stone remained."\_

---

This is the saga of **Last Stone Weight**.

You are given an array `stones` where `stones[i]` is the weight of the `i`th stone.

Each round:

-   Pick the **two heaviest** stones, call them `y` (heaviest) and `x` (second heaviest).
-   If `x == y` → both destroyed. Neither re-enters.
-   If `x != y` → both destroyed, but `y - x` re-enters the pile.

Repeat until **at most one stone** remains.

Return the weight of the last stone, or `0` if none remain.

```
Input:  [2, 7, 4, 1, 8, 1]
Round 1: pick 8 and 7 → 8-7=1 → pile: [2,4,1,1,1]
Round 2: pick 4 and 2 → 4-2=2 → pile: [2,1,1,1]
Round 3: pick 2 and 1 → 2-1=1 → pile: [1,1,1]
Round 4: pick 1 and 1 → 0     → pile: [1]
Output: 1
```

---

## 🧠 The Oracle's Core Insight — Max-Heap Runs the Arena

The problem demands two operations repeatedly:

```
1. Find and remove the two largest elements.
2. Optionally insert a new element (the survivor).
```

Scanning the array for the maximum every round is O(N) per round —
O(N²) total. Too slow.

Sorting after every round is also expensive.

The **Max-Heap** is the perfect tool:

```
Build the heap once:            O(N)
Each round:
  pop largest  (y):             O(log N)
  pop second largest (x):       O(log N)
  if y != x: push (y - x):      O(log N)
Total: O(N log N)
```

In C++, `priority_queue` is a **max-heap by default** —
the largest element always sits at the top.

No comparator needed. No flipping. The default serves us perfectly.

---

### 📜 The Scroll of the Stone Arena

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🏟️ The Arena is Built — Load All Stones into the Max-Heap

```cpp
int lastStoneWeight(vector<int>& stones) {
    priority_queue<int> maxHeap(stones.begin(), stones.end());
```

All stones were hurled into the arena at once.

The `priority_queue` constructor accepted the full vector
and built a valid max-heap in O(N) time —
the heaviest stone immediately rose to the top.

No manual insertion loop needed.
The arena was ready.

---

## ⚔️ The Tournament Runs — Round by Round

```cpp
    while (maxHeap.size() > 1) {
```

The tournament continued as long as
**at least two stones** remained to clash.

A single stone standing alone had no opponent —
the tournament was over.

---

### 🥇 Pull the Two Heaviest Stones

```cpp
        int y = maxHeap.top(); maxHeap.pop();
        int x = maxHeap.top(); maxHeap.pop();
```

The Analyst reached into the heap and pulled the two champions —
the heaviest (`y`) first, then the second heaviest (`x`).

Both were removed from the arena.
The heap adjusted in O(log N) for each pop.

The collision was about to happen.

---

### 💥 The Collision

```cpp
        if (y != x) {
            maxHeap.push(y - x);
        }
    }
```

**If they weighed the same** (`y == x`) —
both were completely destroyed.
Nothing was pushed back.
The arena lost two stones.

**If they weighed differently** (`y != x`) —
the lighter stone `x` was obliterated.
The heavier stone `y` survived, reduced by `x`.
The survivor `y - x` was thrown back into the heap.
The arena lost one stone net.

Either way, the tournament continued
with one or two fewer stones in the arena.

---

## 🏁 The Last Stone Speaks

```cpp
    if (maxHeap.empty()) return 0;
    return maxHeap.top();
}
```

When fewer than two stones remained, the while loop ended.

If the heap was **empty** — the last two stones had equal weight
and annihilated each other in the final round.
Return `0` — no stone survived.

If the heap had **one stone** — it was the sole survivor.
Return its weight.

---

### 🎺 The Trial of the Stone Arena

```cpp
int main() {
    vector<int> stones = {2, 7, 4, 1, 8, 1};
    cout << lastStoneWeight(stones) << endl;
    // expected: 1
    return 0;
}
```

The tournament unfolded:

**Initial heap (max at top):** `{8, 7, 4, 2, 1, 1}`

| Round | y (heaviest) | x (2nd heaviest) | y == x? | Survivor | Heap After      |
| ----- | ------------ | ---------------- | ------- | -------- | --------------- |
| 1     | 8            | 7                | No      | 1        | {4, 2, 1, 1, 1} |
| 2     | 4            | 2                | No      | 2        | {2, 1, 1, 1}    |
| 3     | 2            | 1                | No      | 1        | {1, 1, 1}       |
| 4     | 1            | 1                | Yes     | none     | {1}             |

Only one stone remained — weight `1`.
The arena fell silent.

**Answer: 1** ✓

---

### 🧠 Memory of the Stone Arena Law

-   **Max-Heap** = the arena — largest stone always at the top
-   `priority_queue<int>` in C++ is a max-heap **by default** — no comparator needed
-   **Build heap** from vector in one line using constructor: O(N)
-   **While `heap.size() > 1`:** pop `y`, pop `x`, if `y != x` push `y - x`
-   **If equal:** both destroyed — nothing pushed back, heap shrinks by 2
-   **If unequal:** `y - x` pushed back — heap shrinks by 1
-   **After loop:** if empty → return `0`; else return `heap.top()`
-   **Time:** O(N log N) — each stone is pushed/popped at most once, each at O(log N)
-   **Space:** O(N) — the heap holds all stones initially

Thus is remembered the saga of **Last Stone Weight**,
where the Analyst loaded all boulders into a Max-Heap arena —
pulling the two heaviest champions each round,
smashing them together,
returning the survivor if one existed —
and repeating until the arena emptied or one stone stood alone,
with the heap always knowing — instantly, without searching —
who the two heaviest combatants were. 🪨⚔️✨
