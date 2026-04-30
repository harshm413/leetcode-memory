## 📡⏱️ _The Signal Across the Network: The Network Delay Time Saga_

> \_"In the Kingdom of Networks,
> nodes were connected by directed, weighted edges --
> each edge carrying a travel time.
>
> A signal was sent from a source node `k`.
> It traveled along every outgoing edge simultaneously,
> spreading through the network like ripples.
>
> The Oracle was commanded:
>
> **'How long until EVERY node has received the signal?
> If some node can never be reached -- return -1.'**
>
> The Oracle recognized this as the textbook application
> of **Dijkstra's Algorithm**:
>
> **Find the shortest path from `k` to EVERY other node.
> The answer is the MAXIMUM of all shortest paths --
> because the signal reaches every node at its shortest time,
> and the last node to be reached determines the total delay.**
>
> Dijkstra from source `k`.
> Find the max distance among all reachable nodes.
> If any node is unreachable -- return -1."\_

---

This is the saga of **Network Delay Time**.

Given:
-   `n` nodes labeled `1` to `n`.
-   `times[i] = [u, v, w]` -- directed edge from `u` to `v` with travel time `w`.
-   A signal starts at node `k`.

Return the **minimum time** for all nodes to receive the signal.
If not all nodes can be reached, return `-1`.

```
Input:  times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2
Output: 2

Input:  times = [[1,2,1]], n = 2, k = 1
Output: 1

Input:  times = [[1,2,1]], n = 2, k = 2
Output: -1  (node 1 unreachable from node 2)
```

---

## 🧠 The Oracle's Core Insight -- Dijkstra + Max of All Distances

This is single-source shortest path on a weighted directed graph
with non-negative weights. Textbook Dijkstra.

```
1. Run Dijkstra from node k.
2. dist[i] = shortest time for signal to reach node i.
3. Answer = max(dist[1], dist[2], ..., dist[n]).
4. If any dist[i] == INF → some node unreachable → return -1.
```

The signal travels along ALL edges simultaneously.
Each node receives the signal at its shortest-path time.
The LAST node to receive it determines the total delay.

```
Time:  O((V + E) log V) -- Dijkstra with min-heap
Space: O(V + E)
```

---

### 📜 The Scroll of the Signal Network

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;
```

---

## 🏗️ Build the Weighted Directed Graph

```cpp
int networkDelayTime(vector<vector<int>>& times, int n, int k) {
    vector<vector<pair<int, int>>> graph(n + 1);
```

Adjacency list for nodes `1` to `n`.
Size `n + 1` because nodes are **1-indexed**.

---

```cpp
    for (auto& t : times) {
        int u = t[0];
        int v = t[1];
        int w = t[2];
        graph[u].push_back({v, w});
    }
```

For each edge `[u, v, w]`:
add `{v, w}` (neighbor, weight) to `graph[u]`.

**Directed** -- only one direction. `u → v`, NOT `v → u`.

---

## 🗺️ Initialize Distances

```cpp
    vector<int> dist(n + 1, INT_MAX);
    dist[k] = 0;
```

`dist[i]` = shortest time to reach node `i` from source `k`.
All start at `INT_MAX` (unreachable) except the source (0).

---

## 📦 The Min-Heap

```cpp
    priority_queue<pair<int, int>,
                   vector<pair<int, int>>,
                   greater<pair<int, int>>> pq;
```

A min-heap of `{distance, node}`.
The node with the smallest known distance is always on top.

`greater<>` flips C++'s default max-heap into a min-heap.

---

### 🏁 Push the Source

```cpp
    pq.push({0, k});
```

Source `k` has distance 0. It enters the heap.

---

## 🌊 Dijkstra's Main Loop

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

The heap may contain old entries with longer distances.
If this entry is outdated -- skip it.

> _"If someone already delivered the signal faster,
> this messenger is too late. Dismiss them."_

---

### 🔁 Relax All Outgoing Edges

```cpp
        for (auto& [neighbor, weight] : graph[node]) {
```

Check every outgoing edge from this node.

---

### 📐 Compute New Distance

```cpp
            int newDist = dist[node] + weight;
```

The time to reach the neighbor through this node =
time to reach this node + travel time of the edge.

---

### ✅ If Shorter -- Update and Push

```cpp
            if (newDist < dist[neighbor]) {
                dist[neighbor] = newDist;
                pq.push({newDist, neighbor});
            }
        }
    }
```

If this path is faster than any previously known path
to the neighbor → update and push to heap.

---

## 🏁 Find the Maximum Distance

```cpp
    int maxTime = 0;
    for (int i = 1; i <= n; i++) {
        if (dist[i] == INT_MAX) return -1;
        maxTime = max(maxTime, dist[i]);
    }
    return maxTime;
}
```

Walk through all nodes (1 to n).

**If any node is unreachable** (`INT_MAX`) → the signal
can never reach everyone. Return `-1`.

**Otherwise** → the answer is the maximum distance.
The last node to receive the signal determines the total delay.

> _"The network is only as fast as its slowest delivery.
> The signal reaches everyone at their shortest time --
> but the LAST arrival is what the King cares about."_

---

### 🎺 The Trial of the Signal Network

```cpp
int main() {
    vector<vector<int>> t1 = {{2,1,1},{2,3,1},{3,4,1}};
    cout << networkDelayTime(t1, 4, 2) << endl; // expected: 2

    vector<vector<int>> t2 = {{1,2,1}};
    cout << networkDelayTime(t2, 2, 1) << endl; // expected: 1

    vector<vector<int>> t3 = {{1,2,1}};
    cout << networkDelayTime(t3, 2, 2) << endl; // expected: -1

    return 0;
}
```

---

**Full trace for times=[[2,1,1],[2,3,1],[3,4,1]], n=4, k=2:**

```
Graph:
  2 → 1 (w=1)
  2 → 3 (w=1)
  3 → 4 (w=1)

Source: node 2.
```

**Initial:** `dist = [_, INF, 0, INF, INF]` (index 0 unused).

| Pop (d, node) | Relax                              | dist after          |
| ------------- | ---------------------------------- | ------------------- |
| (0, 2)        | 2→1: 0+1=1 < INF → dist[1]=1      | [_, 1, 0, INF, INF] |
|               | 2→3: 0+1=1 < INF → dist[3]=1      | [_, 1, 0, 1, INF]   |
| (1, 1)        | No outgoing edges from 1.          | (no change)         |
| (1, 3)        | 3→4: 1+1=2 < INF → dist[4]=2      | [_, 1, 0, 1, 2]     |
| (2, 4)        | No outgoing edges from 4.          | (no change)         |

**All nodes reachable.** `dist = [_, 1, 0, 1, 2]`.

`maxTime = max(1, 0, 1, 2) = 2`.

**Answer: 2** ✓

Signal reaches node 1 at time 1, node 3 at time 1, node 4 at time 2.
The last arrival is node 4 at time 2.

---

**Trace for times=[[1,2,1]], n=2, k=1:**

```
Graph: 1 → 2 (w=1).
```

Dijkstra from 1: dist[1]=0, dist[2]=1.
maxTime = 1.

**Answer: 1** ✓

---

**Trace for times=[[1,2,1]], n=2, k=2:**

```
Graph: 1 → 2 (w=1). Source = 2.
```

Dijkstra from 2: dist[2]=0. No outgoing edges from 2.
dist[1] = INF → unreachable.

**Answer: -1** ✓

The edge goes 1→2, not 2→1. Directed graph.
Node 1 cannot be reached from node 2.

---

**Trace for a more complex graph:**

```
times = [[1,2,1],[1,3,4],[2,3,2]], n=3, k=1

Graph:
  1 → 2 (w=1)
  1 → 3 (w=4)
  2 → 3 (w=2)
```

| Pop (d, node) | Relax                              | dist after     |
| ------------- | ---------------------------------- | -------------- |
| (0, 1)        | 1→2: 0+1=1. 1→3: 0+4=4.           | [_, 0, 1, 4]   |
| (1, 2)        | 2→3: 1+2=3 < 4 → dist[3]=3.       | [_, 0, 1, 3]   |
| (3, 3)        | No outgoing.                       | (no change)    |

maxTime = max(0, 1, 3) = 3.

**Answer: 3** ✓

The direct path 1→3 costs 4.
The path 1→2→3 costs 1+2=3. Cheaper.
Dijkstra found the shorter path.

---

## 🔍 Why Dijkstra and Not BFS?

The graph is **weighted** -- edges have different travel times.
BFS only works for unweighted graphs (all edges cost 1).

A 1-hop path with weight 100 is slower than
a 3-hop path with total weight 5.
BFS would pick the 1-hop path. Wrong.

Dijkstra's min-heap ensures we always process
the globally closest node first -- regardless of hop count.

---

## 🔍 Why Max of All Distances?

The signal travels along ALL edges simultaneously.
It's not choosing one path -- it's flooding the entire network.

Each node receives the signal at its shortest-path time.
The question asks: when has EVERY node received it?
That's the time of the LAST arrival = the maximum distance.

---

## 🔍 Why 1-Indexed?

Nodes are labeled `1` to `n` (not `0` to `n-1`).
The graph and dist arrays use size `n + 1`.
Index 0 is unused.

Always check the problem's indexing.

---

### 🧠 Memory of the Signal Network Law

-   **Dijkstra** from source `k` on a weighted directed graph
-   **Build graph:** `graph[u].push_back({v, w})` -- directed, 1-indexed
-   **Min-heap:** `{distance, node}` with `greater<>` for min-heap
-   **Lazy deletion:** skip stale entries (`d > dist[node]`)
-   **Relaxation:** `newDist = dist[node] + weight`. If shorter → update + push.
-   **Answer:** `max(dist[1..n])` -- the last node to receive the signal
-   **Unreachable:** if any `dist[i] == INT_MAX` → return -1
-   **Time:** O((V + E) log V)
-   **Space:** O(V + E)
-   **Edge cases:**
    -   Single node → 0 (signal already there)
    -   Disconnected node → -1
    -   All edges from source → max of direct weights

Thus is remembered the saga of **Network Delay Time**,
where the Oracle sent a signal from node k
and tracked its spread through Dijkstra's Algorithm --
finding the shortest time to every node,
then declaring the answer as the maximum --
because the network is only as fast
as its slowest delivery,
and the last node to hear the signal
sets the total delay. 📡⏱️✨
