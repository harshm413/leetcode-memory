# 🕸️📐 The Complete Guide to Graph Representation in C++

> _"Before you can traverse a graph, search a graph, or solve any graph problem --
> you must first BUILD the graph in memory.
> How you represent it determines everything:
> how fast you can check if an edge exists,
> how fast you can iterate over neighbors,
> and how much memory you use.
> This guide covers every representation you will ever need."_

---

## Table of Contents

1. [What Is a Graph?](#1-what-is-a-graph)
2. [Types of Graphs](#2-types-of-graphs)
3. [Adjacency Matrix](#3-adjacency-matrix)
4. [Adjacency List (Vector of Vectors)](#4-adjacency-list-vector-of-vectors)
5. [Adjacency List (HashMap)](#5-adjacency-list-hashmap)
6. [Edge List](#6-edge-list)
7. [Weighted Graphs](#7-weighted-graphs)
8. [Implicit Graphs (Grids)](#8-implicit-graphs-grids)
9. [Building Graphs from LeetCode Input](#9-building-graphs-from-leetcode-input)
10. [Comparison Table](#10-comparison-table)
11. [Which Representation to Use?](#11-which-representation-to-use)

---

## 1. What Is a Graph?

A graph is a collection of **nodes** (vertices) and **edges** (connections).

```
Nodes: {0, 1, 2, 3, 4}
Edges: {(0,1), (1,2), (2,3), (3,4), (0,4)}
```

An edge `(u, v)` connects node `u` to node `v`.

The two fundamental questions any representation must answer:

1. **"Is there an edge between u and v?"** (edge existence)
2. **"What are all the neighbors of u?"** (neighbor iteration)

Different representations optimize for different questions.

---

## 2. Types of Graphs

### Directed vs Undirected

**Undirected:** edge `(u, v)` goes both ways. If u connects to v, v connects to u.

```
0 -- 1 -- 2
```

**Directed:** edge `(u, v)` goes one way only. u → v does NOT imply v → u.

```
0 → 1 → 2
```

### Weighted vs Unweighted

**Unweighted:** all edges are equal. Just "connected or not."

**Weighted:** each edge has a cost/distance/weight.

```
0 --5-- 1 --3-- 2
```

### Cyclic vs Acyclic

**Cyclic:** contains at least one cycle (a path that returns to its start).
**Acyclic:** no cycles. A directed acyclic graph is called a **DAG**.

### Connected vs Disconnected

**Connected (undirected):** every node can reach every other node.
**Disconnected:** some nodes cannot reach each other (multiple components).

### Dense vs Sparse

**Dense:** many edges. Close to V² edges.
**Sparse:** few edges. Close to V edges.

This distinction matters for choosing the representation.

---

## 3. Adjacency Matrix

A 2D array where `matrix[i][j] = 1` if there's an edge from `i` to `j`.

### Unweighted Undirected Graph

```cpp
int V = 5;
vector<vector<int>> matrix(V, vector<int>(V, 0));
```

All entries start at 0 (no edges).

**Adding an edge (undirected):**

```cpp
void addEdge(vector<vector<int>>& matrix, int u, int v) {
    matrix[u][v] = 1;
    matrix[v][u] = 1;
}
```

Both directions set to 1 because the graph is undirected.

**Adding an edge (directed):**

```cpp
void addEdge(vector<vector<int>>& matrix, int u, int v) {
    matrix[u][v] = 1;
}
```

Only one direction. u → v does not imply v → u.

**Checking if an edge exists:**

```cpp
bool hasEdge(vector<vector<int>>& matrix, int u, int v) {
    return matrix[u][v] == 1;
}
```

O(1). Instant lookup. This is the adjacency matrix's strength.

**Getting all neighbors of a node:**

```cpp
vector<int> getNeighbors(vector<vector<int>>& matrix, int u) {
    vector<int> neighbors;
    for (int v = 0; v < matrix.size(); v++) {
        if (matrix[u][v] == 1) {
            neighbors.push_back(v);
        }
    }
    return neighbors;
}
```

O(V). Must scan the entire row. This is the adjacency matrix's weakness.

**Full example:**

```cpp
int main() {
    int V = 4;
    vector<vector<int>> matrix(V, vector<int>(V, 0));

    // Undirected edges: 0-1, 1-2, 2-3, 0-3
    addEdge(matrix, 0, 1);
    addEdge(matrix, 1, 2);
    addEdge(matrix, 2, 3);
    addEdge(matrix, 0, 3);

    // Print the matrix
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            cout << matrix[i][j] << " ";
        }
        cout << endl;
    }
    // Output:
    // 0 1 0 1
    // 1 0 1 0
    // 0 1 0 1
    // 1 0 1 0
}
```

**When to use adjacency matrix:**
- Dense graphs (many edges).
- Need O(1) edge existence check.
- Small number of nodes (V ≤ ~1000, because V² memory).

**When NOT to use:**
- Sparse graphs (wastes memory on zeros).
- Large V (V = 10,000 → 100 million entries).

```
Space: O(V²)
Edge check: O(1)
Neighbor iteration: O(V)
Add edge: O(1)
```

---

## 4. Adjacency List (Vector of Vectors)

The most common representation in competitive programming and interviews.

Each node has a list of its neighbors.

### Unweighted Undirected Graph

```cpp
int V = 5;
vector<vector<int>> graph(V);
```

`graph[u]` is a vector of all nodes connected to `u`.

**Adding an edge (undirected):**

```cpp
void addEdge(vector<vector<int>>& graph, int u, int v) {
    graph[u].push_back(v);
    graph[v].push_back(u);
}
```

Both directions. u's list gets v, v's list gets u.

**Adding an edge (directed):**

```cpp
void addEdge(vector<vector<int>>& graph, int u, int v) {
    graph[u].push_back(v);
}
```

Only u → v. One direction.

**Getting all neighbors:**

```cpp
for (int neighbor : graph[u]) {
    // process neighbor
}
```

O(degree of u). Only iterates over actual neighbors. Fast for sparse graphs.

**Checking if an edge exists:**

```cpp
bool hasEdge(vector<vector<int>>& graph, int u, int v) {
    for (int neighbor : graph[u]) {
        if (neighbor == v) return true;
    }
    return false;
}
```

O(degree of u). Must scan the neighbor list. Slower than matrix.

**Full example:**

```cpp
int main() {
    int V = 4;
    vector<vector<int>> graph(V);

    // Undirected edges: 0-1, 1-2, 2-3, 0-3
    addEdge(graph, 0, 1);
    addEdge(graph, 1, 2);
    addEdge(graph, 2, 3);
    addEdge(graph, 0, 3);

    // Print adjacency list
    for (int i = 0; i < V; i++) {
        cout << i << ": ";
        for (int neighbor : graph[i]) {
            cout << neighbor << " ";
        }
        cout << endl;
    }
    // Output:
    // 0: 1 3
    // 1: 0 2
    // 2: 1 3
    // 3: 2 0
}
```

**When to use adjacency list:**
- Sparse graphs (most LeetCode problems).
- Need fast neighbor iteration.
- Nodes are labeled 0 to V-1 (integer labels).

```
Space: O(V + E)
Edge check: O(degree)
Neighbor iteration: O(degree)
Add edge: O(1)
```

---

## 5. Adjacency List (HashMap)

When nodes are NOT labeled 0 to V-1 --
they might be strings, large numbers, or non-contiguous.

```cpp
unordered_map<string, vector<string>> graph;
```

**Adding an edge (undirected, string nodes):**

```cpp
void addEdge(unordered_map<string, vector<string>>& graph,
             string u, string v) {
    graph[u].push_back(v);
    graph[v].push_back(u);
}
```

**Adding an edge (directed, integer nodes with gaps):**

```cpp
unordered_map<int, vector<int>> graph;

void addEdge(unordered_map<int, vector<int>>& graph, int u, int v) {
    graph[u].push_back(v);
}
```

Works even if nodes are `{1, 100, 5000}` -- no wasted space.

**Getting neighbors:**

```cpp
for (auto& neighbor : graph[u]) {
    // process neighbor
}
```

Same as vector-of-vectors, but with hash map lookup first.

**Full example (string nodes):**

```cpp
int main() {
    unordered_map<string, vector<string>> graph;

    addEdge(graph, "Alice", "Bob");
    addEdge(graph, "Bob", "Charlie");
    addEdge(graph, "Alice", "Charlie");

    for (auto& [node, neighbors] : graph) {
        cout << node << ": ";
        for (auto& n : neighbors) cout << n << " ";
        cout << endl;
    }
    // Output (order may vary):
    // Alice: Bob Charlie
    // Bob: Alice Charlie
    // Charlie: Bob Alice
}
```

**When to use HashMap adjacency list:**
- Nodes are strings, large integers, or non-contiguous.
- Don't know the number of nodes upfront.
- Graph is built dynamically.

```
Space: O(V + E)
Edge check: O(degree)
Neighbor iteration: O(degree)
Add edge: O(1) amortized
```

---

## 6. Edge List

Just a list of edges. No adjacency structure.

```cpp
vector<pair<int, int>> edges;

edges.push_back({0, 1});
edges.push_back({1, 2});
edges.push_back({2, 3});
```

Or with weights:

```cpp
vector<tuple<int, int, int>> edges; // {weight, u, v}

edges.push_back({5, 0, 1});
edges.push_back({3, 1, 2});
```

**When to use edge list:**
- Union-Find problems (Redundant Connection, Kruskal's MST).
- When you just need to iterate over all edges.
- When the input IS an edge list (many LeetCode problems).

**When NOT to use:**
- When you need fast neighbor lookup.
- When you need to traverse the graph (DFS/BFS).

```
Space: O(E)
Edge check: O(E) -- must scan entire list
Neighbor iteration: O(E) -- must scan entire list
Add edge: O(1)
```

The edge list is the simplest representation
but the worst for traversal. It's a storage format, not a traversal format.

---

## 7. Weighted Graphs

### Adjacency Matrix (Weighted)

```cpp
vector<vector<int>> matrix(V, vector<int>(V, 0));
// or use INT_MAX/INF for "no edge"
vector<vector<int>> matrix(V, vector<int>(V, INT_MAX));

matrix[u][v] = weight;
```

`matrix[u][v] = 5` means edge from u to v with weight 5.
`matrix[u][v] = INT_MAX` means no edge.

### Adjacency List (Weighted)

```cpp
vector<vector<pair<int, int>>> graph(V);
// graph[u] = list of {neighbor, weight}

graph[u].push_back({v, weight});
```

**Adding a weighted undirected edge:**

```cpp
void addEdge(vector<vector<pair<int,int>>>& graph,
             int u, int v, int w) {
    graph[u].push_back({v, w});
    graph[v].push_back({u, w});
}
```

**Iterating over weighted neighbors:**

```cpp
for (auto& [neighbor, weight] : graph[u]) {
    cout << "Edge to " << neighbor << " with weight " << weight << endl;
}
```

**Full example:**

```cpp
int main() {
    int V = 4;
    vector<vector<pair<int,int>>> graph(V);

    addEdge(graph, 0, 1, 5);
    addEdge(graph, 1, 2, 3);
    addEdge(graph, 2, 3, 7);

    for (int i = 0; i < V; i++) {
        cout << i << ": ";
        for (auto& [neighbor, weight] : graph[i]) {
            cout << "(" << neighbor << ",w=" << weight << ") ";
        }
        cout << endl;
    }
    // Output:
    // 0: (1,w=5)
    // 1: (0,w=5) (2,w=3)
    // 2: (1,w=3) (3,w=7)
    // 3: (2,w=7)
}
```

### Weighted Edge List

```cpp
vector<tuple<int, int, int>> edges; // {u, v, weight}
// or sorted by weight for Kruskal's:
vector<tuple<int, int, int>> edges; // {weight, u, v}
```

Used in Kruskal's MST -- sort edges by weight, process with Union-Find.

---

## 8. Implicit Graphs (Grids)

Many graph problems use a **2D grid** as the graph.
The grid IS the graph -- no adjacency list needed.

**Nodes:** each cell `(r, c)`.
**Edges:** adjacent cells (up, down, left, right).

```cpp
int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};

for (auto& d : dirs) {
    int nr = r + d[0];
    int nc = c + d[1];
    if (nr >= 0 && nr < rows && nc >= 0 && nc < cols) {
        // (nr, nc) is a valid neighbor of (r, c)
    }
}
```

**No adjacency list is built.** Neighbors are computed on the fly.

This is used in:
- Number of Islands
- Rotting Oranges
- Walls and Gates
- Pacific Atlantic Water Flow
- Surrounded Regions
- Shortest Path in Binary Matrix

**8-directional neighbors (including diagonals):**

```cpp
int dirs[8][2] = {{-1,0},{1,0},{0,-1},{0,1},
                   {-1,-1},{-1,1},{1,-1},{1,1}};
```

**Grid as a graph -- the mental model:**

```
Grid:           Graph equivalent:
1 1 0           (0,0)--(0,1)
1 0 1                |
0 1 1           (1,0)     (1,2)
                       |       |
                  (2,1)--(2,2)
```

Each `1` cell is a node. Adjacent `1` cells are connected by edges.
The grid representation is implicit -- no explicit graph is built.

---

## 9. Building Graphs from LeetCode Input

### From Edge List (Most Common)

```
Input: n = 4, edges = [[0,1],[1,2],[2,3]]
```

**Undirected:**

```cpp
vector<vector<int>> graph(n);
for (auto& e : edges) {
    graph[e[0]].push_back(e[1]);
    graph[e[1]].push_back(e[0]);
}
```

**Directed:**

```cpp
vector<vector<int>> graph(n);
for (auto& e : edges) {
    graph[e[0]].push_back(e[1]);
}
```

### From Prerequisites (Course Schedule Style)

```
Input: prerequisites = [[1,0],[2,0],[3,1]]
Meaning: [a, b] = a requires b (edge b → a)
```

```cpp
vector<vector<int>> graph(numCourses);
vector<int> inDegree(numCourses, 0);
for (auto& p : prerequisites) {
    graph[p[1]].push_back(p[0]);
    inDegree[p[0]]++;
}
```

**Watch the direction!** `[a, b]` means b must come before a.
Edge goes from b to a (b → a).

### From Adjacency List Input (Clone Graph Style)

```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Node 1's neighbors: [2, 4]
Node 2's neighbors: [1, 3]
...
```

The input IS the adjacency list. Just use it directly.

### From Grid (Island Problems)

No building needed. The grid IS the graph.
Use the direction array to find neighbors on the fly.

---

## 10. Comparison Table

| Representation      | Space    | Edge Check | Neighbors  | Add Edge | Best For              |
| -------------------- | -------- | ---------- | ---------- | -------- | --------------------- |
| Adjacency Matrix     | O(V²)   | O(1)       | O(V)       | O(1)     | Dense, small V        |
| Adjacency List (vec) | O(V+E)  | O(degree)  | O(degree)  | O(1)     | Sparse, integer nodes |
| Adjacency List (map) | O(V+E)  | O(degree)  | O(degree)  | O(1)*    | String/non-contiguous |
| Edge List            | O(E)    | O(E)       | O(E)       | O(1)     | Union-Find, MST       |
| Implicit (Grid)      | O(M×N)  | O(1)       | O(4 or 8)  | N/A      | Grid problems         |

`*` amortized due to hash map.

---

## 11. Which Representation to Use?

**Default choice: Adjacency List (vector of vectors).**
This covers 90% of LeetCode graph problems.

Use **Adjacency Matrix** when:
- V is small (≤ 1000) AND you need O(1) edge checks.
- The graph is dense.
- Floyd-Warshall or similar matrix-based algorithms.

Use **HashMap Adjacency List** when:
- Nodes are strings or non-contiguous integers.
- You don't know V upfront.

Use **Edge List** when:
- Union-Find problems (just iterate over edges).
- Kruskal's MST (sort edges by weight).
- The input is already an edge list and you don't need neighbor lookup.

Use **Implicit Grid** when:
- The problem gives you a 2D grid.
- Neighbors are adjacent cells.
- No explicit graph construction needed.

---

## Quick Reference -- Building an Adjacency List

**The code you'll write in 90% of graph problems:**

```cpp
// Undirected
int n = /* number of nodes */;
vector<vector<int>> graph(n);
for (auto& e : edges) {
    graph[e[0]].push_back(e[1]);
    graph[e[1]].push_back(e[0]);
}

// Directed
vector<vector<int>> graph(n);
for (auto& e : edges) {
    graph[e[0]].push_back(e[1]);
}

// Weighted undirected
vector<vector<pair<int,int>>> graph(n);
for (auto& e : edges) {
    graph[e[0]].push_back({e[1], e[2]});
    graph[e[1]].push_back({e[0], e[2]});
}
```

Memorize these three. They are the starting point of almost every graph solution.

---

## Final Words

Graph representation is not the hard part of graph problems --
but getting it wrong wastes time and causes bugs.

The rules are simple:

1. **Adjacency list** is the default. Use it unless you have a reason not to.
2. **Undirected = add both directions.** Forgetting this is the #1 bug.
3. **Watch the edge direction** in directed graphs. Read the problem carefully.
4. **Weighted = pair<int,int>** in the adjacency list. `{neighbor, weight}`.
5. **Grids are implicit graphs.** Don't build an adjacency list for them.

Get the representation right, and the algorithm flows naturally. 🕸️📐
