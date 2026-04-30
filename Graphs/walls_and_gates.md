## 🏰🚪 _The Flood from Every Gate: The Walls and Gates Saga_

> \_"In a vast dungeon represented as a grid,
> three kinds of cells existed:
>
> -   `-1` -- a **wall** (impassable).
> -   `0` -- a **gate** (the destination).
> -   `INF` (2147483647) -- an **empty room**.
>
> The Oracle was commanded:
>
> **'For every empty room,
> find the distance to its NEAREST gate.
> Fill each room with that distance.
> If a room cannot reach any gate, leave it as INF.'**
>
> The naive approach: for each empty room,
> BFS outward to find the nearest gate.
> O(rooms × grid size) -- too slow.
>
> The Oracle flipped the problem:
>
> **Instead of searching FROM each room TO a gate,
> search FROM all gates TO all rooms simultaneously.**
>
> **Multi-source BFS.**
>
> Start BFS from ALL gates at once.
> Every gate is at distance 0.
> Their neighbors are at distance 1.
> Their neighbors' neighbors are at distance 2.
> And so on.
>
> The BFS wavefront expanded outward from every gate
> simultaneously -- like water flooding from multiple sources.
> The first wave to reach a room
> was guaranteed to be the shortest distance.
>
> No room was visited twice.
> No gate was favored over another.
> The flood was fair and optimal."\_

---

This is the saga of **Walls and Gates** (LeetCode 286).

Given a 2D grid:
-   `-1` = wall
-   `0` = gate
-   `INF` = empty room (2147483647)

Fill each empty room with the distance to its **nearest gate**.
If impossible to reach a gate, leave it as `INF`.

```
Input:
  INF  -1   0  INF
  INF INF INF  -1
  INF  -1 INF  -1
    0  -1 INF INF

Output:
   3  -1   0   1
   2   2   1  -1
   1  -1   2  -1
   0  -1   3   4
```

---

## 🧠 The Oracle's Core Insight -- Multi-Source BFS

**Single-source BFS** from one gate finds distances from THAT gate.
To find the nearest gate for every room,
we'd need to BFS from every gate separately -- expensive.

**Multi-source BFS** starts from ALL gates simultaneously.

```
1. Push ALL gates into the queue (each at distance 0).
2. BFS outward. For each cell dequeued:
   - Check all 4 neighbors.
   - If neighbor is an empty room (INF):
     - Set its distance = current distance + 1.
     - Push it into the queue.
   - Walls and already-visited rooms are skipped.
```

The BFS wavefront expands uniformly from every gate.
The first time a room is reached, it's by the nearest gate --
because BFS explores in order of distance.

**Why not DFS?**
DFS does not guarantee shortest paths.
BFS does -- it explores all distance-1 cells before distance-2 cells.

```
Time:  O(rows × cols) -- every cell visited at most once
Space: O(rows × cols) -- the queue
```

---

### 📜 The Scroll of the Flooding Dungeon

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🚪 Step One -- Find All Gates and Enqueue Them

```cpp
void wallsAndGates(vector<vector<int>>& rooms) {
    int rows = rooms.size();
    if (rows == 0) return;
    int cols = rooms[0].size();

    queue<pair<int, int>> q;
```

The Oracle prepared a BFS queue of `{row, col}` pairs.

---

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (rooms[r][c] == 0) {
                q.push({r, c});
            }
        }
    }
```

Every gate (value `0`) was pushed into the queue.

These were the **sources** of the flood.
All gates started at distance 0 -- they were already at themselves.

> _"The flood begins at every gate simultaneously.
> No gate has priority over another.
> They all enter the queue together."_

---

## 🌊 Step Two -- BFS Flood Outward

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

The four cardinal directions -- up, down, left, right.

---

```cpp
    while (!q.empty()) {
        auto [r, c] = q.front();
        q.pop();
```

The Oracle dequeued the next cell to process.
This cell's distance was already set correctly.

---

### 🧭 Check All Four Neighbors

```cpp
        for (auto& d : dirs) {
            int nr = r + d[0];
            int nc = c + d[1];
```

Each neighbor was computed.

---

### 🏠 If Neighbor Is an Unvisited Empty Room

```cpp
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && rooms[nr][nc] == INT_MAX) {
                rooms[nr][nc] = rooms[r][c] + 1;
                q.push({nr, nc});
            }
        }
    }
}
```

**Boundary check** -- stay within the grid.

**`rooms[nr][nc] == INT_MAX`** -- the room is empty and unvisited.
`INT_MAX` (2147483647) is the sentinel for "unreached."

If the neighbor was an empty room:

1. **Set its distance** = current cell's distance + 1.
2. **Push it into the queue** for further exploration.

**Walls (`-1`)** are never `INT_MAX` → skipped automatically.
**Already visited rooms** have a distance < `INT_MAX` → skipped.
**Gates (`0`)** are never `INT_MAX` → skipped.

> _"The flood only enters rooms that have never been touched.
> The first wave to reach a room sets its distance --
> and that distance is guaranteed to be the shortest,
> because BFS explores in order of distance."_

---

### 🎺 The Trial of the Flooding Dungeon

```cpp
int main() {
    const int INF = INT_MAX;
    vector<vector<int>> rooms = {
        {INF, -1,  0, INF},
        {INF, INF, INF, -1},
        {INF, -1, INF, -1},
        {  0, -1, INF, INF}
    };

    wallsAndGates(rooms);

    for (auto& row : rooms) {
        for (int cell : row) {
            if (cell == -1) cout << " -1";
            else cout << "  " << cell;
        }
        cout << endl;
    }
    // expected:
    //  3 -1  0  1
    //  2  2  1 -1
    //  1 -1  2 -1
    //  0 -1  3  4
    return 0;
}
```

---

**Full BFS trace:**

```
Initial grid:
  INF  -1   0  INF
  INF INF INF  -1
  INF  -1 INF  -1
    0  -1 INF INF
```

**Step 1 -- Enqueue all gates:**
Queue: `[(0,2), (3,0)]` -- two gates found.

**BFS wave by wave:**

**Wave 0 (distance 0) -- process gates:**

| Dequeue | Neighbors checked          | Rooms updated              |
| ------- | -------------------------- | -------------------------- |
| (0,2)   | (0,1)=-1 skip, (0,3)=INF✓, (1,2)=INF✓ | rooms[0][3]=1, rooms[1][2]=1 |
| (3,0)   | (2,0)=INF✓, (3,1)=-1 skip | rooms[2][0]=1              |

Queue after wave 0: `[(0,3), (1,2), (2,0)]`

**Wave 1 (distance 1):**

| Dequeue | Rooms updated                          |
| ------- | -------------------------------------- |
| (0,3)   | No INF neighbors (walls and boundary)  |
| (1,2)   | rooms[1][1]=2                          |
| (2,0)   | rooms[1][0]=2                          |

Queue: `[(1,1), (1,0)]`

**Wave 2 (distance 2):**

| Dequeue | Rooms updated                          |
| ------- | -------------------------------------- |
| (1,1)   | rooms[2][2]=3 (wait, 1+1=2... let me recheck) |

Actually let me re-trace more carefully:

After wave 1: rooms[1][1]=2, rooms[1][0]=2.

| Dequeue | Current dist | Neighbors                    | Updates          |
| ------- | ------------ | ---------------------------- | ---------------- |
| (1,1)   | 2            | (0,1)=-1, (2,1)=-1, (1,0)=2, (1,2)=1 | none (all visited/walls) |
| (1,0)   | 2            | (0,0)=INF✓, (2,0)=1, (1,1)=2 | rooms[0][0]=3   |

Queue: `[(0,0)]`

**Wave 3 (distance 3):**

| Dequeue | Updates          |
| ------- | ---------------- |
| (0,0)   | No INF neighbors |

Now back to (1,2) which was processed in wave 1 and set rooms[2][2]:

Actually (1,2) was dequeued in wave 1 with distance 1.
Its neighbor (2,2) = INF → rooms[2][2] = 2. Push (2,2).

Then (2,2) dequeued with distance 2.
Its neighbor (3,2) = INF → rooms[3][2] = 3. Push (3,2).

Then (3,2) dequeued with distance 3.
Its neighbor (3,3) = INF → rooms[3][3] = 4. Push (3,3).

**Final grid:**

```
  3  -1   0   1
  2   2   1  -1
  1  -1   2  -1
  0  -1   3   4
```

**Answer matches expected output** ✓

---

**Trace for grid with no gates:**

```
INF INF
INF INF
```

No gates found → queue is empty → BFS never runs.
All rooms stay `INF`.

---

**Trace for grid with all gates:**

```
0 0
0 0
```

All cells are gates (distance 0). No empty rooms to fill.

---

## 🔍 Why Multi-Source BFS and Not Single-Source?

**Single-source BFS from each gate:**
-   BFS from gate 1: O(rows × cols).
-   BFS from gate 2: O(rows × cols).
-   ...
-   Total: O(gates × rows × cols). Slow if many gates.

**Multi-source BFS (all gates at once):**
-   Every cell visited exactly once.
-   Total: O(rows × cols). Always.

The multi-source approach is optimal because
the BFS wavefronts from different gates
naturally merge without conflict.
The first wave to reach a room wins --
and that's always the nearest gate.

---

## 🔄 Why BFS and Not DFS?

BFS guarantees **shortest path** in an unweighted graph.
It explores all cells at distance `d` before any cell at distance `d+1`.

DFS does NOT guarantee shortest paths.
It might reach a room via a long winding path first,
then find a shorter path later -- requiring updates.

For shortest distance problems on grids, **always use BFS**.

---

## 🌐 The Multi-Source BFS Pattern

This pattern appears in many grid problems:

| Problem              | Sources                    | What floods?          |
| -------------------- | -------------------------- | --------------------- |
| Walls and Gates      | All gates (value 0)        | Distance to nearest gate |
| Rotting Oranges      | All rotten oranges         | Time to rot fresh ones |
| 01 Matrix            | All 0-cells                | Distance to nearest 0  |
| Shortest Bridge      | All cells of one island    | Distance to other island |

The template is always the same:
enqueue all sources → BFS outward → first visit = shortest distance.

---

### 🧠 Memory of the Flooding Gate Law

-   **Multi-source BFS** -- start from ALL gates simultaneously
-   **Enqueue all gates** (value 0) at the start
-   **BFS outward:** for each dequeued cell, check 4 neighbors
    -   If neighbor is `INT_MAX` (empty room) → set distance = current + 1, enqueue
    -   Walls (-1), gates (0), already-visited rooms → skip
-   **First visit = shortest distance** -- BFS guarantees this
-   **No visited array needed** -- `INT_MAX` check serves as the visited flag
-   **Time:** O(rows × cols) -- every cell visited at most once
-   **Space:** O(rows × cols) -- the queue
-   **Edge cases:**
    -   No gates → all rooms stay INF
    -   No empty rooms → nothing to fill
    -   Room surrounded by walls → stays INF

Thus is remembered the saga of **Walls and Gates**,
where the Oracle did not search from each room to a gate
but instead flooded from every gate outward --
a multi-source BFS that expanded uniformly,
wave by wave, distance by distance --
the first wave to reach each room
setting its distance to the nearest gate --
until every reachable room was filled
and the unreachable remained forever INF. 🏰🚪✨
