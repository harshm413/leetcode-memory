## 🏗️💧 _The Widest Vessel: The Container With Most Water Saga_

> \_"The Oracle was given an array of heights.
> Each element represented a vertical line on the x-axis.
>
> She was commanded:
>
> **'Find two lines that, together with the x-axis,
> form a container that holds the most water.'**
>
> Water area = width × min(height of two lines).
> The shorter line is the bottleneck — water overflows there.
>
> The Oracle used two pointers from opposite ends:
>
> **Start with the WIDEST container (left=0, right=n-1).**
> **Move the SHORTER line inward.**
>
> Why move the shorter one? Because:
> -   Moving the taller one can only DECREASE the area
>     (width shrinks, height can't increase past the shorter bottleneck).
> -   Moving the shorter one MIGHT find a taller line
>     (width shrinks, but height might increase enough to compensate).
>
> The shorter line has nothing to gain by staying. Move it."\_

---

This is the saga of **Container With Most Water (LeetCode 11)**.

Given an array `height` of n non-negative integers:
-   Find two lines that form the container with the most water.
-   Return the maximum area.

```
Input:  height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
Output: 49  (lines at index 1 and 8: min(8,7) × 7 = 49)

Input:  height = [1, 1]
Output: 1   (min(1,1) × 1 = 1)

Input:  height = [4, 3, 2, 1, 4]
Output: 16  (lines at index 0 and 4: min(4,4) × 4 = 16)
```

---

## 🧠 The Area Formula

```
area = width × height
     = (right - left) × min(height[left], height[right])
```

**Width** = distance between the two lines = `right - left`.
**Height** = the SHORTER of the two lines = `min(h[left], h[right])`.

Water overflows at the shorter line. The taller line doesn't help
beyond the shorter one's height.

---

## 🧠 Why Move the Shorter Line? — The Proof

The current area is limited by the shorter line.

**If we move the TALLER line inward:**
-   Width decreases by 1 (always).
-   Height stays the same or decreases (still limited by the shorter line,
    which hasn't moved). It can NEVER increase.
-   Area = smaller width × same/smaller height = GUARANTEED worse or equal.
-   **Provably useless. Every container with the taller line + smaller width is worse.**

**If we move the SHORTER line inward:**
-   Width decreases by 1 (always).
-   Height MIGHT increase (if we find a taller line to replace the short one).
-   Area MIGHT increase (if the height gain outweighs the width loss).
-   **Not guaranteed to improve, but it's the only move with a CHANCE.**

> _"The shorter line is the bottleneck.
> Moving the taller line can't help — the bottleneck stays.
> Moving the shorter line might find a taller replacement.
> It's the only move that has hope."_

---

## 🧠 Why This Doesn't Miss the Optimal Pair

When we move the shorter line, we discard all containers that include
that short line paired with anything INSIDE the current range.

But ALL those containers are worse than the current one:
-   They have smaller width (inside = closer together).
-   They have the same height limit (still bottlenecked by the short line).
-   Area = smaller width × same height = worse.

So discarding them is safe. The optimal pair is never skipped.

---

### 📜 The Scroll of the Widest Vessel

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 💧 The Two-Pointer Solution

### Initialize

```cpp
int maxArea(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int maxWater = 0;
```

Start with the widest possible container (opposite ends).
`maxWater` tracks the best area found.

---

### Converge inward

```cpp
    while (left < right) {
```

---

### Compute current area

```cpp
        int width = right - left;
        int h = min(height[left], height[right]);
        maxWater = max(maxWater, width * h);
```

Width × min height. Update maximum if this is the best.

---

### Move the shorter line inward

```cpp
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    return maxWater;
}
```

The shorter line moves. If equal — either can move (both are bottlenecks,
moving either has the same "might improve" logic).

> _"The shorter wall steps aside.
> Maybe the next wall is taller.
> The taller wall stays — it's not the problem."_

---

### 🎺 The Trial of the Widest Vessel

```cpp
int main() {
    vector<int> h1 = {1, 8, 6, 2, 5, 4, 8, 3, 7};
    cout << maxArea(h1) << endl; // expected: 49

    vector<int> h2 = {1, 1};
    cout << maxArea(h2) << endl; // expected: 1

    vector<int> h3 = {4, 3, 2, 1, 4};
    cout << maxArea(h3) << endl; // expected: 16

    vector<int> h4 = {1, 2, 1};
    cout << maxArea(h4) << endl; // expected: 2

    return 0;
}
```

---

**Full trace for height = [1, 8, 6, 2, 5, 4, 8, 3, 7]:**

| left | right | h[l] | h[r] | width | min_h | area | maxWater | Move |
|------|-------|------|------|-------|-------|------|----------|------|
| 0 | 8 | 1 | 7 | 8 | 1 | 8 | 8 | left++ (1<7) |
| 1 | 8 | 8 | 7 | 7 | 7 | **49** | **49** | right-- (8>7) |
| 1 | 7 | 8 | 3 | 6 | 3 | 18 | 49 | right-- (8>3) |
| 1 | 6 | 8 | 8 | 5 | 8 | 40 | 49 | right-- (equal) |
| 1 | 5 | 8 | 4 | 4 | 4 | 16 | 49 | right-- (8>4) |
| 1 | 4 | 8 | 5 | 3 | 5 | 15 | 49 | right-- (8>5) |
| 1 | 3 | 8 | 2 | 2 | 2 | 4 | 49 | right-- (8>2) |
| 1 | 2 | 8 | 6 | 1 | 6 | 6 | 49 | right-- (8>6) |
| 1 | 1 | — | — | — | — | — | 49 | left>=right, stop |

**Answer: 49** ✓ (lines at indices 1 and 8: min(8,7) × 7 = 49)

The optimal was found at step 2. The rest of the search confirmed
nothing better exists.

---

**Trace for height = [4, 3, 2, 1, 4]:**

| left | right | h[l] | h[r] | area | maxWater | Move |
|------|-------|------|------|------|----------|------|
| 0 | 4 | 4 | 4 | 4×4=**16** | **16** | right-- (equal) |
| 0 | 3 | 4 | 1 | 3×1=3 | 16 | right-- (4>1) |
| 0 | 2 | 4 | 2 | 2×2=4 | 16 | right-- (4>2) |
| 0 | 1 | 4 | 3 | 1×3=3 | 16 | right-- (4>3) |
| 0 | 0 | — | — | — | 16 | stop |

**Answer: 16** ✓ (widest container with equal heights was the best)

---

**Trace for height = [1, 2, 1]:**

| left | right | h[l] | h[r] | area | maxWater | Move |
|------|-------|------|------|------|----------|------|
| 0 | 2 | 1 | 1 | 2×1=2 | 2 | right-- (equal) |
| 0 | 1 | 1 | 2 | 1×1=1 | 2 | left++ (1<2) |
| 1 | 1 | — | — | — | 2 | stop |

**Answer: 2** ✓

---

## 🔍 Container With Most Water vs Trapping Rain Water

| Aspect | Container (this) | Trapping Rain Water |
|--------|-----------------|---------------------|
| What to find | Max area between TWO lines | Total water trapped between ALL lines |
| Lines used | Exactly 2 (the container walls) | All lines contribute |
| Formula | `width × min(h[l], h[r])` | `min(leftMax, rightMax) - h[i]` per cell |
| Approach | Two pointers (move shorter) | Prefix/suffix max or two pointers |
| Answer | Single maximum area | Sum of water at every position |

Different problems despite similar visuals. Don't confuse them.

---

## 🔍 Why Not Brute Force?

```cpp
// O(N²) — check every pair
for (int i = 0; i < n; i++)
    for (int j = i + 1; j < n; j++)
        maxWater = max(maxWater, (j-i) * min(h[i], h[j]));
```

Works but O(N²). The two-pointer approach is O(N) because
each step provably eliminates one element without missing the optimal.

---

## 🔍 What If Heights Are Equal?

When `height[left] == height[right]`, moving either is fine.
Both are equally "short" — both are bottlenecks.
Moving either has the same "might find something taller" logic.

Some implementations move both (`left++; right--;`). Also correct.
The answer doesn't change.

---

### 🧠 Memory of the Widest Vessel Law

-   **Two pointers:** start at widest (`left=0, right=n-1`)
-   **Area:** `(right - left) × min(h[left], h[right])`
-   **Move the SHORTER line** — only move with a chance of improvement
-   **Why safe:** all containers with the shorter line + smaller width are provably worse
-   **Equal heights:** move either (both are bottlenecks)
-   **Different from Trapping Rain Water** — this finds ONE max area, not total trapped
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Container With Most Water**,
where the Oracle started with the widest vessel
and moved the shorter wall inward —
knowing that the shorter wall was the bottleneck,
that moving the taller wall was provably futile,
that only the shorter wall had hope of finding
a taller replacement —
until the maximum area was found
in a single inward march from both ends. 🏗️💧✨
