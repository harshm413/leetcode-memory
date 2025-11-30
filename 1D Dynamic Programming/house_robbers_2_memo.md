## 🏰🔮 _The Ring of Cursed Mansions: The House Robber II Saga_

> \*"In the first saga, the Oracle walked a straight road of houses.
> But now she faced a far more dangerous realm —
> a **circular village**,
> where the first and last mansions were bound
> by ancient blood and magic.
>
> The curse remained:
> **never rob two neighboring houses.**
>
> But a new decree echoed through the moonlit air:
>
> **‘If you rob the first mansion…
> the last becomes forbidden.
> If you rob the last…
> the first must remain untouched.’**
>
> Thus the Oracle split fate itself
> into two parallel timelines,
> each explored with the sacred art of memoization."\*

---

The Thieves’ Guild brought a new challenge:
the houses now formed a **circle**,
meaning:

-   House 1 is adjacent to House 2
-   …and also adjacent to **House n**

Thus, the Oracle could no longer treat the problem
as a simple line.

She invoked the Law of Split Destinies:

> **Case A:** Rob from houses `0` to `n-2`
>
> **Case B:** Rob from houses `1` to `n-1`

The true answer was the richer of these two timelines.

Thus began the saga of **House Robber II**.

---

### 📜 The Scroll of Circular Mansions

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The circular village awaited inspection.

---

## 🔮 The Oracle’s Memoized Destiny Spell

_Rob a subrange of houses (line form)_

```cpp
int dfs(int i, int end, vector<int>& nums, vector<int>& memo) {
    if (i > end) return 0;
```

If she stepped past the allowed boundary,
there was no more fortune to take.

---

### 📒 The Ledger Check

```cpp
    if (memo[i] != -1) return memo[i];
```

If destiny had been written before,
she reclaimed it immediately.

---

### 🏚️ Two Futures at Each Mansion

```cpp
    int rob = nums[i] + dfs(i + 2, end, nums, memo);
    int skip = dfs(i + 1, end, nums, memo);
```

-   **Rob this mansion** → jump two forward
-   **Skip it** → move one forward

The tides of gold rose and fell by this choice.

---

### 📓 Record the Optimal Fate

```cpp
    memo[i] = max(rob, skip);
    return memo[i];
}
```

The sacred ledger ensured
no subproblem was ever solved twice.

---

## 🏛️ The Bifurcation of Destiny

_Two separate timelines decide the true answer_

```cpp
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];
```

If only one mansion existed,
the Oracle simply robbed it.

---

### 🌗 Case A — Rob from 0 to n-2

```cpp
    vector<int> memoA(n, -1);
    int caseA = dfs(0, n - 2, nums, memoA);
```

The Oracle avoided the last mansion
to ensure the circle remained unbroken.

---

### 🌘 Case B — Rob from 1 to n-1

```cpp
    vector<int> memoB(n, -1);
    int caseB = dfs(1, n - 1, nums, memoB);
```

Now she avoided the first mansion,
choosing a different future.

---

### 🌕 The Final Choice

```cpp
    return max(caseA, caseB);
}
```

The greater destiny between A and B
became the true fate of the Thieves’ Guild.

---

### 🎺 The Trial of the Cursed Circle

```cpp
int main() {
    vector<int> nums = {2, 3, 2};
    cout << rob(nums) << endl; // expected: 3
    return 0;
}
```

The Oracle examined two fates:

-   Case A: rob only house 1 → 2
-   Case B: rob only house 2 → 3

Thus the maximum gold was **3**.

---

### 🧠 Memory of the Circular Curse

-   Houses form a **circle** → first and last cannot both be robbed
-   Split into two timelines:

    -   Rob houses `[0 … n-2]`
    -   Rob houses `[1 … n-1]`

-   Use memoized DFS to compute each timeline
-   Final answer = max(caseA, caseB)
-   **Time:** O(n)
-   **Space:** O(n) memo + O(n) recursion stack

Thus is remembered the saga of **House Robber II**,
where the Oracle bends fate itself,
splits reality into two timelines,
and discovers the richest path through a cursed circle
of silent mansions. 🏰✨
