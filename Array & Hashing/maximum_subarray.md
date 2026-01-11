## 🔥📜 _The Burning Path of Strength: The Maximum Subarray Saga_

> \*"In the Valley of Numbers,
> a path of integers stretched endlessly —
> some stones burned with strength,
> others drained the soul with loss.
>
> The Oracle was summoned with a fierce decree:
>
> **‘Find the contiguous path
> whose total strength is the greatest of all.’**
>
> The path could begin anywhere.
> It could end anywhere.
>
> But it had to be continuous —
> a single, unbroken march.
>
> To solve this, the Oracle invoked
> the legendary **Kadane’s Law**,
> where weakness is discarded
> the moment it threatens the future."\*

---

This is the saga of **Maximum Subarray**.

You are given an integer array `nums`.
Your task:

-   Find the **maximum possible sum**
-   of any **contiguous subarray**

Numbers may be positive, negative, or zero.

---

## 🧠 The Oracle’s Core Insight — Drop the Burden

The Oracle understood a crucial truth:

> If the sum carried so far becomes **negative**,
> it will only weaken any future path.

So at each step, she made a choice:

-   **Continue** the current path
-   or **abandon** it and start fresh here

The stronger option always won.

---

## ⚔️ The Oracle’s Kadane Ritual

_Carry strength, drop weakness_

```cpp
int maxSubArray(vector<int>& nums) {
    int curr = nums[0];
    int best = nums[0];
```

The Oracle began with the first stone.

---

### 🧭 Walk the Burning Path

```cpp
    for (int i = 1; i < nums.size(); i++) {
        curr = max(nums[i], curr + nums[i]);
        best = max(best, curr);
    }
```

At each step:

-   Either start anew at `nums[i]`
-   Or extend the current path

Whichever gave greater strength was chosen.

The Oracle recorded the greatest strength ever achieved.

---

### 🏁 Return the Mightiest Path

```cpp
    return best;
}
```

The strongest contiguous path was revealed.

---

### 🎺 The Trial of the Burning Valley

```cpp
int main() {
    vector<int> nums = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << maxSubArray(nums) << endl; // expected: 6
    return 0;
}
```

The Oracle found the path:

`{4, -1, 2, 1}` → sum = **6**

No other contiguous march burned brighter.

---

### 🧠 Memory of the Burning Law

-   Use Kadane’s Algorithm
-   At each step:
    `curr = max(nums[i], curr + nums[i])`
-   Track global maximum
-   Drop negative prefixes immediately
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Maximum Subarray**,
where the Oracle walks a valley of gains and losses,
casting aside weakening burdens —
and uncovering the single contiguous path
that burns brighter than all others. 🔥✨
