## ⛽🔄 _The Circular Road: The Gas Station Saga_

> \_"Along a circular route,
> `n` gas stations stood in a ring.
> Each station `i` had `gas[i]` units of fuel available
> and the cost to drive to the next station was `cost[i]`.
>
> The Oracle started with an EMPTY tank.
> She could choose any station to begin her journey.
>
> She was commanded:
>
> **'Find the starting station from which you can
> travel around the circuit ONCE in the clockwise direction.
> If no such station exists -- return -1.
> If a solution exists, it is guaranteed to be UNIQUE.'**
>
> The Oracle saw two key insights:
>
> **Insight 1 -- Feasibility check:**
> If `sum(gas) < sum(cost)` → impossible.
> The total fuel available across all stations
> is not enough to cover the total cost of the entire circuit.
> No starting point can save you.
>
> **Insight 2 -- Finding the start (the reset trick):**
> Walk the circuit from station 0.
> Track the current tank (fuel gained - fuel spent).
> If the tank ever goes NEGATIVE at station `i`:
> -   The current starting station CANNOT complete the circuit.
> -   Moreover, NO station between the current start and `i`
>     can be the answer either.
> -   Reset: try starting from station `i + 1`.
>
> **Why can we skip all stations between start and i?**
>
> If we started at `start` and reached station `j` (between start and i)
> with some positive fuel in the tank,
> then starting at `j` with an EMPTY tank would be WORSE
> (we'd have less fuel at `j` than we did coming from `start`).
> If we couldn't make it from `start` with extra fuel,
> we certainly can't make it from `j` with zero fuel.
>
> One pass. Track the tank. Reset on failure.
> The last reset point is the answer."\_

---

This is the saga of **Gas Station**.

Given `n` gas stations arranged in a circle:
-   `gas[i]` = fuel available at station `i`.
-   `cost[i]` = fuel needed to travel from station `i` to station `i+1`.

Start with an empty tank. Return the starting station index
to complete the full circuit. If impossible, return `-1`.
The solution is unique if it exists.

```
Input:  gas = [1, 2, 3, 4, 5], cost = [3, 4, 5, 1, 2]
Output: 3

Input:  gas = [2, 3, 4], cost = [3, 4, 3]
Output: -1
```

---

## 🧠 The Oracle's Core Insight -- Total Check + Reset on Negative

**Two separate concerns:**

1. **Can the circuit be completed AT ALL?**
   `sum(gas) >= sum(cost)` → yes. Otherwise → no (-1).

2. **WHERE to start?**
   Walk from 0 to n-1. Track `tank` (running fuel balance).
   Whenever `tank < 0` → reset start to `i + 1`, tank to 0.
   After the loop, `start` is the answer.

```
totalGas = 0, totalCost = 0   (for feasibility)
tank = 0, start = 0           (for finding the start)

For i = 0 to n-1:
  totalGas += gas[i]
  totalCost += cost[i]
  tank += gas[i] - cost[i]

  If tank < 0:
    start = i + 1
    tank = 0

If totalGas < totalCost: return -1
Else: return start
```

```
Time:  O(n) -- single pass
Space: O(1) -- four variables
```

---

### 📜 The Scroll of the Circular Road

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⛽ The Greedy Ritual

```cpp
int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int n = gas.size();
    int totalGas = 0, totalCost = 0;
    int tank = 0;
    int start = 0;
```

Four variables:
-   `totalGas` / `totalCost` -- for the feasibility check.
-   `tank` -- running fuel balance from the current `start`.
-   `start` -- the candidate starting station.

---

### 🔁 Walk Every Station

```cpp
    for (int i = 0; i < n; i++) {
        totalGas += gas[i];
        totalCost += cost[i];
```

Accumulate totals for the feasibility check at the end.

---

### ⛽ Update the Tank

```cpp
        tank += gas[i] - cost[i];
```

At station `i`: gain `gas[i]` fuel, spend `cost[i]` to reach station `i+1`.
Net change = `gas[i] - cost[i]`.

If positive → we gained more than we spent. Tank grows.
If negative → we spent more than we gained. Tank shrinks.

---

### 🛑 Tank Goes Negative -- Reset

```cpp
        if (tank < 0) {
            start = i + 1;
            tank = 0;
        }
    }
```

If `tank < 0` → we can't reach station `i + 1` from the current `start`.

**Reset:** abandon the current start. Try `i + 1` as the new start.
Reset tank to 0 (starting fresh with an empty tank at the new station).

**Why skip everything between `start` and `i`?**

When we were at any station `j` between `start` and `i`,
our tank was ≥ 0 (otherwise we'd have reset earlier).
That means we arrived at `j` with EXTRA fuel from the `start→j` segment.

If starting from `start` (with that extra fuel) we still couldn't reach `i+1`,
then starting from `j` (with ZERO fuel, no extra) would be even worse.

So no station from `start` to `i` can be the answer. Skip them all.

> _"If the journey from start to here consumed more than it gave --
> even with the head start of accumulated fuel --
> then starting anywhere in between, with nothing,
> would only be worse.
> Abandon them all. Start fresh from the next station."_

---

### 🏁 Feasibility Check and Return

```cpp
    if (totalGas < totalCost) return -1;
    return start;
}
```

**If `totalGas < totalCost`:** the total fuel across ALL stations
is insufficient to cover the total cost. No starting point works. Return -1.

**Otherwise:** the circuit IS completable, and `start` is the unique answer.

**Why is `start` correct if feasible?**

If the total is sufficient, a valid start MUST exist.
Our algorithm eliminates all invalid starts (everything before the last reset).
The remaining candidate (`start`) is the only one left → it must be correct.

> _"If the total fuel can cover the total cost,
> then somewhere on this ring, a starting point exists.
> The last reset point is that place --
> the only station not eliminated by failure."_

---

### 🎺 The Trial of the Circular Road

```cpp
int main() {
    vector<int> g1 = {1, 2, 3, 4, 5};
    vector<int> c1 = {3, 4, 5, 1, 2};
    cout << canCompleteCircuit(g1, c1) << endl; // expected: 3

    vector<int> g2 = {2, 3, 4};
    vector<int> c2 = {3, 4, 3};
    cout << canCompleteCircuit(g2, c2) << endl; // expected: -1

    vector<int> g3 = {5, 1, 2, 3, 4};
    vector<int> c3 = {4, 4, 1, 5, 1};
    cout << canCompleteCircuit(g3, c3) << endl; // expected: 4

    return 0;
}
```

---

**Full trace for gas=[1,2,3,4,5], cost=[3,4,5,1,2]:**

```
Net at each station: gas[i] - cost[i]
  Station 0: 1-3 = -2
  Station 1: 2-4 = -2
  Station 2: 3-5 = -2
  Station 3: 4-1 = +3
  Station 4: 5-2 = +3

totalGas = 15, totalCost = 15. Feasible (15 >= 15).
```

| i | gas[i]-cost[i] | tank | tank < 0? | start |
|---|----------------|------|-----------|-------|
| 0 | -2             | -2   | ✓ Reset   | 1     |
| 1 | -2             | -2   | ✓ Reset   | 2     |
| 2 | -2             | -2   | ✓ Reset   | 3     |
| 3 | +3             | 3    | No        | 3     |
| 4 | +3             | 6    | No        | 3     |

Feasible → **Answer: 3** ✓

**Verification -- start at station 3:**

| Station | Tank before | + gas | - cost | Tank after |
|---------|-------------|-------|--------|------------|
| 3       | 0           | +4    | -1     | 3          |
| 4       | 3           | +5    | -2     | 6          |
| 0       | 6           | +1    | -3     | 4          |
| 1       | 4           | +2    | -4     | 2          |
| 2       | 2           | +3    | -5     | 0          |

Tank never goes negative. Circuit completed with tank = 0 at the end. ✓

---

**Full trace for gas=[2,3,4], cost=[3,4,3]:**

```
Net: -1, -1, +1.
totalGas = 9, totalCost = 10. 9 < 10 → IMPOSSIBLE.
```

**Answer: -1** ✓

Doesn't matter where we start -- total fuel < total cost.

---

**Trace for gas=[5,1,2,3,4], cost=[4,4,1,5,1]:**

```
Net: +1, -3, +1, -2, +3.
totalGas = 15, totalCost = 15. Feasible.
```

| i | net | tank | tank < 0? | start |
|---|-----|------|-----------|-------|
| 0 | +1  | 1    | No        | 0     |
| 1 | -3  | -2   | ✓ Reset   | 2     |
| 2 | +1  | 1    | No        | 2     |
| 3 | -2  | -1   | ✓ Reset   | 4     |
| 4 | +3  | 3    | No        | 4     |

**Answer: 4** ✓

---

## 🔍 Why the Reset Trick Works -- The Proof

**Claim:** if starting from `start` we fail at station `i` (tank < 0),
then no station `j` where `start ≤ j ≤ i` can be the answer.

**Proof:**

Let `prefix[j]` = net fuel accumulated from `start` to `j`.
Since we didn't reset before `j`, `prefix[j] ≥ 0` for all `j` between start and i.

If we start at `j` instead of `start`:
-   We lose the `prefix[j]` fuel we would have had.
-   Our tank at station `i` would be `tank_from_start - prefix[j]`.
-   Since `tank_from_start < 0` and `prefix[j] ≥ 0`:
    `tank_from_j = tank_from_start - prefix[j] ≤ tank_from_start < 0`.
-   Even worse! Station `j` fails too.

So all stations from `start` to `i` fail. Skip them all. ∎

---

## 🔍 Why We Don't Need to Simulate the Full Circuit

The feasibility check (`totalGas >= totalCost`) guarantees
that IF a valid start exists, the circuit CAN be completed from it.

The reset logic finds the ONLY candidate that wasn't eliminated.
If the total is sufficient, this candidate MUST work.

We don't need to verify by simulating the full circuit --
the math guarantees it.

---

## 🔍 Why the Solution Is Unique

The problem guarantees uniqueness. Intuitively:
if two starting points both worked, the "valley" (most negative prefix)
would need to be avoidable from two different directions --
which contradicts the structure of a single circular route
with a unique minimum prefix point.

---

## 🔍 Connection to Other Problems

| Problem              | Technique                          |
| -------------------- | ---------------------------------- |
| **Gas Station (this)** | Total check + reset on negative  |
| Maximum Subarray     | Kadane's (reset on negative)       |
| Circular Subarray    | Kadane's + circular trick          |

The "reset when negative" idea is similar to Kadane's algorithm.
In Kadane's: reset the running sum when it goes negative.
In Gas Station: reset the starting point when the tank goes negative.

---

### 🧠 Memory of the Circular Road Law

-   **Feasibility:** `sum(gas) < sum(cost)` → return -1 (impossible)
-   **Finding start:** track `tank`. If `tank < 0` → `start = i+1`, `tank = 0`
-   **Why reset works:** if start→i fails with accumulated fuel, j→i (with zero fuel) fails worse
-   **Answer:** `start` after the loop (the last reset point)
-   **No need to simulate full circuit** -- feasibility check guarantees correctness
-   **Similar to Kadane's** -- "reset on negative" pattern
-   **Time:** O(n). **Space:** O(1).
-   **Edge cases:**
    -   All gas == cost → start at 0 (tank never goes negative, stays 0)
    -   Single station: gas[0] >= cost[0] → return 0, else -1
    -   Answer is the last station (n-1) → start = n-1 after final reset

Thus is remembered the saga of **Gas Station**,
where the Oracle drove the circular road
with four variables in hand --
accumulating fuel, spending cost,
tracking the running tank balance --
and whenever the tank ran dry,
she abandoned all stations behind,
knowing that if she couldn't reach here with extra fuel,
starting in between with nothing would only be worse --
until the last reset point stood as the answer,
validated by the total fuel being sufficient
to complete the journey. ⛽🔄✨
