## 👑📜 _The Watchers of the Right Horizon: The Leaders in an Array Saga_

> \*"In the Line of Warriors,
> each fighter stood facing the horizon to the **right**.
>
> Some were mighty but overshadowed,
> their strength hidden by greater warriors beyond them.
>
> Others stood unchallenged —
> no one to their right was stronger.
>
> The Oracle was commanded:
>
> **‘Find all the true leaders —
> those who are greater than everyone
> standing to their right.’**
>
> The Oracle knew that looking right
> again and again would be wasteful.
>
> Instead, she chose to walk **from the end**,
> where the horizon is always clear."\*

---

This is the saga of **Leaders in an Array**.

You are given an array `nums`.
An element is called a **leader** if:

```
it is greater than all elements to its right
```

The **rightmost element** is always a leader —
for no one stands beyond it.

---

## 🧠 The Oracle’s Core Insight — The Rightmost Has No Rival

The Oracle realized:

-   From the **right**, the strongest seen so far is always a leader
-   As you move left:

    -   If a warrior is **greater than the max so far**,
        they become a new leader
    -   Otherwise, they are overshadowed

Thus, a single backward march was enough.

---

### 📜 The Scroll of the Silent Horizon

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Reverse March Ritual

_Walk from the horizon inward_

```cpp
vector<int> leaders(vector<int>& nums) {
    int n = nums.size();
    vector<int> result;
```

The Oracle prepared a scroll
to record the true leaders.

---

### 🌅 Begin from the Rightmost Warrior

```cpp
    int maxRight = nums[n - 1];
    result.push_back(maxRight);
```

The rightmost warrior stood alone —
a leader by destiny.

---

### 🧭 March Leftward and Judge Strength

```cpp
    for (int i = n - 2; i >= 0; i--) {
        if (nums[i] > maxRight) {
            maxRight = nums[i];
            result.push_back(nums[i]);
        }
    }
```

Each warrior was compared
against the strongest seen to their right.

Only those who surpassed the horizon’s champion
were recorded as leaders.

---

### 🔄 Restore Original Order (Optional)

```cpp
    reverse(result.begin(), result.end());
    return result;
}
```

Since leaders were discovered from right to left,
the Oracle reversed the list
to present them in original order.

---

### 🎺 The Trial of the Watchful Warriors

```cpp
int main() {
    vector<int> nums = {16, 17, 4, 3, 5, 2};
    vector<int> ans = leaders(nums);

    for (int x : ans) cout << x << " ";
    cout << endl; // expected: 17 5 2
    return 0;
}
```

The Oracle declared the leaders:

-   `17` → none stronger to its right
-   `5` → stronger than `2`
-   `2` → rightmost, uncontested

Others were overshadowed.

---

### 🧠 Memory of the Horizon Law

-   Rightmost element is always a leader
-   Traverse array from right to left
-   Maintain maximum seen so far
-   Add element if it exceeds `maxRight`
-   Reverse result to preserve order
-   **Time:** O(n)
-   **Space:** O(n) (for output)

Thus is remembered the saga of **Leaders in an Array**,
where the Oracle ignores the noise of the crowd,
looks only toward the horizon,
and reveals the true watchers —
those who stand unmatched
against everything that follows them. 👑✨
