## 🥷🧠 _The Warrior's Schedule (Memoization): The Ninja's Training Saga_

> \_"A ninja trained for `n` days.
> Each day, three activities were available:
> -   Activity 0 (running) with merit `points[day][0]`.
> -   Activity 1 (fighting) with merit `points[day][1]`.
> -   Activity 2 (learning) with merit `points[day][2]`.
>
> But there was a rule:
>
> **The ninja cannot perform the same activity
> on two consecutive days.**
>
> If the ninja ran on day 2,
> he could NOT run on day 3.
> He must choose fighting or learning on day 3.
>
> The Oracle was commanded:
>
> **'What is the maximum total merit
> the ninja can earn across all n days?'**
>
> The Oracle saw this as a DP problem
> with TWO dimensions of state:
>
> **Which day are we on?**
> **What activity did we do LAST?**
>
> The last activity determines which activities
> are available today.
>
> `solve(day, lastActivity)` =
> maximum merit from day `day` to the last day,
> given that `lastActivity` was performed on the previous day.
>
> For each day, try all activities EXCEPT the last one.
> Take the maximum.
>
> This was the **pick with exclusion** pattern --
> like House Robber, but with 3 choices instead of 2,
> and the constraint is 'not the same as last' instead of 'not adjacent'."\_

---

This is the saga of **Ninja's Training (Memoization)**.

Given a 2D array `points[n][3]`:
-   `points[day][activity]` = merit points for that activity on that day.
-   The ninja cannot do the same activity on two consecutive days.
-   Find the **maximum total merit** across all `n` days.

```
Input:
  points = [[1,2,5],
            [3,1,1],
            [3,3,3]]
Output: 11  (day 0: activity 2 (5), day 1: activity 0 (3), day 2: activity 2 (3) → 5+3+3=11)

Input:
  points = [[10,40,70],
            [20,50,80],
            [30,60,90]]
Output: 210  (day 0: act 2 (70), day 1: act 1 (50), day 2: act 2 (90) → 70+50+90=210)
```

---

## 🧠 The Oracle's Core Insight -- State = (Day, Last Activity)

The state needs TWO pieces of information:

1. **Which day** are we deciding for?
2. **What was the last activity?** (determines what's available today)

```
solve(day, last) = max merit from day `day` to the end,
                   given that `last` was done on day-1.
```

**Transition:**

```
For each activity `act` from 0 to 2:
  If act != last:
    option = points[day][act] + solve(day + 1, act)
solve(day, last) = max of all valid options
```

**Base case:**

```
If day == n: no more days. Return 0.
```

**Initial call:**

```
solve(0, 3)
```

`last = 3` means "no previous activity" (day 0 has no restriction).
Using 3 as a sentinel -- it's not 0, 1, or 2, so all activities are allowed.

```
Time:  O(N × 4 × 3) = O(N) -- N days, 4 possible last values, 3 choices each
Space: O(N × 4) -- memo table + recursion stack
```

---

### 📜 The Scroll of the Warrior's Schedule

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🥷 The Memoization Ritual

### The Cache

```cpp
int ninjasTraining(vector<vector<int>>& points) {
    int n = points.size();
    vector<vector<int>> memo(n, vector<int>(4, -1));
```

`memo[day][last]` = maximum merit from `day` to end,
given `last` was the previous activity.

`last` ranges from 0 to 3:
-   0, 1, 2 = the three activities.
-   3 = no previous activity (first day).

`-1` = not yet computed.

---

### Launch the Recursion

```cpp
    return solve(0, 3, points, memo);
}
```

Start at day 0 with no previous activity (`last = 3`).

---

## 🔮 The Recursive Function

```cpp
int solve(int day, int last, vector<vector<int>>& points,
          vector<vector<int>>& memo) {
```

"What is the maximum merit from day `day` to the end,
given that activity `last` was done yesterday?"

---

### 🛑 Base Case -- No More Days

```cpp
    if (day == points.size()) return 0;
```

Past the last day. No more merit to earn.

---

### 📖 Check the Cache

```cpp
    if (memo[day][last] != -1) return memo[day][last];
```

Already solved this (day, last) pair? Return it.

---

### 🔁 Try Every Activity Except the Last One

```cpp
    int best = 0;
```

`best` will hold the maximum merit across all valid choices.

---

```cpp
    for (int act = 0; act < 3; act++) {
```

Try each of the 3 activities.

---

### 🚫 Skip If Same as Yesterday

```cpp
        if (act == last) continue;
```

Cannot repeat yesterday's activity.
If `last = 3` (no previous), this condition is never true --
all 3 activities are available on day 0.

> _"The ninja cannot train the same way two days in a row.
> Variety is the path to mastery."_

---

### 🥷 Compute the Merit of This Choice

```cpp
        int merit = points[day][act] + solve(day + 1, act, points, memo);
```

Today's merit for activity `act`
PLUS the best merit from tomorrow onward
(with `act` as the new "last activity").

---

### ⚖️ Track the Best

```cpp
        best = max(best, merit);
    }
```

If this choice gives more merit than the current best → update.

---

### 📝 Cache and Return

```cpp
    memo[day][last] = best;
    return best;
}
```

The maximum merit for this (day, last) state was cached.

---

### 🎺 The Trial of the Warrior's Schedule

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

**Full recursion trace for points = [[1,2,5],[3,1,1],[3,3,3]]:**

```
solve(0, 3):  ← day 0, no restriction
  act=0: 1 + solve(1, 0)
    solve(1, 0):  ← day 1, can't do act 0
      act=1: 1 + solve(2, 1)
        solve(2, 1): ← day 2, can't do act 1
          act=0: 3 + solve(3, 0) = 3+0 = 3
          act=2: 3 + solve(3, 2) = 3+0 = 3
        memo[2][1] = max(3, 3) = 3
      act=1: 1 + 3 = 4
      act=2: 1 + solve(2, 2)
        solve(2, 2): ← day 2, can't do act 2
          act=0: 3 + 0 = 3
          act=1: 3 + 0 = 3
        memo[2][2] = 3
      act=2: 1 + 3 = 4
    memo[1][0] = max(4, 4) = 4
  act=0: 1 + 4 = 5

  act=1: 2 + solve(1, 1)
    solve(1, 1): ← day 1, can't do act 1
      act=0: 3 + solve(2, 0)
        solve(2, 0): ← day 2, can't do act 0
          act=1: 3 + 0 = 3
          act=2: 3 + 0 = 3
        memo[2][0] = 3
      act=0: 3 + 3 = 6
      act=2: 1 + memo[2][2] = 1 + 3 = 4
    memo[1][1] = max(6, 4) = 6
  act=1: 2 + 6 = 8

  act=2: 5 + solve(1, 2)
    solve(1, 2): ← day 1, can't do act 2
      act=0: 3 + memo[2][0] = 3 + 3 = 6
      act=1: 1 + memo[2][1] = 1 + 3 = 4
    memo[1][2] = max(6, 4) = 6
  act=2: 5 + 6 = 11

memo[0][3] = max(5, 8, 11) = 11
```

**Answer: 11** ✓

Optimal: day 0 → act 2 (5), day 1 → act 0 (3), day 2 → act 0 or 1 or 2 (3).
Total: 5 + 3 + 3 = 11.

---

**Trace for points = [[10,40,70],[20,50,80],[30,60,90]]:**

```
Day 0, act 2: 70 + solve(1, 2)
  Day 1, act 1: 50 + solve(2, 1)
    Day 2, act 2: 90 + 0 = 90
  50 + 90 = 140
70 + 140 = 210
```

**Answer: 210** ✓ (70 + 50 + 90)

---

## 🔍 Why the State Needs "Last Activity"

Without tracking the last activity,
we can't enforce the "no consecutive same activity" rule.

If we only tracked `day`, we wouldn't know
which activities are forbidden today.

The `last` parameter carries the constraint forward.

---

## 🔍 Why `last = 3` for the First Day?

On day 0, there's no previous day.
All 3 activities are available.

Using `3` as a sentinel means `act != last` is always true
(since `act` is 0, 1, or 2 -- never 3).
All activities pass the filter.

---

## 🔍 How This Relates to House Robber

| House Robber                      | Ninja's Training                  |
| --------------------------------- | --------------------------------- |
| 1D array of values                | 2D array (days × activities)      |
| 2 choices: rob or skip            | 3 choices: activity 0, 1, or 2    |
| Constraint: no adjacent           | Constraint: no same as yesterday  |
| State: `(index)`                  | State: `(day, lastActivity)`      |
| `max(pick, skip)`                 | `max over valid activities`       |

Same "pick with constraint" pattern. More choices, 2D state.

---

### 🧠 Memory of the Warrior's Schedule (Memoization) Law

-   **State:** `(day, lastActivity)` -- which day, what was done yesterday
-   **Transition:** try all 3 activities except `last`. Take the max.
-   **Base case:** `day == n` → return 0
-   **Initial call:** `solve(0, 3)` -- day 0, no restriction (sentinel 3)
-   **Memo table:** `memo[n][4]` -- n days × 4 possible last values (0,1,2,3)
-   **Time:** O(N × 4 × 3) = O(N)
-   **Space:** O(N × 4)
-   **Edge cases:**
    -   Single day → max of the 3 activities
    -   All activities equal → any valid sequence, same total

Thus is remembered the saga of **Ninja's Training (Memoization)**,
where the Oracle tracked not just the day
but also the last activity performed --
trying every valid choice at each day,
skipping only the one done yesterday --
and caching every (day, last) pair
so the warrior's optimal schedule
was found without a single recomputation. 🥷🧠✨
