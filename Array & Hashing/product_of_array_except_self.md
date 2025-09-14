## 🐚 _The Tide of Silent Products: The Product of Array Except Self Saga_

> _"Each shell on the shore holds the sea beyond it,
> but never itself. To know what rests beneath a shell,
> listen to the tide that came before, and the tide that follows."_

---

Along the endless strand, a line of shells lay — each bearing a number. The villagers wished to know, for every shell, what the product of **all other shells** would be if that one were removed. They could multiply all numbers and divide by the shell’s value, but divisions break when zeros wash ashore. So the Sage devised a tide-watching ritual: measure the product of everything that comes **before** a shell (the incoming tide), and the product of everything that comes **after** it (the outgoing tide). Multiply those two — and you hold the product of all except the shell itself.

Thus began the rite of **Product of Array Except Self**.

---

### 📜 The Tools of the Shore

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The cartographers brought their parchments (`vector`) and ink. They would record two invisible currents — the prefix tide and the suffix tide — without ever dividing.

---

### 🌊 The Ritual of Two Tides

```cpp
vector<int> productExceptSelf(const vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, 1);
```

The Sage opened the ledger `res`, setting each place to `1` — the neutral crest of a wave.

---

```cpp
    // Prefix products (left tide)
    long long prefix = 1;
    for (int i = 0; i < n; ++i) {
        res[i] = static_cast<int>(prefix); // product of elements to the left of i
        prefix *= nums[i];
    }
```

Walking from left to right, the Sage recorded the incoming tide at each shell: `prefix` is the product of all shells seen so far (to the left). For position `i`, `res[i]` first stores that left-product. Then the tide swells by multiplying in `nums[i]` as the Sage moves on.

---

```cpp
    // Suffix products (right tide) and final combine
    long long suffix = 1;
    for (int i = n - 1; i >= 0; --i) {
        res[i] = static_cast<int>( (long long)res[i] * suffix ); // left * right
        suffix *= nums[i];
    }
    return res;
}
```

Then the Sage turned from right to left. `suffix` carried the outgoing tide — the product of all shells to the right. For each shell, multiply the stored left-tide by the right-tide to form the final answer: the product of all other shells. No division was needed; zeros behaved like mysterious coves, and were handled naturally by the tides.

---

### 🎺 The Trial of the Pebbles

```cpp
int main() {
    vector<int> nums = {1, 2, 3, 4};
    vector<int> ans = productExceptSelf(nums);
    for (int x : ans) cout << x << " ";
    cout << endl; // expected: 24 12 8 6
    return 0;
}
```

Four shells `1, 2, 3, 4` lined the sand. The rite revealed the product each would feel if removed: `24, 12, 8, 6`. The villagers nodded — the tides had spoken.

---

### 🧭 Memory of the Tide

-   **Two passes** — left-to-right to record prefix products; right-to-left to multiply suffix products.
-   **No division** — robust against zeros; the tides naturally account for missing shells.
-   **O(n) time, O(1) extra space** (ignoring the output array) — the Sage’s ritual is efficient and concise.
-   **Key idea:** result\[i] = product of elements left of i × product of elements right of i.

Thus is remembered the saga of the **Product of Array Except Self** — where each shell learns its fate by listening to the tides that pass it on both sides.
