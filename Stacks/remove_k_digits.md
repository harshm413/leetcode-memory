## 🗡️📜 _The Forging of the Smallest Number: The Remove K Digits Saga_

> \*"In the Kingdom of Digits,
> numbers stood in a long procession —
> proud, towering, and sometimes excessive.
>
> The Oracle was commanded:
>
> **‘Remove exactly `k` digits
> so that the remaining number
> becomes the smallest possible.’**
>
> But she could not rearrange them.
> She could only remove.
>
> And she knew this truth:
>
> A larger digit standing before a smaller one
> is a weakness in the formation.
>
> Remove such pride early,
> and the kingdom becomes humble and minimal."\*

---

This is the epic saga of **Remove K Digits**.

You are given:

-   a string `num` representing a non-negative integer
-   an integer `k`

Your task:

-   Remove exactly `k` digits
-   Return the **smallest possible number**
-   Remove leading zeros
-   If empty → return `"0"`

---

## 🧠 The Oracle’s Core Insight — Remove Peaks First

To minimize the number:

-   If a digit is **greater than the next digit**,
    removing it makes the number smaller.
-   Therefore, use a **monotonic increasing stack**
-   Whenever the current digit is smaller than the top,
    remove the larger digit (if k > 0)

---

## ⚔️ The Oracle’s Digit Forging Ritual

```cpp
string removeKdigits(string num, int k) {
    vector<char> st;
```

The stack stores digits of the forming number.

---

### 🔥 Remove Larger Previous Digits

```cpp
    for (char c : num) {
        while (!st.empty() && k > 0 && st.back() > c) {
            st.pop_back();
            k--;
        }
        st.push_back(c);
    }
```

If current digit is smaller,
remove the larger one before it.

---

### 🗡️ If Removals Still Remain

```cpp
    while (k > 0 && !st.empty()) {
        st.pop_back();
        k--;
    }
```

If the number was already increasing,
remove from the end.

---

### 🧹 Remove Leading Zeros

```cpp
    string result = "";
    for (char c : st) {
        if (!(result.empty() && c == '0')) {
            result += c;
        }
    }
```

Leading zeros are not allowed.

---

### 🏁 Handle Empty Result

```cpp
    return result.empty() ? "0" : result;
}
```

If all digits were removed,
return `"0"`.

---

### 🎺 The Trial of the Forged Number

```cpp
int main() {
    string num = "1432219";
    int k = 3;
    cout << removeKdigits(num, k) << endl;
    // expected: "1219"
    return 0;
}
```

The Oracle removed:

-   4
-   3
-   2

The smallest possible number emerged:
**1219**

---

## 🧠 Memory of the Forging Law

-   Use monotonic increasing stack
-   Remove larger previous digits first
-   If k remains → remove from end
-   Strip leading zeros
-   Return "0" if empty
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Remove K Digits**,
where the Oracle tempers a number
like a blacksmith forging steel —
shaving away excess pride,
removing towering peaks before valleys —
until only the smallest possible form
remains gleaming in precision. 🗡️✨
