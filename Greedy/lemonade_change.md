## 🍋💰 _The Lemonade Stand: The Lemonade Change Saga_

> \_"At a lemonade stand,
> each lemonade cost **$5**.
> Customers lined up, each paying with a $5, $10, or $20 bill.
>
> The Oracle had to make correct change for each customer.
> She started with NO money in the register.
>
> She was commanded:
>
> **'Can you provide change for every customer
> in the order they arrive?'**
>
> The Oracle tracked her bills:
> -   $5 bills -- the most versatile (change for $10 and $20).
> -   $10 bills -- only useful as part of change for $20.
> -   $20 bills -- useless for making change (too large).
>
> The greedy rule:
>
> **When giving change for $20, prefer using a $10 + $5
> over three $5 bills.**
>
> Why? Because $5 bills are more flexible --
> they can make change for BOTH $10 and $20 customers.
> $10 bills can only help with $20 customers.
> Spending a $10 (less flexible) preserves $5s (more flexible)."\_

---

This is the saga of **Lemonade Change**.

Each lemonade costs $5. Customers pay with $5, $10, or $20.

-   Return `true` if you can provide correct change for every customer.
-   You start with no money.

```
Input:  bills = [5, 5, 5, 10, 20]
Output: true

Input:  bills = [5, 5, 10, 10, 20]
Output: false
```

---

## 🧠 The Oracle's Core Insight -- Track $5 and $10 Bills

Only $5 and $10 bills are useful for making change.
$20 bills are never used as change (nothing costs more than $5).

**Three cases:**

| Customer pays | Change needed | How to give change |
|---------------|---------------|-------------------|
| $5            | $0            | No change. Just collect. |
| $10           | $5            | Give one $5 bill. |
| $20           | $15           | Give one $10 + one $5 (preferred). OR give three $5s. |

**Greedy for $20 change:** prefer $10 + $5 over $5 + $5 + $5.
$5 bills are more versatile -- save them when possible.

```
Time:  O(n) -- single pass
Space: O(1) -- two counters
```

---

### 📜 The Scroll of the Lemonade Stand

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🍋 The Greedy Ritual

```cpp
bool lemonadeChange(vector<int>& bills) {
    int fives = 0;
    int tens = 0;
```

Two counters. That's all we need.
$20 bills are never used for change -- no need to track them.

---

### 🔁 Process Each Customer

```cpp
    for (int bill : bills) {
```

---

### 💵 Customer Pays $5 -- No Change Needed

```cpp
        if (bill == 5) {
            fives++;
        }
```

Collect the $5. No change required.
The simplest case.

---

### 💵 Customer Pays $10 -- Give $5 Change

```cpp
        else if (bill == 10) {
            if (fives == 0) return false;
            fives--;
            tens++;
        }
```

Need to give $5 back. If no $5 bills available → can't make change → `false`.
Otherwise: spend one $5, gain one $10.

---

### 💵 Customer Pays $20 -- Give $15 Change (Greedy)

```cpp
        else {
            if (tens > 0 && fives > 0) {
                tens--;
                fives--;
            } else if (fives >= 3) {
                fives -= 3;
            } else {
                return false;
            }
        }
    }
```

Need to give $15 back. Two options:

**Option 1 (preferred):** one $10 + one $5 = $15. ✓
**Option 2 (fallback):** three $5 bills = $15. ✓

**Why prefer option 1?**
$5 bills are needed for BOTH $10 and $20 customers.
$10 bills are only needed for $20 customers.
Using a $10 (less flexible) preserves $5s (more flexible).

If neither option works → can't make change → `false`.

> _"Spend the less flexible bill first.
> The $10 can only help here.
> The $5 might be needed later for a $10 customer.
> Be greedy with flexibility, not with quantity."_

---

### ✅ All Customers Served

```cpp
    return true;
}
```

If we made it through all customers without failing → `true`.

---

### 🎺 The Trial of the Lemonade Stand

```cpp
int main() {
    vector<int> b1 = {5, 5, 5, 10, 20};
    cout << lemonadeChange(b1) << endl; // expected: 1 (true)

    vector<int> b2 = {5, 5, 10, 10, 20};
    cout << lemonadeChange(b2) << endl; // expected: 0 (false)

    vector<int> b3 = {5, 5, 10, 20, 5, 5, 5, 5, 5, 5, 5, 5, 20};
    cout << lemonadeChange(b3) << endl; // expected: 1 (true)

    return 0;
}
```

---

**Full trace for bills = [5, 5, 5, 10, 20]:**

| Customer | Bill | Change needed | Action              | fives | tens |
|----------|------|---------------|---------------------|-------|------|
| 1        | $5   | $0            | Collect.            | 1     | 0    |
| 2        | $5   | $0            | Collect.            | 2     | 0    |
| 3        | $5   | $0            | Collect.            | 3     | 0    |
| 4        | $10  | $5            | Give one $5.        | 2     | 1    |
| 5        | $20  | $15           | Give $10+$5.        | 1     | 0    |

All served. **Answer: true** ✓

---

**Full trace for bills = [5, 5, 10, 10, 20]:**

| Customer | Bill | Change needed | Action              | fives | tens |
|----------|------|---------------|---------------------|-------|------|
| 1        | $5   | $0            | Collect.            | 1     | 0    |
| 2        | $5   | $0            | Collect.            | 2     | 0    |
| 3        | $10  | $5            | Give one $5.        | 1     | 1    |
| 4        | $10  | $5            | Give one $5.        | 0     | 2    |
| 5        | $20  | $15           | Need $10+$5: have $10 but fives=0! Need 3×$5: only 0! **FAIL.** | — | — |

**Answer: false** ✓

After two $10 customers, all $5 bills are gone.
The $20 customer needs at least one $5, but none remain.

---

## 🔍 Why the Greedy Choice Is Optimal

Consider: you have two $5 bills and one $10 bill.
A $20 customer arrives. You can give:
-   $10 + $5 → leaves you with one $5.
-   $5 + $5 + $5 → impossible (only have two $5s).

But even if you HAD three $5s:
-   $10 + $5 → leaves two $5s and zero $10s.
-   $5 + $5 + $5 → leaves zero $5s and one $10.

The first option is better: two $5s can serve two future $10 customers.
One $10 can only serve one future $20 customer (and needs a $5 anyway).

**$5 bills are always more valuable to keep.** Spend $10s first.

---

## 🔍 Why We Don't Track $20 Bills

A $20 bill can never be used as change for any customer:
-   $5 customer: no change needed.
-   $10 customer: needs $5 change (not $20).
-   $20 customer: needs $15 change (not $20).

$20 bills are dead weight. No need to count them.

---

### 🧠 Memory of the Lemonade Stand Law

-   **Track only $5 and $10 bills** ($20 is useless for change)
-   **$5 customer:** collect, no change
-   **$10 customer:** give one $5. If none → false.
-   **$20 customer:** prefer $10+$5 over $5+$5+$5 (greedy)
-   **Greedy rule:** spend less flexible bills first (preserve $5s)
-   **$5 bills are the most versatile** -- needed for both $10 and $20 change
-   **Time:** O(n). **Space:** O(1).
-   **Edge cases:**
    -   First customer pays $10 or $20 → immediately false
    -   All $5 customers → always true
    -   Alternating $5 and $10 → works as long as $5s keep coming

Thus is remembered the saga of **Lemonade Change**,
where the Oracle at her lemonade stand
tracked only two denominations --
giving change greedily,
always spending the less flexible $10 before the precious $5 --
because the $5 bill was the universal key
that could unlock change for any customer,
while the $10 was a specialist
useful only in one situation. 🍋💰✨
