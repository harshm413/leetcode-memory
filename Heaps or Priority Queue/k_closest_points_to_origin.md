## 🌍📍 _The K Nearest Scouts to the Capital: The K Closest Points to Origin Saga_

> \_"The Kingdom's capital stood at the Origin —
> the point (0, 0) at the very center of the map.
>
> Scattered across the lands were countless scouts,
> each stationed at a different point on the map.
>
> The General was commanded:
>
> **'Find the K scouts closest to the capital.
> Distance is measured as the crow flies —
> straight line from scout to origin.'**
>
> The General thought about sorting all scouts by distance —
> but with millions of scouts, sorting everything
> just to find K of them felt wasteful.
>
> Then a wiser officer stepped forward:
>
> \*\*'We do not need to sort everyone.
> We only need to track the K closest candidates.
>
> Maintain a Max-Heap of exactly K scouts —
> the K closest found so far.
>
> The heap's top is always the FARTHEST among the current K candidates.
>
> When a new scout arrives:
> if they are closer than the farthest candidate in the heap —
> the farthest is expelled and the newcomer takes their place.
> If not — the newcomer is too far and is ignored.
>
> When all scouts have been evaluated —
> the K scouts left in the heap are the answer.'\*\*
>
> No full sort. No wasted comparison.
> A rolling competition for the K closest seats.
> The max-heap of size K ran the selection —
> every scout evaluated in O(log K),
> the entire army processed in O(N log K)."\_

---

This is the saga of **K Closest Points to Origin**.

You are given an array `points` where `points[i] = [xi, yi]`
and an integer `k`.

Return the **k closest points to the origin** `(0, 0)`.

Distance is the **Euclidean distance**:

```
distance = sqrt(x² + y²)
```

But since we only compare distances — not compute them —
we use the **squared distance** `x² + y²` to avoid the square root entirely.
Comparing `x² + y²` is equivalent to comparing `sqrt(x² + y²)`.

The answer may be returned in **any order**.

```
Input:  points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
(-2)²+2² = 8  vs  1²+3² = 10 → [-2,2] is closer
```

---

## 🧠 The Oracle's Core Insight — Max-Heap of Size K

We want the K **smallest** distances.
We maintain a **max-heap of size K** — tracking the K closest seen so far.
The heap's top is the **farthest among the current K candidates**.

When a new point arrives:

```
If heap.size() < k           → always push it (not enough candidates yet)
Else if new_dist < heap.top().dist → pop the farthest, push the newcomer
Else                         → newcomer is farther than all K candidates, ignore
```

After processing all N points, the heap contains exactly the K closest.

**Why Max-Heap for finding the K smallest?**

> We want to evict the FARTHEST among our K candidates
> when a closer newcomer arrives.
> The max-heap always shows us the farthest instantly.
> This is the same pattern as Kth Largest in a Stream —
> but applied to distances instead of values.

---

### 📜 The Scroll of the Scouting Map

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🗺️ The Selection Ritual — Max-Heap of K Closest

```cpp
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
```

The General opened the map and began evaluating scouts.

---

```cpp
    // Max-heap: stores {squared_distance, index}
    // Top of heap = farthest among current K candidates
    priority_queue<pair<int,int>> maxHeap;
```

The General built a **Max-Heap** storing pairs of `{squared_distance, index}`.

`pair` compares by first element by default —
so `priority_queue<pair<int,int>>` is a max-heap on squared distance.

The farthest of the current K candidates always floated to the top.

---

```cpp
    for (int i = 0; i < (int)points.size(); i++) {
        int x = points[i][0];
        int y = points[i][1];
        int dist = x*x + y*y;
```

Each scout was evaluated one by one.
Their **squared distance** from the origin was computed —
no `sqrt` needed, no floating point — pure integer arithmetic.

---

```cpp
        maxHeap.push({dist, i});
```

The scout was pushed into the heap —
a contestant vying for one of the K closest seats.

---

```cpp
        if ((int)maxHeap.size() > k) {
            maxHeap.pop();
        }
    }
```

If the heap exceeded K scouts —
the **farthest** one (the heap's top) was immediately expelled.

> _"Only K seats exist around the capital.
> The moment a closer scout arrives and fills the K+1th seat,
> the farthest of the K+1 is thrown out —
> for they are the least deserving of proximity."_

After this line, the heap held at most K scouts —
always the K closest seen so far.

---

## 🏁 Collect the K Closest and Return

```cpp
    vector<vector<int>> result;
    while (!maxHeap.empty()) {
        result.push_back(points[maxHeap.top().second]);
        maxHeap.pop();
    }
    return result;
}
```

The K scouts remaining in the heap were collected.

Each entry's `.second` held the original index in `points` —
so we retrieved the actual `[x, y]` coordinates.

The result was assembled and returned.

Order did not matter — the problem asked for any valid ordering.

---

### 🎺 The Trial of the Scouting Map

```cpp
int main() {
    vector<vector<int>> points = {{1,3},{-2,2},{5,1},{0,2}};
    int k = 2;

    vector<vector<int>> result = kClosest(points, k);
    for (auto& p : result) {
        cout << "[" << p[0] << "," << p[1] << "] ";
    }
    cout << endl;
    // Distances squared: [1,3]=10, [-2,2]=8, [5,1]=26, [0,2]=4
    // 2 closest: [0,2] (dist²=4) and [-2,2] (dist²=8)
    return 0;
}
```

The General evaluated all four scouts:

**Squared distances:**
| Scout | x²+y² |
|---------|--------|
| [1,3] | 10 |
| [-2,2] | 8 |
| [5,1] | 26 |
| [0,2] | 4 |

**Heap trace (max-heap, k=2):**

| Scout  | dist² | Push → Heap         | Size>2? | Pop (farthest) | Heap After  |
| ------ | ----- | ------------------- | ------- | -------------- | ----------- |
| [1,3]  | 10    | {10,0}              | No      | —              | {10}        |
| [-2,2] | 8     | {10,0},{8,1}        | No      | —              | {10,8}      |
| [5,1]  | 26    | {26,2},{10,0},{8,1} | Yes     | 26 (farthest)  | {10,8}      |
| [0,2]  | 4     | {10,0},{8,1},{4,3}  | Yes     | 10 (farthest)  | {8,1},{4,3} |

Final heap: `{dist²=8, idx=1}` and `{dist²=4, idx=3}`
→ Points: `[-2,2]` and `[0,2]` ✓

The two scouts stationed closest to the capital were found.

---

### 🧠 Memory of the K Nearest Scouts Law

-   **Squared distance** `x²+y²` — never compute `sqrt`, comparison is identical and integer-safe
-   **Max-Heap of size K** — holds the K closest seen so far; top = farthest of those K
-   Store `{squared_distance, index}` pairs — `index` lets you retrieve the original point at the end
-   **For each point:** push → if `heap.size() > k` → pop the max (farthest expelled)
-   **After all points:** heap contains exactly the K closest points
-   **Collect result:** drain heap using `.top().second` to recover original `[x,y]`
-   This is the **same K-window pattern** as Kth Largest in a Stream — max-heap to find K smallest
-   **Time:** O(N log K) — N points, each pushed and possibly popped at O(log K) cost
-   **Space:** O(K) — heap never holds more than K+1 elements at any moment

Thus is remembered the saga of **K Closest Points to Origin**,
where the General evaluated every scout on the map
with a Max-Heap that never held more than K candidates —
always expelling the farthest the moment a closer challenger appeared —
processing the entire army in O(N log K)
and delivering the K scouts nearest to the capital
without ever sorting the full map. 🌍📍✨
