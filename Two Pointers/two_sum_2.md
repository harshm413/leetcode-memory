## 🏹📜 _The Twin Seekers of the Ordered Path: The Two Sum II Saga_

> \*"Along the Road of Sorted Stones,
> numbers lay in calm ascending order,
> each knowing its place,
> none out of line.
>
> The Oracle was given a sacred target — **T** —
> and a strict promise:
>
> **‘Exactly one pair exists
> whose combined strength equals the target.’**
>
> No chaos was needed.
> No memory of the past was required.
>
> The Oracle placed two seekers
> at opposite ends of the road —
> one at the dawn, one at the dusk —
> and let order itself guide their steps."\*

---

This is the saga of
**Two Sum II – Input Array Is Sorted**.

You are given:

-   a **sorted** array `numbers` (1-indexed in result)
-   an integer `target`

Your task:

-   Find **two numbers** such that they add up to `target`
-   Return their **1-based indices**
-   Exactly **one solution exists**

---

## 🧠 The Oracle’s Core Insight — Order Enables Direction

Because the array is **sorted**:

-   If the sum is **too small** → move the left pointer right
-   If the sum is **too large** → move the right pointer left

No backtracking.
No guessing.
Each move is meaningful.

This is the **Two-Pointer Law of Order**.

---

### 📜 The Scroll of Ascending Stones

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Two-Seeker Ritual

_One from the dawn, one from the dusk_

```cpp
vector<int> twoSum(vector<int>& numbers, int target) {
    int left = 0;
    int right = numbers.size() - 1;
```

Two seekers took their positions:

-   `left` at the beginning
-   `right` at the end

---

### 🔍 Walk the Road Until Balance Is Found

```cpp
    while (left < right) {
        int sum = numbers[left] + numbers[right];
```

The combined strength of the two seekers
was measured.

---

### ⚖️ Adjust According to the Target

```cpp
        if (sum == target) {
            return {left + 1, right + 1};
        }
        else if (sum < target) {
            left++;
        }
        else {
            right--;
        }
    }
    return {};
}
```

-   If balance was perfect → destiny found
-   If too weak → left seeker moved forward
-   If too strong → right seeker stepped back

Order guided every move.

---

### 🎺 The Trial of the Ordered Path

```cpp
int main() {
    vector<int> numbers = {2, 7, 11, 15};
    int target = 9;

    vector<int> ans = twoSum(numbers, target);
    cout << ans[0] << " " << ans[1] << endl; // expected: 1 2
    return 0;
}
```

The Oracle revealed:

-   `2 + 7 = 9`
-   Indices → **1, 2**

The seekers met in perfect balance.

---

### 🧠 Memory of the Ordered Path Law

-   Array is already sorted
-   Use two pointers from both ends
-   Move based on sum comparison
-   Exactly one solution guaranteed
-   Return **1-based indices**
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Two Sum II – Input Array Is Sorted**,
where the Oracle trusts order over memory,
direction over guesswork —
and sends two seekers from opposite ends
to meet precisely at the point of destiny. 🏹✨
