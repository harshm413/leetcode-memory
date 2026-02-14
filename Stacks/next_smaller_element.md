## 🌑⚔️ _The Whisper of the Smaller Shadow: The Next Smaller Element Saga_

> \*"In the Line of Rising Giants,
> each warrior stood tall —
> yet none knew
> which smaller shadow
> would appear to their right.
>
> The Oracle was commanded:
>
> \*\*‘For every warrior,
> reveal the next smaller warrior
> standing to his right.
>
> If none exists,
> mark his fate as -1.’\*\*
>
> Searching again and again would waste time.
>
> The Oracle knew:
> when a smaller warrior appears,
> he resolves the destiny
> of many who were waiting."\*

---

This is the epic saga of **Next Smaller Element**.

You are given an array `nums`.

Your task:

-   For each element, find the **next smaller element to its right**
-   If none exists → return `-1`

---

## 🧠 The Oracle’s Core Insight — Resolve When Smaller Appears

The Oracle realized:

-   If the current element is **smaller** than the previous,
    it fulfills the prophecy for that previous element.
-   Maintain a **monotonic increasing stack**
-   When a smaller element appears,
    pop larger ones and assign answers.

---

## ⚔️ The Oracle’s Monotonic Stack Ritual

_Wait for the smaller shadow_

```cpp
vector<int> nextSmallerElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;
```

The stack stores indices
of elements still waiting for a smaller one.

---

### 🌊 Walk Through the Line

```cpp
    for (int i = 0; i < n; i++) {
```

Each warrior stepped forward.

---

### 🌑 Resolve Waiting Warriors

```cpp
        while (!st.empty() && nums[i] < nums[st.top()]) {
            result[st.top()] = nums[i];
            st.pop();
        }
```

If the current warrior was smaller,
he fulfilled the prophecy
for all larger warriors waiting.

---

### 🏗️ Add Current Warrior

```cpp
        st.push(i);
    }
    return result;
}
```

If no smaller warrior had appeared yet,
he remained waiting.

---

### 🎺 The Trial of the Shadows

```cpp
int main() {
    vector<int> nums = {4, 8, 5, 2, 25};
    vector<int> ans = nextSmallerElement(nums);

    for (int x : ans) cout << x << " ";
    cout << endl;
    // expected: 2 5 2 -1 -1
    return 0;
}
```

The Oracle revealed:

-   4 → 2
-   8 → 5
-   5 → 2
-   2 → -1
-   25 → -1

Some found their smaller shadow.
Some stood unmatched.

---

### 🧠 Memory of the Shadow Law

-   Use monotonic increasing stack
-   Store indices
-   When current < stack top → resolve
-   Default answer = -1
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Next Smaller Element**,
where the Oracle walks the line once,
allowing smaller shadows to fulfill waiting destinies —
proving that even the mightiest giant
must one day face
a smaller successor. 🌑✨
