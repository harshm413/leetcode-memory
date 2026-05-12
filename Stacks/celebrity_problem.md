## 👑🎭 _The One Who Knows No One: The Celebrity Problem Saga_

> \_"The Oracle was given a party of N people (labeled 0 to N-1).
>
> Among them, there MIGHT be a celebrity:
>
> **A celebrity is known by EVERYONE else,
> but knows NO ONE.**
>
> The Oracle had access to one function:
> `knows(a, b)` — returns true if person `a` knows person `b`.
>
> She was commanded:
>
> **'Find the celebrity, or determine that none exists.
> Minimize the number of knows() calls.'**
>
> The Oracle used a **stack-based elimination:**
>
> **Push everyone onto the stack.
> Pop two people. Ask if A knows B.
> If A knows B → A is NOT the celebrity (celebrities know no one). Eliminate A.
> If A doesn't know B → B is NOT the celebrity (celebrities are known by all). Eliminate B.
> Push the survivor back.
> Repeat until one person remains — the CANDIDATE.
> Verify the candidate with two passes.**
>
> Each comparison eliminates exactly one person.
> N-1 comparisons to find the candidate. 2(N-1) to verify."\_

---

This is the saga of **The Celebrity Problem**.

Given N people and a `knows(a, b)` function:
-   Find the celebrity (known by all, knows no one).
-   Return -1 if no celebrity exists.

```
Input:  N = 4, knows matrix:
        0 knows: [1, 2]
        1 knows: []        ← celebrity candidate (knows no one)
        2 knows: [1]
        3 knows: [1, 2]
Output: 1   (everyone knows 1, and 1 knows no one... but does 0 know 1? yes. 
             Does 3 know 1? yes. Does 2 know 1? yes. 1 knows no one? yes. Celebrity = 1.)

Input:  N = 3, everyone knows everyone
Output: -1  (no celebrity — a celebrity must know NO ONE)
```

---

## 🧠 The Elimination Insight

Every `knows(a, b)` call eliminates exactly ONE person:

**If `knows(a, b)` is TRUE:**
-   A knows B → A is NOT the celebrity (celebrities know no one).
-   B MIGHT be the celebrity. Keep B.

**If `knows(a, b)` is FALSE:**
-   A doesn't know B → B is NOT the celebrity (celebrities are known by everyone).
-   A MIGHT be the celebrity. Keep A.

One call. One elimination. After N-1 eliminations, one candidate remains.

> _"Every question eliminates one person.
> The survivor is not guaranteed to be the celebrity —
> but if a celebrity exists, it MUST be the survivor."_

---

## 🧠 Why Verification Is Needed

The elimination only finds a CANDIDATE. It doesn't prove they're the celebrity.

Example: if no celebrity exists, the last survivor is just whoever
happened to not get eliminated — not necessarily a celebrity.

**Verification:** check two things about the candidate `c`:
1. Does everyone else know `c`? (celebrity is known by all)
2. Does `c` know no one? (celebrity knows no one)

If both pass → `c` is the celebrity. If either fails → no celebrity exists.

---

### 📜 The Scroll of the One Who Knows No One

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## 👑 The Stack-Based Elimination

### The knows() function (given)

```cpp
// Assume this is provided:
// bool knows(int a, int b);
// For this example, we'll use a matrix:
vector<vector<int>> matrix;
bool knows(int a, int b) {
    return matrix[a][b];
}
```

---

### Find the candidate using a stack

```cpp
int findCelebrity(int n) {
    stack<int> st;
    for (int i = 0; i < n; i++) {
        st.push(i);
    }
```

Push everyone onto the stack. All are potential celebrities.

---

### Eliminate until one remains

```cpp
    while (st.size() > 1) {
        int a = st.top(); st.pop();
        int b = st.top(); st.pop();
```

Pop two people. Compare them.

---

```cpp
        if (knows(a, b)) {
            st.push(b);
        } else {
            st.push(a);
        }
    }
```

**`knows(a, b)` is true:** A knows B → A is eliminated. Push B back.
**`knows(a, b)` is false:** A doesn't know B → B is eliminated. Push A back.

One person is eliminated. The other survives and goes back on the stack.

> _"Two contenders face each other.
> One question decides who stays.
> The loser is eliminated forever.
> The winner returns to face the next challenger."_

---

### The candidate

```cpp
    int candidate = st.top();
```

The last person standing. Might be the celebrity. Might not.

---

### Verify the candidate

```cpp
    for (int i = 0; i < n; i++) {
        if (i == candidate) continue;
```

Check every other person against the candidate.

---

### Does everyone know the candidate?

```cpp
        if (!knows(i, candidate)) {
            return -1;
        }
```

If even ONE person doesn't know the candidate → not a celebrity.

---

### Does the candidate know no one?

```cpp
        if (knows(candidate, i)) {
            return -1;
        }
    }
    return candidate;
}
```

If the candidate knows even ONE person → not a celebrity.

If both checks pass for all people → confirmed celebrity.

> _"The candidate faces the final trial.
> Does everyone know them? Do they know no one?
> If both hold — they are the celebrity.
> If either fails — no celebrity exists in this party."_

---

### 🎺 The Trial of the Celebrity

```cpp
int main() {
    // Person 1 is the celebrity
    matrix = {
        {0, 1, 1, 0},  // 0 knows 1, 2
        {0, 0, 0, 0},  // 1 knows no one (celebrity!)
        {0, 1, 0, 0},  // 2 knows 1
        {0, 1, 1, 0}   // 3 knows 1, 2
    };
    cout << findCelebrity(4) << endl; // expected: 1

    // No celebrity
    matrix = {
        {0, 1, 0},
        {1, 0, 1},
        {0, 1, 0}
    };
    cout << findCelebrity(3) << endl; // expected: -1

    return 0;
}
```

---

**Trace for N=4, celebrity = person 1:**

```
Stack: [0, 1, 2, 3]

Pop 3, 2. knows(3, 2)? Yes → 3 eliminated. Push 2. Stack: [0, 1, 2].
Pop 2, 1. knows(2, 1)? Yes → 2 eliminated. Push 1. Stack: [0, 1].
Pop 1, 0. knows(1, 0)? No → 0 eliminated. Push 1. Stack: [1].

Candidate = 1.

Verify:
  i=0: knows(0, 1)? Yes ✓. knows(1, 0)? No ✓.
  i=2: knows(2, 1)? Yes ✓. knows(1, 2)? No ✓.
  i=3: knows(3, 1)? Yes ✓. knows(1, 3)? No ✓.

All pass. Celebrity = 1.
```

**Answer: 1** ✓

---

**Trace for N=3, no celebrity:**

```
Stack: [0, 1, 2]

Pop 2, 1. knows(2, 1)? Yes → 2 eliminated. Push 1. Stack: [0, 1].
Pop 1, 0. knows(1, 0)? Yes → 1 eliminated. Push 0. Stack: [0].

Candidate = 0.

Verify:
  i=1: knows(1, 0)? Yes ✓. knows(0, 1)? Yes ✗ → 0 knows someone!

Return -1. No celebrity.
```

**Answer: -1** ✓

---

## 🔍 The Two-Pointer Alternative (No Stack Needed)

```cpp
int findCelebrity(int n) {
    int candidate = 0;
    for (int i = 1; i < n; i++) {
        if (knows(candidate, i)) {
            candidate = i;
        }
    }
    // Verify candidate (same as above)
    for (int i = 0; i < n; i++) {
        if (i == candidate) continue;
        if (!knows(i, candidate) || knows(candidate, i)) return -1;
    }
    return candidate;
}
```

Same elimination logic without a stack. Walk through people.
If the current candidate knows person `i` → candidate is eliminated,
`i` becomes the new candidate.

Same O(N) eliminations + O(N) verification = O(N) total.
O(1) space (no stack needed).

---

## 🔍 Complexity Analysis

| Phase | knows() calls | Purpose |
|-------|--------------|---------|
| Elimination | N - 1 | Find candidate |
| Verification | 2(N - 1) | Confirm or reject |
| **Total** | **3(N - 1)** | **O(N)** |

Minimum possible: you MUST check every person at least once.
O(N) is optimal.

---

## 🔍 Why the Celebrity Must Survive Elimination

If a celebrity `C` exists:
-   When `C` is compared with anyone: `knows(C, x)` = false (C knows no one).
    So the OTHER person is eliminated. C survives.
-   When anyone is compared with `C`: `knows(x, C)` = true (everyone knows C).
    So `x` is eliminated. C survives.

C can NEVER be eliminated. It always wins the comparison.
So if a celebrity exists, it MUST be the final candidate.

---

### 🧠 Memory of the Celebrity Problem Law

-   **Elimination:** each `knows(a, b)` call eliminates one person
-   **Stack or single variable:** pop two, eliminate one, push survivor
-   **N-1 eliminations** → one candidate remains
-   **Verification:** check everyone knows candidate AND candidate knows no one
-   **If celebrity exists → it MUST survive** (it always wins comparisons)
-   **Two-pointer alternative:** O(1) space, same logic without stack
-   **Total:** O(N) knows() calls. O(1) space (two-pointer version).

Thus is remembered the saga of **The Celebrity Problem**,
where the Oracle eliminated contenders one by one —
each question revealing who could NOT be the celebrity —
until one candidate remained,
then verified through two final checks:
known by all, knows no one —
the one who stands above the crowd
yet recognizes no face in it. 👑🎭✨
