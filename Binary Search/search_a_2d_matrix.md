## 🗺️🔎 _The Scroll of Flattened Realms: The Search a 2D Matrix Saga_

> \*"In the Library of Ordered Realms,
> rows of numbers were arranged with sacred discipline.
>
> Each row was sorted from left to right.
>
> And the first number of every row
> was greater than the last of the previous one.
>
> The Oracle was commanded:
>
> **‘Search for the target in this 2D matrix.’**
>
> The foolish would treat it as a grid.
>
> But the Oracle saw deeper —
> she saw that this matrix
> was merely a **flattened sorted array in disguise**."\*

---

This is the epic saga of **Search a 2D Matrix**.

You are given:

-   a 2D matrix `matrix`
-   an integer `target`

Properties:

-   Each row is sorted.
-   First element of each row > last element of previous row.

Your task:

-   Return `true` if target exists.
-   Otherwise `false`.

---

## 🧠 The Oracle’s Core Insight — Treat It as 1D

If there are:

```
m rows
n columns
```

Then the total elements:

```
m * n
```

We can imagine:

```
index → row = index / n
index → col = index % n
```

Thus perform binary search
on range `[0, m*n - 1]`.

---

## ⚔️ The Oracle’s Flattened Binary Ritual

```cpp
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    if (matrix.empty() || matrix[0].empty()) return false;

    int m = matrix.size();
    int n = matrix[0].size();

    int left = 0;
    int right = m * n - 1;
```

Two sentinels guard the flattened scroll.

---

### 🌊 Narrow the Search

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle inspects the middle index.

---

### 🧭 Convert 1D Index to 2D Coordinates

```cpp
        int row = mid / n;
        int col = mid % n;
        int value = matrix[row][col];
```

The hidden grid position is revealed.

---

### 🎯 Compare with Target

```cpp
        if (value == target) {
            return true;
        }
```

The target is found.

---

### 🔻 Move Right

```cpp
        else if (value < target) {
            left = mid + 1;
        }
```

Search in the greater half.

---

### 🔺 Move Left

```cpp
        else {
            right = mid - 1;
        }
    }
    return false;
}
```

Search in the smaller half.

---

### 🎺 The Trial of the Hidden Scroll

```cpp
int main() {
    vector<vector<int>> matrix = {
        {1, 3, 5, 7},
        {10, 11, 16, 20},
        {23, 30, 34, 60}
    };

    cout << searchMatrix(matrix, 3) << endl;  // true
    cout << searchMatrix(matrix, 13) << endl; // false
    return 0;
}
```

The Oracle halved the flattened realm
until truth was revealed.

---

## 🧠 Memory of the Flattened Realm Law

-   Matrix behaves like sorted 1D array
-   Total size = m × n
-   Convert index to row & column
-   Use binary search
-   **Time:** O(log(mn))
-   **Space:** O(1)

---

Thus is remembered the saga of
**Search a 2D Matrix**,
where the Oracle looks beyond rows and columns —
flattening the illusion of dimensions —
and with disciplined halving,
finds the hidden target
within the ordered scroll. 🗺️✨
