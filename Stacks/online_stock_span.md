## 📈🏰 _The Lookback of the Market Watcher: The Online Stock Span Saga_

> \_"In the Market of Endless Days,
> stock prices arrived one by one --
> each day a new number, streaming endlessly.
>
> The Market Watcher was asked a question
> after every single day:
>
> **'How many consecutive days back from today
> (including today itself)
> had a price less than or equal to today's price?'**
>
> This was the **span** --
> the length of the lookback streak
> where today's price dominated.
>
> The naive approach would scan backward
> from every day, counting until a higher price was found.
> O(N) per call -- O(N²) total. Too slow for the endless market.
>
> The Watcher devised a stack-based ritual:
>
> **Maintain a stack of (price, span) pairs.
> When a new price arrives,
> pop every stack entry whose price is <= today's price.
> Absorb their spans into today's span.
> Push today's (price, totalSpan) onto the stack.**
>
> Each entry was pushed once and popped at most once.
> Amortized O(1) per call.
> The market conquered."\_

---

This is the saga of **Online Stock Span**.

Design a class `StockSpanner` that:

-   Receives stock prices one day at a time via `next(price)`.
-   Returns the **span** of that day's price.

The span is the maximum number of **consecutive days**
(starting from today going backward)
where the price was **less than or equal** to today's price.

```
next(100) → 1
next(80)  → 1
next(60)  → 1
next(70)  → 2   (70 and 60)
next(60)  → 1
next(75)  → 4   (75, 60, 70, 60)
next(85)  → 6   (85, 75, 60, 70, 60, 80)
```

---

## 🧠 The Oracle's Core Insight -- Stack of (Price, Span) Pairs

This is the **Previous Greater Element** problem in disguise.

The span of today's price = distance to the previous day
with a **strictly greater** price (or the beginning if none exists).

A monotonic decreasing stack tracks this efficiently.

But instead of storing indices (like Daily Temperatures),
we store **(price, span)** pairs --
because this is a streaming problem with no array to index into.

When a new price arrives:

```
totalSpan = 1  (today itself)

While stack is not empty AND stack.top().price <= today's price:
  totalSpan += stack.top().span   (absorb the popped day's span)
  Pop it -- today's price dominates it

Push (today's price, totalSpan)
Return totalSpan
```

The popped entries' spans are **absorbed** into today's span.
Their consecutive days of dominance now belong to today.

```
Time:  O(1) amortized per call -- each entry pushed once, popped once
Space: O(N) -- the stack
```

---

### 📜 The Scroll of the Endless Market

```cpp
#include <iostream>
#include <stack>
using namespace std;
```

---

## 🏗️ The Market Watcher Is Built

```cpp
class StockSpanner {
    stack<pair<int, int>> st;
```

The stack held `{price, span}` pairs.

Each entry represented a day and the number of consecutive days
(including itself) that it dominated looking backward.

The stack maintained a **monotonically decreasing** order of prices
from bottom to top.

---

```cpp
public:
    StockSpanner() {}
```

The market opened. The stack was empty.

---

## 📈 The Next Ritual -- Process a New Day

```cpp
    int next(int price) {
        int span = 1;
```

Today's span started at `1` -- today itself always counts.

---

### 💥 Absorb All Weaker Days

```cpp
        while (!st.empty() && st.top().first <= price) {
            span += st.top().second;
            st.pop();
        }
```

The Watcher looked at the stack's top.

If the top's price was **less than or equal** to today's price --
today dominated that day AND all the days it had already absorbed.

The popped entry's span was added to today's span.
Then it was removed -- today had consumed its legacy.

The while loop continued until:
-   The stack was empty (today dominates everything seen so far), or
-   The top's price was strictly greater (today's streak is broken).

> _"When a stronger day arrives,
> it swallows the spans of all weaker days beneath it.
> Their history becomes its history.
> Their reach becomes its reach."_

---

### 📥 Today Joins the Stack

```cpp
        st.push({price, span});
        return span;
    }
};
```

Today's price and its total accumulated span
were pushed onto the stack.

The span was returned as the answer for today.

---

### 🎺 The Trial of the Endless Market

```cpp
int main() {
    StockSpanner sp;
    cout << sp.next(100) << endl; // 1
    cout << sp.next(80)  << endl; // 1
    cout << sp.next(60)  << endl; // 1
    cout << sp.next(70)  << endl; // 2
    cout << sp.next(60)  << endl; // 1
    cout << sp.next(75)  << endl; // 4
    cout << sp.next(85)  << endl; // 6
    return 0;
}
```

---

**Full trace:**

| Call      | Price | Stack before (price,span)     | Pops & absorbs                  | Span | Stack after              |
| --------- | ----- | ----------------------------- | ------------------------------- | ---- | ------------------------ |
| next(100) | 100   | []                            | --                              | 1    | [(100,1)]                |
| next(80)  | 80    | [(100,1)]                     | 80 < 100 → stop                | 1    | [(100,1),(80,1)]         |
| next(60)  | 60    | [(100,1),(80,1)]              | 60 < 80 → stop                 | 1    | [(100,1),(80,1),(60,1)]  |
| next(70)  | 70    | [(100,1),(80,1),(60,1)]       | 70 >= 60 → pop(60,1), span=2   | 2    | [(100,1),(80,1),(70,2)]  |
| next(60)  | 60    | [(100,1),(80,1),(70,2)]       | 60 < 70 → stop                 | 1    | [(100,1),(80,1),(70,2),(60,1)] |
| next(75)  | 75    | [(100,1),(80,1),(70,2),(60,1)]| 75>=60 → pop(60,1), span=2     |      |                          |
|           |       |                               | 75>=70 → pop(70,2), span=4     | 4    | [(100,1),(80,1),(75,4)]  |
| next(85)  | 85    | [(100,1),(80,1),(75,4)]       | 85>=75 → pop(75,4), span=5     |      |                          |
|           |       |                               | 85>=80 → pop(80,1), span=6     | 6    | [(100,1),(85,6)]         |

**Answers: [1, 1, 1, 2, 1, 4, 6]** ✓

At `next(75)`: popped `(60,1)` and `(70,2)`. Absorbed spans: 1+1+2 = 4.
The `70` had already absorbed `60`'s original span -- so `75` inherited
the full chain: days 60, 70, 60 (the earlier one), plus itself.

At `next(85)`: popped `(75,4)` and `(80,1)`. Absorbed: 1+4+1 = 6.
85 dominated 6 consecutive days looking back.

---

**Trace for strictly increasing `[10, 20, 30, 40]`:**

| Call     | Pops              | Span | Stack after  |
| -------- | ----------------- | ---- | ------------ |
| next(10) | --                | 1    | [(10,1)]     |
| next(20) | pop(10,1) → +1    | 2    | [(20,2)]     |
| next(30) | pop(20,2) → +2    | 3    | [(30,3)]     |
| next(40) | pop(30,3) → +3    | 4    | [(40,4)]     |

**Answers: [1, 2, 3, 4]** ✓

Each new price dominates everything before it.
The stack always has exactly one entry.

---

**Trace for strictly decreasing `[40, 30, 20, 10]`:**

| Call     | Pops | Span | Stack after                    |
| -------- | ---- | ---- | ------------------------------ |
| next(40) | --   | 1    | [(40,1)]                       |
| next(30) | --   | 1    | [(40,1),(30,1)]                |
| next(20) | --   | 1    | [(40,1),(30,1),(20,1)]         |
| next(10) | --   | 1    | [(40,1),(30,1),(20,1),(10,1)]  |

**Answers: [1, 1, 1, 1]** ✓

No day dominates any previous day. Stack grows monotonically.

---

## 🔍 Why Store Spans Instead of Indices?

In Daily Temperatures, we stored indices because we had the full array.
Here, prices arrive one at a time -- there's no array to index into.

By storing `(price, span)` pairs, each entry carries
its own accumulated history. When it's popped,
its span is transferred to the new entry --
no need to look up past prices by index.

This is the streaming-friendly version of the monotonic stack.

---

## 🔄 Connection to Previous Greater Element

The span of day `i` = `i - index of the previous day with strictly greater price`.

If no such day exists, the span extends to the beginning.

This is exactly the **Previous Greater Element** problem --
and the monotonic decreasing stack solves it.

---

### 🧠 Memory of the Market Lookback Law

-   **Monotonic decreasing stack** of `{price, span}` pairs
-   **`next(price)`:**
    -   Start `span = 1`
    -   While stack top's price <= today's price → pop, absorb its span
    -   Push `{price, span}`, return `span`
-   Popped entries' spans are **absorbed** -- their history becomes today's
-   Stack maintains strictly decreasing prices from bottom to top
-   This is the **Previous Greater Element** pattern for streaming data
-   **Time:** O(1) amortized per call -- each entry pushed once, popped once
-   **Space:** O(N) -- stack holds at most N entries
-   **Edge cases:**
    -   Strictly increasing → each day absorbs all previous, span = day number
    -   Strictly decreasing → no absorption, every span = 1
    -   All same price → each day absorbs all previous (<=)

Thus is remembered the saga of **Online Stock Span**,
where the Market Watcher stood at the edge of the endless stream,
carrying a stack of price-span pairs --
absorbing the legacy of every weaker day,
transferring their spans to the new dominant price --
until the lookback streak was fully measured
and the span was returned
before the next day's price even arrived. 📈🏰✨
