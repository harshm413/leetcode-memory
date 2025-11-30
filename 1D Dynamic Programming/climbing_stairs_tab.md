## 🧗 _The Steps of Destiny: The Climbing Stairs Saga_

> \*"Before the Oracle rose a sacred staircase —
> n steps tall, carved by forgotten gods.
>
> Each ascent could be taken in strides of **one** or **two**,
> and the question whispered through the mountain winds:
>
> **‘In how many distinct ways can a traveler reach the summit?’**
>
> The Oracle discovered that the staircase obeyed
> the Law of Paired Paths —
> each step’s count is born from the two steps before it.
> Thus the number of ways rises like a Fibonacci flame."\*

---

A traveler stands at the foot of a staircase of `n` steps.
At every point, they may:

-   climb **1** step
-   or climb **2** steps

Each choice branches the future.
The total number of unique paths to the summit
forms a pattern of destiny:

> `ways(n) = ways(n-1) + ways(n-2)`

The Oracle used **bottom-up dynamic programming**,
filling the path-count in **ascending order**,
so the final answer rests at the **last position** of the array —
exactly where the summit lies.

Thus began the saga of **Climbing Stairs**.

---

### 📜 The Scroll of Ascending Logic

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The staircase was prepared for calculation.

---

## 🧗 The Oracle’s Bottom-Up Ascent

_From ground to summit, computing ways as she climbs_

```cpp
int climbStairs(int n) {
    if (n <= 2) return n;
```

If the staircase held only 1 or 2 steps,
the answer was simply the count of steps themselves —
for the traveler had that many distinct ways.

---

### 🪜 Step 1 — Initialize the First Two Ways

```cpp
    vector<int> dp(n + 1);
    dp[1] = 1;
    dp[2] = 2;
```

The Oracle planted known truths:

-   `dp[1] = 1` way
-   `dp[2] = 2` ways

These were the seeds from which all further paths would grow.

---

### 🧭 Step 2 — Build the Ways Toward Heaven

```cpp
    for (int i = 3; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
```

From the 3rd step onward,
each total was born from:

-   all ways to reach the step before it
-   plus all ways to reach two steps before it

The staircase revealed its Fibonacci nature.

---

### 🌄 Step 3 — The Summit

```cpp
    return dp[n];
}
```

The summit’s solution sat at `dp[n]`,
the final step of the Oracle’s calculation.

---

### 🎺 The Trial of the Sacred Staircase

```cpp
int main() {
    cout << climbStairs(2) << endl; // 2
    cout << climbStairs(3) << endl; // 3
    cout << climbStairs(5) << endl; // 8
    return 0;
}
```

For:

-   2 steps → **2** ways
-   3 steps → **3** ways
-   5 steps → **8** ways

The Oracle confirmed the pattern —
every ascent rose from the union of two smaller paths.

---

### 🧠 Memory of the Stairway Law

-   `ways(n) = ways(n-1) + ways(n-2)`
-   Bottom-up DP fills from step 1 to step n
-   Final answer rests at `dp[n]`
-   **Time:** O(n)
-   **Space:** O(n) (can be O(1) with two variables)

Thus is remembered the saga of **Climbing Stairs**,
where the Oracle climbs step by calculated step,
discovering that every path to the summit
is woven from the echoes of two paths below. 🧗✨
