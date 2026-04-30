## 🔐🔢 _The Combination Maze: The Open the Lock Saga_

> \_"In the Vault of Four Wheels,
> a lock stood with four circular dials --
> each showing a digit from `0` to `9`.
>
> The lock started at `"0000"`.
> The target combination was known.
>
> Each move, the Oracle could turn
> ONE wheel ONE slot --
> either up (0→1→2→...→9→0)
> or down (0→9→8→...→1→0).
>
> But the vault was trapped.
> Certain combinations were **deadends** --
> if the lock ever showed a deadend combination,
> the wheels would jam forever.
>
> The Oracle was commanded:
>
> **'Find the minimum number of moves
> to reach the target from "0000",
> without ever passing through a deadend.
> If impossible -- return -1.'**
>
> The Oracle saw this as a graph problem:
>
> **Each combination is a NODE (10,000 possible states).
> Each single-wheel turn is an EDGE
> (8 neighbors per state -- 4 wheels × 2 directions).
> Deadends are BLOCKED nodes.
> Find the shortest path from "0000" to target.**
>
> Shortest path in an unweighted graph = **BFS**.
>
> BFS from "0000".
> Skip deadends.
> The first time we reach the target = minimum moves."\_

---

This is the saga of **Open the Lock**.

A lock has 4 circular wheels, each with digits `0-9`.
Starting at `"0000"`, find the **minimum turns** to reach `target`.

Each turn: rotate ONE wheel ONE slot (up or down).
Certain combinations are `deadends` -- cannot be visited.

Return minimum turns, or `-1` if impossible.

```
Input:  deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6

Input:  deadends = ["8888"], target = "0009"
Output: 1

Input:  deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"],
        target = "8888"
Output: -1
```

---

## 🧠 The Oracle's Core Insight -- BFS on a State Graph

This is NOT a grid problem. It's a **state-space graph**.

-   **Nodes:** all 10,000 combinations from `"0000"` to `"9999"`.
-   **Edges:** each combination connects to 8 neighbors
    (4 wheels × 2 directions = 8 possible single turns).
-   **Blocked nodes:** deadends.
-   **Start:** `"0000"`.
-   **Goal:** `target`.

Shortest path in an unweighted graph → **BFS**.

Each BFS level = one turn.
The first time we reach the target = minimum turns.

```
Time:  O(10,000 × 8) = O(1) -- fixed state space
Space: O(10,000) -- visited set + queue
```

---

### 📜 The Scroll of the Combination Vault

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <queue>
#include <unordered_set>
using namespace std;
```

---

## 🚫 Step One -- Build the Deadend Set

```cpp
int openLock(vector<string>& deadends, string target) {
    unordered_set<string> dead(deadends.begin(), deadends.end());
```

The Oracle loaded all deadends into a hash set
for O(1) lookup.

---

### 🛑 Edge Case -- Start Is a Deadend

```cpp
    if (dead.count("0000")) return -1;
```

If the starting combination itself is a deadend --
the lock jams immediately. Impossible.

---

### 🎯 Edge Case -- Already at Target

```cpp
    if (target == "0000") return 0;
```

If the target IS the starting position --
zero moves needed.

---

## 📦 Prepare the BFS

```cpp
    queue<string> q;
    unordered_set<string> visited;
```

Two tools:

-   `q` -- the BFS queue of combinations to explore.
-   `visited` -- tracks which combinations have been seen,
    preventing revisits and infinite loops.

---

### 🏁 Enqueue the Start

```cpp
    q.push("0000");
    visited.insert("0000");
```

The starting combination entered the queue.
It was immediately marked as visited.

---

### ⏱️ Initialize the Turn Counter

```cpp
    int turns = 0;
```

`turns` tracked how many moves had been made.
Each BFS level = one turn.

---

## 🌊 The BFS -- Level by Level

```cpp
    while (!q.empty()) {
```

The BFS continued as long as there were combinations to explore.

---

### 📸 Snapshot the Current Level

```cpp
        int size = q.size();
        turns++;
```

`size` captured how many combinations were in the current level.
`turns` incremented -- we were about to process one more level of moves.

> _"Every combination in this level
> is reachable in `turns - 1` moves.
> Their neighbors are reachable in `turns` moves."_

---

### 🔁 Process Every Combination in This Level

```cpp
        for (int i = 0; i < size; i++) {
            string curr = q.front();
            q.pop();
```

One combination was dequeued.
Its 8 neighbors would now be explored.

---

### 🔄 Generate All 8 Neighbors

```cpp
            for (int w = 0; w < 4; w++) {
```

The Oracle examined each of the **4 wheels**.

---

#### ⬆️ Turn This Wheel UP

```cpp
                string up = curr;
                up[w] = (curr[w] - '0' + 1) % 10 + '0';
```

The wheel at position `w` was turned up by 1.

`curr[w] - '0'` converted the character to a digit (0-9).
`+ 1` turned it up.
`% 10` wrapped `9` back to `0` (circular dial).
`+ '0'` converted back to a character.

> _"The wheel clicks forward.
> 0 becomes 1. 5 becomes 6. 9 wraps to 0.
> The circle has no end."_

---

#### ⬇️ Turn This Wheel DOWN

```cpp
                string down = curr;
                down[w] = (curr[w] - '0' + 9) % 10 + '0';
```

The wheel was turned down by 1.

`+ 9` instead of `- 1` avoids negative numbers.
`(x + 9) % 10` is equivalent to `(x - 1 + 10) % 10`.
`0` wraps to `9`. `5` becomes `4`.

> _"The wheel clicks backward.
> 0 wraps to 9. 3 becomes 2.
> Adding 9 mod 10 is the trick
> to avoid negative arithmetic."_

---

#### 🎯 Check If We Reached the Target

```cpp
                if (up == target) return turns;
                if (down == target) return turns;
```

If either neighbor IS the target --
we found the shortest path. Return `turns`.

BFS guarantees this is the minimum number of moves
because it explores all `k`-move states before any `k+1`-move states.

---

#### 🚫 Skip Deadends

```cpp
                if (!dead.count(up) && !visited.count(up)) {
                    visited.insert(up);
                    q.push(up);
                }
```

If the UP neighbor is NOT a deadend
and NOT already visited --
mark it visited and enqueue it for the next level.

---

```cpp
                if (!dead.count(down) && !visited.count(down)) {
                    visited.insert(down);
                    q.push(down);
                }
            }
        }
    }
```

Same check for the DOWN neighbor.

Deadends are treated as walls -- we never enter them.
Visited combinations are skipped -- no revisits.

> _"A deadend is a trap. Never step on it.
> A visited state is a road already walked.
> Never walk it again."_

---

## 🏁 Target Unreachable

```cpp
    return -1;
}
```

If the BFS exhausted all reachable states
without finding the target --
the target was unreachable (blocked by deadends).

Return `-1`.

---

### 🎺 The Trial of the Combination Vault

```cpp
int main() {
    vector<string> d1 = {"0201","0101","0102","1212","2002"};
    cout << openLock(d1, "0202") << endl; // expected: 6

    vector<string> d2 = {"8888"};
    cout << openLock(d2, "0009") << endl; // expected: 1

    vector<string> d3 = {"8887","8889","8878","8898","8788","8988","7888","9888"};
    cout << openLock(d3, "8888") << endl; // expected: -1

    return 0;
}
```

---

**Trace for deadends = ["8888"], target = "0009":**

Start: `"0000"`. Target: `"0009"`.

**Level 1 (turns = 1):**
Neighbors of `"0000"`:
-   Wheel 0 up: `"1000"`, down: `"9000"`
-   Wheel 1 up: `"0100"`, down: `"0900"`
-   Wheel 2 up: `"0010"`, down: `"0090"`
-   Wheel 3 up: `"0001"`, down: **`"0009"` ← TARGET!**

**Answer: 1** ✓

One turn of wheel 3 downward: `0 → 9`.

---

**Trace for target = "8888", all neighbors of "8888" are deadends:**

```
Deadends: 8887, 8889, 8878, 8898, 8788, 8988, 7888, 9888
```

These are ALL 8 neighbors of `"8888"`.
No path can ever reach `"8888"` --
every adjacent state is blocked.

BFS explores the entire reachable state space
but never reaches `"8888"`.

**Answer: -1** ✓

---

**Partial trace for target = "0202", deadends = ["0201","0101","0102","1212","2002"]:**

```
"0000" → "0100" → "0200" → "0201" DEAD → try other paths
"0000" → "0001" → "0002" → "0102" DEAD → try other paths
"0000" → ... → eventually reaches "0202" in 6 moves
```

BFS explores all 1-move states, then 2-move, then 3-move...
The deadends block certain paths, forcing longer detours.
The shortest unblocked path takes 6 moves.

**Answer: 6** ✓

---

## 🔍 Why BFS and Not DFS?

**BFS** explores states level by level (by number of moves).
The first time it reaches the target = shortest path. Guaranteed.

**DFS** would explore one deep path first.
It might find the target in 50 moves
when a 6-move path existed.
DFS does NOT guarantee shortest path.

**For shortest path in unweighted graphs → always BFS.**

---

## 🔍 Why Strings and Not Integers?

You could represent states as integers (0 to 9999)
and use `int visited[10000]` instead of a hash set.

```cpp
int state = d1 * 1000 + d2 * 100 + d3 * 10 + d4;
```

This would be faster (array lookup vs hash lookup)
but slightly more complex to generate neighbors.

Strings are cleaner for this problem --
each wheel is one character, easy to manipulate.
The hash set handles the visited tracking.

For interviews, strings are the standard approach.

---

## 🌐 The State-Space BFS Pattern

This problem is a template for **implicit graph BFS**:

| Problem              | State                | Neighbors              | Blocked?     |
| -------------------- | -------------------- | ---------------------- | ------------ |
| Open the Lock        | 4-digit combination  | 8 per state (4×2)     | Deadends     |
| Word Ladder          | A word               | All 1-letter changes   | Not in dict  |
| Sliding Puzzle       | Board configuration  | Swap blank with adj    | None         |
| Minimum Genetic Mutation | Gene string       | All 1-char mutations   | Not in bank  |

The graph is never built explicitly.
Neighbors are generated on the fly.
BFS finds the shortest path through the implicit graph.

---

### 🧠 Memory of the Combination Maze Law

-   **State-space BFS** -- each combination is a node, each turn is an edge
-   **8 neighbors per state:** 4 wheels × 2 directions (up/down)
-   **Wheel up:** `(digit + 1) % 10`. **Wheel down:** `(digit + 9) % 10`
-   **Deadends** loaded into a hash set for O(1) lookup
-   **Visited set** prevents revisiting states
-   **Level-by-level BFS:** `size = q.size()` snapshot, `turns++` per level
-   **Target check** on each generated neighbor -- return `turns` immediately
-   **Edge cases:**
    -   `"0000"` is a deadend → return -1
    -   Target is `"0000"` → return 0
    -   All neighbors of target are deadends → return -1
-   **Time:** O(10⁴ × 8) = O(1) -- fixed state space
-   **Space:** O(10⁴) -- visited set + queue

Thus is remembered the saga of **Open the Lock**,
where the Oracle navigated a maze of 10,000 combinations --
BFS-ing from `"0000"`, turning one wheel at a time,
avoiding the deadly traps of deadend states --
level by level, turn by turn,
until the target combination was reached
in the fewest possible moves --
or declared unreachable
when every path led to a dead end. 🔐🔢✨
