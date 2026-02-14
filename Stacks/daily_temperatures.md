## 🌡️🔥 _The Prophecy of Warmer Days: The Daily Temperatures Saga_

> \*"In the Calendar of Seasons,
> each day carried a number —
> a measure of warmth beneath the sun.
>
> The Oracle was commanded:
>
> \*\*‘For every day, tell us
> how many days must pass
> until a warmer one arrives.
>
> If no warmer day ever comes,
> record zero.’\*\*
>
> The foolish would check each future day,
> again and again —
> but the Oracle knew a better way.
>
> She would walk the calendar once,
> carrying a stack of unresolved days —
> waiting patiently
> for the sun to rise hotter."\*

---

This is the epic saga of **Daily Temperatures**.

You are given an array `temperatures` where:

-   `temperatures[i]` = temperature on day `i`

Your task:

-   Return an array `answer`
-   Where `answer[i]` is the number of days
    until a **warmer temperature**
-   If none exists → `0`

---

## 🧠 The Oracle’s Core Insight — Resolve When Warmer Appears

The Oracle realized:

-   If today is warmer than a previous day,
    that previous day’s wait is over.
-   Keep track of days **still waiting**
-   Use a **monotonic decreasing stack**

The stack holds **indices** of days
whose warmer day hasn’t arrived yet.

---

## ⚔️ The Oracle’s Stack of Waiting Days Ritual

_Resolve colder days when warmth arrives_

```cpp
vector<int> dailyTemperatures(vector<int>& temperatures) {
    int n = temperatures.size();
    vector<int> answer(n, 0);
    stack<int> st;
```

The stack stored indices of unresolved days.

---

### 🌞 Walk Through the Calendar

```cpp
    for (int i = 0; i < n; i++) {
```

Each day stepped into sunlight.

---

### 🔥 Resolve Waiting Days

```cpp
        while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
            int prev = st.top();
            st.pop();
            answer[prev] = i - prev;
        }
```

If today was warmer than a previous waiting day:

-   That day’s wait ended
-   The number of days passed was recorded

---

### 🧊 Add Current Day to Waiting Stack

```cpp
        st.push(i);
    }
    return answer;
}
```

If today had no warmer successor yet,
it waited in the stack.

---

### 🎺 The Trial of the Changing Seasons

```cpp
int main() {
    vector<int> temperatures = {73,74,75,71,69,72,76,73};
    vector<int> ans = dailyTemperatures(temperatures);

    for (int x : ans) cout << x << " ";
    cout << endl;
    // expected: 1 1 4 2 1 1 0 0
    return 0;
}
```

Each colder day was resolved
when a warmer day appeared.

---

### 🧠 Memory of the Sun’s Law

-   Use a monotonic decreasing stack (store indices)
-   When current temp > stack top temp → resolve
-   Distance = `currentIndex - previousIndex`
-   Unresolved days remain 0
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of **Daily Temperatures**,
where the Oracle walks through shifting seasons,
carrying unresolved days in memory —
waiting for warmth to arrive —
and resolving each prophecy
the moment the sun burns brighter. 🌡️✨
