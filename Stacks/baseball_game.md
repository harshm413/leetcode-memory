## ⚾📜 _The Chronicle of the Scorekeeper: The Baseball Game Saga_

> \*"In the Arena of Innings,
> a strange baseball game was played —
> not with bats and gloves,
> but with **records** written one by one.
>
> Each record carried meaning:
> some added new points,
> some erased the past,
> some doubled glory,
> and some recalled history itself.
>
> The Oracle was commanded:
>
> **‘Given the list of operations,
> calculate the final score.’**
>
> But beware —
> the past could be undone,
> repeated,
> or strengthened.
>
> To survive this game,
> the Oracle needed a perfect memory
> of valid rounds —
> a **stack of truth**."\*

---

This is the saga of **Baseball Game** (LeetCode).

You are given a list of strings `operations`.
Each operation represents a record in the game:

-   An **integer** → record a new score
-   `"+"` → sum of the last two valid scores
-   `"D"` → double the last valid score
-   `"C"` → invalidate (remove) the last valid score

Your task:

-   Return the **sum of all valid scores** after all operations

---

## 🧠 The Oracle’s Core Insight — The Past Must Be Remembered

The Oracle understood:

-   Operations depend on **previous valid rounds**
-   Invalidated rounds must disappear completely
-   The **most recent rounds matter the most**

This is the perfect use of a **stack**:

-   Push new scores
-   Pop when invalidated
-   Peek when repeating or doubling

---

## ⚔️ The Oracle’s Stack Ritual

_Record, undo, repeat, and double_

```cpp
int calPoints(vector<string>& operations) {
    vector<int> st;
```

The Oracle prepared a stack
to store only **valid scores**.

---

### 🧾 Walk Through the Records

```cpp
    for (string& op : operations) {
```

Each operation was read in order.

---

### ❌ Cancel the Last Round

```cpp
        if (op == "C") {
            st.pop_back();
        }
```

The last score was erased from history.

---

### 🔁 Double the Last Score

```cpp
        else if (op == "D") {
            st.push_back(2 * st.back());
        }
```

The last round’s glory was doubled
and recorded as a new score.

---

### ➕ Add the Last Two Scores

```cpp
        else if (op == "+") {
            int last = st.back();
            int secondLast = st[st.size() - 2];
            st.push_back(last + secondLast);
        }
```

The Oracle summoned the strength
of the last two valid rounds.

---

### 🏏 Record a New Score

```cpp
        else {
            st.push_back(stoi(op));
        }
    }
```

A numeric record was added directly.

---

### 🧮 Calculate the Final Score

```cpp
    int total = 0;
    for (int score : st) {
        total += score;
    }
    return total;
}
```

All valid rounds were summed.

---

### 🎺 The Trial of the Strange Game

```cpp
int main() {
    vector<string> ops = {"5", "2", "C", "D", "+"};
    cout << calPoints(ops) << endl; // expected: 30
    return 0;
}
```

The Oracle traced the game:

-   `"5"` → 5
-   `"2"` → 5, 2
-   `"C"` → 5
-   `"D"` → 5, 10
-   `"+"` → 5, 10, 15

Final score = **30**

---

### 🧠 Memory of the Scorekeeper Law

-   Use a stack to store valid scores
-   `"C"` → pop
-   `"D"` → push double of last
-   `"+"` → push sum of last two
-   Numbers → push directly
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of the **Baseball Game**,
where the Oracle keeps perfect score
by honoring valid rounds,
erasing mistakes,
doubling triumphs,
and recalling history when needed —
until the final tally reveals
the true outcome of the game. ⚾✨
