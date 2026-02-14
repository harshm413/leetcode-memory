## 📈🔥 _The Span of the Rising Market: The Online Stock Span Saga_

> \*"In the Market of Endless Days,
> prices arrived one by one —
> not all at once,
> but streaming through time.
>
> The Oracle was commanded:
>
> **‘For each new price,
> tell us how many consecutive days (including today)
> the price has been less than or equal to today’s.’**
>
> She could not revisit the entire past each time.
> The market was alive —
> answers were required instantly.
>
> Thus she forged a structure
> that remembered only the meaningful peaks —
> collapsing weaker days into a single span."\*

---

This is the epic saga of **Online Stock Span**.

You must design a class `StockSpanner` that supports:

```cpp
int next(int price);
```

For each incoming `price`,
return the **stock span**:

> The number of consecutive days
> (ending today)
> where price ≤ today’s price.

---

## 🧠 The Oracle’s Core Insight — Collapse Weaker Days

If today’s price is higher than previous days:

-   Those weaker days will never matter again
-   Their spans can be merged into today’s span

So the Oracle used:

-   A **monotonic decreasing stack**
-   Each element stored:

    ```
    (price, span)
    ```

Instead of remembering every day,
she remembered only compressed spans.

---

## ⚔️ The Oracle’s Market Ritual

_Merge weaker days into today_

```cpp
class StockSpanner {
    stack<pair<int,int>> st;
```

The stack stored:

-   `first` → price
-   `second` → span

---

### 🏛️ Constructor

```cpp
public:
    StockSpanner() {}
```

No preparation needed —
the market begins empty.

---

### 📈 Process a New Price

```cpp
    int next(int price) {
        int span = 1;
```

Each new day begins with span = 1
(counting itself).

---

### 🔥 Collapse Weaker Prices

```cpp
        while (!st.empty() && st.top().first <= price) {
            span += st.top().second;
            st.pop();
        }
```

If previous prices were weaker or equal:

-   Their spans merged into today’s
-   They were removed from memory

---

### 🏔️ Record Today’s Peak

```cpp
        st.push({price, span});
        return span;
    }
};
```

Today’s price and total span
were recorded as a new meaningful peak.

---

### 🎺 The Trial of the Rising Market

```cpp
int main() {
    StockSpanner sp;

    cout << sp.next(100) << endl; // 1
    cout << sp.next(80) << endl;  // 1
    cout << sp.next(60) << endl;  // 1
    cout << sp.next(70) << endl;  // 2
    cout << sp.next(60) << endl;  // 1
    cout << sp.next(75) << endl;  // 4
    cout << sp.next(85) << endl;  // 6

    return 0;
}
```

Each time the price rose,
weaker days merged into a larger span.

---

### 🧠 Memory of the Market Law

-   Use a **monotonic decreasing stack**
-   Store `(price, span)` pairs
-   Merge spans when current price ≥ previous price
-   Each price pushed and popped at most once
-   **Time:** O(1) amortized per call
-   **Space:** O(n)

Thus is remembered the saga of **Online Stock Span**,
where the Oracle does not remember every day,
but instead compresses history —
collapsing weaker prices into stronger peaks —
revealing instantly
how long today’s rise has truly lasted. 📈✨
