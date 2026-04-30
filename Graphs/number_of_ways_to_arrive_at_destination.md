## 🛤️🔢 _The Many Shortest Roads: The Number of Ways to Arrive at Destination Saga_

> \_"In the Kingdom of Intersections,
> roads connected junctions with travel times.
> The traveler needed to go from junction `0`
> to junction `n-1`.
>
> The Oracle had already mastered Dijkstra --
> she could find the SHORTEST time.
>
> But now the King demanded more:
>
> **'How many DIFFERENT shortest paths exist?
> Not just the shortest time --
> but the COUNT of distinct routes
> that achieve that shortest time.'**
>
> The Oracle adapted Dijkstra:
>
> **Alongside the distance array,
> maintain a WAYS array.**
>
> `ways[v]` = number of shortest paths from source to `v`.
>
> When relaxing an edge `u → v`:
> -   If a SHORTER path is found → `ways[v] = ways[u]`
>     (all previous paths are obsolete, start fresh with u's count).
> -   If an EQUAL-length path is found → `ways[v] += ways[u]`
>     (u provides additional shortest paths).
> -   If a LONGER path is found → ignore.
>
> Dijkstra + path counting.
> Same heap. One extra array."\_

---

This is the saga of **Number of Ways to Arrive at Destination**.

Given:
-   `n` intersections (0 to n-1).
-   `roads[i] = [u, v, time]` -- bidirectional road with travel time.

Find the number of ways to travel from `0` to `n-1`
in the **shortest** amount of time.

Return the answer modulo `10⁹ + 7`.

```
Input:  n=7, roads=[[0,6,7],[0,1,2],[1,2,3],[1,3,3],[6,3,3],
                     [3,5,1],[6,5,1],[2,5,1],[0,4,5],[4,6,2]]
Output: 4

Input:  n=2, roads=[[1,0,10]]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Dijkstra with Path Counting

Standard Dijkstra finds the shortest distance.
We extend it with a `ways[]` array:

```
dist[v]  = shortest distance from 0 to v
ways[v]  = number of shortest paths from 0 to v
```

**Three cases when relaxing edge u → v with weight w:**

```
newDist = dist[u] + w

Case 1: newDist < dist[v]  →  SHORTER path found
  dist[v] = newDist
  ways[v] = ways[u]         ← all old paths are obsolete
  Push to heap.

Case 2: newDist == dist[v]  →  EQUAL-length path found
  ways[v] += ways[u]        ← u contributes more shortest paths
  Do NOT push (distance unchanged, already in heap).

Case 3: newDist > dist[v]  →  LONGER path, ignore.
```

**Why `ways[v] = ways[u]` in Case 1?**

A shorter path was found. Every previous path to `v` was longer --
they no longer count as "shortest." Reset.
The new count = however many shortest paths reach `u`
(each of those extends to `v` via this edge).

**Why `ways[v] += ways[u]` in Case 2?**

An equally short path was found through `u`.
The existing paths to `v` are still valid.
Add `u`'s paths on top.

```
Time:  O((V + E) log V)
Space: O(V + E)
```

---

### 📜 The Scroll of the Many Roads

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;
```

---

## 🏗️ Build the Weighted Undirected Graph

```cpp
int countPaths(int n, vector<vector<int>>& roads) {
    const int MOD = 1e9 + 7;
    vector<vector<pair<int, long long>>> graph(n);
```

Adjacency list. Using `long long` for weights
because travel times can be large and sums can overflow.

---

```cpp
    for (auto& r : roads) {
        int u = r[0], v = r[1];
        long long w = r[2];
        graph[u].push_back({v, w});
        graph[v].push_back({u, w});
    }
```

**Undirected** -- add both directions.
Road between `u` and `v` means `u → v` AND `v → u`.

---

## 🗺️ Initialize Distance and Ways Arrays

```cpp
    vector<long long> dist(n, LLONG_MAX);
    vector<long long> ways(n, 0);
```

`dist[i]` = shortest distance from 0 to i. Starts at infinity.
`ways[i]` = number of shortest paths from 0 to i. Starts at 0.

Using `long long` for distances to prevent overflow.

---

```cpp
    dist[0] = 0;
    ways[0] = 1;
```

Source node 0: distance 0, exactly 1 way (being there).

---

## 📦 The Min-Heap

```cpp
    priority_queue<pair<long long, int>,
                   vector<pair<long long, int>>,
                   greater<pair<long long, int>>> pq;
```

Min-heap of `{distance, node}`.

---

```cpp
    pq.push({0, 0});
```

Push the source.

---

## 🌊 Modified Dijkstra Loop

```cpp
    while (!pq.empty()) {
        auto [d, node] = pq.top();
        pq.pop();
```

Pop the node with the smallest distance.

---

### 🛡️ Stale Entry Check

```cpp
        if (d > dist[node]) continue;
```

Outdated entry. A shorter path was already found. Skip.

---

### 🔁 Relax All Neighbors

```cpp
        for (auto& [neighbor, weight] : graph[node]) {
            long long newDist = dist[node] + weight;
```

Compute the distance to the neighbor through this node.

---

### 📐 Case 1 -- Shorter Path Found

```cpp
            if (newDist < dist[neighbor]) {
                dist[neighbor] = newDist;
                ways[neighbor] = ways[node];
                pq.push({newDist, neighbor});
            }
```

A strictly shorter path to the neighbor was discovered.

**`dist[neighbor] = newDist`** -- update the shortest distance.

**`ways[neighbor] = ways[node]`** -- RESET the count.
All previous paths were longer -- they're obsolete.
The new count = the number of shortest paths to `node`
(each one extends to `neighbor` via this edge).

**Push to heap** -- the neighbor needs to be re-processed
with its new shorter distance.

> _"A shorter road was found.
> Everything we knew before is outdated.
> The count starts fresh --
> inheriting only the paths that led here."_

---

### 📐 Case 2 -- Equal-Length Path Found

```cpp
            else if (newDist == dist[neighbor]) {
                ways[neighbor] = (ways[neighbor] + ways[node]) % MOD;
            }
```

An equally short path was found through `node`.

**`ways[neighbor] += ways[node]`** -- ADD to the count.
The existing shortest paths are still valid.
`node` provides additional shortest paths.

**Do NOT push to heap** -- the distance didn't change.
The neighbor is already in the heap (or already processed)
with the correct distance.

**Modulo** to prevent overflow (answer can be huge).

> _"Another road of the same length was found.
> The old paths still count.
> The new paths are added on top.
> The total grows."_

---

### Case 3 -- Longer Path → Ignored

No code needed. `newDist > dist[neighbor]` falls through.
A longer path is never useful.

---

### Close the Loop

```cpp
        }
    }
```

---

## 🏁 Return the Answer

```cpp
    return ways[n - 1];
}
```

`ways[n-1]` = number of shortest paths from 0 to n-1.

---

### 🎺 The Trial of the Many Roads

```cpp
int main() {
    vector<vector<int>> r1 = {
        {0,6,7},{0,1,2},{1,2,3},{1,3,3},{6,3,3},
        {3,5,1},{6,5,1},{2,5,1},{0,4,5},{4,6,2}
    };
    cout << countPaths(7, r1) << endl; // expected: 4

    vector<vector<int>> r2 = {{1,0,10}};
    cout << countPaths(2, r2) << endl; // expected: 1

    return 0;
}
```

---

**Trace for n=2, roads=[[1,0,10]]:**

```
Graph: 0 ↔ 1 (w=10)
```

Dijkstra from 0: dist[0]=0, dist[1]=10. ways[0]=1, ways[1]=1.

Only one path: 0→1. **Answer: 1** ✓

---

**Partial trace for the 7-node example:**

```
Shortest distance from 0 to 6 = 7.

Four shortest paths (all cost 7):
  0 → 6                    (direct, cost 7)
  0 → 1 → 2 → 5 → 6       (2+3+1+1 = 7)
  0 → 1 → 3 → 5 → 6       (2+3+1+1 = 7)
  0 → 4 → 6                (5+2 = 7)
```

During Dijkstra, when node 6 is reached:
-   First via direct edge (cost 7): ways[6] = ways[0] = 1.
-   Then via 0→4→6 (cost 7): equal → ways[6] += ways[4].
-   Then via paths through 5→6 (cost 7): equal → ways[6] += ways[5].

The ways accumulate as equal-length paths are discovered.

**Answer: 4** ✓

---

**Trace for a simple diamond:**

```
n=4, roads=[[0,1,1],[0,2,1],[1,3,1],[2,3,1]]

Graph:
  0 → 1 (1)
  0 → 2 (1)
  1 → 3 (1)
  2 → 3 (1)
```

| Pop (d, node) | Relax                                          | dist      | ways      |
| ------------- | ---------------------------------------------- | --------- | --------- |
| (0, 0)        | 0→1: d=1 < INF → dist[1]=1, ways[1]=1         | [0,1,INF,INF] | [1,1,0,0] |
|               | 0→2: d=1 < INF → dist[2]=1, ways[2]=1         | [0,1,1,INF]   | [1,1,1,0] |
| (1, 1)        | 1→3: d=2 < INF → dist[3]=2, ways[3]=1         | [0,1,1,2]     | [1,1,1,1] |
| (1, 2)        | 2→3: d=2 == 2 → ways[3] += ways[2] = 1+1 = 2 | [0,1,1,2]     | [1,1,1,2] |
| (2, 3)        | destination reached.                           |               |           |

**Answer: ways[3] = 2** ✓

Two shortest paths: 0→1→3 and 0→2→3. Both cost 2.

---

## 🔍 Why `ways[v] = ways[u]` (Reset) on Shorter Path?

When a shorter path is found, ALL previous paths to `v` were longer.
They no longer qualify as "shortest."

Example:
-   First, we find path A→B→v with cost 10. ways[v] = 3.
-   Later, we find path A→C→v with cost 8. SHORTER!
-   The 3 paths of cost 10 are no longer shortest.
-   Reset: ways[v] = ways[C] (however many shortest paths reach C).

If we did `ways[v] += ways[u]` here, we'd be counting
the old longer paths alongside the new shorter ones. Wrong.

---

## 🔍 Why `long long` Everywhere?

-   **Distances:** travel times can be up to 10⁹.
    Sum of two edges can exceed `INT_MAX` (2 × 10⁹ > 2.1 × 10⁹).
    Use `long long` for `dist[]` and `newDist`.

-   **Ways:** the count can be astronomically large.
    Modulo keeps it bounded, but intermediate additions
    of two modded values can still exceed `int` range.
    Use `long long` for `ways[]`.

-   **Heap:** stores distances as `long long`.

---

## 🔍 Why Modulo Only on Ways, Not Distances?

Distances must be exact for correct comparisons.
If you modulo a distance, `dist[v] < dist[u]` might give wrong results.

Ways are just counts -- modulo doesn't affect the algorithm's logic.
We only read `ways[v]` at the end, never compare it.

---

### 🧠 Memory of the Many Roads Law

-   **Dijkstra + ways[] array** -- count shortest paths alongside distances
-   **Three cases on relaxation:**
    -   `newDist < dist[v]` → RESET: `dist[v] = newDist`, `ways[v] = ways[u]`, push
    -   `newDist == dist[v]` → ADD: `ways[v] += ways[u]`, do NOT push
    -   `newDist > dist[v]` → ignore
-   **Reset vs Add** is the key distinction:
    -   Shorter path → old paths obsolete → reset count
    -   Equal path → old paths still valid → add to count
-   **Use `long long`** for distances (overflow) and ways (large counts)
-   **Modulo** only on ways, never on distances
-   **Undirected graph** → add both directions
-   **Time:** O((V + E) log V)
-   **Space:** O(V + E)
-   **Edge cases:**
    -   Direct edge is the only shortest path → 1
    -   Multiple equal-cost paths → sum of all ways
    -   n = 1 → ways[0] = 1 (already there)

Thus is remembered the saga of
**Number of Ways to Arrive at Destination**,
where the Oracle extended Dijkstra with a counting array --
resetting the count when a shorter road was found,
adding to it when an equally short road appeared --
until every shortest path to the destination
was counted and the total was known. 🛤️🔢✨
