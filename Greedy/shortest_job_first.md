## ⏱️📋 _The Quickest First: The Shortest Job First Saga_

> \_"In the Kingdom of Processes,
> `n` jobs waited in a queue to be executed.
> Each job had a **burst time** --
> the time it takes to complete.
>
> The CPU could only run ONE job at a time.
> All jobs arrived at time 0 (non-preemptive SJF).
>
> The Oracle was commanded:
>
> **'Schedule the jobs to minimize the AVERAGE WAITING TIME.'**
>
> The Oracle knew the answer instantly:
>
> **Sort jobs by burst time (ascending).
> Execute the shortest job first.**
>
> Why? Because a short job finishing early
> reduces the waiting time for ALL jobs behind it.
> A long job going first makes everyone else wait longer.
>
> This is the **Shortest Job First (SJF)** scheduling algorithm --
> provably optimal for minimizing average waiting time
> when all jobs arrive at the same time."\_

---

This is the saga of **Shortest Job First (SJF)**.

Given `n` processes with burst times:

-   All arrive at time 0.
-   Non-preemptive (once started, a job runs to completion).
-   Return the **average waiting time**.

Waiting time of a job = time it starts executing - arrival time (0).

```
Input:  burst = [6, 8, 7, 3]
Output: 7.0   (average waiting time)

Input:  burst = [1, 2, 3, 4]
Output: 2.5
```

---

## 🧠 The Oracle's Core Insight -- Sort by Burst Time

**Waiting time** of job `i` = sum of burst times of all jobs executed BEFORE it.

To minimize AVERAGE waiting time:
execute shorter jobs first → they add less to everyone else's wait.

```
Sort burst times ascending.
waiting[0] = 0  (first job starts immediately)
waiting[i] = waiting[i-1] + burst[i-1]  (waits for all previous jobs)

Average = sum(waiting) / n
```

**Why is this optimal?**

If a long job (burst 10) goes before a short job (burst 2):
-   Short job waits 10 units.

If the short job goes first:
-   Long job waits 2 units.

Swapping saves 10 - 2 = 8 units of total waiting time.
Always putting shorter jobs first minimizes total wait.

```
Time:  O(n log n) -- sorting
Space: O(1) extra
```

---

### 📜 The Scroll of the Quickest First

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⏱️ The SJF Ritual

```cpp
double shortestJobFirst(vector<int>& burst) {
    int n = burst.size();
    sort(burst.begin(), burst.end());
```

Sort burst times in ascending order. Shortest first.

---

### 📐 Compute Waiting Times

```cpp
    double totalWait = 0;
    int currentTime = 0;

    for (int i = 0; i < n; i++) {
        totalWait += currentTime;
        currentTime += burst[i];
    }
```

For each job (in sorted order):
-   Its waiting time = `currentTime` (time elapsed so far).
-   After it runs, `currentTime` increases by its burst time.

`totalWait` accumulates all individual waiting times.

> _"Each job waits for all jobs before it to finish.
> The shorter the earlier jobs, the less everyone waits."_

---

### 📤 Average Waiting Time

```cpp
    return totalWait / n;
}
```

---

### 🎺 The Trial of the Quickest First

```cpp
int main() {
    vector<int> b1 = {6, 8, 7, 3};
    cout << shortestJobFirst(b1) << endl; // expected: 7.0

    vector<int> b2 = {1, 2, 3, 4};
    cout << shortestJobFirst(b2) << endl; // expected: 2.5

    return 0;
}
```

---

**Full trace for burst = [6, 8, 7, 3]:**

**After sorting:** [3, 6, 7, 8].

| Job (sorted) | Burst | Waiting time (currentTime before) | currentTime after |
|--------------|-------|-----------------------------------|-------------------|
| Job 1        | 3     | 0                                 | 3                 |
| Job 2        | 6     | 3                                 | 9                 |
| Job 3        | 7     | 9                                 | 16                |
| Job 4        | 8     | 16                                | 24                |

Total waiting = 0 + 3 + 9 + 16 = 28.
Average = 28 / 4 = **7.0** ✓

---

**Trace for burst = [1, 2, 3, 4] (already sorted):**

| Job | Burst | Waiting |
|-----|-------|---------|
| 1   | 1     | 0       |
| 2   | 2     | 1       |
| 3   | 3     | 3       |
| 4   | 4     | 6       |

Total = 0 + 1 + 3 + 6 = 10. Average = 10 / 4 = **2.5** ✓

---

**What if we DON'T sort? (burst = [6, 8, 7, 3] unsorted):**

| Job | Burst | Waiting |
|-----|-------|---------|
| 1   | 6     | 0       |
| 2   | 8     | 6       |
| 3   | 7     | 14      |
| 4   | 3     | 21      |

Total = 0 + 6 + 14 + 21 = 41. Average = 41 / 4 = 10.25.

**Sorted gives 7.0 vs unsorted 10.25.** Sorting is clearly better.

---

## 🔍 Why SJF Is Optimal (Exchange Argument)

Take any schedule. If job `i` (burst `a`) comes before job `j` (burst `b`)
and `a > b`:
-   Job j waits `a` units because of job i.
-   If we swap: job i waits `b` units because of job j.
-   Net change: `b - a < 0` → total waiting DECREASES.

So any schedule with a longer job before a shorter job
can be improved by swapping. The optimal schedule has
jobs in non-decreasing burst order = SJF.

---

## 🔍 Limitations

-   **Non-preemptive:** once a job starts, it runs to completion.
-   **All arrive at time 0:** if jobs arrive at different times, need SRTF (preemptive version).
-   **Starvation:** long jobs may wait forever if short jobs keep arriving (in dynamic systems).

---

### 🧠 Memory of the Quickest First Law

-   **Sort by burst time** (ascending)
-   **Waiting time of job i** = sum of burst times of all jobs before it
-   **Average waiting time** = total waiting / n
-   **Why optimal?** Short jobs finishing early reduce wait for everyone behind
-   **Exchange argument:** swapping a longer job before a shorter one always increases total wait
-   **Time:** O(n log n). **Space:** O(1).
-   **Edge cases:**
    -   Single job → waiting time = 0
    -   All same burst → any order gives same average
    -   Already sorted → already optimal

Thus is remembered the saga of **Shortest Job First**,
where the Oracle sorted processes by their burst time --
shortest first, longest last --
knowing that every short job finishing early
reduced the suffering of all who waited behind --
until the average waiting time was minimized
by the simple act of prioritizing the quick. ⏱️📋✨
