## 🌊⚔️ _The Twin Walls of the Endless Sea: The Container With Most Water Saga_

> \*"Along the Shores of Heights,
> stone walls rose from the ocean floor —
> some tall and proud,
> others short and worn by time.
>
> Between any two walls,
> water could be held —
> but only as much as
> the **shorter wall allowed**,
> and only as wide
> as the distance between them.
>
> The Oracle was commanded:
>
> **‘Choose two walls
> that can hold the most water.’**
>
> She knew brute force would drown her in possibilities.
>
> Instead, she placed two sentinels
> at opposite ends of the shore
> and let logic, not force,
> guide their steps."\*

---

This is the saga of **Container With Most Water**.

You are given an array `height`,
where each element represents
the height of a vertical wall.

Your task:

-   Choose **two different indices**
-   Form a container with the x-axis
-   Return the **maximum amount of water** it can hold

---

## 🧠 The Oracle’s Core Insight — Width Shrinks, Height Must Improve

The Oracle understood two unbreakable laws:

1. **Water is limited by the shorter wall**
2. **Width always shrinks** when pointers move inward

So:

-   Keeping the **shorter wall** while shrinking width
    can never increase area
-   The only hope is to **move the shorter wall**,
    searching for a taller one

Thus began the **Two-Pointer Ocean Ritual**.

---

### 📜 The Scroll of Tides and Walls

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Two-Sentinel Ritual

_One at dawn, one at dusk_

```cpp
int maxArea(vector<int>& height) {
    int left = 0;
    int right = height.size() - 1;
    int best = 0;
```

Two sentinels were placed:

-   `left` at the first wall
-   `right` at the last wall

---

### 🌊 Measure the Current Container

```cpp
    while (left < right) {
        int h = min(height[left], height[right]);
        int w = right - left;
        best = max(best, h * w);
```

The Oracle measured:

-   Height → minimum of the two walls
-   Width → distance between them
-   Area → `height × width`

---

### 🔄 Move the Weaker Wall

```cpp
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    return best;
}
```

-   If the left wall was shorter → move left inward
-   Otherwise → move right inward

Only by seeking a taller wall
could a greater container be formed.

---

### 🎺 The Trial of the Endless Shore

```cpp
int main() {
    vector<int> height = {1,8,6,2,5,4,8,3,7};
    cout << maxArea(height) << endl; // expected: 49
    return 0;
}
```

The Oracle found the greatest vessel:

-   Walls at heights **8** and **7**
-   Width = 7
-   Area = **49**

No other pair could hold more.

---

### 🧠 Memory of the Ocean Law

-   Use two pointers from both ends
-   Area = min(height[left], height[right]) × width
-   Always move the shorter wall
-   Width shrinks, height must improve
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Container With Most Water**,
where the Oracle stands between twin walls and endless sea,
trusting not brute force,
but the quiet logic of tides —
moving only where weakness lies,
until the greatest vessel of all
is revealed beneath the sunlit horizon. 🌊✨
