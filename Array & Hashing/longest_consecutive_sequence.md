## 🏔️ _The March of Unbroken Steps: The Longest Consecutive Sequence Saga_

> _"A path is measured not by stones alone,
> but by the long, unbroken steps between them.
> Find the longest trail where each step greets its neighbor —
> and you find the road that remembers time itself."_

---

Across the highlands, scattered stones bore numbers — some isolated, some forming trails where each stone differed from its neighbor by exactly one. Travelers sought the longest unbroken trail they could walk by stepping from `x` to `x+1` without gaps. The Mapmaker was called: not to sort all the stones, but to spot the starts of true trails and measure how far they ran.

Thus began the quest of the **Longest Consecutive Sequence**.

---

### 📜 The Map and the Marks

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

The Mapmaker unrolled his parchments (`vector`) and brought the **Set of Stones** (`unordered_set`) that would remember which stones stood where. With the set, he could tell at a glance if a neighbor existed — without sorting or long marches.

---

### 🧭 The Mapmaker’s Ritual

```cpp
int longestConsecutive(vector<int>& nums) {
    unordered_set<int> stones(nums.begin(), nums.end());
    int longest = 0;
```

All stones were stamped into the set — a quick ledger of presence. `longest` kept the memory of the longest trail discovered so far.

---

### 🔎 Seek the Trailheads

```cpp
    for (int x : stones) {
        // only start counting at the beginning of a sequence
        if (!stones.count(x - 1)) {
            int current = x;
            int length = 1;
```

For each stone `x`, the Mapmaker asked: _Is there a stone one less (`x-1`)?_ If not, `x` is a **trailhead** — the true beginning of a consecutive path. Only from trailheads did he begin counting, so each path is measured once without repetition.

---

### 🏃 The Walk Along the Path

```cpp
            while (stones.count(current + 1)) {
                current++;
                length++;
            }
            longest = max(longest, length);
        }
    }
    return longest;
}
```

From the trailhead `x`, the Mapmaker stepped forward while `x+1`, `x+2`, ... existed in the set. He tallied `length` until the trail broke. He then recorded the longest trail seen and continued — never restarting in the middle of a known path.

---

### 🎺 The Trial of Many Stones

```cpp
int main() {
    vector<int> nums = {100, 4, 200, 1, 3, 2};
    cout << longestConsecutive(nums) << endl; // expected: 4 (1,2,3,4)
    return 0;
}
```

Among the scattered stones, one trail ran `1,2,3,4` — four steps unbroken. Others sat alone like 100 or 200. The Mapmaker’s ritual found the longest unbroken march: `4`.

---

### 🧠 Memory of the Trail

-   **unordered_set** → fast presence check for any stone.
-   **Start only at x where x-1 doesn't exist** → ensures each consecutive run is counted once.
-   **While loop current+1 exists** → walk the trail, increment length.
-   **O(n) average time** — each stone is visited at most a couple of times (insert + single walk).
-   **Why this works:** no sorting required; presence checks and starting only at trailheads yield a linear-time discovery of the longest consecutive run.

Thus is remembered the saga of the **Longest Consecutive Sequence**, where the Mapmaker measures the land by finding the longest road of neighboring stones — a march of unbroken steps across the highlands. 🏔️
