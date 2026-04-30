## 🍊🦠 _The Plague of the Fruit Grid: The Rotting Oranges Saga_

> \_"In the Grid of Oranges,
> a plague had begun.
>
> Some oranges were **fresh** (value `1`).
> Some were already **rotten** (value `2`).
> Some cells were **empty** (value `0`).
>
> Every minute, a rotten orange infected
> all fresh oranges directly adjacent to it --
> up, down, left, right.
>
> The Oracle was commanded:
>
> **'How many minutes until no fresh orange remains?
> If it is impossible -- return -1.'**
>
> The Oracle recognized the pattern:
>
> **Multi-source BFS.**
>
> All rotten oranges were the sources.
> They all started rotting simultaneously at minute 0.
> Each BFS wave = one minute of spreading.
>
> When the BFS ended:
> -   If fresh oranges remained → impossible → return -1.
> -   Otherwise → the number of waves = the answer."\_

---

This is the saga of **Rotting Oranges**.

Given a 2D grid:
-   `0` = empty cell
-   `1` = fresh orange
-   `2` = rotten orange

Every minute, fresh oranges adjacent (4-directionally)
to a rotten orange become rotten.

Return the **minimum minutes** until no fresh orange remains.
If impossible, return `-1`.

```
Input:
  [[2,1,1],
   [1,1,0],
   [0,1,1]]
Output: 4

Input:
  [[2,1,1],
   [0,1,1],
   [1,0,1]]
Output: -1

Input:
  [[0,2]]
Output: 0
```

---

## 🧠 The Oracle's Core Insight -- Multi-Source BFS with Wave Counting

This is the same multi-source BFS as Walls and Gates,
with two twists:

1. **Count the waves** (each wave = 1 minute).
2. **Check for survivors** (fresh oranges never reached).

```
Time:  O(rows × cols) -- every cell visited at most once
Space: O(rows × cols) -- the queue
```

---

### 📜 The Scroll of the Plague Grid

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
```

---

## 🦠 The Oracle Opens the Ritual

```cpp
int orangesRotting(vector<vector<int>>& grid) {
    int rows = grid.size();
    int cols = grid[0].size();
```

The Oracle measured the grid -- `rows` by `cols`.

---

## 📦 Prepare the Queue and the Fresh Counter

```cpp
    queue<pair<int, int>> q;
    int fresh = 0;
```

Two tools:

-   `q` -- the BFS queue. It will hold the positions
    of all rotten oranges, wave by wave.

-   `fresh` -- the count of fresh oranges remaining.
    This is the Oracle's survival tracker.
    When it reaches 0, the plague is complete.

---

## 🔍 Scan the Grid -- Find Sources and Count Targets

```cpp
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
```

The Oracle walked every cell in the grid.

---

### 🦠 Rotten Orange Found -- Enqueue It

```cpp
            if (grid[r][c] == 2) {
                q.push({r, c});
            }
```

Every rotten orange was pushed into the queue.
These were the **sources** of the plague --
the starting points of the multi-source BFS.

All rotten oranges entered the queue together,
at the same "level" -- minute 0.

> _"The plague begins at every rotten orange simultaneously.
> No rotten orange has priority over another.
> They all enter the queue as equals."_

---

### 🍊 Fresh Orange Found -- Count It

```cpp
            else if (grid[r][c] == 1) {
                fresh++;
            }
        }
    }
```

Every fresh orange was counted.
This told the Oracle how many oranges needed to be infected
before the plague was complete.

> _"The fresh count is the Oracle's scoreboard.
> Every time a fresh orange falls to the plague,
> the count drops by one.
> When it reaches zero -- victory."_

---

## 🛑 Early Exit -- No Fresh Oranges

```cpp
    if (fresh == 0) return 0;
```

If there were no fresh oranges to begin with --
the plague was already complete. Zero minutes needed.

This handles grids with only rotten oranges,
only empty cells, or a mix with no fresh.

---

## 🧭 Prepare the Direction Array

```cpp
    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
```

The four cardinal directions:
up `(-1,0)`, down `(1,0)`, left `(0,-1)`, right `(0,1)`.

Used to check all four neighbors of every cell.

---

## ⏱️ Initialize the Minute Counter

```cpp
    int minutes = 0;
```

`minutes` tracked how many waves of rotting had occurred.
It started at 0 -- no waves processed yet.

---

## 🌊 The BFS Flood Begins -- Wave by Wave

```cpp
    while (!q.empty()) {
```

The BFS continued as long as there were rotten oranges
waiting to spread the plague.

---

### 📸 Snapshot the Current Wave Size

```cpp
        int size = q.size();
```

**This is the most critical line for wave counting.**

`size` captured how many rotten oranges were in the queue
RIGHT NOW -- before any new ones were added.

These `size` oranges all belong to the CURRENT wave.
Any oranges enqueued during this loop belong to the NEXT wave.

Without this snapshot, all oranges would blend together
and we couldn't tell when one minute ended and the next began.

> _"The snapshot freezes the current wave.
> Process exactly this many.
> Everything new goes to the next wave.
> This is how BFS counts levels."_

---

### 🔁 Process Every Orange in This Wave

```cpp
        for (int i = 0; i < size; i++) {
```

Exactly `size` oranges were processed -- no more, no less.
This was one complete minute of plague spreading.

---

### 🍊 Dequeue One Rotten Orange

```cpp
            auto [r, c] = q.front();
            q.pop();
```

One rotten orange was pulled from the queue.
Its position `(r, c)` was known.
It was about to infect its neighbors.

---

### 🧭 Check All Four Neighbors

```cpp
            for (auto& d : dirs) {
                int nr = r + d[0];
                int nc = c + d[1];
```

Each of the four neighbors was computed.
`nr` = neighbor row, `nc` = neighbor column.

---

### 🛡️ Boundary Check

```cpp
                if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) {
                    continue;
                }
```

If the neighbor was outside the grid -- skip it.
Can't infect what doesn't exist.

---

### 🍊 Is the Neighbor a Fresh Orange?

```cpp
                if (grid[nr][nc] != 1) {
                    continue;
                }
```

If the neighbor was NOT a fresh orange -- skip it.

Empty cells (`0`) can't be infected.
Already rotten cells (`2`) are already infected.
Only fresh oranges (`1`) are targets.

---

### 🦠 Infect the Fresh Orange

```cpp
                grid[nr][nc] = 2;
```

The fresh orange turned rotten.

This also served as the **visited marker** --
once set to `2`, this cell would never be processed again.

> _"The plague touches the fresh orange.
> It turns rotten instantly --
> but it won't spread until the NEXT minute.
> That's why it's enqueued, not processed now."_

---

### 📉 Decrement the Fresh Count

```cpp
                fresh--;
```

One fewer fresh orange remained.
The Oracle's scoreboard updated.

---

### 📥 Enqueue for the Next Wave

```cpp
                q.push({nr, nc});
            }
        }
```

The newly rotten orange was pushed into the queue.
It would spread the plague in the NEXT wave --
not this one.

---

### ⏱️ One Wave Complete -- Increment Minutes

```cpp
        if (!q.empty()) {
            minutes++;
        }
    }
```

After processing all `size` oranges in this wave:

If the queue still has oranges (the next wave exists) --
one minute has passed. Increment.

The `if (!q.empty())` check prevents counting an extra minute
after the very last wave, when no new oranges were enqueued.

> _"A minute passes only if the plague has more work to do.
> The last wave infects the final oranges --
> but no minute is counted after the plague dies out."_

---

## 🏁 Check for Survivors

```cpp
    return fresh == 0 ? minutes : -1;
}
```

If all fresh oranges were reached (`fresh == 0`) --
return the number of minutes.

If any fresh oranges survived (`fresh > 0`) --
they were unreachable, isolated by empty cells or walls.
Return `-1`.

> _"If even one fresh orange survives the plague,
> the mission has failed."_

---

### 🎺 The Trial of the Plague Grid

```cpp
int main() {
    vector<vector<int>> g1 = {{2,1,1},{1,1,0},{0,1,1}};
    cout << orangesRotting(g1) << endl; // expected: 4

    vector<vector<int>> g2 = {{2,1,1},{0,1,1},{1,0,1}};
    cout << orangesRotting(g2) << endl; // expected: -1

    vector<vector<int>> g3 = {{0,2}};
    cout << orangesRotting(g3) << endl; // expected: 0

    return 0;
}
```

---

**Full wave-by-wave trace for grid1:**

```
Initial:
  2 1 1
  1 1 0
  0 1 1

Rotten: [(0,0)].  Fresh: 6.
```

**Wave 1 (minute 0 → 1):**
Process (0,0). Infect (0,1) and (1,0). fresh = 4.

```
  2 2 1
  2 1 0
  0 1 1
```

**Wave 2 (minute 1 → 2):**
Process (0,1) and (1,0).
(0,1) infects (0,2). (1,0) infects (1,1). fresh = 2.

```
  2 2 2
  2 2 0
  0 1 1
```

**Wave 3 (minute 2 → 3):**
Process (0,2) and (1,1).
(0,2) has no fresh neighbors. (1,1) infects (2,1). fresh = 1.

```
  2 2 2
  2 2 0
  0 2 1
```

**Wave 4 (minute 3 → 4):**
Process (2,1). Infects (2,2). fresh = 0.

```
  2 2 2
  2 2 0
  0 2 2
```

Queue empty. fresh == 0. **Answer: 4** ✓

---

**Trace for grid2 (impossible):**

```
  2 1 1
  0 1 1
  1 0 1
```

Rotten: [(0,0)]. Fresh: 5.

BFS spreads through (0,1), (0,2), (1,1), (1,2)...
But (2,0) is isolated -- no rotten neighbor can reach it
(cell (1,0) is empty, blocking the path).

After BFS: fresh = 1. **Answer: -1** ✓

---

**Trace for `[[0,2]]`:**

Fresh = 0. Early exit. **Answer: 0** ✓

---

**Trace for `[[1]]` (single fresh, no rotten):**

Fresh = 1. Queue empty (no rotten sources). BFS never runs.
fresh > 0. **Answer: -1** ✓

---

## 🔍 Why BFS and Not DFS?

BFS guarantees **shortest distance** from any source.
Each wave = one unit of time.
The first wave to reach a cell = the earliest possible infection.

DFS would explore one deep path first,
potentially reaching distant oranges before nearby ones.
It cannot correctly model simultaneous spreading.

**For anything involving "minimum time" or "shortest distance" on a grid,
always use BFS.**

---

## 🔄 The Multi-Source BFS Pattern Family

| Problem              | Sources              | What spreads?         | Extra check?         |
| -------------------- | -------------------- | --------------------- | -------------------- |
| Walls and Gates      | All gates            | Distance              | None                 |
| Rotting Oranges      | All rotten oranges   | Infection             | fresh == 0?          |
| 01 Matrix            | All 0-cells          | Distance to nearest 0 | None                 |
| Shortest Bridge      | All cells of island 1| Distance to island 2  | Stop when reached    |

Same template every time:
enqueue all sources → BFS wave by wave → first visit = optimal.

---

### 🧠 Memory of the Plague Law

-   **Multi-source BFS** from all rotten oranges simultaneously
-   **Scan grid:** enqueue all rotten (value 2), count all fresh (value 1)
-   **Early exit:** fresh == 0 → return 0
-   **Level-by-level BFS:** `size = q.size()` snapshot per wave
-   **For each rotten orange:** check 4 neighbors
    -   Out of bounds → skip
    -   Not fresh → skip
    -   Fresh → mark rotten, decrement fresh, enqueue
-   **After each wave:** if queue not empty → minutes++
-   **After BFS:** fresh == 0 → return minutes; else → return -1
-   **`grid[nr][nc] = 2`** serves as both infection AND visited marking
-   **Time:** O(rows × cols)
-   **Space:** O(rows × cols)
-   **Edge cases:**
    -   No fresh → 0
    -   No rotten but fresh exist → -1
    -   Isolated fresh orange → -1
    -   All rotten → 0

Thus is remembered the saga of **Rotting Oranges**,
where the Oracle unleashed a multi-source plague
from every rotten orange at once --
BFS wave by wave, minute by minute,
each wave infecting every adjacent fresh orange --
counting the waves until the plague was complete
or declaring failure
when a lone fresh orange survived
beyond the reach of the rot. 🍊🦠✨
