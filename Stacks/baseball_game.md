## ⚾📜 _The Chronicle of the Scorekeeper: The Baseball Game Saga_

> \_"In the Arena of Innings,
> a strange baseball game was played --
> not with bats and gloves,
> but with **records** written one by one
> onto a living scroll.
>
> Each record carried meaning:
> some added new points to the tally,
> some erased the last round entirely,
> some doubled the glory of the previous round,
> and some summoned the strength of the last two rounds combined.
>
> The Oracle was commanded:
>
> **'Given the list of operations,
> calculate the final score --
> the sum of all rounds that survived.'**
>
> The Oracle knew that the past could be undone,
> repeated, or strengthened at any moment.
> She needed a perfect memory of valid rounds --
> a structure where the most recent round
> was always visible and removable.
>
> **A stack.**
>
> Push new scores. Pop when invalidated.
> Peek at the top for doubling.
> Pop-peek-push for summation.
>
> The stack was the living scroll --
> always reflecting the truth of the game."\_

---

This is the saga of **Baseball Game**.

You are given a list of strings `operations`.
Each operation is one of four types:

-   An **integer x** → record a new score of x
-   `"+"` → record a new score = sum of the previous two valid scores
-   `"D"` → record a new score = double the previous valid score
-   `"C"` → invalidate and remove the previous valid score

Return the **sum of all valid scores** after processing all operations.

```
Input:  ops = ["5","2","C","D","+"]
Output: 30

Input:  ops = ["5","-2","4","C","D","9","+","+"]
Output: 27

Input:  ops = ["1"]
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- The Stack IS the Scoreboard

Every operation depends on the **most recent** valid scores.
Invalidation removes the most recent.
Doubling reads the most recent.
Summation reads the two most recent.

A `stack<int>` gives O(1) access to the top and O(1) push/pop.

But a stack does NOT support random access --
you cannot do `stack[n-2]`. To read the second element,
you must **pop the top, peek the new top, then push back**.

After all operations, drain the stack and sum everything.

```
Time:  O(N) -- one pass through operations, O(1) per operation
Space: O(N) -- the stack holds at most N scores
```

---

### 📜 The Scroll of the Arena

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <string>
using namespace std;
```

---

## ⚔️ The Oracle's Scorekeeping Ritual

```cpp
int calPoints(vector<string>& operations) {
    stack<int> st;
```

The Oracle opened the living scroll --
a true `stack<int>` to store valid scores.

Only the top is visible. Only the top can be removed.
This is the discipline of the stack.

---

## 🔁 Process Each Operation

```cpp
    for (const string& op : operations) {
```

Each record was read in order, one by one.

---

### ❌ "C" -- Cancel the Last Round

```cpp
        if (op == "C") {
            st.pop();
        }
```

The last valid score was erased from history.
The stack shrank by one.

> _"The past is undone.
> The round never happened.
> The scroll forgets."_

---

### 🔁 "D" -- Double the Last Score

```cpp
        else if (op == "D") {
            st.push(2 * st.top());
        }
```

The Oracle peeked at the top (`st.top()`),
doubled it, and pushed the result as a **new** score.

The original score remained untouched beneath --
"D" does not modify the previous round,
it creates a new round whose value is double.

---

### ➕ "+" -- Sum of Last Two Scores

```cpp
        else if (op == "+") {
            int top1 = st.top();
            st.pop();
            int top2 = st.top();
            st.push(top1);
            st.push(top1 + top2);
        }
```

This is the trickiest operation on a true stack.

A stack has no random access -- you cannot peek at the second element.
So the Oracle performed a careful dance:

1. **Pop the top** → save it as `top1`.
2. **Peek the new top** → this is `top2` (the second-to-last).
3. **Push `top1` back** → restore the stack to its original state.
4. **Push `top1 + top2`** → the new score sits on top.

The stack now has all original scores plus the new sum on top.

> _"To see beneath the surface,
> the Oracle must lift the top stone,
> glimpse what lies below,
> then carefully place everything back
> before adding the new truth."_

---

### 🔢 Integer -- Record a New Score

```cpp
        else {
            st.push(stoi(op));
        }
    }
```

A numeric string was converted to an integer
and pushed onto the stack as a new valid score.

`stoi` handles both positive and negative numbers.

---

## 🏆 Sum All Surviving Scores

```cpp
    int total = 0;
    while (!st.empty()) {
        total += st.top();
        st.pop();
    }
    return total;
}
```

After all operations, the stack held every valid score
that survived cancellations.

The Oracle drained the stack element by element,
summing each score as it was popped.

> _"A stack cannot be iterated.
> It can only be emptied.
> Each score speaks once as it leaves,
> adding its voice to the final tally."_

---

### 🎺 The Trial of the Strange Game

```cpp
int main() {
    vector<string> ops1 = {"5", "2", "C", "D", "+"};
    cout << calPoints(ops1) << endl; // expected: 30

    vector<string> ops2 = {"5", "-2", "4", "C", "D", "9", "+", "+"};
    cout << calPoints(ops2) << endl; // expected: 27

    vector<string> ops3 = {"1"};
    cout << calPoints(ops3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for `["5", "2", "C", "D", "+"]`:**

| Step | Op  | Action                              | Stack (top→)    |
| ---- | --- | ----------------------------------- | --------------- |
| 1    | "5" | Push 5                              | [5]             |
| 2    | "2" | Push 2                              | [5, 2]          |
| 3    | "C" | Pop 2                               | [5]             |
| 4    | "D" | top=5, push 10                      | [5, 10]         |
| 5    | "+" | pop 10, peek 5, push 10, push 15    | [5, 10, 15]     |

Drain: 15 + 10 + 5 = **30** ✓

Step 3 erased the `2`. Step 4 doubled the surviving `5`.
Step 5: pop `10`, peek `5`, restore `10`, push `10+5=15`.

---

**Full trace for `["5", "-2", "4", "C", "D", "9", "+", "+"]`:**

| Step | Op   | Action                              | Stack (top→)            |
| ---- | ---- | ----------------------------------- | ----------------------- |
| 1    | "5"  | Push 5                              | [5]                     |
| 2    | "-2" | Push -2                             | [5, -2]                 |
| 3    | "4"  | Push 4                              | [5, -2, 4]              |
| 4    | "C"  | Pop 4                               | [5, -2]                 |
| 5    | "D"  | top=-2, push -4                     | [5, -2, -4]             |
| 6    | "9"  | Push 9                              | [5, -2, -4, 9]          |
| 7    | "+"  | pop 9, peek -4, push 9, push 5      | [5, -2, -4, 9, 5]       |
| 8    | "+"  | pop 5, peek 9, push 5, push 14      | [5, -2, -4, 9, 5, 14]   |

Drain: 14 + 5 + 9 + (-4) + (-2) + 5 = **27** ✓

"D" doubled a negative number at step 5 -- works identically.
"C" at step 4 erased `4`, making `-2` the new top for "D".

---

**Trace for `["1"]`:**

| Step | Op  | Action | Stack |
| ---- | --- | ------ | ----- |
| 1    | "1" | Push 1 | [1]   |

Drain: **1** ✓

---

## 🔍 The "+" Dance -- Why Pop-Peek-Push?

A `stack<int>` only exposes `.top()`. There is no `.second()` or `[n-2]`.

To access the second element:

```cpp
int top1 = st.top(); st.pop();   // remove top, save it
int top2 = st.top();              // now the second element is visible
st.push(top1);                    // restore the original top
st.push(top1 + top2);            // push the new sum
```

This 4-step dance is the cost of using a true stack.
If you used a `vector<int>` instead, you could index directly --
but the stack enforces the LIFO discipline
that makes the problem's intent clear.

---

### 🧠 Memory of the Scorekeeper Law

-   **`stack<int>`** stores all valid scores -- LIFO access only
-   **Four operations:**
    -   Integer → `push(stoi(op))`
    -   `"C"` → `pop()` -- erase last valid score
    -   `"D"` → `push(2 * top())` -- double last, push as new
    -   `"+"` → pop top, peek second, push top back, push sum
-   "D" and "+" create **new** scores -- they don't modify existing ones
-   "C" **removes** the last score -- it truly vanishes
-   **Drain the stack** at the end to compute the final sum
-   **Time:** O(N) -- one pass, O(1) per operation
-   **Space:** O(N) -- stack holds at most N scores
-   **Edge cases:**
    -   Negative numbers work identically
    -   Single operation → just that score

Thus is remembered the saga of **Baseball Game**,
where the Oracle kept perfect score
on a living stack that grew and shrank --
pushing new rounds, popping mistakes,
doubling triumphs with a peek at the top,
and performing the careful pop-peek-push dance
to summon the combined strength of the last two survivors --
until every operation had been honored
and the stack was drained to reveal
the true outcome of the game. ⚾📜✨
