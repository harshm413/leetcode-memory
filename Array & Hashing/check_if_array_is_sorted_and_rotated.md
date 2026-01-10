## 🔄👑 _The Turning Crown of Order: The Check if Array Is Sorted and Rotated Saga_

> \*"In the Kingdom of Sequences,
> an ancient law defined harmony:
> numbers must rise in non-decreasing order.
>
> Yet a curious ritual existed —
> the **Royal Rotation** —
> where the crown of the sequence could be turned,
> moving the first part to the end,
> while preserving the inner order.
>
> The Oracle was summoned with a precise task:
>
> **‘Tell us whether this sequence
> is a sorted array that has merely been rotated.’**
>
> No rearranging was allowed.
> No rebuilding of the realm.
>
> Only observation,
> sharp eyes,
> and a single walk around the circle."\*

---

This is the saga of **Check if Array Is Sorted and Rotated**.

You are given an array `nums`.
Your task is to determine whether:

-   the array is **non-decreasingly sorted**, **or**
-   it can become so by **rotating** it any number of times.

A rotation means:

```
[1, 2, 3, 4, 5]
→ [3, 4, 5, 1, 2]
```

---

## 🧠 The Oracle’s Core Insight — The Law of Breaks

In a perfectly sorted (non-decreasing) array,
**there are zero drops** —
every next number is ≥ the previous.

In a sorted **and rotated** array,
there can be **at most one drop**.

A _drop_ occurs when:

```
nums[i] > nums[i+1]
```

More than one such break
means the crown has shattered —
the array is not a valid rotation of a sorted order.

---

## 👁️ The Oracle’s Circular Inspection Ritual

_Walk once around the ring_

```cpp
bool check(vector<int>& nums) {
    int n = nums.size();
    int drops = 0;
```

The Oracle prepared to count the breaks in harmony.

---

### 🔍 Inspect Every Neighbor (Circularly)

```cpp
    for (int i = 0; i < n; i++) {
        if (nums[i] > nums[(i + 1) % n]) {
            drops++;
            if (drops > 1) return false;
        }
    }
```

She compared each element with the next —
even the **last with the first**,
for the array formed a circle.

-   Each time order broke → `drops++`
-   If breaks exceeded one → the sequence was invalid

---

### 🏁 The Final Verdict

```cpp
    return true;
}
```

Zero or one break meant
the array was sorted or sorted-and-rotated.

---

### 🎺 The Trial of the Turning Crown

```cpp
int main() {
    vector<int> a = {3, 4, 5, 1, 2};
    vector<int> b = {2, 1, 3, 4};
    vector<int> c = {1, 2, 3};

    cout << check(a) << endl; // true
    cout << check(b) << endl; // false
    cout << check(c) << endl; // true
    return 0;
}
```

The Oracle judged:

-   `{3,4,5,1,2}` → one break → **valid rotation**
-   `{2,1,3,4}` → two breaks → **invalid**
-   `{1,2,3}` → no breaks → **already sorted**

---

### 🧠 Memory of the Turning Crown Law

-   Array is circularly checked
-   Count places where order decreases
-   At most **one drop** allowed
-   Compare last element with first
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Check if Array Is Sorted and Rotated**,
where the Oracle walks once around the crowned ring,
counting fractures in order —
and declares whether the sequence
is a rightful rotation of harmony
or a shattered crown beyond repair. 🔄👑✨
