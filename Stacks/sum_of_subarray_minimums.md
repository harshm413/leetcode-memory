## 🌋📜 _The Tribute of Every Valley: The Sum of Subarray Minimums Saga_

> \*"Across the Land of Numbers lay countless subarrays —
> small stretches, long stretches,
> overlapping realms of power.
>
> In each such realm,
> one element stood as the minimum —
> the deepest valley,
> the humblest ruler.
>
> The Oracle was commanded:
>
> \*\*‘For every possible subarray,
> take its minimum value.
>
> Now sum them all.’\*\*
>
> The foolish would generate every subarray —
> O(n²) realms,
> O(n³) effort.
>
> But the Oracle knew a deeper truth:
>
> Each element does not merely belong to one subarray —
> it reigns as minimum
> over a specific span.
>
> Count that span wisely,
> and the tribute is revealed."\*

---

This is the epic saga of **Sum of Subarray Minimums**.

You are given an array `arr`.

Your task:

-   For every contiguous subarray
-   Find its minimum
-   Return the sum of all such minimums
-   Answer modulo `1e9 + 7`

---

## 🧠 The Oracle’s Core Insight — Count Contribution of Each Element

Instead of generating all subarrays:

For each element `arr[i]`:

Find:

-   `left` → number of elements strictly greater to the left
-   `right` → number of elements greater or equal to the right

Then:

```
Contribution = arr[i] * left * right
```

Because:

-   `left` choices to start
-   `right` choices to end
-   All such subarrays will have `arr[i]` as minimum

To compute this efficiently,
the Oracle used **monotonic stacks**.

---

## ⚔️ The Oracle’s Boundary Ritual

_Find Previous Smaller and Next Smaller_

```cpp
int sumSubarrayMins(vector<int>& arr) {
    int n = arr.size();
    const int MOD = 1e9 + 7;
    vector<int> left(n), right(n);
    stack<int> st;
```

Two arrays:

-   `left[i]` → distance to previous smaller element
-   `right[i]` → distance to next smaller element

---

### 🌄 Count Left Span (Previous Smaller)

```cpp
    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] > arr[i]) {
            st.pop();
        }
        left[i] = st.empty() ? i + 1 : i - st.top();
        st.push(i);
    }
```

Strictly greater ensures no double counting.

---

### 🔁 Clear Stack for Right Span

```cpp
    while (!st.empty()) st.pop();
```

---

### 🌅 Count Right Span (Next Smaller or Equal)

```cpp
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && arr[st.top()] >= arr[i]) {
            st.pop();
        }
        right[i] = st.empty() ? n - i : st.top() - i;
        st.push(i);
    }
```

Using `>=` ensures correct handling of duplicates.

---

### 💰 Calculate Total Tribute

```cpp
    long long result = 0;
    for (int i = 0; i < n; i++) {
        result = (result + (long long)arr[i] * left[i] * right[i]) % MOD;
    }
    return result;
}
```

Each valley contributes according to
how many realms it rules.

---

### 🎺 The Trial of the Valleys

```cpp
int main() {
    vector<int> arr = {3,1,2,4};
    cout << sumSubarrayMins(arr) << endl;
    // expected: 17
    return 0;
}
```

The Oracle counted:

-   Subarrays and their minimums
-   Total tribute = **17**

---

### 🧠 Memory of the Valley Law

-   Each element contributes to multiple subarrays
-   Use monotonic stacks to find boundaries
-   Previous strictly smaller
-   Next smaller or equal
-   Contribution = value × left × right
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Sum of Subarray Minimums**,
where the Oracle does not enumerate every realm,
but instead crowns each valley
with the exact span of its dominion —
summing the tribute
of every subarray kingdom
with a single disciplined traversal. 🌋✨
