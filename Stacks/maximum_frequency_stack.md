## 👑⚔️ _The Throne of Recurring Power: The Maximum Frequency Stack Saga_

> \*"In the Kingdom of Numbers,
> some warriors appeared rarely,
> others returned again and again —
> their presence growing stronger with each arrival.
>
> The Oracle was commanded:
>
> \*\*‘Design a stack that removes
> not the most recent warrior,
> but the one who appears most frequently.
>
> If two warriors share equal power,
> remove the most recently added among them.’\*\*
>
> This was no ordinary stack.
>
> Power was measured by frequency,
> but ties were broken by recency.
>
> The Oracle needed a throne system —
> one throne per frequency —
> and always to know
> who sat at the highest throne."\*

---

This is the epic saga of **Maximum Frequency Stack**.

You must design a class `FreqStack` that supports:

-   `push(val)`
-   `pop()`

Rules:

-   Remove and return the element with **highest frequency**
-   If tie → return the **most recently inserted**

---

## 🧠 The Oracle’s Core Insight — Group by Frequency

The Oracle maintained:

1. `freq[val]` → how many times each value appears
2. `group[f]` → stack of values with frequency `f`
3. `maxFreq` → current highest frequency

Thus:

-   When pushing:

    -   Increase frequency
    -   Add value to the corresponding frequency stack

-   When popping:

    -   Remove from the highest frequency stack
    -   Decrease frequency
    -   Update `maxFreq` if needed

---

## ⚔️ The Oracle’s Throne Ritual

```cpp
class FreqStack {
    unordered_map<int, int> freq;
    unordered_map<int, stack<int>> group;
    int maxFreq;
```

-   `freq` → tracks count
-   `group` → stacks grouped by frequency
-   `maxFreq` → highest frequency currently present

---

### 🏛️ Constructor

```cpp
public:
    FreqStack() {
        maxFreq = 0;
    }
```

The kingdom begins empty.

---

### 📥 Push a Warrior

```cpp
    void push(int val) {
        freq[val]++;
        int f = freq[val];
```

The warrior’s power increases.

---

```cpp
        if (f > maxFreq) {
            maxFreq = f;
        }
```

If this is the new highest power,
a new throne is acknowledged.

---

```cpp
        group[f].push(val);
    }
```

The warrior sits upon the throne of frequency `f`.

---

### 📤 Pop the Most Powerful Warrior

```cpp
    int pop() {
        int val = group[maxFreq].top();
        group[maxFreq].pop();
```

The warrior at the highest throne
(and most recent among equals) is removed.

---

```cpp
        freq[val]--;
```

Their frequency decreases.

---

```cpp
        if (group[maxFreq].empty()) {
            maxFreq--;
        }
```

If the throne becomes empty,
the kingdom’s maximum power falls.

---

```cpp
        return val;
    }
};
```

The chosen warrior is returned.

---

### 🎺 The Trial of the Throne

```cpp
int main() {
    FreqStack fs;
    fs.push(5);
    fs.push(7);
    fs.push(5);
    fs.push(7);
    fs.push(4);
    fs.push(5);

    cout << fs.pop() << endl; // 5
    cout << fs.pop() << endl; // 7
    cout << fs.pop() << endl; // 5
    cout << fs.pop() << endl; // 4

    return 0;
}
```

The Oracle removed:

-   `5` (freq 3)
-   `7` (freq 2, most recent)
-   `5`
-   `4`

Power ruled first.
Recency broke ties.

---

### 🧠 Memory of the Throne Law

-   Track frequency of each value
-   Maintain stacks grouped by frequency
-   Keep `maxFreq` updated
-   Pop from highest frequency stack
-   If tie → most recent wins
-   **Time:** O(1) per operation
-   **Space:** O(n)

Thus is remembered the saga of **Maximum Frequency Stack**,
where the Oracle does not remove the newest blindly,
nor the oldest blindly —
but instead honors **power first, recency second**,
maintaining a kingdom of thrones
where the most dominant warrior
always stands closest to removal. 👑✨
