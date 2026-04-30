## 🎨🌊 _The Painter's Spell: The Flood Fill Saga_

> \_"In the Grid of Colors,
> every cell was painted with a number --
> its color.
>
> The Oracle was given a starting cell `(sr, sc)`
> and a new color.
>
> She was commanded:
>
> **'Starting from this cell,
> repaint every cell that shares the SAME original color
> AND is connected to the starting cell
> (horizontally or vertically, through same-colored cells).
> Change them all to the new color.'**
>
> The Oracle recognized this as the simplest DFS flood fill --
> the same "burn and spread" pattern as Number of Islands,
> but instead of sinking land to water,
> she was repainting one color to another.
>
> Start at `(sr, sc)`.
> Record the original color.
> DFS to every connected cell with that same color.
> Paint each one with the new color.
>
> One edge case to watch:
> **if the new color equals the original color -- do nothing.**
> Otherwise the DFS would revisit painted cells forever
> (they'd still match the 'original' color)."\_

---

This is the saga of **Flood Fill**.

Given a 2D grid `image`, a starting cell `(sr, sc)`, and a `color`:

-   Starting from `(sr, sc)`, change the color of every cell
    that is connected (4-directionally) to the starting cell
    AND has the same color as the starting cell.
-   Return the modified image.

```
Input:
  image = [[1,1,1],[1,1,0],[1,0,1]], sr = 1, sc = 1, color = 2
Output:
  [[2,2,2],[2,2,0],[2,0,1]]

Input:
  image = [[0,0,0],[0,0,0]], sr = 0, sc = 0, color = 0
Output:
  [[0,0,0],[0,0,0]]  (new color == original color, nothing changes)
```

---

## 🧠 The Oracle's Core Insight -- DFS with Color Matching

This is the most basic DFS flood fill:

```
1. Record the original color at (sr, sc).
2. If original color == new color → return immediately (nothing to do).
3. DFS from (sr, sc):
   - If out of bounds → stop.
   - If cell's color != original color → stop.
   - Paint the cell with new color.
   - Recurse to all 4 neighbors.
```

Painting the cell with the new color serves as the **visited marker** --
once painted, the cell no longer matches the original color,
so the DFS won't revisit it.

**The critical edge case:**
If `newColor == originalColor`, painting changes nothing.
The cell still matches the original color after painting.
DFS would revisit it → infinite recursion.
The early return prevents this.

```
Time:  O(rows × cols) -- every cell visited at most once
Space: O(rows × cols) -- recursion stack worst case
```

---

### 📜 The Scroll of the Painter's Grid

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🎨 The DFS -- Paint and Spread

```cpp
void dfs(vector<vector<int>>& image, int r, int c,
         int originalColor, int newColor) {
```

The Oracle entered a cell, ready to paint and spread.

---

### 🛑 Out of Bounds -- Stop

```cpp
    int rows = image.size();
    int cols = image[0].size();

    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
```

Walked off the grid. Turn back.

---

### 🛑 Wrong Color -- Stop

```cpp
    if (image[r][c] != originalColor) return;
```

This cell doesn't match the original color.
It's either a different color (boundary of the fill region)
or already painted with the new color (visited).

Either way -- don't touch it. Stop.

> _"The paint only spreads to cells
> that wear the original color.
> A different color is a wall.
> An already-painted cell is a wall too."_

---

### 🎨 Paint This Cell

```cpp
    image[r][c] = newColor;
```

The cell was repainted.

This also marks it as visited --
it no longer matches `originalColor`,
so the DFS will never return here.

---

### 🧭 Spread to All Four Neighbors

```cpp
    dfs(image, r - 1, c, originalColor, newColor);
    dfs(image, r + 1, c, originalColor, newColor);
    dfs(image, r, c - 1, originalColor, newColor);
    dfs(image, r, c + 1, originalColor, newColor);
}
```

The paint spread up, down, left, right.
Each neighbor was checked -- if it matched the original color,
it was painted and the spread continued.

---

## 🌊 The Main Ritual

```cpp
vector<vector<int>> floodFill(vector<vector<int>>& image,
                               int sr, int sc, int color) {
    int originalColor = image[sr][sc];
```

Record the original color of the starting cell.
This is the color we're looking for during the DFS.

---

### 🛡️ Edge Case -- Same Color, Nothing to Do

```cpp
    if (originalColor == color) return image;
```

If the new color is the same as the original --
every cell is already the "right" color.
No painting needed. Return immediately.

**Without this check, the DFS would loop forever:**
painting a cell with the same color doesn't change it,
so it still matches `originalColor`,
so the DFS visits it again, and again, and again.

> _"If the paint is the same color as the canvas,
> the brush changes nothing.
> And a brush that changes nothing
> will never know when to stop."_

---

### 🔥 Start the Flood Fill

```cpp
    dfs(image, sr, sc, originalColor, color);
    return image;
}
```

DFS from the starting cell.
Every connected cell with the original color was repainted.
Return the modified image.

---

### 🎺 The Trial of the Painter's Spell

```cpp
int main() {
    vector<vector<int>> img1 = {{1,1,1},{1,1,0},{1,0,1}};
    img1 = floodFill(img1, 1, 1, 2);
    for (auto& row : img1) {
        for (int c : row) cout << c << " ";
        cout << endl;
    }
    // expected:
    // 2 2 2
    // 2 2 0
    // 2 0 1

    vector<vector<int>> img2 = {{0,0,0},{0,0,0}};
    img2 = floodFill(img2, 0, 0, 0);
    for (auto& row : img2) {
        for (int c : row) cout << c << " ";
        cout << endl;
    }
    // expected:
    // 0 0 0
    // 0 0 0
    return 0;
}
```

---

**Full trace for image = [[1,1,1],[1,1,0],[1,0,1]], sr=1, sc=1, color=2:**

```
Original:
  1 1 1
  1 1 0
  1 0 1

originalColor = 1, newColor = 2.
1 != 2 → proceed with DFS.
```

**DFS from (1,1):**

| Visit   | Color match? | Action          | Image after              |
| ------- | ------------ | --------------- | ------------------------ |
| (1,1)   | 1 == 1 ✓     | Paint 2. Spread.| `[[1,1,1],[1,2,0],[1,0,1]]` |
| (0,1)   | 1 == 1 ✓     | Paint 2. Spread.| `[[1,2,1],[1,2,0],[1,0,1]]` |
| (0,0)   | 1 == 1 ✓     | Paint 2. Spread.| `[[2,2,1],[1,2,0],[1,0,1]]` |
| (1,0)   | 1 == 1 ✓     | Paint 2. Spread.| `[[2,2,1],[2,2,0],[1,0,1]]` |
| (2,0)   | 1 == 1 ✓     | Paint 2. Spread.| `[[2,2,1],[2,2,0],[2,0,1]]` |
| (2,1)   | 0 != 1 ✗     | Stop.           |                          |
| (0,2)   | 1 == 1 ✓     | Paint 2. Spread.| `[[2,2,2],[2,2,0],[2,0,1]]` |
| (1,2)   | 0 != 1 ✗     | Stop.           |                          |

```
Final:
  2 2 2
  2 2 0
  2 0 1
```

**Answer matches expected** ✓

The `0`s acted as walls -- the paint couldn't cross them.
Cell (2,2) has color `1` but is NOT connected to the starting cell
through same-colored cells (blocked by `0`s). It stayed `1`.

---

**Trace for image = [[0,0,0],[0,0,0]], sr=0, sc=0, color=0:**

`originalColor = 0`, `newColor = 0`. Same color → return immediately.

**Answer: unchanged** ✓

---

**Trace for image = [[0,0,0],[0,1,1]], sr=1, sc=1, color=1:**

`originalColor = 1`, `newColor = 1`. Same color → return immediately.

**Answer: unchanged** ✓

---

**Trace for image = [[2]], sr=0, sc=0, color=5:**

`originalColor = 2`, `newColor = 5`. Different → DFS.
Paint (0,0) from 2 to 5. No neighbors (1×1 grid).

**Answer: [[5]]** ✓

---

## 🔍 Why This Is the Simplest Graph Problem

Flood Fill is the **hello world** of grid graph problems.

-   One DFS call (not counting components).
-   No counting, no tracking sizes, no multi-source.
-   Just "start here, spread to matching neighbors, change color."

Every harder grid problem builds on this foundation:
-   Number of Islands = flood fill + counting.
-   Max Area = flood fill + size tracking.
-   Surrounded Regions = flood fill from borders.

Master flood fill and the rest follows naturally.

---

## 🔄 BFS Alternative

Replace DFS with BFS -- same result:

```cpp
vector<vector<int>> floodFill(vector<vector<int>>& image,
                               int sr, int sc, int color) {
    int originalColor = image[sr][sc];
    if (originalColor == color) return image;

    int rows = image.size(), cols = image[0].size();
    queue<pair<int,int>> q;
    q.push({sr, sc});
    image[sr][sc] = color;

    int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
    while (!q.empty()) {
        auto [r, c] = q.front(); q.pop();
        for (auto& d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && image[nr][nc] == originalColor) {
                image[nr][nc] = color;
                q.push({nr, nc});
            }
        }
    }
    return image;
}
```

BFS fills level by level (like ripples).
DFS fills depth-first (like a snake).
Both produce the same final image.

---

### 🧠 Memory of the Painter's Spell Law

-   **Record original color** at `(sr, sc)`
-   **Edge case:** if `originalColor == newColor` → return immediately (prevents infinite loop)
-   **DFS:** if out of bounds or wrong color → stop. Else paint + recurse to 4 neighbors.
-   **Painting = visiting:** the new color prevents revisits (no longer matches original)
-   **BFS alternative:** queue-based, same logic
-   **Time:** O(rows × cols)
-   **Space:** O(rows × cols) -- recursion stack or queue
-   **Edge cases:**
    -   Same color → do nothing
    -   Single cell → paint just that cell
    -   Entire grid same color → paint everything
    -   Starting cell surrounded by different colors → paint only starting cell

Thus is remembered the saga of **Flood Fill**,
where the Oracle started at a single cell
and let the paint spread outward --
touching every connected cell that wore the original color,
repainting each one,
stopping at boundaries of different colors --
until the entire connected region
was transformed by the painter's spell. 🎨🌊✨
