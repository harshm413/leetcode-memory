## 🌡️🔥 _The Watchers of Warmer Days: The Daily Temperatures Saga_

> \_"In the Kingdom of Weather,
> each day carried a temperature --
> some warm, some cold,
> all recorded on a long scroll.
>
> The people asked a simple question:
>
> **'For each day, how many days must we wait
> until a warmer day arrives?
> If no warmer day ever comes -- the answer is 0.'**
>
> The naive approach would scan forward
> from every day, searching for the next warmer one.
> O(N²) -- too slow for a kingdom of many seasons.
>
> The Oracle devised a faster ritual:
>
> **Walk the scroll from left to right.
> Maintain a stack of days still waiting for warmth.
> When a warmer day arrives,
> it answers every waiting day it surpasses.**
>
> The stack held indices of days
> whose warmer successor had not yet been found.
> Each new day popped every waiting day
> it was warmer than --
> recording the distance as the answer.
>
> This was the **Monotonic Decreasing Stack** --
> a stack where temperatures never increased from bottom to top.
> Every new warmer day shattered the pattern
> and resolved the waiting."\_

---

This is the saga of **Daily Temperatures**.

Given an array `temperatures` where `temperatures[i]`
is the temperature on day `i`:

-   Return an array `answer` where `answer[i]` is the number of days
    you have to wait after day `i` to get a warmer temperature.
-   If no future day is warmer, `answer[i] = 0`.

```
Input:  [73,74,75,71,69,72,76,73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]

Input:  [30,40,50,60]
Output: [1, 1, 1, 0]

Input:  [30,60,90]
Output: [1, 1, 0]
```

---

## 🧠 The Oracle's Core Insight -- Monotonic Decreasing Stack

The key observation:

> **When a warmer day arrives,
> it is the answer for ALL recent cooler days still waiting.**

The stack stores **indices** of days (not temperatures)
in a monotonically decreasing order of temperature.

When a new day `i` arrives with temperature `T[i]`:

```
While the stack is not empty AND T[i] > T[stack.top()]:
  The day at stack.top() has found its warmer day!
  answer[stack.top()] = i - stack.top()
  Pop it -- its wait is over.

Push i onto the stack -- it now waits for its own warmer day.
```

Days that never find a warmer successor stay on the stack
and their answer remains `0` (the default).

```
Time:  O(N) -- each index is pushed and popped at most once
Space: O(N) -- the stack
```

---

### 📜 The Scroll of the Weather Kingdom

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Warmth-Finding Ritual

```cpp
vector<int> dailyTemperatures(vector<int>& temperatures) {
    int n = temperatures.size();
    vector<int> answer(n, 0);
    stack<int> st;
```

The Oracle prepared:

-   `answer` -- initialized to all `0`s. Days that never find warmth keep `0`.
-   `st` -- a stack of **indices**, not temperatures.
    The stack would maintain a decreasing order of temperatures.

---

## 🔁 Walk the Scroll Day by Day

```cpp
    for (int i = 0; i < n; i++) {
```

Each day was processed from left to right.

---

### 🔥 Resolve All Waiting Days That This Day Warms

```cpp
        while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
            int waitingDay = st.top();
            st.pop();
            answer[waitingDay] = i - waitingDay;
        }
```

The Oracle checked: is today warmer than the day on top of the stack?

If yes -- that waiting day had found its answer.
The distance `i - waitingDay` was recorded.
The waiting day was popped -- its vigil was over.

The while loop continued -- today might be warmer
than multiple waiting days stacked beneath.

> _"A single warm day can end the wait
> of many cold days at once.
> The stack unwinds until it meets
> a day that is still warmer -- or empties entirely."_

---

### 📥 Today Joins the Waiting Stack

```cpp
        st.push(i);
    }
    return answer;
}
```

After resolving all days it could,
today's index was pushed onto the stack.

It now waited for its own warmer successor.

Days remaining on the stack at the end
never found a warmer day -- their answer stays `0`.

---

### 🎺 The Trial of the Weather Scroll

```cpp
int main() {
    vector<int> t1 = {73, 74, 75, 71, 69, 72, 76, 73};
    auto a1 = dailyTemperatures(t1);
    for (int x : a1) cout << x << " "; cout << endl;
    // expected: 1 1 4 2 1 1 0 0

    vector<int> t2 = {30, 40, 50, 60};
    auto a2 = dailyTemperatures(t2);
    for (int x : a2) cout << x << " "; cout << endl;
    // expected: 1 1 1 0

    return 0;
}
```

---

**Full trace for `[73, 74, 75, 71, 69, 72, 76, 73]`:**

| Day i | T[i] | Stack before (indices) | While loop pops              | answer updates       | Stack after |
| ----- | ---- | ---------------------- | ---------------------------- | -------------------- | ----------- |
| 0     | 73   | []                     | --                           | --                   | [0]         |
| 1     | 74   | [0]                    | T[1]=74 > T[0]=73 → pop 0   | ans[0] = 1-0 = 1    | [1]         |
| 2     | 75   | [1]                    | T[2]=75 > T[1]=74 → pop 1   | ans[1] = 2-1 = 1    | [2]         |
| 3     | 71   | [2]                    | T[3]=71 < T[2]=75 → stop    | --                   | [2, 3]      |
| 4     | 69   | [2, 3]                | T[4]=69 < T[3]=71 → stop    | --                   | [2, 3, 4]   |
| 5     | 72   | [2, 3, 4]             | T[5]=72 > T[4]=69 → pop 4   | ans[4] = 5-4 = 1    |             |
|       |      |                        | T[5]=72 > T[3]=71 → pop 3   | ans[3] = 5-3 = 2    | [2, 5]      |
| 6     | 76   | [2, 5]                | T[6]=76 > T[5]=72 → pop 5   | ans[5] = 6-5 = 1    |             |
|       |      |                        | T[6]=76 > T[2]=75 → pop 2   | ans[2] = 6-2 = 4    | [6]         |
| 7     | 73   | [6]                   | T[7]=73 < T[6]=76 → stop    | --                   | [6, 7]      |

**Final answer: [1, 1, 4, 2, 1, 1, 0, 0]** ✓

Days 6 and 7 remained on the stack -- no warmer day followed. Answer = 0.

Day 5 (72°) resolved two waiting days at once: day 4 (69°) and day 3 (71°).
Day 6 (76°) resolved two more: day 5 (72°) and day 2 (75°).

---

**Full trace for `[30, 40, 50, 60]` (strictly increasing):**

| Day i | T[i] | Pops           | answer updates    | Stack after |
| ----- | ---- | -------------- | ----------------- | ----------- |
| 0     | 30   | --             | --                | [0]         |
| 1     | 40   | pop 0          | ans[0] = 1        | [1]         |
| 2     | 50   | pop 1          | ans[1] = 1        | [2]         |
| 3     | 60   | pop 2          | ans[2] = 1        | [3]         |

**Answer: [1, 1, 1, 0]** ✓

Every day is immediately warmer than the previous.
Each waiting day is resolved the very next day.

---

**Trace for `[60, 50, 40, 30]` (strictly decreasing):**

| Day i | T[i] | Pops | Stack after      |
| ----- | ---- | ---- | ---------------- |
| 0     | 60   | --   | [0]              |
| 1     | 50   | --   | [0, 1]           |
| 2     | 40   | --   | [0, 1, 2]        |
| 3     | 30   | --   | [0, 1, 2, 3]    |

**Answer: [0, 0, 0, 0]** ✓

No day is ever followed by a warmer one.
The stack grows monotonically and is never popped.

---

## 🔍 Why Monotonic Decreasing?

The stack maintains temperatures in decreasing order
from bottom to top. Why?

Because when a new temperature arrives:
-   If it's cooler → it sits on top (decreasing order preserved).
-   If it's warmer → it pops everything cooler (resolving their wait).

After the pops, the new temperature sits on top --
still maintaining the decreasing invariant.

This is the **Next Greater Element** pattern --
one of the most powerful stack patterns in all of algorithms.
Daily Temperatures is simply "next greater element"
where the answer is the **distance** instead of the value.

---

### 🧠 Memory of the Warmer Day Law

-   **Monotonic decreasing stack** of indices (temperatures decrease bottom→top)
-   **For each day i:** while `T[i] > T[stack.top()]` → pop and record distance
-   **Distance:** `answer[waitingDay] = i - waitingDay`
-   **Push i** after resolving all poppable days
-   Days left on the stack at the end → answer stays 0 (no warmer day)
-   This is the **Next Greater Element** pattern applied to distances
-   **Time:** O(N) -- each index pushed once, popped at most once (amortized)
-   **Space:** O(N) -- the stack
-   **Edge cases:**
    -   Strictly increasing → every day answered by the next, answer = [1,1,...,0]
    -   Strictly decreasing → no day answered, answer = [0,0,...,0]
    -   All same temperature → no day answered, answer = [0,0,...,0]

Thus is remembered the saga of **Daily Temperatures**,
where the Oracle walked the weather scroll
carrying a stack of days still waiting for warmth --
each new warm day shattering the vigil
of every cooler day beneath it,
recording the distance of their wait --
until the scroll was fully read
and only the unanswered days remained,
their zeros a testament
to the warmth that never came. 🌡️🔥✨
