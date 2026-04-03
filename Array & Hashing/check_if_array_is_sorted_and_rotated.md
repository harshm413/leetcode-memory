## 🔄👑 _The Turning Crown of Order: The Check if Array Is Sorted and Rotated Saga_

> \_"In the Kingdom of Sequences,
> an ancient law defined harmony:
> numbers must rise in non-decreasing order.
>
> Yet a curious ritual existed —
> the **Royal Rotation** —
> where the crown of the sequence could be turned,
> moving some soldiers from the front to the back,
> while preserving the inner order.
>
> The Oracle was summoned with a precise task:
>
> **'Tell us whether this sequence
> is a sorted array that has merely been rotated —
> or a disordered mess beyond redemption.'**
>
> No rearranging was allowed.
> No rebuilding of the realm.
>
> Only observation,
> sharp eyes,
> and a single walk around the circle.
>
> The Oracle's key insight was elegant:
>
> In a sorted array — there are ZERO drops
> (a drop is where a number is greater than its successor).
>
> In a sorted-and-rotated array —
> there is EXACTLY ONE drop
> (where the rotated tail meets the original front).
>
> More than one drop means the array
> is neither sorted nor a valid rotation.
>
> But the array must be viewed as a CIRCLE —
> the last element must be compared with the first —
> for the rotation wraps around."\_

---

This is the saga of **Check if Array Is Sorted and Rotated**.

You are given an array `nums`.

Return `true` if it was originally sorted in non-decreasing order,
then **rotated** some number of positions. Otherwise return `false`.

```
[3, 4, 5, 1, 2]  →  was [1,2,3,4,5] rotated left by 3  →  true
[2, 1, 3, 4]     →  two drops: (2>1) and (4>2 wraps)    →  false
[1, 2, 3]        →  already sorted, zero drops           →  true
```

A **rotation** means:

```
[1, 2, 3, 4, 5]
→ [3, 4, 5, 1, 2]  (moved first two to the back)
```

---

## 🧠 The Oracle's Core Insight — Count the Drops, Check the Circle

In a non-decreasing sorted array:

```
[1, 1, 2, 3, 4]   →  0 drops  →  valid (already sorted)
```

In a sorted-and-rotated array:

```
[3, 4, 5, 1, 2]   →  1 drop (5 > 1)  →  valid rotation
```

In any invalid array:

```
[2, 1, 3, 4]      →  2 drops (2>1 and 4>2 wraparound)  →  invalid
```

The **wraparound comparison** — last element vs first element —
is what makes this a circular check.

Without it, `[3, 4, 5, 1, 2]` would look like it has one drop
but we would miss checking if the last element `2` > first element `3`,
which it isn't — confirming only one real break point.

**Rule:**

```
Count all positions i where nums[i] > nums[(i+1) % n].
If count <= 1  →  return true
If count > 1   →  return false
```

The modulo `% n` wraps the last index around to index `0`,
making the comparison circular.

---

### 📜 The Scroll of the Circular Kingdom

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔍 The Oracle's Circular Inspection Ritual

_Walk once around the ring and count the breaks in harmony_

```cpp
bool check(vector<int>& nums) {
    int n = nums.size();
    int drops = 0;
```

The Oracle prepared to count the breaks in order —
`drops` began at zero.

---

```cpp
    for (int i = 0; i < n; i++) {
        if (nums[i] > nums[(i + 1) % n]) {
            drops++;
        }
    }
```

She walked every position from `0` to `n-1`.

At each position `i`, she compared the current element
with the **next** element — using `(i + 1) % n`
to wrap the last element back to index `0`.

Every time order broke — `nums[i] > nums[i+1]` —
a drop was recorded.

This circular walk was the secret:
it treated the array as a ring,
checking the natural boundary between
the rotated-back portion and the original front.

---

```cpp
    return drops <= 1;
}
```

**Zero drops** — the array was already sorted (non-decreasing).
A zero-rotation is technically a valid rotation.

**One drop** — exactly one break in order existed.
This is the signature of a valid sorted-and-rotated array.
The break is where the rotated tail reconnects with the original head.

**Two or more drops** — the array was neither sorted nor a valid rotation.
The crown had shattered — order had been broken in multiple places.

---

### 🎺 The Trial of the Turning Crown

```cpp
int main() {
    vector<int> a = {3, 4, 5, 1, 2};
    vector<int> b = {2, 1, 3, 4};
    vector<int> c = {1, 2, 3};
    vector<int> d = {1, 1, 1};
    vector<int> e = {3, 3, 1, 2, 2};

    cout << check(a) << endl; // 1 (true)  — one drop: 5>1
    cout << check(b) << endl; // 0 (false) — two drops: 2>1 and 4>2 (wrap)
    cout << check(c) << endl; // 1 (true)  — zero drops, already sorted
    cout << check(d) << endl; // 1 (true)  — all equal, zero drops
    cout << check(e) << endl; // 1 (true)  — one drop: 3>1
    return 0;
}
```

**Trace for `[3, 4, 5, 1, 2]` (n=5):**

| i   | nums[i] | nums[(i+1)%5] | Drop? | drops |
| --- | ------- | ------------- | ----- | ----- |
| 0   | 3       | 4             | No    | 0     |
| 1   | 4       | 5             | No    | 0     |
| 2   | 5       | 1             | Yes   | 1     |
| 3   | 1       | 2             | No    | 1     |
| 4   | 2       | 3 (wraps!)    | No    | 1     |

drops = 1 ≤ 1 → **true** ✓

**Trace for `[2, 1, 3, 4]` (n=4):**

| i   | nums[i] | nums[(i+1)%4] | Drop? | drops |
| --- | ------- | ------------- | ----- | ----- |
| 0   | 2       | 1             | Yes   | 1     |
| 1   | 1       | 3             | No    | 1     |
| 2   | 3       | 4             | No    | 1     |
| 3   | 4       | 2 (wraps!)    | Yes   | 2     |

drops = 2 > 1 → **false** ✓

The circular wrap at `i=3` comparing `4` with `2` (the first element)
was the critical check that exposed the second drop.

**Trace for `[3, 3, 1, 2, 2]` (n=5):**

| i   | nums[i] | nums[(i+1)%5] | Drop? | drops |
| --- | ------- | ------------- | ----- | ----- |
| 0   | 3       | 3             | No    | 0     |
| 1   | 3       | 1             | Yes   | 1     |
| 2   | 1       | 2             | No    | 1     |
| 3   | 2       | 2             | No    | 1     |
| 4   | 2       | 3 (wraps!)    | No    | 1     |

drops = 1 ≤ 1 → **true** ✓ (was `[1,2,2,3,3]` rotated left by 2)

---

### 🧠 Memory of the Turning Crown Law

-   A **drop** = position where `nums[i] > nums[(i+1) % n]`
-   **Zero drops** → already sorted (valid, rotation by 0)
-   **One drop** → valid sorted-and-rotated array (exactly one break point)
-   **Two or more drops** → invalid — the crown is shattered
-   The **`% n` modulo** is the entire secret — it wraps the last element comparison back to the first, making the inspection circular
-   Without the circular wrap, a valid rotation like `[3,4,5,1,2]` would appear valid but we would miss the critical check confirming the wrap-around is clean
-   **Time:** O(N) — one single pass around the circle
-   **Space:** O(1) — only one counter variable `drops`

Thus is remembered the saga of **Check if Array Is Sorted and Rotated**,
where the Oracle walked once around the crowned ring,
counting fractures in order —
using the modulo to close the circle
and compare the last soldier with the first —
declaring the sequence a rightful rotation of harmony
if at most one break existed,
or a shattered crown beyond repair
if two or more breaks were found. 🔄👑✨
