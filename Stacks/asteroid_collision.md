## ☄️⚔️ _The Clash of Celestial Titans: The Asteroid Collision Saga_

> \*"Across the Void of Space,
> asteroids drifted in a single line —
> some racing east, others charging west.
>
> Their sizes spoke of power,
> their directions of destiny.
>
> The Oracle was commanded:
>
> \*\*‘Simulate the collisions.
> When two asteroids meet,
> only the stronger survives.
>
> If equal in might,
> both are reduced to cosmic dust.’\*\*
>
> The challenge was not movement,
> but **memory** —
> remembering which asteroids were still alive
> when the next clash occurred.
>
> Thus the Oracle chose
> the ancient structure of survival:
> the **Stack of Fate**."\*

---

This is the fabulous epic of **Asteroid Collision**.

You are given an integer array `asteroids`:

-   Absolute value → **size** of the asteroid
-   Sign → **direction**

    -   `+` → moving right
    -   `-` → moving left

Your task:

-   Return the state of asteroids **after all collisions**

---

## 🧠 The Oracle’s Core Insight — Only Opposites Collide

The Oracle observed:

-   Asteroids moving in the **same direction never meet**
-   A collision happens **only when**:

    ```
    stack top > 0   AND   current < 0
    ```

    (right-moving meets left-moving)

Thus, every new asteroid must be tested
against the **last surviving one**.

---

## ⚔️ The Oracle’s Stack of Fate Ritual

_Survival of the strongest_

```cpp
vector<int> asteroidCollision(vector<int>& asteroids) {
    vector<int> st;
```

The stack held only **survivors**.

---

### 🌌 Walk the Cosmic Line

```cpp
    for (int a : asteroids) {
        bool destroyed = false;
```

Each asteroid entered space,
its fate yet undecided.

---

### 💥 Resolve Collisions

```cpp
        while (!st.empty() && st.back() > 0 && a < 0) {
```

Only opposing directions caused conflict.

---

### 🏋️ Compare Strengths

```cpp
            if (abs(st.back()) < abs(a)) {
                st.pop_back();
                continue;
            }
```

If the incoming asteroid was stronger,
the old survivor was destroyed.

---

```cpp
            else if (abs(st.back()) == abs(a)) {
                st.pop_back();
            }
```

Equal power meant **mutual destruction**.

---

```cpp
            destroyed = true;
            break;
        }
```

If the incoming asteroid was destroyed,
its journey ended here.

---

### 🌠 Record the Survivor

```cpp
        if (!destroyed) {
            st.push_back(a);
        }
    }
    return st;
}
```

Only those who survived all clashes
were recorded.

---

### 🎺 The Trial of the Cosmic War

```cpp
int main() {
    vector<int> asteroids = {5, 10, -5};
    vector<int> ans = asteroidCollision(asteroids);

    for (int x : ans) cout << x << " ";
    cout << endl; // expected: 5 10
    return 0;
}
```

The Oracle witnessed:

-   `10` destroyed `-5`
-   `5` remained untouched

Final survivors: **5, 10**

---

### 🧠 Memory of the Celestial Law

-   Use a stack to track survivors
-   Collisions occur only when directions oppose
-   Smaller asteroid is destroyed
-   Equal sizes → both destroyed
-   Repeat until no collision possible
-   **Time:** O(n)
-   **Space:** O(n)

Thus ends the fabulous epic of **Asteroid Collision**,
where the Oracle watches silent giants clash in the void —
testing strength against strength,
direction against destiny —
until only the true survivors
drift onward through the stars. ☄️✨
