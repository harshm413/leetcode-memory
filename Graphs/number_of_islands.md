## 🌊 _The Archipelago of Hidden Kingdoms: The Number of Islands Saga_

> \*"Across the great Grid-Sea lay a mosaic of lands and waters—
> some islands vast, some mere specks of soil.
>
> But the map was unmarked.
> None knew how many kingdoms rose above the tide.
>
> The Oracle was summoned and given a task:
>
> **‘Count the islands.
> Every patch of land connected by north, south, east, or west
> belongs to the same kingdom.’**
>
> And so she began her voyage,
> exploring each uncharted shore with depth-first steps,
> sinking visited lands beneath the waves
> so they could not be counted twice."\*

---

A **grid** of `'1'`s (land) and `'0'`s (water) represented the great sea.
An island was defined as a connected body of land
touching only in the four cardinal directions.

To reveal these hidden kingdoms,
the Oracle used a ritual of **DFS exploration**,
marking each land cell as visited
and ensuring every island was counted exactly once.

Thus began the saga of **Number of Islands**.

---

### 📜 The Scroll of the Grid-Sea

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

Each cell in the grid was either water or land,
awaiting the Oracle’s voyage.

---

## 🏝️ The Oracle’s Island-Exploration Ritual

_DFS to sink each island as it is counted_

```cpp
void dfs(vector<vector<char>>& grid, int r, int c) {
```

She stepped onto land at `(r, c)`
to explore the full extent of its kingdom.

---

### 🌊 Step 1 — Boundary and Water Check

```cpp
    if (r < 0 || c < 0 || r >= grid.size() || c >= grid[0].size())
        return;
    if (grid[r][c] == '0')
        return;
```

If she walked off the map
or into water,
her exploration stopped.

---

### 🏴‍☠️ Step 2 — Sink the Land

_Mark as visited to avoid double counting_

```cpp
    grid[r][c] = '0';
```

She sank the land beneath the waves—
not destroying it,
but marking that this piece of the island
had already been explored.

---

### 🧭 Step 3 — Explore All Cardinal Directions

```cpp
    dfs(grid, r+1, c); // down
    dfs(grid, r-1, c); // up
    dfs(grid, r, c+1); // right
    dfs(grid, r, c-1); // left
}
```

She walked in all four directions,
discovering every land cell that belonged to the same kingdom.

Only when all connected land was visited
was the island fully revealed.

---

## 🌅 The Grand Counting Ritual

```cpp
int numIslands(vector<vector<char>>& grid) {
    if (grid.empty()) return 0;

    int count = 0;
```

Zero islands to start—
the map lay silent.

---

```cpp
    for (int r = 0; r < grid.size(); r++) {
        for (int c = 0; c < grid[0].size(); c++) {
            if (grid[r][c] == '1') {
                count++;
                dfs(grid, r, c);
            }
        }
    }
```

The Oracle walked the entire grid:

-   Whenever she found untouched land (`'1'`),
    a **new island** was discovered — `count++`
-   She then invoked DFS to **sink that whole island**
    so it would not be counted again

In this way, each kingdom was counted exactly once.

---

```cpp
    return count;
}
```

The total number of islands was returned—
the true geography of the Grid-Sea.

---

### 🎺 The Trial of the Archipelago

```cpp
int main() {
    vector<vector<char>> grid = {
        {'1','1','0','0','0'},
        {'1','1','0','0','0'},
        {'0','0','1','0','0'},
        {'0','0','0','1','1'}
    };

    cout << numIslands(grid) << endl; // expected: 3
    return 0;
}
```

Three kingdoms rose from the sea:

1. The great island in the northwest
2. The lonely speck in the center
3. The twin islands in the southeast, bound as one

The Oracle declared the count: **3**.

---

### 🧠 Memory of the Island-Revealing Journey

-   Use DFS to explore and “sink” each island.
-   Count an island when encountering a `'1'`.
-   Convert visited land to `'0'` to avoid recounting.
-   Only four directions count—no diagonals.
-   **Time:** O(rows × cols)
-   **Space:** O(rows × cols) worst-case recursion depth.

Thus is remembered the saga of **Number of Islands**,
where the Oracle sails the Grid-Sea,
discovering hidden kingdoms one by one
until the entire archipelago stands revealed beneath the sun. 🌊🏝️✨
