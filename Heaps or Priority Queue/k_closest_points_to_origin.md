## 🌍📍 _The K Nearest Scouts to the Capital: The K Closest Points to Origin Saga_

> \_"The Kingdom's capital stood at the Origin --
> the point (0, 0) at the very center of the map.
>
> Scattered across the lands were countless scouts,
> each stationed at a different point.
>
> The General was commanded:
>
> **'Find the K scouts closest to the capital.
> Distance is measured as the crow flies --
> straight line from scout to origin.'**
>
> The General thought about sorting all scouts by distance --
> but with millions of scouts, sorting everything
> just to find K of them felt wasteful.
>
> A wiser officer stepped forward:
>
> **'Maintain a Max-Heap of exactly K scouts --
> the K closest found so far.
> The heap's top is always the FARTHEST among the K candidates.
> When a closer scout arrives --
> the farthest is expelled and the newcomer takes their place.'**
>
> No full sort. A rolling competition.
> The max-heap of size K ran the selection --
> every scout evaluated in O(log K),
> the entire army processed in O(N log K)."\_

---

This is the saga of **K Closest Points to Origin**.

Given an array `points` where `points[i] = [xi, yi]`
and an integer `k`:

-   Return the **k closest points** to the origin `(0, 0)`.
-   Distance = Euclidean distance = `sqrt(x² + y²)`.
-   Since we only compare distances, use **squared distance** `x² + y²`
    to avoid `sqrt` entirely. Comparison is identical.
-   Answer may be returned in **any order**.

```
Input:  points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]

Input:  points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
```

---

## 🧠 The Oracle's Core Insight -- Max-Heap of Size K

We want the K **smallest** distances.
A **max-heap of size K** tracks the K closest seen so far.
The heap's top is the **farthest** among the current K candidates.

When a new point arrives:

```
Push it into the heap.
If heap.size() > k → pop the max (farthest expelled).
```

After processing all N points, the heap holds exactly the K closest.

**Why max-heap for finding K smallest?**

We need to evict the FARTHEST among our K candidates
when a closer newcomer arrives.
The max-heap always shows us the farthest instantly at the top.

This is the same pattern as Kth Largest in a Stream --
but applied to distances instead of values,
and we want the K smallest instead of K largest.

```
Time:  O(N log K) -- N points, each push/pop is O(log K)
Space: O(K) -- heap never exceeds K+1 elements
```

---

### 📜 The Scroll of the Scouting Map

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## ⚔️ The General's Selection Ritual

```cpp
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    // Max-heap of {squared_distance, index}
    priority_queue<pair<int, int>> maxHeap;
```

The heap stored `{squared_distance, index}` pairs.

C++ `priority_queue<pair<int,int>>` is a max-heap by default.
`pair` compares by first element -- so the largest squared distance
always floats to the top.

---

## 🔁 Evaluate Every Scout

```cpp
    for (int i = 0; i < (int)points.size(); i++) {
        int x = points[i][0];
        int y = points[i][1];
        int dist = x * x + y * y;
```

Each scout's **squared distance** from the origin was computed.
No `sqrt` needed -- pure integer arithmetic.

`x² + y²` preserves the distance ordering:
if `a² + b² < c² + d²`, then `sqrt(a²+b²) < sqrt(c²+d²)`.

---

### 📥 Push and Expel If Oversized

```cpp
        maxHeap.push({dist, i});

        if ((int)maxHeap.size() > k) {
            maxHeap.pop();
        }
    }
```

Every scout was pushed into the heap.
If the heap exceeded K -- the farthest (max distance at top) was expelled.

> _"Only K seats exist around the capital.
> The farthest candidate is always the first to fall."_

---

## 🏁 Collect the K Closest

```cpp
    vector<vector<int>> result;
    while (!maxHeap.empty()) {
        result.push_back(points[maxHeap.top().second]);
        maxHeap.pop();
    }
    return result;
}
```

The K survivors were drained from the heap.
Each entry's `.second` held the original index --
used to retrieve the actual `[x, y]` coordinates.

Order didn't matter -- the problem accepted any arrangement.

---

### 🎺 The Trial of the Scouting Map

```cpp
int main() {
    vector<vector<int>> pts1 = {{1,3},{-2,2},{5,1},{0,2}};
    auto r1 = kClosest(pts1, 2);
    for (auto& p : r1) cout << "[" << p[0] << "," << p[1] << "] ";
    cout << endl;
    // expected: [0,2] [-2,2] (any order)

    vector<vector<int>> pts2 = {{3,3},{5,-1},{-2,4}};
    auto r2 = kClosest(pts2, 2);
    for (auto& p : r2) cout << "[" << p[0] << "," << p[1] << "] ";
    cout << endl;
    // expected: [3,3] [-2,4] (any order)

    return 0;
}
```

---

**Full trace for `[[1,3],[-2,2],[5,1],[0,2]]`, k=2:**

**Squared distances:**

| Index | Point  | x²+y²       |
| ----- | ------ | ------------ |
| 0     | [1,3]  | 1+9 = 10    |
| 1     | [-2,2] | 4+4 = 8     |
| 2     | [5,1]  | 25+1 = 26   |
| 3     | [0,2]  | 0+4 = 4     |

**Heap trace (max-heap, k=2):**

| Scout  | dist² | Heap after push          | Size>2? | Pop (farthest) | Heap after     |
| ------ | ----- | ------------------------ | ------- | -------------- | -------------- |
| [1,3]  | 10    | {(10,0)}                 | No      | --             | {(10,0)}       |
| [-2,2] | 8     | {(10,0),(8,1)}           | No      | --             | {(10,0),(8,1)} |
| [5,1]  | 26    | {(26,2),(10,0),(8,1)}    | Yes     | (26,2)         | {(10,0),(8,1)} |
| [0,2]  | 4     | {(10,0),(8,1),(4,3)}     | Yes     | (10,0)         | {(8,1),(4,3)}  |

**Final heap:** `{(8,1), (4,3)}` → points `[-2,2]` and `[0,2]`.

**Answer: [[-2,2], [0,2]]** ✓

At [5,1]: dist²=26 was the farthest, expelled immediately.
At [0,2]: dist²=4 entered, [1,3] with dist²=10 was expelled as the new farthest.

---

**Full trace for `[[3,3],[5,-1],[-2,4]]`, k=2:**

**Squared distances:** [3,3]=18, [5,-1]=26, [-2,4]=20.

| Scout  | dist² | Heap after push       | Size>2? | Pop        | Heap after      |
| ------ | ----- | --------------------- | ------- | ---------- | --------------- |
| [3,3]  | 18    | {(18,0)}              | No      | --         | {(18,0)}        |
| [5,-1] | 26    | {(26,1),(18,0)}       | No      | --         | {(26,1),(18,0)} |
| [-2,4] | 20    | {(26,1),(20,2),(18,0)}| Yes     | (26,1)     | {(20,2),(18,0)} |

**Answer: [[3,3], [-2,4]]** ✓

[5,-1] was the farthest and got expelled when [-2,4] arrived.

---

**Trace for `[[0,0],[1,1]]`, k=1:**

| Scout | dist² | Heap after push | Size>1? | Pop    | Heap after |
| ----- | ----- | --------------- | ------- | ------ | ---------- |
| [0,0] | 0     | {(0,0)}         | No      | --     | {(0,0)}    |
| [1,1] | 2     | {(2,1),(0,0)}   | Yes     | (2,1)  | {(0,0)}    |

**Answer: [[0,0]]** ✓ -- the origin itself is the closest point.

---

**Trace for `[[1,0],[0,1]]`, k=2:**

Both have dist²=1. Heap holds both. No expulsion.

**Answer: [[1,0],[0,1]]** ✓ -- equal distances, both kept.

---

## 🔄 Alternative -- Min-Heap (Sort All, Take K)

Instead of a max-heap of size K, use a min-heap of all N points:

```cpp
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    auto cmp = [&](int a, int b) {
        return points[a][0]*points[a][0] + points[a][1]*points[a][1]
             > points[b][0]*points[b][0] + points[b][1]*points[b][1];
    };
    priority_queue<int, vector<int>, decltype(cmp)> minHeap(cmp);

    for (int i = 0; i < (int)points.size(); i++) minHeap.push(i);

    vector<vector<int>> result;
    for (int i = 0; i < k; i++) {
        result.push_back(points[minHeap.top()]);
        minHeap.pop();
    }
    return result;
}
```

Build a min-heap of all N points → O(N).
Pop K times → O(K log N).
Total: O(N + K log N).

This is better when K is very small relative to N.
The max-heap approach is O(N log K) -- better when K is moderate.

For interviews, the max-heap of size K is the standard answer.

---

## 🔍 Why Squared Distance, Not Euclidean?

`sqrt` is:
-   Expensive (floating point computation).
-   Unnecessary (monotonic function -- doesn't change ordering).
-   Risky (floating point precision issues).

`x² + y²` is:
-   Integer arithmetic (fast, exact).
-   Same ordering as `sqrt(x² + y²)`.
-   No precision issues.

Always use squared distance when you only need to **compare** distances.

---

### 🧠 Memory of the K Nearest Scouts Law

-   **Squared distance** `x²+y²` -- never compute `sqrt`, comparison is identical
-   **Max-Heap of size K** -- holds K closest; top = farthest of those K
-   Store `{squared_distance, index}` pairs
-   **For each point:** push → if `heap.size() > k` → pop max (farthest expelled)
-   **After all points:** drain heap, retrieve original `[x,y]` via stored index
-   Same **K-window pattern** as Kth Largest in a Stream
-   **Alternative:** min-heap of all N, pop K times → O(N + K log N)
-   **Time:** O(N log K) -- N points, O(log K) per push/pop
-   **Space:** O(K)
-   **Edge cases:**
    -   k = N → return all points (no expulsion ever happens)
    -   k = 1 → heap holds only the closest point
    -   Equal distances → both kept (no tie-breaking needed)
    -   Origin in the array → dist²=0, always closest

Thus is remembered the saga of **K Closest Points to Origin**,
where the General evaluated every scout on the map
with a Max-Heap that never held more than K candidates --
always expelling the farthest the moment a closer challenger appeared --
using squared distance to avoid the treachery of floating point --
until the K scouts nearest to the capital
were the only ones left standing. 🌍📍✨
