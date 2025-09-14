## 🌊 _The Valley of Vessels: The Container With Most Water Saga_

> _"Two cliffs stand on opposite shores; between them, water may rest.
> The sea’s capacity is not in a single cliff’s height,
> but in the meeting of both — the lower will hold the tide,
> and the farther apart they stand, the wider the basin."_

---

Along a jagged coastline, towers rose with differing heights — cliffs of stone, columns of memory. Villagers wondered: between which two cliffs could they capture the most water if they built a trough from one cliff to the other? The naive folk measured every pair, but the Sage knew a swifter way: start with the widest possible basin (the farthest cliffs) and then walk inward, always nudging the shorter cliff, for only by raising the shorter side might the basin’s capacity increase.

Thus began the rite of the **Container With Most Water**.

---

### 📜 The Tools of the Coast

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The cartographers carried a list of cliff heights (`vector<int>`) and a simple formula: area = min(height\[left], height\[right]) \* width (right - left). They also kept the two-pointer method in their chest — one pointer at each end of the shore.

---

### 🧭 The Sage’s March — Two Pointers, One Goal

```cpp
int maxArea(vector<int>& height) {
    int l = 0;
    int r = height.size() - 1;
    long long best = 0;
```

The Sage set two sentinels: `l` at the leftmost cliff, `r` at the rightmost. `best` recorded the largest basin seen so far.

---

```cpp
    while (l < r) {
        long long h = min(height[l], height[r]);
        long long w = r - l;
        best = max(best, h * w);
```

At each stance, the Sage measured the basin: the water’s height is limited by the shorter cliff (`h`), and the width is the distance between them (`w`). The product gave the current capacity, and if it exceeded `best`, it was remembered.

---

```cpp
        // move the pointer at the shorter cliff inward:
        // only that can possibly increase the min height and thus possibly increase area
        if (height[l] < height[r]) ++l;
        else --r;
    }
    return static_cast<int>(best);
}
```

Then the Sage moved — but not both. The shorter cliff was nudged inward, seeking a taller partner. Moving the taller cliff inward cannot increase the limiting height, so it would never produce a larger basin at this width. This simple wisdom lets the Sage scan all possibilities in linear time.

---

### 🎺 The Trial of the Cliffs

```cpp
int main() {
    vector<int> heights = {1,8,6,2,5,4,8,3,7};
    cout << maxArea(heights) << endl; // expected: 49 (between heights[1]=8 and heights[8]=7)
    return 0;
}
```

From these cliffs the Sage finds the largest basin: the valley between the second cliff (height 8) and the last cliff (height 7), wide and deep enough to hold the most water.

---

### 🧭 Memory of the Basin

-   **Two pointers** — start at both ends, march inward.
-   **Area formula:** `area = min(height[l], height[r]) * (r - l)`.
-   **Move the smaller pointer** — only by moving the shorter side can the limiting height increase and possibly yield a larger area.
-   **Time:** O(n) — every cliff visited at most once.
-   **Space:** O(1) — constant extra memory.

Thus is remembered the saga of the **Container With Most Water**: begin wide, measure honestly, and let the shorter cliff lead the way inward toward the deepest basin. 🌊
