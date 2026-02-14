## ⚖️🔥 _The Dominion of Extremes: The Sum of Subarray Ranges Saga_

> \*"Across the Realm of Numbers lay countless subarrays —
> and within each realm,
> two rulers emerged:
>
> the Highest Peak
> and the Deepest Valley.
>
> The Oracle was commanded:
>
> \*\*‘For every subarray,
> take (maximum − minimum).
>
> Now sum them all.’\*\*
>
> The foolish would list every realm.
> The wise would understand this truth:
>
> Each element does not merely appear —
> it rules as a maximum
> and also as a minimum
> across specific territories.
>
> Count those dominions separately.
> Subtract valley tribute from peak tribute.
> The answer shall emerge."\*

---

This is the epic saga of **Sum of Subarray Ranges**.

You are given an array `nums`.

Your task:

-   For every subarray:

    ```
    range = max(subarray) - min(subarray)
    ```

-   Return the total sum of all ranges.

---

## 🧠 The Oracle’s Core Insight — Separate the Extremes

Instead of computing every subarray:

```
Total = (Sum of all subarray maximums)
      - (Sum of all subarray minimums)
```

So we calculate:

-   Contribution of each element as **maximum**
-   Contribution of each element as **minimum**

Using **monotonic stacks**.

---

# 🔥 Part I — Contribution as Minimum

Same idea as previous saga.

For each `nums[i]`:

-   `leftMin[i]` → distance to previous smaller (strict)
-   `rightMin[i]` → distance to next smaller or equal

Contribution:

```
nums[i] * leftMin[i] * rightMin[i]
```

---

# 🏔️ Part II — Contribution as Maximum

Now reverse the logic.

For each `nums[i]`:

-   `leftMax[i]` → distance to previous greater (strict)
-   `rightMax[i]` → distance to next greater or equal

Contribution:

```
nums[i] * leftMax[i] * rightMax[i]
```

---

## ⚔️ The Oracle’s Double-Stack Ritual

```cpp
long long subArrayRanges(vector<int>& nums) {
    int n = nums.size();
    long long minSum = 0, maxSum = 0;
    stack<int> st;
```

---

### 🌄 Calculate Minimum Contribution

```cpp
    vector<long long> left(n), right(n);

    // Previous Smaller (strict)
    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] > nums[i])
            st.pop();
        left[i] = st.empty() ? i + 1 : i - st.top();
        st.push(i);
    }
```

Clear stack:

```cpp
    while (!st.empty()) st.pop();
```

Next Smaller (or equal):

```cpp
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && nums[st.top()] >= nums[i])
            st.pop();
        right[i] = st.empty() ? n - i : st.top() - i;
        st.push(i);
    }

    for (int i = 0; i < n; i++)
        minSum += nums[i] * left[i] * right[i];
```

---

### 🏔️ Calculate Maximum Contribution

Clear stack again:

```cpp
    while (!st.empty()) st.pop();
```

Previous Greater (strict):

```cpp
    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] < nums[i])
            st.pop();
        left[i] = st.empty() ? i + 1 : i - st.top();
        st.push(i);
    }
```

Clear stack:

```cpp
    while (!st.empty()) st.pop();
```

Next Greater (or equal):

```cpp
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && nums[st.top()] <= nums[i])
            st.pop();
        right[i] = st.empty() ? n - i : st.top() - i;
        st.push(i);
    }

    for (int i = 0; i < n; i++)
        maxSum += nums[i] * left[i] * right[i];
```

---

### ⚖️ Final Tribute

```cpp
    return maxSum - minSum;
}
```

---

### 🎺 The Trial of the Extremes

```cpp
int main() {
    vector<int> nums = {1,2,3};
    cout << subArrayRanges(nums) << endl;
    // expected: 4
    return 0;
}
```

Subarrays:

-   [1] → 0
-   [2] → 0
-   [3] → 0
-   [1,2] → 1
-   [2,3] → 1
-   [1,2,3] → 2

Total = **4**

---

## 🧠 Memory of the Dominion Law

-   Total = sum(max contributions) − sum(min contributions)
-   Use monotonic stacks for boundaries
-   Strict vs non-strict comparisons prevent double counting
-   Each element contributes as both max and min
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Sum of Subarray Ranges**,
where the Oracle measures every realm
not by enumerating them,
but by counting the dominion of peaks
and the tribute of valleys —
subtracting depth from height
to reveal the total power
of all subarray kingdoms. ⚖️✨
