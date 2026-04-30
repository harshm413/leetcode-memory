## 🏘️🗺️ _The Kingdoms Within the Kingdom: The Number of Provinces Saga_

> \_"In the land of `n` cities,
> friendships were recorded in a sacred matrix --
> `isConnected[i][j] = 1` meant city `i` and city `j`
> were directly connected.
>
> A **province** was a group of cities
> where every city could reach every other city --
> directly or through a chain of connections.
>
> The Oracle was commanded:
>
> **'How many provinces exist?'**
>
> The Oracle recognized this instantly:
>
> **This is counting connected components --
> but the input is an adjacency MATRIX, not an edge list.**
>
> The matrix told her directly:
> `isConnected[i][j] == 1` → city `i` and city `j` are neighbors.
>
> No need to build an adjacency list.
> Just DFS through the matrix.
>
> Walk every city.
> When an unvisited city is found --
> a new province is discovered.
> DFS to visit every city in that province.
> Count the provinces."\_

---

This is the saga of **Number of Provinces**.

Given an `n × n` matrix `isConnected` where:
-   `isConnected[i][j] = 1` means city `i` and city `j` are directly connected.
-   `isConnected[i][i] = 1` always (a city is connected to itself).

Return the number of **provinces** (connected components).

```
Input:  isConnected = [[1,1,0],[1,1,0],[0,0,1]]
Output: 2   (cities {0,1} and city {2})

Input:  isConnected = [[1,0,0],[0,1,0],[0,0,1]]
Output: 3   (every city is its own province)

Input:  isConnected = [[1,1,1],[1,1,1],[1,1,1]]
Output: 1   (all cities connected)
```

---

## 🧠 The Oracle's Core Insight -- DFS on an Adjacency Matrix

This is **Number of Connected Components** --
but the graph is given as an **adjacency matrix**
instead of an edge list.

The matrix IS the graph. Row `i` tells you all neighbors of city `i`.

```
For each city 0 to n-1:
  If not visited:
    provinces++
    DFS to visit all cities in this province
```

No adjacency list needed. The matrix serves directly.

```
Time:  O(N²) -- checking every cell of the N×N matrix
Space: O(N) -- visited array + recursion stack
```

---

### 📜 The Scroll of the Provincial Map

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔥 The DFS -- Explore One Province

```cpp
void dfs(vector<vector<int>>& isConnected, vector<bool>& visited, int city) {
```

The Oracle entered a city and began exploring its province.

---

### 🏙️ Mark This City as Visited

```cpp
    visited[city] = true;
```

This city now belongs to the current province.
It will never be counted again.

---

### 🔁 Check Every Other City for a Connection

```cpp
    for (int other = 0; other < (int)isConnected.size(); other++) {
```

The Oracle scanned the entire row of the matrix --
checking every other city for a direct connection.

---

### 🔗 If Connected and Unvisited -- Recurse

```cpp
        if (isConnected[city][other] == 1 && !visited[other]) {
            dfs(isConnected, visited, other);
        }
    }
}
```

If `isConnected[city][other] == 1` → they are directly connected.
If `other` hasn't been visited → it's part of this province. Recurse.

> _"The matrix row is the neighbor list.
> Every `1` in row `city` is a neighbor.
> Visit every unvisited neighbor --
> and their neighbors, and their neighbors --
> until the entire province is explored."_

---

## 🏘️ The Main Ritual -- Walk, Discover, Count

```cpp
int findCircleNum(vector<vector<int>>& isConnected) {
    int n = isConnected.size();
    vector<bool> visited(n, false);
    int provinces = 0;
```

`n` cities. All start unvisited. Province count starts at 0.

---

### 🔁 Check Every City

```cpp
    for (int i = 0; i < n; i++) {
```

Walk through every city.

---

### 🏘️ New Province Discovered

```cpp
        if (!visited[i]) {
            provinces++;
            dfs(isConnected, visited, i);
        }
    }
    return provinces;
}
```

When an unvisited city is found:
1. **`provinces++`** -- a new province discovered.
2. **`dfs(...)`** -- explore the entire province, marking all its cities.

The next unvisited city must belong to a DIFFERENT province.

---

### 🎺 The Trial of the Provincial Map

```cpp
int main() {
    vector<vector<int>> m1 = {{1,1,0},{1,1,0},{0,0,1}};
    cout << findCircleNum(m1) << endl; // expected: 2

    vector<vector<int>> m2 = {{1,0,0},{0,1,0},{0,0,1}};
    cout << findCircleNum(m2) << endl; // expected: 3

    vector<vector<int>> m3 = {{1,1,1},{1,1,1},{1,1,1}};
    cout << findCircleNum(m3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `[[1,1,0],[1,1,0],[0,0,1]]`:**

```
Matrix:
  1 1 0
  1 1 0
  0 0 1
```

| City i | Visited? | Action                              | provinces |
| ------ | -------- | ----------------------------------- | --------- |
| 0      | No       | provinces++. DFS from 0.            | 1         |
|        |          | → visited[0]=true. Row 0: [1,1,0]. |           |
|        |          | → other=0: self, skip (visited).    |           |
|        |          | → other=1: connected, unvisited → DFS(1). |    |
|        |          |   → visited[1]=true. Row 1: [1,1,0].|          |
|        |          |   → other=0: visited. other=1: self. other=2: not connected. |  |
|        |          | → other=2: not connected. Done.     |           |
| 1      | Yes      | Skip.                               | 1         |
| 2      | No       | provinces++. DFS from 2.            | 2         |
|        |          | → visited[2]=true. Row 2: [0,0,1]. |           |
|        |          | → other=0: not connected. other=1: not connected. other=2: self. |  |

**Answer: 2** ✓

Province 1: {0, 1}. Province 2: {2}.

---

**Trace for `[[1,0,0],[0,1,0],[0,0,1]]`:**

No connections between any cities (only self-connections on diagonal).
Each city triggers a new DFS that visits only itself.

**Answer: 3** ✓ -- three isolated cities, three provinces.

---

**Trace for `[[1,1,1],[1,1,1],[1,1,1]]`:**

City 0 triggers DFS. Row 0 connects to 1 and 2.
DFS visits 0 → 1 → 2. All visited in one DFS call.

**Answer: 1** ✓ -- all cities in one province.

---

## 🔍 How This Differs from Number of Connected Components

| Connected Components (edge list)  | Number of Provinces (matrix)      |
| --------------------------------- | --------------------------------- |
| Input: `n` + edge list            | Input: `n × n` adjacency matrix   |
| Build adjacency list first        | Matrix IS the adjacency structure |
| Neighbor iteration: O(degree)     | Neighbor iteration: O(N) per node |
| Total: O(V + E)                   | Total: O(N²)                      |

The algorithm is identical -- DFS to find components.
The only difference is how neighbors are discovered:
-   Edge list → `graph[node]` gives neighbors directly.
-   Matrix → scan row `node` for `1`s.

---

## 🔄 Union-Find Alternative

```cpp
int findCircleNum(vector<vector<int>>& isConnected) {
    int n = isConnected.size();
    UnionFind uf(n);

    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (isConnected[i][j] == 1) {
                uf.unite(i, j);
            }
        }
    }
    return uf.getComponents();
}
```

Scan the upper triangle of the matrix (avoid double-counting).
For each `1`, union the two cities.
Return the remaining component count.

Same O(N²) time (scanning the matrix dominates).

---

## 🔍 Why `j = i + 1` in Union-Find?

The matrix is symmetric (`isConnected[i][j] == isConnected[j][i]`).
Scanning only the upper triangle (`j > i`) avoids processing
each edge twice.

Also skips the diagonal (`i == i`), which is always 1
but doesn't represent an edge to another city.

---

### 🧠 Memory of the Provincial Map Law

-   **Connected components** on an adjacency matrix
-   **DFS:** for each unvisited city → provinces++ → DFS to visit entire province
-   **Neighbor discovery:** scan row `city` for `1`s (O(N) per city)
-   **No adjacency list needed** -- the matrix IS the graph
-   **Union-Find alternative:** scan upper triangle, union connected pairs
-   **Time:** O(N²) -- must scan the entire matrix
-   **Space:** O(N) -- visited array + recursion stack
-   **Edge cases:**
    -   All disconnected → N provinces
    -   All connected → 1 province
    -   Single city → 1 province

Thus is remembered the saga of **Number of Provinces**,
where the Oracle walked the adjacency matrix
city by city --
and every time she found an unvisited city,
she declared a new province
and DFS-ed through its row of connections,
visiting every city reachable through chains of `1`s --
until the entire matrix was explored
and the number of provinces was known. 🏘️🗺️✨
