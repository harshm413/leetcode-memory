## ✈️💰 _The Budget Traveler's Dilemma: The Cheapest Flights Within K Stops Saga_

> \_"In the Kingdom of Airports,
> flights connected cities with different prices.
> A traveler needed to fly from `src` to `dst`.
>
> But there was a constraint:
>
> **At most K stops allowed.**
>
> A direct flight = 0 stops.
> One layover = 1 stop.
> Two layovers = 2 stops.
>
> The Oracle was commanded:
>
> **'Find the cheapest price to fly from src to dst
> with at most K stops.
> If no such route exists -- return -1.'**
>
> The Oracle considered her options:
>
> **Dijkstra?** Tricky -- the K-stop constraint
> means a longer but cheaper path might be invalid
> if it uses too many stops.
> Dijkstra doesn't naturally handle hop constraints.
>
> **Bellman-Ford?** Perfect.
> Bellman-Ford relaxes ALL edges in each iteration.
> After `i` iterations, it finds shortest paths
> using at most `i` edges.
> K stops = K+1 edges.
> Run Bellman-Ford for exactly K+1 iterations.
>
> **BFS with pruning?** Also works.
> BFS level by level, where each level = one flight.
> Track the cheapest known cost to each city.
> Prune paths that are already more expensive.
>
> The Oracle chose **Modified Bellman-Ford** --
> clean, correct, and naturally respects the hop limit."\_

---

This is the saga of **Cheapest Flights Within K Stops**.

Given:
-   `n` cities, `flights` as `[from, to, price]`.
-   `src` (source), `dst` (destination), `k` (max stops).

Return the **cheapest price** from `src` to `dst`
with at most `k` stops. If impossible, return `-1`.

```
Input:  n=4, flights=[[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]],
        src=0, dst=3, k=1
Output: 700   (0→1→3, price 100+600=700, 1 stop)

Input:  n=3, flights=[[0,1,100],[1,2,100],[0,2,500]],
        src=0, dst=2, k=1
Output: 200   (0→1→2, price 100+100=200, 1 stop)

Input:  n=3, flights=[[0,1,100],[1,2,100],[0,2,500]],
        src=0, dst=2, k=0
Output: 500   (0→2 direct, 0 stops)
```

---

## 🧠 The Oracle's Core Insight -- Bellman-Ford with Limited Iterations

Standard Bellman-Ford runs `V-1` iterations
to find shortest paths with any number of edges.

**Key property:** after `i` iterations of Bellman-Ford,
`dist[v]` holds the shortest path to `v` using **at most `i` edges**.

K stops = K+1 flights (edges).
So run Bellman-Ford for exactly **K+1 iterations**.

**Critical detail:** in each iteration,
use the distances from the PREVIOUS iteration --
not the current one being updated.
Otherwise, a single iteration might chain multiple relaxations,
effectively using more edges than intended.

```
Time:  O(K × E) -- K+1 iterations, each scans all edges
Space: O(V)
```

---

### 📜 The Scroll of the Budget Traveler

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## ✈️ The Oracle's Modified Bellman-Ford Ritual

```cpp
int findCheapestPrice(int n, vector<vector<int>>& flights,
                      int src, int dst, int k) {
```

---

### 🗺️ Initialize Distances

```cpp
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;
```

`dist[i]` = cheapest known price to reach city `i` from `src`.
All start at `INT_MAX` (unreachable) except the source (0).

---

## 🔁 Run K+1 Iterations

```cpp
    for (int i = 0; i <= k; i++) {
```

Each iteration allows one more flight (edge).
After `k+1` iterations, paths with at most `k+1` edges
(= `k` stops) are found.

---

### 📸 Snapshot the Previous Distances

```cpp
        vector<int> prev = dist;
```

**This is the most critical line.**

We copy the ENTIRE distance array before this iteration.
All relaxations in this iteration use `prev` (last iteration's values) --
NOT the current `dist` being updated.

**Why?**

Without the copy, a single iteration could chain:
`dist[A]` updated → immediately used to update `dist[B]` →
used to update `dist[C]`. That's 3 edges in 1 iteration.
The hop count would be wrong.

With the copy, each iteration adds exactly ONE edge.
`prev[u]` is the cost using at most `i` edges.
`dist[v] = prev[u] + price` uses at most `i+1` edges.

> _"The snapshot freezes the past.
> This iteration can only build on what was known BEFORE --
> not on what is being discovered NOW.
> This ensures each iteration adds exactly one flight."_

---

### 🔁 Relax All Edges Using Previous Distances

```cpp
        for (auto& f : flights) {
            int from = f[0];
            int to = f[1];
            int price = f[2];
```

Each flight is an edge: `from → to` with cost `price`.

---

### 📐 The Relaxation

```cpp
            if (prev[from] != INT_MAX && prev[from] + price < dist[to]) {
                dist[to] = prev[from] + price;
            }
        }
    }
```

**`prev[from] != INT_MAX`** -- the source city must be reachable
(using at most `i` edges from the previous iteration).

**`prev[from] + price < dist[to]`** -- this flight offers
a cheaper route to `to` than any previously known.

If both conditions hold → update `dist[to]`.

Note: we read from `prev` but write to `dist`.
This is what prevents chaining within one iteration.

> _"Can I reach `from` using the flights I had before?
> If yes -- does flying from there to `to` beat the current best?
> If yes -- update. But only using yesterday's knowledge."_

---

## 🏁 Return the Answer

```cpp
    return dist[dst] == INT_MAX ? -1 : dist[dst];
}
```

If `dist[dst]` is still `INT_MAX` → destination unreachable
within K stops. Return `-1`.

Otherwise → return the cheapest price.

---

### 🎺 The Trial of the Budget Traveler

```cpp
int main() {
    vector<vector<int>> f1 = {{0,1,100},{1,2,100},{2,0,100},{1,3,600},{2,3,200}};
    cout << findCheapestPrice(4, f1, 0, 3, 1) << endl; // expected: 700

    vector<vector<int>> f2 = {{0,1,100},{1,2,100},{0,2,500}};
    cout << findCheapestPrice(3, f2, 0, 2, 1) << endl; // expected: 200

    cout << findCheapestPrice(3, f2, 0, 2, 0) << endl; // expected: 500

    return 0;
}
```

---

**Full trace for n=3, flights=[[0,1,100],[1,2,100],[0,2,500]], src=0, dst=2, k=1:**

```
Flights: 0→1 ($100), 1→2 ($100), 0→2 ($500)
K=1 → at most 2 flights (1 stop).
```

**Initial:** `dist = [0, INF, INF]`

**Iteration 0 (1st flight allowed):**
`prev = [0, INF, INF]`

| Flight    | prev[from] | prev[from]+price | dist[to] before | Update?          |
| --------- | ---------- | ---------------- | --------------- | ---------------- |
| 0→1 $100  | 0          | 100              | INF             | dist[1] = 100    |
| 1→2 $100  | INF        | --               | --              | Skip (unreachable)|
| 0→2 $500  | 0          | 500              | INF             | dist[2] = 500    |

After iter 0: `dist = [0, 100, 500]`

**Iteration 1 (2nd flight allowed):**
`prev = [0, 100, 500]`

| Flight    | prev[from] | prev[from]+price | dist[to] before | Update?          |
| --------- | ---------- | ---------------- | --------------- | ---------------- |
| 0→1 $100  | 0          | 100              | 100             | No (not cheaper) |
| 1→2 $100  | 100        | 200              | 500             | dist[2] = 200    |
| 0→2 $500  | 0          | 500              | 200             | No (not cheaper) |

After iter 1: `dist = [0, 100, 200]`

**Answer: dist[2] = 200** ✓

Path: 0→1→2. Price: 100+100=200. Stops: 1. Within limit.

---

**Trace for same flights but k=0 (direct flights only):**

**Iteration 0:**
`prev = [0, INF, INF]`

| Flight    | Update?          |
| --------- | ---------------- |
| 0→1 $100  | dist[1] = 100    |
| 1→2 $100  | Skip (1 unreachable in prev) |
| 0→2 $500  | dist[2] = 500    |

Only 1 iteration (k+1 = 1). Done.

**Answer: dist[2] = 500** ✓

Only direct flights allowed. 0→2 costs $500.
The cheaper 0→1→2 ($200) requires 1 stop -- not allowed with k=0.

---

**Trace for n=4, src=0, dst=3, k=1:**

```
Flights: 0→1($100), 1→2($100), 2→0($100), 1→3($600), 2→3($200)
```

**Iteration 0:** `prev = [0, INF, INF, INF]`
-   0→1: dist[1] = 100.
-   0→2: not a flight. (No direct 0→2 edge.)
-   1→3: skip (1 unreachable in prev).

After: `dist = [0, 100, INF, INF]`

**Iteration 1:** `prev = [0, 100, INF, INF]`
-   0→1: 100, not cheaper than 100.
-   1→2: prev[1]=100, 100+100=200. dist[2]=200.
-   1→3: prev[1]=100, 100+600=700. dist[3]=700.
-   2→3: prev[2]=INF. Skip.

After: `dist = [0, 100, 200, 700]`

**Answer: dist[3] = 700** ✓

Path: 0→1→3. Price: 700. 1 stop.
The path 0→1→2→3 ($400) would be cheaper but needs 2 stops -- exceeds k=1.

---

## 🔍 Why Not Standard Dijkstra?

Standard Dijkstra finds the cheapest path regardless of hops.
It might find a 5-hop path costing $200
and ignore a 1-hop path costing $500.

With the K-stop constraint, the 5-hop path might be invalid.
Dijkstra doesn't track hop count naturally.

You CAN modify Dijkstra with state `(cost, city, stops_remaining)`,
but it's more complex and can be slower
(the state space explodes).

Bellman-Ford with K+1 iterations is cleaner --
the iteration count IS the hop count.

---

## 🔍 Why Copy the Distance Array?

Without the copy:

```
Iteration 0:
  0→1: dist[1] = 100.
  1→2: dist[1] is now 100 (just updated!) → dist[2] = 200.
```

Two flights used in ONE iteration. The hop count is wrong.
With k=0, this would incorrectly allow 0→1→2.

With the copy:

```
Iteration 0:
  0→1: dist[1] = 100.  (using prev[0] = 0)
  1→2: prev[1] = INF.  Skip.  (prev hasn't been updated)
```

Only one flight per iteration. Correct.

> _"The copy is the firewall between iterations.
> Without it, the present contaminates the past,
> and the hop count becomes a lie."_

---

## 🔄 BFS Alternative

Level-by-level BFS where each level = one flight:

```cpp
int findCheapestPrice(int n, vector<vector<int>>& flights,
                      int src, int dst, int k) {
    vector<vector<pair<int,int>>> graph(n);
    for (auto& f : flights) graph[f[0]].push_back({f[1], f[2]});

    vector<int> dist(n, INT_MAX);
    dist[src] = 0;
    queue<pair<int,int>> q; // {city, cost}
    q.push({src, 0});

    int stops = 0;
    while (!q.empty() && stops <= k) {
        int size = q.size();
        for (int i = 0; i < size; i++) {
            auto [city, cost] = q.front(); q.pop();
            for (auto& [next, price] : graph[city]) {
                int newCost = cost + price;
                if (newCost < dist[next]) {
                    dist[next] = newCost;
                    q.push({next, newCost});
                }
            }
        }
        stops++;
    }
    return dist[dst] == INT_MAX ? -1 : dist[dst];
}
```

Each BFS level = one flight. Stop after K+1 levels.
Same idea as Bellman-Ford but with adjacency list traversal.

---

### 🧠 Memory of the Budget Traveler Law

-   **Modified Bellman-Ford** with exactly K+1 iterations
-   **K stops = K+1 edges** (flights)
-   **Critical: copy dist array** before each iteration (`prev = dist`)
    -   Read from `prev`, write to `dist`
    -   Prevents chaining multiple flights in one iteration
-   **Relaxation:** `if (prev[from] != INT_MAX && prev[from] + price < dist[to])`
-   **After K+1 iterations:** `dist[dst]` = cheapest price within K stops
-   **BFS alternative:** level-by-level, each level = one flight, stop after K+1 levels
-   **Dijkstra is tricky** -- doesn't naturally handle hop constraints
-   **Time:** O(K × E)
-   **Space:** O(V)
-   **Edge cases:**
    -   k=0 → only direct flights
    -   No path within K stops → -1
    -   src == dst → 0

Thus is remembered the saga of **Cheapest Flights Within K Stops**,
where the Oracle adapted Bellman-Ford to the skies --
running exactly K+1 iterations,
each adding one more flight to the journey,
always reading from yesterday's distances
to ensure the hop count stayed honest --
until the cheapest route within the budget of stops
was found. ✈️💰✨
