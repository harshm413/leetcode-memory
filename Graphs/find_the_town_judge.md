## 👨‍⚖️🏘️ _The One Who Is Trusted by All: The Find the Town Judge Saga_

> \_"In a small town of `n` people,
> trust flowed like arrows between citizens --
> person `a` trusts person `b` was a directed edge
> from `a` to `b`.
>
> Among them, a **Town Judge** might exist.
>
> The Town Judge had two defining properties:
>
> **One -- The Judge trusts NOBODY.**
> Not a single outgoing arrow.
>
> **Two -- EVERYBODY else trusts the Judge.**
> Exactly `n - 1` incoming arrows.
>
> The Oracle was commanded:
>
> **'Find the Town Judge, or declare that none exists.'**
>
> The Oracle saw this as a graph problem --
> a directed graph of trust relationships.
>
> The Judge was the node with:
> -   **In-degree = n - 1** (everyone trusts them)
> -   **Out-degree = 0** (they trust no one)
>
> She could track both with a single array:
>
> **For each person, compute `in-degree - out-degree`.
> The Judge is the person whose score is exactly `n - 1`.**
>
> Why? Because `in = n-1` and `out = 0` → score = `n-1`.
> No other person can achieve this score.
>
> One pass through the trust edges.
> One pass through the scores.
> The Judge revealed."\_

---

This is the saga of **Find the Town Judge**.

In a town of `n` people labeled `1` to `n`:

-   `trust[i] = [a, b]` means person `a` trusts person `b`.
-   The Town Judge trusts nobody and is trusted by everyone else.
-   Return the Judge's label, or `-1` if no Judge exists.

There is at most one Town Judge.

```
Input:  n = 2, trust = [[1,2]]
Output: 2

Input:  n = 3, trust = [[1,3],[2,3]]
Output: 3

Input:  n = 3, trust = [[1,3],[2,3],[3,1]]
Output: -1

Input:  n = 1, trust = []
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Trust Score = In-Degree Minus Out-Degree

In graph terms:

-   Each trust relationship `[a, b]` is a **directed edge** from `a` to `b`.
-   **In-degree** of a person = how many people trust them.
-   **Out-degree** of a person = how many people they trust.

The Judge has:
-   In-degree = `n - 1` (everyone else trusts them).
-   Out-degree = `0` (they trust nobody).

Instead of tracking two separate arrays,
the Oracle used a single **trust score**:

```
score[person] = in-degree - out-degree
```

For the Judge: `score = (n-1) - 0 = n-1`.

**Can anyone else have score `n-1`?**

No. The maximum in-degree is `n-1` (everyone trusts you).
But if you trust even one person, your out-degree is at least 1,
making your score at most `n-2`.

Only the Judge can reach `n-1`.

```
Time:  O(E + N) -- E = trust edges, N = people
Space: O(N) -- the score array
```

---

### 📜 The Scroll of the Trusted Town

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle's Judge-Finding Ritual

```cpp
int findJudge(int n, vector<vector<int>>& trust) {
    vector<int> score(n + 1, 0);
```

The Oracle prepared a score array for persons `1` to `n`.
All scores started at 0.

`n + 1` size because people are labeled 1-indexed.

---

## 🔁 Process Every Trust Edge

```cpp
    for (auto& t : trust) {
        int a = t[0];
        int b = t[1];
        score[a]--;
        score[b]++;
    }
```

For each trust relationship `[a, b]`:

**`score[a]--`** -- person `a` trusts someone.
Their out-degree increased, so their score decreased.

**`score[b]++`** -- person `b` is trusted by someone.
Their in-degree increased, so their score increased.

> _"Every arrow of trust
> costs the giver one point
> and grants the receiver one point.
> The Judge -- who gives nothing and receives everything --
> accumulates the highest score."_

---

## 🔍 Find the Person with Score n-1

```cpp
    for (int i = 1; i <= n; i++) {
        if (score[i] == n - 1) {
            return i;
        }
    }
    return -1;
}
```

The Oracle walked through every person.

The one with score exactly `n - 1` was the Judge.

If no one had that score -- no Judge existed. Return `-1`.

> _"Only the Judge can score n-1.
> Everyone else either trusts someone (losing points)
> or isn't trusted by everyone (missing points)."_

---

### 🎺 The Trial of the Trusted Town

```cpp
int main() {
    vector<vector<int>> t1 = {{1,2}};
    cout << findJudge(2, t1) << endl; // expected: 2

    vector<vector<int>> t2 = {{1,3},{2,3}};
    cout << findJudge(3, t2) << endl; // expected: 3

    vector<vector<int>> t3 = {{1,3},{2,3},{3,1}};
    cout << findJudge(3, t3) << endl; // expected: -1

    vector<vector<int>> t4 = {};
    cout << findJudge(1, t4) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for n = 3, trust = [[1,3],[2,3]]:**

**Processing edges:**

| Edge  | score[1] | score[2] | score[3] |
| ----- | -------- | -------- | -------- |
| Start | 0        | 0        | 0        |
| [1,3] | -1       | 0        | +1       |
| [2,3] | -1       | -1       | +2       |

**Final scores:** `score = [_, -1, -1, 2]`

`n - 1 = 2`. Person 3 has score 2. **Judge = 3** ✓

Person 3 is trusted by both 1 and 2 (in-degree 2)
and trusts nobody (out-degree 0). Score = 2 - 0 = 2.

---

**Full trace for n = 3, trust = [[1,3],[2,3],[3,1]]:**

| Edge  | score[1] | score[2] | score[3] |
| ----- | -------- | -------- | -------- |
| Start | 0        | 0        | 0        |
| [1,3] | -1       | 0        | +1       |
| [2,3] | -1       | -1       | +2       |
| [3,1] | 0        | -1       | +1       |

**Final scores:** `score = [_, 0, -1, 1]`

No one has score 2. **Judge = -1** ✓

Person 3 was close (trusted by 2 people) but they trust person 1.
Out-degree = 1, so score = 2 - 1 = 1. Not enough.

> _"The moment the Judge trusts anyone,
> they lose their crown.
> A Judge must trust NOBODY."_

---

**Full trace for n = 2, trust = [[1,2]]:**

| Edge  | score[1] | score[2] |
| ----- | -------- | -------- |
| [1,2] | -1       | +1       |

`n - 1 = 1`. Person 2 has score 1. **Judge = 2** ✓

---

**Trace for n = 1, trust = []:**

No edges. All scores remain 0.
`n - 1 = 0`. Person 1 has score 0. **Judge = 1** ✓

A single person in town trusts nobody (vacuously true)
and is trusted by everyone else (vacuously true -- there's nobody else).

---

**Trace for n = 4, trust = [[1,3],[1,4],[2,3],[2,4],[4,3]]:**

| Edge  | score[1] | score[2] | score[3] | score[4] |
| ----- | -------- | -------- | -------- | -------- |
| Start | 0        | 0        | 0        | 0        |
| [1,3] | -1       | 0        | +1       | 0        |
| [1,4] | -2       | 0        | +1       | +1       |
| [2,3] | -2       | -1       | +2       | +1       |
| [2,4] | -2       | -2       | +2       | +2       |
| [4,3] | -2       | -2       | +3       | +1       |

`n - 1 = 3`. Person 3 has score 3. **Judge = 3** ✓

Person 3 is trusted by persons 1, 2, and 4 (in-degree 3).
Person 3 trusts nobody (out-degree 0). Score = 3.

---

## 🔍 Why One Array Instead of Two?

You could track `inDegree[]` and `outDegree[]` separately:

```cpp
for (auto& t : trust) {
    outDegree[t[0]]++;
    inDegree[t[1]]++;
}
for (int i = 1; i <= n; i++) {
    if (inDegree[i] == n - 1 && outDegree[i] == 0) return i;
}
```

This works but uses two arrays.

The single `score = in - out` array is equivalent
because the Judge is the ONLY person where `in - out = n - 1`.
One array, same result, slightly cleaner.

---

## 🌐 The Graph Perspective

This problem is about **in-degree and out-degree** in a directed graph.

| Concept        | In this problem                    |
| -------------- | ---------------------------------- |
| Nodes          | People (1 to n)                    |
| Directed edges | Trust relationships [a → b]        |
| In-degree      | Number of people who trust you     |
| Out-degree     | Number of people you trust         |
| Town Judge     | In-degree = n-1, Out-degree = 0    |

This is one of the simplest directed graph problems --
no traversal needed, just degree counting.

---

### 🧠 Memory of the Town Judge Law

-   **Trust score:** `score[person] = in-degree - out-degree`
-   **For each edge [a,b]:** `score[a]--`, `score[b]++`
-   **Judge:** the person with `score == n - 1`
-   Only the Judge can reach `n-1` (max in-degree, zero out-degree)
-   **No traversal needed** -- just count degrees
-   **Time:** O(E + N) -- process edges + scan scores
-   **Space:** O(N) -- one score array
-   **Edge cases:**
    -   n = 1, no edges → person 1 is the Judge
    -   Judge trusts someone → no Judge exists
    -   Not everyone trusts the same person → no Judge
    -   Multiple candidates → impossible (at most one Judge)

Thus is remembered the saga of **Find the Town Judge**,
where the Oracle did not traverse the graph
but simply counted the trust flowing in and out --
decrementing for every arrow given,
incrementing for every arrow received --
until the one person who gave nothing
and received everything
stood revealed with a score of exactly n - 1. 👨‍⚖️🏘️✨
