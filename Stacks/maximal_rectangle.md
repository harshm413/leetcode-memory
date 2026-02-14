## 🏛️🔥 _The Empire of Expanding Rectangles: The Maximal Rectangle Saga_

> \*"Across a vast binary battlefield,
> rows of `'1'` and `'0'` formed shifting terrain.
>
> `'1'` meant solid ground.
> `'0'` meant broken earth.
>
> The Oracle was commanded:
>
> **‘Find the largest rectangle
> made entirely of solid ground.’**
>
> But she knew this was no simple grid.
>
> Each row could become a histogram.
>
> And within every histogram,
> a hidden rectangle awaited discovery."\*

---

This is the epic saga of **Maximal Rectangle**.

You are given a 2D binary matrix of `'0'`s and `'1'`s.

Your task:

-   Find the area of the **largest rectangle containing only '1's**

---

## 🧠 The Oracle’s Core Insight — Turn Rows into Histograms

For each row:

-   Build a height array:

    -   If cell == '1' → increase height
    -   If cell == '0' → reset height to 0

Thus every row becomes a **Histogram problem**.

Then apply:

> Largest Rectangle in Histogram
> for each row.

---

## ⚔️ The Oracle’s Two-Layer Ritual

First: The histogram solver.

---

### 🔥 Largest Rectangle in Histogram

```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> st;
    int maxArea = 0;
    int n = heights.size();

    for (int i = 0; i <= n; i++) {
        int currHeight = (i == n) ? 0 : heights[i];

        while (!st.empty() && currHeight < heights[st.top()]) {
            int h = heights[st.top()];
            st.pop();
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, h * width);
        }
        st.push(i);
    }
    return maxArea;
}
```

---

### 🏛️ The Maximal Rectangle Ritual

```cpp
int maximalRectangle(vector<vector<char>>& matrix) {
    if (matrix.empty()) return 0;

    int rows = matrix.size();
    int cols = matrix[0].size();
    vector<int> heights(cols, 0);
    int maxArea = 0;
```

Initialize histogram heights.

---

### 🌄 Build Heights Row by Row

```cpp
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] == '1')
                heights[j] += 1;
            else
                heights[j] = 0;
        }
```

Each row transforms the histogram.

---

### 🔥 Apply Histogram Solver

```cpp
        maxArea = max(maxArea, largestRectangleArea(heights));
    }
    return maxArea;
}
```

The Oracle evaluates each row’s terrain.

---

### 🎺 The Trial of the Battlefield

```cpp
int main() {
    vector<vector<char>> matrix = {
        {'1','0','1','0','0'},
        {'1','0','1','1','1'},
        {'1','1','1','1','1'},
        {'1','0','0','1','0'}
    };

    cout << maximalRectangle(matrix) << endl;
    // expected: 6
    return 0;
}
```

The Oracle found:

-   A rectangle of area **6**
-   Hidden within the layered terrain

---

## 🧠 Memory of the Expanding Empire Law

-   Convert each row into histogram heights
-   Apply Largest Rectangle in Histogram
-   Maintain global maximum
-   **Time:** O(rows × cols)
-   **Space:** O(cols)

Thus is remembered the saga of
**Maximal Rectangle**,
where the Oracle scans each layer of the battlefield,
raising histograms from rows of stone —
and within them uncovering
the greatest rectangle of solid ground,
hidden in plain sight. 🏛️✨
