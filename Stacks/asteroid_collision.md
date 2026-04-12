## ☄️💥 _The War of the Drifting Stones: The Asteroid Collision Saga_

> \_"Across the void of space,
> asteroids drifted in a single line --
> some hurtling to the right,
> others charging to the left.
>
> Positive meant rightward. Negative meant leftward.
> The absolute value was the asteroid's size.
>
> When two asteroids met head-on --
> one moving right, the next moving left --
> they collided.
>
> **The smaller one exploded.
> The larger one survived.
> If equal in size -- both were destroyed.**
>
> Asteroids moving in the same direction never met.
> Two leftward asteroids passed in peace.
> Two rightward asteroids drifted apart.
>
> Only the collision of right-then-left mattered.
>
> The Oracle was commanded:
>
> **'Simulate all collisions.
> Return the state of the asteroids
> after all collisions have resolved.'**
>
> The Oracle reached for a stack.
>
> She would process asteroids one by one.
> Rightward asteroids were pushed -- waiting for a potential collision.
> Leftward asteroids challenged the stack's top --
> colliding again and again until they were destroyed,
> or they destroyed everything in their path,
> or no more rightward asteroids remained to fight."\_

---

This is the saga of **Asteroid Collision**.

Given an array `asteroids` where:

-   Positive value = moving **right**
-   Negative value = moving **left**
-   Absolute value = size

Asteroids moving the same direction never meet.
When a right-moving meets a left-moving, they collide:
-   Smaller one explodes.
-   Equal size → both explode.
-   Larger one survives unchanged.

Return the state after all collisions.

```
Input:  [5, 10, -5]
Output: [5, 10]

Input:  [8, -8]
Output: []

Input:  [10, 2, -5]
Output: [10]

Input:  [-2, -1, 1, 2]
Output: [-2, -1, 1, 2]
```

---

## 🧠 The Oracle's Core Insight -- Stack Simulates the Collision Chain

A collision only happens when a **rightward** asteroid (positive, on the stack)
meets a **leftward** asteroid (negative, incoming).

Two leftward asteroids never collide (both going left).
Two rightward asteroids never collide (both going right).
A leftward followed by a rightward never collide (moving apart).

Only **right-then-left** = collision.

The stack holds the surviving asteroids so far.
When a leftward asteroid arrives:

```
While the stack's top is rightward AND smaller → top explodes, pop.
If the stack's top is rightward AND equal → both explode, pop, discard incoming.
If the stack's top is rightward AND larger → incoming explodes, discard it.
If the stack is empty or top is leftward → no collision, push incoming.
```

The incoming leftward asteroid may destroy multiple rightward ones
before being stopped or surviving.

```
Time:  O(N) -- each asteroid is pushed and popped at most once
Space: O(N) -- the stack
```

---

### 📜 The Scroll of the Void

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Collision Ritual

```cpp
vector<int> asteroidCollision(vector<int>& asteroids) {
    stack<int> st;
```

The Oracle opened the void --
a stack to hold surviving asteroids.

---

## 🔁 Process Each Asteroid

```cpp
    for (int ast : asteroids) {
```

Each asteroid drifted in, one by one.

---

### ☄️ Rightward Asteroid -- No Immediate Danger

```cpp
        if (ast > 0) {
            st.push(ast);
            continue;
        }
```

A positive asteroid moved rightward.
No collision possible with anything already on the stack
(stack holds either rightward or leftward survivors --
rightward won't collide with rightward,
and leftward already passed safely).

Push it and move on.

---

### 💥 Leftward Asteroid -- Collision Chain Begins

```cpp
        bool survived = true;

        while (!st.empty() && st.top() > 0) {
```

A negative asteroid charged leftward.
It could collide with rightward asteroids on the stack.

The `survived` flag tracked whether the incoming asteroid
made it through all collisions alive.

The while loop continued as long as:
-   The stack had asteroids, AND
-   The top asteroid was rightward (positive) -- a collision target.

---

### 💀 Top Is Smaller -- It Explodes

```cpp
            if (st.top() < -ast) {
                st.pop();
                continue;
            }
```

The rightward asteroid on top was smaller than the incoming leftward one.
(`-ast` gives the incoming's size since `ast` is negative.)

The top exploded. Pop it.
The incoming asteroid survived and continued its rampage --
the while loop checked the next stack element.

---

### 🤝 Equal Size -- Both Explode

```cpp
            else if (st.top() == -ast) {
                st.pop();
                survived = false;
                break;
            }
```

Both asteroids were the same size.
Mutual destruction. The top was popped,
and the incoming was marked as destroyed.

---

### 🛡️ Top Is Larger -- Incoming Explodes

```cpp
            else {
                survived = false;
                break;
            }
        }
```

The rightward asteroid on top was larger.
The incoming leftward asteroid was destroyed.
The top survived untouched.

---

### 🌌 Incoming Survived All Collisions

```cpp
        if (survived) {
            st.push(ast);
        }
    }
```

If the incoming asteroid destroyed every rightward asteroid
it encountered (or the stack was empty / top was leftward),
it survived and was pushed onto the stack.

---

## 🏁 Collect the Survivors

```cpp
    vector<int> result(st.size());
    for (int i = st.size() - 1; i >= 0; i--) {
        result[i] = st.top();
        st.pop();
    }
    return result;
}
```

The stack was drained into a result vector.
Since a stack gives elements in reverse order (LIFO),
the Oracle filled the vector from back to front
to preserve the original left-to-right ordering.

---

### 🎺 The Trial of the Drifting Stones

```cpp
int main() {
    vector<int> a1 = {5, 10, -5};
    auto r1 = asteroidCollision(a1);
    for (int x : r1) cout << x << " "; cout << endl; // 5 10

    vector<int> a2 = {8, -8};
    auto r2 = asteroidCollision(a2);
    for (int x : r2) cout << x << " "; cout << endl; // (empty)

    vector<int> a3 = {10, 2, -5};
    auto r3 = asteroidCollision(a3);
    for (int x : r3) cout << x << " "; cout << endl; // 10

    vector<int> a4 = {-2, -1, 1, 2};
    auto r4 = asteroidCollision(a4);
    for (int x : r4) cout << x << " "; cout << endl; // -2 -1 1 2

    return 0;
}
```

---

**Full trace for `[5, 10, -5]`:**

| Step | Asteroid | Action                              | Stack after |
| ---- | -------- | ----------------------------------- | ----------- |
| 1    | 5        | Positive → push                     | [5]         |
| 2    | 10       | Positive → push                     | [5, 10]     |
| 3    | -5       | top=10 > 5 → incoming explodes      | [5, 10]     |

**Answer: [5, 10]** ✓ — the `-5` was destroyed by `10`.

---

**Full trace for `[10, 2, -5]`:**

| Step | Asteroid | Action                              | Stack after |
| ---- | -------- | ----------------------------------- | ----------- |
| 1    | 10       | Positive → push                     | [10]        |
| 2    | 2        | Positive → push                     | [10, 2]     |
| 3    | -5       | top=2 < 5 → top explodes, pop       | [10]        |
|      |          | top=10 > 5 → incoming explodes      | [10]        |

**Answer: [10]** ✓ — `-5` destroyed `2` but was stopped by `10`.

---

**Full trace for `[8, -8]`:**

| Step | Asteroid | Action                              | Stack after |
| ---- | -------- | ----------------------------------- | ----------- |
| 1    | 8        | Positive → push                     | [8]         |
| 2    | -8       | top=8 == 8 → both explode           | []          |

**Answer: []** ✓ — mutual destruction.

---

**Full trace for `[-2, -1, 1, 2]`:**

| Step | Asteroid | Action                              | Stack after     |
| ---- | -------- | ----------------------------------- | --------------- |
| 1    | -2       | Negative, stack empty → push        | [-2]            |
| 2    | -1       | Negative, top=-2 (negative) → push  | [-2, -1]        |
| 3    | 1        | Positive → push                     | [-2, -1, 1]     |
| 4    | 2        | Positive → push                     | [-2, -1, 1, 2]  |

**Answer: [-2, -1, 1, 2]** ✓

No collisions at all. The leftward asteroids are already moving away
from the rightward ones. They never meet.

---

**Trace for `[-2, 2, -1, -2]` (chain collision):**

| Step | Asteroid | Action                              | Stack after |
| ---- | -------- | ----------------------------------- | ----------- |
| 1    | -2       | Negative, stack empty → push        | [-2]        |
| 2    | 2        | Positive → push                     | [-2, 2]     |
| 3    | -1       | top=2 > 1 → incoming explodes       | [-2, 2]     |
| 4    | -2       | top=2 == 2 → both explode           | [-2]        |

**Answer: [-2]** ✓

---

## 🔍 Why the While Loop, Not Just One Check?

A single leftward asteroid can destroy **multiple** rightward ones:

```
[1, 2, 3, -10]
```

`-10` destroys `3`, then `2`, then `1`. All three popped.
The while loop handles this chain of collisions naturally.

Without the loop, only the first collision would be processed.

---

### 🧠 Memory of the Collision Law

-   **Stack** holds surviving asteroids left to right
-   **Positive (rightward)** → always push, no immediate collision
-   **Negative (leftward)** → collision chain with rightward tops:
    -   `top < |incoming|` → top explodes, pop, continue loop
    -   `top == |incoming|` → both explode, pop, break
    -   `top > |incoming|` → incoming explodes, break
    -   Stack empty or top is negative → no collision, push incoming
-   **Collect result** by draining stack back-to-front into a vector
-   **Time:** O(N) -- each asteroid pushed/popped at most once (amortized)
-   **Space:** O(N) -- the stack
-   **Key insight:** only right-then-left causes collision;
    same direction or left-then-right never collide

Thus is remembered the saga of **Asteroid Collision**,
where the Oracle simulated the war of drifting stones
with a stack that absorbed rightward travelers
and let leftward challengers fight their way through --
popping the weak, halting at the strong,
annihilating equals --
until every collision had resolved
and only the survivors remained,
drifting peacefully through the void. ☄️💥✨
