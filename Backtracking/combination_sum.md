## 🔥 _The Paths of Infinite Offerings: The Combination Sum Saga_

> \*"In the Realm of Sacred Numbers,
> the Oracle was given a target —
> an offering that must be matched exactly.
> Before her stood candidates, each a flame of power,
> and she was told:
> ‘Use any flame as many times as needed.
> Explore all paths.
> Find every combination whose total equals the sacred sum.’
>
> Thus she entered the Shrine of Backtracking,
> where choices are made…
> and unmade…
> until every valid path is revealed."\*

---

In the kingdom of ascending flames (candidate numbers),
the Oracle was tasked with finding **all combinations**
whose sum equaled the sacred **target**.

Unlike other quests, each flame could be taken **more than once** —
but the Oracle must keep combinations **non-decreasing**
and **unique** in structure.

This journey required **backtracking**:
a method where the Oracle selects a candidate,
explores all deeper possibilities,
and steps back when the path grows too large.

Thus began the saga of **Combination Sum**.

---

### 📜 The Scroll of Sacred Flames

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

Each flame was a simple number,
but together they formed a vast space of possibilities.

---

## 🔥 The Oracle’s Backtracking Ritual

```cpp
void backtrack(vector<int>& candidates, int target, int start,
               vector<int>& path, vector<vector<int>>& res) {
```

The Oracle carried:

-   **candidates**: the list of available flames
-   **target**: remaining offering amount
-   **start**: index where she may choose flames (to avoid duplicates)
-   **path**: the current combination
-   **res**: final list of valid offerings

---

### 🌟 Step 1 — If Target Reached, Record the Offering

```cpp
    if (target == 0) {
        res.push_back(path);
        return;
    }
```

When the remaining offering was exactly zero,
the Oracle bowed — the combination was complete.

---

### 🌑 Step 2 — Explore Further Flames

```cpp
    for (int i = start; i < candidates.size(); i++) {
        if (candidates[i] > target) break;
```

The Oracle walked through all flames from `start` onward.
Flames greater than the remaining target could not help
and were abandoned.

---

### 🔥 Step 3 — Choose the Flame

```cpp
        path.push_back(candidates[i]);
```

The Oracle took a flame into her hands,
adding it to the current offering.

---

### 🔁 Step 4 — Descend Deeper

_allowing reuse of the same flame_

```cpp
        backtrack(candidates, target - candidates[i], i, path, res);
```

She descended further,
using the same flame again (index `i`),
or exploring greater flames.

---

### 🔄 Step 5 — Backtrack

_undo the choice_

```cpp
        path.pop_back();
    }
}
```

When the path dead-ended or succeeded,
the Oracle gently returned the flame,
restoring the previous state.

---

## 🔮 The Invocation of the Sacred Quest

```cpp
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    sort(candidates.begin(), candidates.end());
    vector<vector<int>> res;
    vector<int> path;
    backtrack(candidates, target, 0, path, res);
    return res;
}
```

The flames were sorted to maintain order,
and the ritual began.

---

### 🎺 The Trial of the Sacred Flames

```cpp
int main() {
    vector<int> candidates = {2, 3, 6, 7};
    int target = 7;

    vector<vector<int>> ans = combinationSum(candidates, target);

    for (auto& combo : ans) {
        for (int x : combo) cout << x << " ";
        cout << endl;
    }
    return 0;
}
```

The Oracle found two offerings:

-   `2 2 3` (2+2+3 = 7)
-   `7` (7 = 7)

Both perfectly matched the sacred target.

---

### 🧠 Memory of the Backtracking Shrine

-   Sort candidates for pruning and consistency.
-   At each step:

    -   If `target == 0` → record combination
    -   If `target < 0` → stop

-   Use index `start` to avoid duplicated combinations.
-   Allow reuse of each candidate by passing `i` again.
-   **Time:** exponential, but efficient via pruning.
-   **Space:** depth of recursion stack.

Thus is remembered the saga of **Combination Sum**,
where the Oracle walks branching paths of fire,
choosing, unchoosing,
and revealing every combination
that satisfies the sacred offering. 🔥✨
