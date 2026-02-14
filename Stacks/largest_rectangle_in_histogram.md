## 🏛️🔥 _The Pillars of the Burning Kingdom: The Largest Rectangle in Histogram Saga_

> \*"Across the Kingdom of Bars,
> pillars rose side by side —
> some tall and proud,
> others short and humble.
>
> The Oracle was commanded:
>
> **‘Find the largest rectangle
> that can be formed within these pillars.’**
>
> A rectangle could stretch across many bars —
> but its height was limited
> by the shortest pillar within its span.
>
> The Oracle knew brute force would burn the kingdom.
>
> Instead, she summoned a stack of increasing heights —
> for whenever a pillar fell shorter than the one before,
> a rectangle’s destiny had been decided."\*

---

This is the epic saga of **Largest Rectangle in Histogram**.

You are given an array `heights`,
where each element represents the height of a bar.

Your task:

-   Return the area of the **largest rectangle**
-   That can be formed using contiguous bars.

---

## 🧠 The Oracle’s Core Insight — When Height Drops, Area Is Final

The Oracle understood:

-   If heights are increasing → keep stacking
-   If a shorter bar appears →
    the taller bar before it can no longer extend

So when a bar of smaller height appears:

-   Pop the taller bar
-   Compute the rectangle area
-   Use the current index to determine width

This calls for a **monotonic increasing stack** of indices.

---

## ⚔️ The Oracle’s Monotonic Stack Ritual

_Resolve rectangles when height falls_

```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> st;
    int maxArea = 0;
    int n = heights.size();
```

The stack stores indices of increasing heights.

---

### 🌊 Walk Through All Bars

```cpp
    for (int i = 0; i <= n; i++) {
        int currHeight = (i == n) ? 0 : heights[i];
```

At the end, the Oracle uses a fake height `0`
to flush remaining bars.

---

### 🔥 Resolve Taller Bars

```cpp
        while (!st.empty() && currHeight < heights[st.top()]) {
            int h = heights[st.top()];
            st.pop();
```

When current height is smaller:

-   The bar at top has found its right boundary.

---

### 📏 Compute Width and Area

```cpp
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, h * width);
        }
```

Width is determined by:

-   If stack empty → from 0 to i
-   Otherwise → between previous smaller element and current

Area = height × width.

---

### 🏗️ Add Current Bar

```cpp
        st.push(i);
    }
    return maxArea;
}
```

The current bar joins the stack
to possibly form future rectangles.

---

### 🎺 The Trial of the Pillars

```cpp
int main() {
    vector<int> heights = {2,1,5,6,2,3};
    cout << largestRectangleArea(heights) << endl;
    // expected: 10
    return 0;
}
```

The Oracle discovered:

-   Bars `5` and `6`
-   Width = 2
-   Area = 10

The greatest rectangle in the kingdom.

---

### 🧠 Memory of the Pillar Law

-   Use monotonic increasing stack
-   Store indices
-   When height decreases → pop and compute area
-   Width depends on previous smaller bar
-   Add sentinel `0` to flush stack
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Largest Rectangle in Histogram**,
where the Oracle walks through rising and falling pillars,
waiting patiently for heights to drop —
and in that moment,
revealing the maximum kingdom
that each pillar could have ruled. 🏛️✨
