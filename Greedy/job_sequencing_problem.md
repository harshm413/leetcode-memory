## 💼💰 _The Deadline Hustle: The Job Sequencing Problem Saga_

> \_"In the Kingdom of Deadlines,
> `n` jobs awaited completion.
> Each job had a **deadline** and a **profit**.
> Each job took exactly **1 unit of time**.
> Only ONE job could be done per time slot.
>
> A job could be scheduled in ANY time slot
> from 1 to its deadline (inclusive).
> If a job's deadline was 4,
> it could be done at time 1, 2, 3, or 4.
>
> The Oracle was commanded:
>
> **'Maximize the total profit.
> How many jobs can be done, and what's the max profit?'**
>
> The Oracle's greedy strategy:
>
> **Sort jobs by PROFIT (descending).
> For each job (highest profit first),
> find the LATEST available slot before its deadline.
> If a slot is free -- schedule it there.
> If no slot is free -- skip the job.**
>
> Why highest profit first?
> We want to guarantee the most valuable jobs get scheduled.
>
> Why latest available slot?
> Scheduling a job as LATE as possible
> leaves earlier slots free for jobs with tighter deadlines.
> This maximizes flexibility for future jobs."\_

---

This is the saga of **Job Sequencing Problem**.

Given `n` jobs, each with a deadline and profit:

-   Each job takes 1 unit of time.
-   Only one job per time slot.
-   A job must be done on or before its deadline.
-   Maximize total profit (and count of jobs done).

```
Input:  jobs = [{id:1, deadline:4, profit:20},
                {id:2, deadline:1, profit:10},
                {id:3, deadline:1, profit:40},
                {id:4, deadline:1, profit:30}]
Output: 2 jobs, profit = 60  (jobs 3 and 1)

Input:  jobs = [{id:1, deadline:2, profit:100},
                {id:2, deadline:1, profit:19},
                {id:3, deadline:2, profit:27},
                {id:4, deadline:1, profit:25},
                {id:5, deadline:1, profit:15}]
Output: 2 jobs, profit = 127  (jobs 1 and 3)
```

---

## 🧠 The Oracle's Core Insight -- Sort by Profit, Schedule Latest

```
1. Sort jobs by profit (descending).
2. Find the maximum deadline (determines the time slots available).
3. Create a slot array of size maxDeadline (all free initially).
4. For each job (highest profit first):
   - Search for the latest free slot from deadline down to 1.
   - If found → schedule it there. Add profit.
   - If not found → skip (all slots before deadline are taken).
```

**Why sort by profit?**
Process the most valuable jobs first.
They get first pick of available slots.

**Why schedule at the latest possible slot?**
If a job has deadline 4, scheduling it at slot 4 (not slot 1)
leaves slots 1, 2, 3 free for jobs with earlier deadlines.
This maximizes the chance of fitting more jobs.

```
Time:  O(n log n + n × maxDeadline) -- sort + slot search per job
Space: O(maxDeadline) -- the slot array
```

---

### 📜 The Scroll of the Deadline Hustle

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 💼 The Job Structure

```cpp
struct Job {
    int id, deadline, profit;
};
```

---

## 💰 The Greedy Ritual

```cpp
pair<int, int> jobSequencing(vector<Job>& jobs, int n) {
```

Returns `{number of jobs done, total profit}`.

---

### 📊 Sort by Profit (Descending)

```cpp
    sort(jobs.begin(), jobs.end(), [](Job& a, Job& b) {
        return a.profit > b.profit;
    });
```

Highest profit jobs get priority.

> _"The most valuable job speaks first.
> It gets the best slot available.
> Lesser jobs take what remains."_

---

### 🗓️ Find Maximum Deadline

```cpp
    int maxDeadline = 0;
    for (auto& job : jobs) {
        maxDeadline = max(maxDeadline, job.deadline);
    }
```

The time slots range from 1 to `maxDeadline`.

---

### 📋 Create the Slot Array

```cpp
    vector<int> slot(maxDeadline + 1, -1);
```

`slot[t] = -1` means time slot `t` is free.
`slot[t] = jobId` means slot `t` is occupied.

Size `maxDeadline + 1` for 1-indexed slots.

---

### 🔁 Schedule Each Job Greedily

```cpp
    int countJobs = 0;
    int totalProfit = 0;

    for (auto& job : jobs) {
```

Process jobs in order of decreasing profit.

---

### 🔍 Find the Latest Free Slot

```cpp
        for (int t = job.deadline; t >= 1; t--) {
            if (slot[t] == -1) {
                slot[t] = job.id;
                countJobs++;
                totalProfit += job.profit;
                break;
            }
        }
    }
```

For this job, search from its deadline DOWN to slot 1.
Find the LATEST free slot.

If found → schedule it. Mark the slot. Add profit.
If no free slot exists (all slots 1 to deadline are taken) → skip.

> _"Start at the deadline and work backward.
> The latest free slot is the best choice --
> it preserves earlier slots for tighter deadlines.
> If nothing is free -- this job cannot be done."_

---

### 📤 The Answer

```cpp
    return {countJobs, totalProfit};
}
```

---

### 🎺 The Trial of the Deadline Hustle

```cpp
int main() {
    vector<Job> jobs1 = {{1,4,20},{2,1,10},{3,1,40},{4,1,30}};
    auto [count1, profit1] = jobSequencing(jobs1, 4);
    cout << count1 << " jobs, profit = " << profit1 << endl;
    // expected: 2 jobs, profit = 60

    vector<Job> jobs2 = {{1,2,100},{2,1,19},{3,2,27},{4,1,25},{5,1,15}};
    auto [count2, profit2] = jobSequencing(jobs2, 5);
    cout << count2 << " jobs, profit = " << profit2 << endl;
    // expected: 2 jobs, profit = 127

    return 0;
}
```

---

**Full trace for jobs = [{1,4,20},{2,1,10},{3,1,40},{4,1,30}]:**

**Step 1 -- Sort by profit (descending):**

| Job | Deadline | Profit |
|-----|----------|--------|
| 3   | 1        | 40     |
| 4   | 1        | 30     |
| 1   | 4        | 20     |
| 2   | 1        | 10     |

maxDeadline = 4. Slots: [_, free, free, free, free] (indices 1-4).

**Step 2 -- Schedule greedily:**

| Job | Profit | Deadline | Search slots | Found? | Action              | Slots after         |
|-----|--------|----------|--------------|--------|---------------------|---------------------|
| 3   | 40     | 1        | slot[1]=free | ✓      | Schedule at 1. +40. | [_, 3, _, _, _]     |
| 4   | 30     | 1        | slot[1]=taken| ✗      | No free slot. Skip. | [_, 3, _, _, _]     |
| 1   | 20     | 4        | slot[4]=free | ✓      | Schedule at 4. +20. | [_, 3, _, _, 1]     |
| 2   | 10     | 1        | slot[1]=taken| ✗      | No free slot. Skip. | [_, 3, _, _, 1]     |

**Answer: 2 jobs, profit = 40 + 20 = 60** ✓

Job 3 (profit 40) takes slot 1. Job 1 (profit 20) takes slot 4.
Jobs 4 and 2 can't fit (their deadline is 1, and slot 1 is taken).

---

**Full trace for jobs = [{1,2,100},{2,1,19},{3,2,27},{4,1,25},{5,1,15}]:**

**Sorted by profit:** Job 1 (100), Job 3 (27), Job 4 (25), Job 2 (19), Job 5 (15).

maxDeadline = 2. Slots: [_, free, free].

| Job | Profit | Deadline | Search | Action              | Slots after   |
|-----|--------|----------|--------|---------------------|---------------|
| 1   | 100    | 2        | slot[2]=free | Schedule at 2. +100. | [_, _, 1]  |
| 3   | 27     | 2        | slot[2]=taken, slot[1]=free | Schedule at 1. +27. | [_, 3, 1] |
| 4   | 25     | 1        | slot[1]=taken | Skip.           | [_, 3, 1]    |
| 2   | 19     | 1        | slot[1]=taken | Skip.           | [_, 3, 1]    |
| 5   | 15     | 1        | slot[1]=taken | Skip.           | [_, 3, 1]    |

**Answer: 2 jobs, profit = 100 + 27 = 127** ✓

---

## 🔍 Why Schedule at the LATEST Slot

Consider: Job A (deadline 3, profit 50) and Job B (deadline 1, profit 40).

**If A is scheduled at slot 1:**
Slot 1 taken. Job B (deadline 1) has no free slot. B is skipped.
Total = 50.

**If A is scheduled at slot 3 (latest):**
Slot 1 is free. Job B takes slot 1.
Total = 50 + 40 = 90. Better!

Scheduling late preserves early slots for jobs with tight deadlines.

---

## 🔍 Optimization -- Union-Find for Slot Finding

The inner loop (searching for a free slot) is O(maxDeadline) per job.
For large deadlines, this can be optimized using **Union-Find**:

Each slot points to the next free slot.
`find(deadline)` gives the latest free slot ≤ deadline in O(α) time.
After scheduling, union the slot with `slot - 1`.

This reduces total time to O(n log n + n × α) ≈ O(n log n).

For interviews, the simple O(n × maxDeadline) approach is usually sufficient.

---

## 🔍 Connection to Other Greedy Problems

| Problem                    | Sort by    | Greedy action              |
| -------------------------- | ---------- | -------------------------- |
| **Job Sequencing (this)**  | Profit ↓   | Schedule at latest free slot |
| Activity Selection         | End time ↑ | Pick if no overlap         |
| Assign Cookies             | Both ↑     | Smallest sufficient cookie |
| Fractional Knapsack        | Ratio ↓    | Take highest ratio first   |

---

### 🧠 Memory of the Deadline Hustle Law

-   **Sort by profit** (descending) -- most valuable first
-   **For each job:** find the LATEST free slot from deadline down to 1
-   **If free slot found:** schedule it, add profit
-   **If no free slot:** skip (can't fit this job)
-   **Why latest slot?** Preserves earlier slots for tighter deadlines
-   **Slot array:** `slot[t] = -1` means free, else occupied
-   **Time:** O(n log n + n × maxDeadline)
-   **Space:** O(maxDeadline)
-   **Edge cases:**
    -   All deadlines = 1 → only the highest profit job is done
    -   All deadlines = n → all jobs can be done (n slots available)
    -   Single job → always done (profit = its profit)

Thus is remembered the saga of **Job Sequencing**,
where the Oracle sorted jobs by their worth --
richest first, poorest last --
and for each job, searched backward from its deadline
for the latest free time slot --
scheduling late to preserve early slots
for jobs with tighter deadlines --
until every valuable job that could fit
was placed in its optimal slot,
and the maximum profit was secured. 💼💰✨
