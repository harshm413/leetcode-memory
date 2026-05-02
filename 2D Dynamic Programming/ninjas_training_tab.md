## 🥷📋 _The Warrior's Schedule (Tabulation): The Ninja's Training Saga_

> \_"The Oracle had solved the ninja's training with memoization.
> Now she built the answer from the ground up.
>
> **Start at day 0. For each day and each possible last activity,
> compute the maximum merit.
> Fill the table day by day.**
>
> But the Oracle saw a cleaner formulation:
>
> **`dp[day][act]` = maximum merit from day 0 to `day`,
> where `act` is the activity done ON day `day`.**
>
> This was slightly different from the memo version
> (which tracked what was done YESTERDAY).
> But it led to a cleaner bottom-up table."\_

---

This is the saga of **Ninja's Training (Tabulation)**.

Same problem:
-   3 activities per day. Can't repeat yesterday's activity.
-   Maximize total merit.

```
Input:  points = [[1,2,5],[3,1,1],[3,3,3]]  →  Output: 11
Input:  points = [[10,40,70],[20,50,80],[30,60,90]]  →  Output: 210
```

---

## 🧠 The Tabulation Formulation

```
dp[day][act] = maximum merit from day 0 to day `day`,
               ending with activity `act` on day `day`.
```

**Base case (day 0):**

```
dp[0][0] = points[0][0]
dp[0][1] = points[0][1]
dp[0][2] = points[0][2]
```

Day 0: no restriction. Each activity's merit is just its own points.

**Transition (day 1 to n-1):**

```
dp[day][act] = points[day][act] + max(dp[day-1][other])
               for all `other` != `act`
```

Today's merit for activity `act`
PLUS the best merit from yesterday
among activities that are NOT `act`.

**Answer:**

```
max(dp[n-1][0], dp[n-1][1], dp[n-1][2])
```

The best of the three possible endings on the last day.

```
Time:  O(N × 3 × 3) = O(N)
Space: O(N × 3) -- or O(3) with optimization
```

---

### 📜 The Scroll of the Bottom-Up Warrior

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📋 The Tabulation Ritual

### Create the DP Table

```cpp
int ninjasTraining(vector<vector<int>>& points) {
    int n = points.size();
    vector<vector<int>> dp(n, vector<int>(3, 0));
```

`dp[day][act]` = max merit from day 0 to `day`, ending with `act`.

---

### 🏁 Fill Day 0 -- No Restrictions

```cpp
    dp[0][0] = points[0][0];
    dp[0][1] = points[0][1];
    dp[0][2] = points[0][2];
```

Day 0: each activity earns its own points. No previous day to conflict with.

> _"The first day is free.
> The ninja can choose any activity.
> Each choice stands on its own."_

---

### 🔁 Fill Day by Day

```cpp
    for (int day = 1; day < n; day++) {
```

For each day from 1 to n-1.

---

### 🔁 For Each Activity Today

```cpp
        for (int act = 0; act < 3; act++) {
```

Consider doing activity `act` on this day.

---

### 🔍 Find the Best Yesterday (Excluding Today's Activity)

```cpp
            int bestYesterday = 0;
            for (int prev = 0; prev < 3; prev++) {
                if (prev != act) {
                    bestYesterday = max(bestYesterday, dp[day - 1][prev]);
                }
            }
```

Look at all 3 activities from yesterday.
Take the maximum among those that are NOT today's activity.

This enforces the "no consecutive same activity" rule.

> _"If I train in fighting today,
> I look at yesterday's running and learning --
> whichever gave more merit.
> I cannot look at yesterday's fighting."_

---

### 📝 Fill the Cell

```cpp
            dp[day][act] = points[day][act] + bestYesterday;
        }
    }
```

Today's merit = today's points + best valid yesterday.

---

### 📤 Return the Best Ending

```cpp
    return max({dp[n - 1][0], dp[n - 1][1], dp[n - 1][2]});
}
```

The answer is the maximum across all three possible
activities on the last day.

`max({a, b, c})` uses initializer list to find the max of three values.

---

### 🎺 The Trial of the Bottom-Up Warrior

```cpp
int main() {
    vector<vector<int>> p1 = {{1,2,5},{3,1,1},{3,3,3}};
    cout << ninjasTraining(p1) << endl; // expected: 11

    vector<vector<int>> p2 = {{10,40,70},{20,50,80},{30,60,90}};
    cout << ninjasTraining(p2) << endl; // expected: 210

    return 0;
}
```

---

**Full table trace for points = [[1,2,5],[3,1,1],[3,3,3]]:**

**Day 0 (base):**

| act | dp[0][act] |
|-----|-----------|
| 0   | 1         |
| 1   | 2         |
| 2   | 5         |

**Day 1:**

| act | Exclude act | bestYesterday          | dp[1][act] = points + best |
|-----|-------------|------------------------|---------------------------|
| 0   | ≠0          | max(dp[0][1], dp[0][2]) = max(2,5) = 5 | 3 + 5 = **8** |
| 1   | ≠1          | max(dp[0][0], dp[0][2]) = max(1,5) = 5 | 1 + 5 = **6** |
| 2   | ≠2          | max(dp[0][0], dp[0][1]) = max(1,2) = 2 | 1 + 2 = **3** |

**Day 2:**

| act | Exclude act | bestYesterday          | dp[2][act] = points + best |
|-----|-------------|------------------------|---------------------------|
| 0   | ≠0          | max(dp[1][1], dp[1][2]) = max(6,3) = 6 | 3 + 6 = **9** |
| 1   | ≠1          | max(dp[1][0], dp[1][2]) = max(8,3) = 8 | 3 + 8 = **11** |
| 2   | ≠2          | max(dp[1][0], dp[1][1]) = max(8,6) = 8 | 3 + 8 = **11** |

**Answer: max(9, 11, 11) = 11** ✓

Optimal path: day 0 → act 2 (5), day 1 → act 0 (3), day 2 → act 1 or 2 (3).
Total: 5 + 3 + 3 = 11.

---

**Table trace for points = [[10,40,70],[20,50,80],[30,60,90]]:**

**Day 0:** dp = [10, 40, 70]

**Day 1:**

| act | bestYesterday (exclude act) | dp[1][act]  |
|-----|---------------------------|-------------|
| 0   | max(40, 70) = 70          | 20+70 = 90  |
| 1   | max(10, 70) = 70          | 50+70 = 120 |
| 2   | max(10, 40) = 40          | 80+40 = 120 |

**Day 2:**

| act | bestYesterday (exclude act) | dp[2][act]   |
|-----|---------------------------|--------------|
| 0   | max(120, 120) = 120       | 30+120 = 150 |
| 1   | max(90, 120) = 120        | 60+120 = 180 |
| 2   | max(90, 120) = 120        | 90+120 = **210** |

**Answer: max(150, 180, 210) = 210** ✓

---

## 🔧 Space Optimization -- O(1) Extra Space

Each day only depends on the previous day.
Replace the 2D table with a single row of 3 values.

```cpp
int ninjasTraining(vector<vector<int>>& points) {
    int n = points.size();
    vector<int> prev = {points[0][0], points[0][1], points[0][2]};
```

`prev[act]` = best merit ending with `act` on the previous day.

---

```cpp
    for (int day = 1; day < n; day++) {
        vector<int> curr(3);
        for (int act = 0; act < 3; act++) {
            int bestPrev = 0;
            for (int p = 0; p < 3; p++) {
                if (p != act) bestPrev = max(bestPrev, prev[p]);
            }
            curr[act] = points[day][act] + bestPrev;
        }
        prev = curr;
    }
```

Each day: compute `curr` from `prev`. Then `prev = curr`.

---

```cpp
    return max({prev[0], prev[1], prev[2]});
}
```

```
Time:  O(N × 9) = O(N)
Space: O(1) -- just two arrays of size 3
```

---

## 🔍 Two Formulations Compared

| Memo formulation                  | Tab formulation                   |
| --------------------------------- | --------------------------------- |
| `solve(day, last)` = best from day to end | `dp[day][act]` = best from 0 to day |
| `last` = what was done YESTERDAY  | `act` = what is done TODAY        |
| Top-down (future → past)          | Bottom-up (past → future)         |
| Sentinel `last=3` for day 0      | No sentinel needed (base case fills directly) |

Both are correct. The tab formulation is slightly cleaner
because it avoids the sentinel value.

---

## 🔍 How This Extends

| Problem              | Activities | Constraint                    |
| -------------------- | ---------- | ----------------------------- |
| Ninja's Training     | 3          | Can't repeat yesterday's      |
| Paint House          | 3 colors   | Can't repeat yesterday's color|
| Paint House II       | K colors   | Can't repeat yesterday's color|
| Best Time Buy/Sell   | buy/sell/hold | State machine transitions  |

Same "day × choice" DP pattern. Different number of choices.

---

### 🧠 Memory of the Warrior's Schedule (Tabulation) Law

-   **State:** `dp[day][act]` = max merit from day 0 to `day`, ending with `act`
-   **Base:** `dp[0][act] = points[0][act]` for all 3 activities
-   **Transition:** `dp[day][act] = points[day][act] + max(dp[day-1][other])` for `other != act`
-   **Answer:** `max(dp[n-1][0], dp[n-1][1], dp[n-1][2])`
-   **Space optimization:** only need previous day's row → O(1) extra
-   **Time:** O(N × 9) = O(N)
-   **Space:** O(1) optimized
-   **Edge cases:**
    -   Single day → max of the 3 activities
    -   All activities equal → any valid sequence

Thus is remembered the saga of **Ninja's Training (Tabulation)**,
where the Oracle built the warrior's schedule day by day --
at each day trying all 3 activities,
looking back at yesterday's best among the other two --
filling the table forward until the last day,
then picking the richest ending --
the optimal training plan revealed
through the discipline of bottom-up computation. 🥷📋✨
