## 🌪️🏔️ _The Moving Crown of Peaks: The Sliding Window Maximum Saga_

> \*"Across the Endless Ridge stood towering peaks of numbers.
>
> A magical window moved across the land —
> never stopping, never turning back.
>
> At each position,
> the Oracle was commanded:
>
> **‘Reveal the highest peak
> within the current window.’**
>
> To search every window from scratch
> would be wasteful.
>
> The Oracle knew this:
>
> When a stronger peak arrives,
> weaker ones behind it lose all future power.
>
> And when a peak falls outside the window,
> its reign must end."\*

---

This is the epic saga of **Sliding Window Maximum**.

You are given:

-   an array `nums`
-   an integer `k` (window size)

Your task:

-   Return the maximum element for every window of size `k`.

---

## 🧠 The Oracle’s Core Insight — Maintain Only Useful Peaks

The Oracle used a **monotonic decreasing deque**:

-   Store **indices**, not values.
-   The front always holds the index of the maximum.
-   Remove:

    -   Indices that leave the window.
    -   Smaller values from the back (they are useless).

---

## ⚔️ The Oracle’s Monotonic Deque Ritual

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;
    vector<int> result;
```

The deque holds indices
in decreasing order of values.

---

### 🌊 Walk Through the Array

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

Each step moves the window forward.

---

### 🚪 Remove Outdated Indices

```cpp
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
```

If an index is no longer in the window,
its reign ends.

---

### 🔥 Remove Smaller Peaks

```cpp
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
```

If the current number is stronger,
weaker peaks behind it are removed.

---

### 🏔️ Add Current Index

```cpp
        dq.push_back(i);
```

The current peak joins the formation.

---

### 👑 Record the Maximum

```cpp
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    return result;
}
```

Once the first full window forms,
the Oracle records the crown of that window.

---

### 🎺 The Trial of the Moving Ridge

```cpp
int main() {
    vector<int> nums = {1,3,-1,-3,5,3,6,7};
    int k = 3;

    vector<int> ans = maxSlidingWindow(nums, k);
    for (int x : ans) cout << x << " ";
    cout << endl;
    // expected: 3 3 5 5 6 7
    return 0;
}
```

Each window revealed its strongest peak
without rescanning the land.

---

## 🧠 Memory of the Moving Crown Law

-   Use monotonic decreasing deque
-   Store indices
-   Remove out-of-window elements
-   Remove smaller elements from back
-   Front always holds maximum
-   **Time:** O(n)
-   **Space:** O(k)

Thus is remembered the saga of
**Sliding Window Maximum**,
where the Oracle walks the ridge only once —
discarding weaker peaks,
retiring outdated rulers,
and at every step
revealing the supreme height
of the moving horizon. 🌪️✨
