## 🏙️🔍 _The Loneliest City: The Find the City With the Smallest Number of Neighbors Saga_

> \_"In the Kingdom of Connected Cities,
> roads linked cities with travel distances.
> Some cities were well-connected --
> many neighbors within easy reach.
> Others were isolated --
> few cities reachable within a given distance.
>
> The King issued a decree:
>
> **'Find the city that has the FEWEST
> neighboring cities reachable within
> a distance threshold.
> If multiple cities tie -- return the one
> with the GREATEST city number.'**
>
> The Oracle thought carefully.
>
> She needed to know the shortest distance
> between EVERY pair of cities.
> Then for each city, count how many others
> are within the threshold.
>
> **All-pairs shortest path → Floyd-Warshall.**
>
> Run Floyd-Warshall to compute the distance
> between every pair of cities.
> Then for each city, count reachable neighbors.
> Return the city with the smallest count
> (largest number to break ties).
>
> Alternatively, run **Dijkstra from every city** --
> but Floyd-Warshall is cleaner when n is small."\_

---

This is the saga of **Find the City With the Smallest Number of Neighbors at a Threshold Distance**.

Given:
-   `n` cities labeled `0` to `n-1`.
-   `edges[i] = [from, to, weight]` -- bidirectional weighted roads.
-   `distanceThreshold` -- the maximum travel distance.

For each city, count how many OTHER cities are reachable
within `distanceThreshold` (using shortest paths).

Return the city with the **smallest** count.
If tied, return the city with the **greatest** number.

```
Input:  n = 4, edges = [[0,1,3],[1,2,1],[1,3,4],[2,3,1]], distanceThreshold = 4
Output: 3

Input:  n = 5, edges = [[0,1,2],[0,4,8],[1,2,3],[1,4,2],[2,3,1],[3,4,1]],
        distanceThreshold = 2
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Floyd-Warshall + Count

This is a direct application of **Floyd-Warshall**:

```
1. Build the distance matrix from edges.
2. Run Floyd-Warshall to find all-pairs shortest paths.
3. For each city i, count cities j where dist[i][j] <= threshold.
4. Return the city with the smallest count (largest number for ties).
```

**Why Floyd-Warshall and not Dijkstra?**

Both work. Floyd-Warshall is O(V³), Dijkstra from every node is O(V × (V+E) log V).
For small `n` (≤ 100), Floyd-Warshall is simpler -- no heap, no adjacency list,
just a triple loop on the matrix.

```
Time:  O(V³)
Space: O(V²)
```

---

### 📜 The Scroll of the Loneliest City

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;
```

---

## 🏗️ Step One -- Build the Distance Matrix

```cpp
int findTheCity(int n, vector<vector<int>>& edges, int distanceThreshold) {
    vector<vector<int>> dist(n, vector<int>(n, INT_MAX));
```

`dist[i][j]` = shortest distance from city `i` to city `j`.
All start at `INT_MAX` (no path known yet).

---

### 🔲 Diagonal -- Distance to Self Is Zero

```cpp
    for (int i = 0; i < n; i++) {
        dist[i][i] = 0;
    }
```

Every city is distance 0 from itself.

---

### 🛤️ Fill Direct Edges

```cpp
    for (auto& e : edges) {
        int u = e[0];
        int v = e[1];
        int w = e[2];
        dist[u][v] = w;
        dist[v][u] = w;
    }
```

For each bidirectional edge, set the distance in both directions.

> _"The matrix begins with only direct roads.
> Floyd-Warshall will discover the shortcuts --
> paths through intermediate cities
> that are shorter than the direct road."_

---

## 🌐 Step Two -- Floyd-Warshall (All-Pairs Shortest Path)

```cpp
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
```

The triple loop. `k` is the intermediate node.
For every pair `(i, j)`, ask:
"Can going through city `k` give a shorter path?"

---

### 📐 The Relaxation

```cpp
                if (dist[i][k] != INT_MAX && dist[k][j] != INT_MAX) {
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
```

If both `i → k` and `k → j` are reachable,
check if the path `i → k → j` is shorter than the current `i → j`.

**`dist[i][k] != INT_MAX && dist[k][j] != INT_MAX`** --
prevents overflow. Can't add to infinity.

**`k` must be the outermost loop.**
When considering `k` as intermediate,
all paths through nodes `0..k-1` must already be computed.

> _"For every possible waypoint k,
> the Oracle asks every pair of cities:
> 'Is the detour through k shorter than your current road?'
> If yes -- the shortcut is recorded."_

---

## 🔢 Step Three -- Count Reachable Neighbors for Each City

```cpp
    int resultCity = -1;
    int minCount = n;
```

`resultCity` = the answer (city with fewest neighbors).
`minCount` = the smallest neighbor count seen so far.
Initialized to `n` (worst case: all cities reachable).

---

```cpp
    for (int i = 0; i < n; i++) {
        int count = 0;
        for (int j = 0; j < n; j++) {
            if (i != j && dist[i][j] <= distanceThreshold) {
                count++;
            }
        }
```

For each city `i`, count how many OTHER cities `j`
have a shortest distance within the threshold.

`i != j` -- don't count yourself.

---

### 🏆 Update the Answer

```cpp
        if (count <= minCount) {
            minCount = count;
            resultCity = i;
        }
    }
    return resultCity;
}
```

If this city has fewer (or equal) reachable neighbors → update.

**Why `<=` and not `<`?**
The problem says: "If there are multiple such cities,
return the city with the greatest number."

Using `<=` means when two cities tie,
the LATER one (greater number) overwrites the earlier one.
Since we iterate `i` from 0 to n-1,
the last city with the minimum count wins.

> _"When two cities are equally lonely,
> the one with the greater number takes the crown.
> The `<=` ensures the later city always wins the tie."_

---

### 🎺 The Trial of the Loneliest City

```cpp
int main() {
    vector<vector<int>> e1 = {{0,1,3},{1,2,1},{1,3,4},{2,3,1}};
    cout << findTheCity(4, e1, 4) << endl; // expected: 3

    vector<vector<int>> e2 = {{0,1,2},{0,4,8},{1,2,3},{1,4,2},{2,3,1},{3,4,1}};
    cout << findTheCity(5, e2, 2) << endl; // expected: 0

    return 0;
}
```

---

**Full trace for n=4, edges=[[0,1,3],[1,2,1],[1,3,4],[2,3,1]], threshold=4:**

**Initial distance matrix (after filling edges):**

```
     0    1    2    3
0: [ 0    3   INF  INF ]
1: [ 3    0    1    4  ]
2: [INF   1    0    1  ]
3: [INF   4    1    0  ]
```

**After Floyd-Warshall:**

k=0: path through 0. No improvements (0 only connects to 1 directly).

k=1: path through 1.
- dist[0][2] = min(INF, dist[0][1] + dist[1][2]) = min(INF, 3+1) = 4.
- dist[0][3] = min(INF, dist[0][1] + dist[1][3]) = min(INF, 3+4) = 7.
- dist[2][0] = min(INF, dist[2][1] + dist[1][0]) = min(INF, 1+3) = 4.
- dist[3][0] = min(INF, dist[3][1] + dist[1][0]) = min(INF, 4+3) = 7.

k=2: path through 2.
- dist[0][3] = min(7, dist[0][2] + dist[2][3]) = min(7, 4+1) = 5.
- dist[1][3] = min(4, dist[1][2] + dist[2][3]) = min(4, 1+1) = 2.
- dist[3][0] = min(7, dist[3][2] + dist[2][0]) = min(7, 1+4) = 5.
- dist[3][1] = min(4, dist[3][2] + dist[2][1]) = min(4, 1+1) = 2.

k=3: path through 3.
- dist[0][2] = min(4, dist[0][3] + dist[3][2]) = min(4, 5+1) = 4 (no change).
- dist[1][0] = min(3, dist[1][3] + dist[3][0]) = min(3, 2+5) = 3 (no change).

**Final distance matrix:**

```
     0    1    2    3
0: [ 0    3    4    5  ]
1: [ 3    0    1    2  ]
2: [ 4    1    0    1  ]
3: [ 5    2    1    0  ]
```

**Count neighbors within threshold=4:**

| City | Neighbors within dist ≤ 4          | Count |
| ---- | ---------------------------------- | ----- |
| 0    | city 1 (dist=3 ✓), city 2 (dist=4 ✓), city 3 (dist=5 ✗) | 2 |
| 1    | city 0 (dist=3 ✓), city 2 (dist=1 ✓), city 3 (dist=2 ✓) | 3 |
| 2    | city 0 (dist=4 ✓), city 1 (dist=1 ✓), city 3 (dist=1 ✓) | 3 |
| 3    | city 0 (dist=5 ✗), city 1 (dist=2 ✓), city 2 (dist=1 ✓) | 2 |

Cities 0 and 3 both have count = 2 (tied for minimum).
Return the one with the **greatest number** → **city 3**.

**Answer: 3** ✓

---

**Trace for n=5, threshold=2:**

After Floyd-Warshall, count neighbors within distance ≤ 2:

| City | Neighbors within dist ≤ 2 | Count |
| ---- | ------------------------- | ----- |
| 0    | city 1 (dist=2 ✓)        | 1     |
| 1    | city 0 (dist=2 ✓), city 4 (dist=2 ✓) | 2 |
| 2    | city 3 (dist=1 ✓), city 4 (dist=2 ✓) | 2 |
| 3    | city 2 (dist=1 ✓), city 4 (dist=1 ✓) | 2 |
| 4    | city 1 (dist=2 ✓), city 2 (dist=2 ✓), city 3 (dist=1 ✓) | 3 |

City 0 has the smallest count (1).

**Answer: 0** ✓

---

## 🔄 Alternative -- Dijkstra from Every City

Instead of Floyd-Warshall, run Dijkstra from each city:

```cpp
int findTheCity(int n, vector<vector<int>>& edges, int distanceThreshold) {
    vector<vector<pair<int,int>>> graph(n);
    for (auto& e : edges) {
        graph[e[0]].push_back({e[1], e[2]});
        graph[e[1]].push_back({e[0], e[2]});
    }

    int resultCity = -1;
    int minCount = n;

    for (int i = 0; i < n; i++) {
        // Dijkstra from city i
        vector<int> dist(n, INT_MAX);
        priority_queue<pair<int,int>, vector<pair<int,int>>,
                        greater<pair<int,int>>> pq;
        dist[i] = 0;
        pq.push({0, i});

        while (!pq.empty()) {
            auto [d, node] = pq.top(); pq.pop();
            if (d > dist[node]) continue;
            for (auto& [neighbor, weight] : graph[node]) {
                if (dist[node] + weight < dist[neighbor]) {
                    dist[neighbor] = dist[node] + weight;
                    pq.push({dist[neighbor], neighbor});
                }
            }
        }

        // Count neighbors within threshold
        int count = 0;
        for (int j = 0; j < n; j++) {
            if (i != j && dist[j] <= distanceThreshold) {
                count++;
            }
        }

        if (count <= minCount) {
            minCount = count;
            resultCity = i;
        }
    }
    return resultCity;
}
```

Run Dijkstra `n` times (once per city).
For each run, count reachable neighbors within threshold.

```
Time:  O(V × (V + E) log V)
Space: O(V + E)
```

---

## 🔍 Floyd-Warshall vs Dijkstra -- Which to Choose Here

| Floyd-Warshall                    | Dijkstra from Every City          |
| --------------------------------- | --------------------------------- |
| O(V³)                             | O(V × (V+E) log V)               |
| Simple triple loop                | Needs heap, adjacency list        |
| Works for dense graphs            | Better for sparse graphs          |
| No adjacency list needed          | Needs adjacency list              |
| Handles negative weights          | Only non-negative weights         |

For this problem: `n ≤ 100`. Both are fast enough.
Floyd-Warshall is simpler to code and think about.
Dijkstra is better if `n` is large and the graph is sparse.

---

## 🔍 Why `<=` for the Tie-Breaking

```cpp
if (count <= minCount) {   // NOT <
    minCount = count;
    resultCity = i;
}
```

If we used `<`, the FIRST city with the minimum count would win.
With `<=`, the LAST city with the minimum count wins.

Since we iterate `i` from 0 to n-1,
the last city = the greatest number. Correct tie-breaking.

---

## 🔍 Edge Cases

-   **All cities directly connected with small weights** → every city has count n-1.
    Return n-1 (greatest number, all tied).
-   **Threshold = 0** → no city can reach any other. All counts = 0.
    Return n-1 (all tied, greatest number wins).
-   **Disconnected cities** → `dist[i][j]` stays INT_MAX. Not counted.
-   **Single city** → return 0.

---

### 🧠 Memory of the Loneliest City Law

-   **Floyd-Warshall** to compute all-pairs shortest paths
-   **Distance matrix:** `dist[i][j]` initialized from edges, `INT_MAX` for no edge, `0` on diagonal
-   **Triple loop:** `k` outermost, then `i`, then `j`
-   **Relaxation:** `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`
-   **Count:** for each city, count others with `dist[i][j] <= threshold`
-   **Tie-breaking:** `count <= minCount` ensures greatest city number wins
-   **Dijkstra alternative:** run from every city, count neighbors per run
-   **Time:** O(V³) for Floyd-Warshall, O(V × (V+E) log V) for Dijkstra
-   **Space:** O(V²)
-   **Edge cases:**
    -   All tied → return n-1 (greatest number)
    -   Threshold = 0 → all counts = 0 → return n-1
    -   Disconnected → unreachable cities not counted

Thus is remembered the saga of **Find the City With the Smallest Number of Neighbors**,
where the Oracle computed the shortest distance
between every pair of cities using Floyd-Warshall --
then counted how many neighbors each city could reach
within the threshold --
and crowned the loneliest city,
the one with the fewest connections to the world,
choosing the greatest number when loneliness was shared. 🏙️🔍✨
