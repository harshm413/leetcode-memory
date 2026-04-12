## 🔢✂️ _The Sculptor of the Smallest Number: The Remove K Digits Saga_

> \_"In the Kingdom of Numerals,
> a great number was carved in stone --
> its digits stretching wide across the wall.
>
> The King commanded:
>
> **'Remove exactly K digits from this number.
> What remains must form the SMALLEST possible number.
> The relative order of the surviving digits must not change.'**
>
> The Oracle studied the number.
>
> She realized a greedy truth:
>
> **A larger digit followed by a smaller digit
> is a place where removing the larger digit
> makes the number smaller.**
>
> Walking left to right,
> whenever the current digit was smaller
> than the previous digit on the stack --
> the previous digit should be removed.
>
> This was a **monotonic increasing stack** --
> greedily removing digits that created
> a 'peak' in the number,
> up to K removals.
>
> After the walk, if removals remained,
> trim from the end (the largest surviving digits).
> Strip leading zeros. Handle the empty case."\_

---

This is the saga of **Remove K Digits**.

Given a string `num` representing a non-negative integer
and an integer `k`:

-   Remove `k` digits to make the result **as small as possible**.
-   Return the result as a string (no leading zeros).

```
Input:  num = "1432219", k = 3
Output: "1219"

Input:  num = "10200", k = 1
Output: "200"

Input:  num = "10", k = 2
Output: "0"
```

---

## 🧠 The Oracle's Core Insight -- Greedy Monotonic Stack

To minimize a number, we want the **leftmost digits to be as small as possible**
(the leftmost digit has the highest place value).

The greedy rule:

> **If the current digit is smaller than the stack's top,
> pop the top (remove that larger digit) --
> it's better to have the smaller digit earlier.**

Continue popping while:
-   Stack is not empty
-   Stack top > current digit
-   We still have removals left (`k > 0`)

After processing all digits:
-   If `k > 0` still -- remove from the end (the number is non-decreasing,
    so the largest digits are at the back).
-   Strip leading zeros.
-   If empty -- return `"0"`.

```
Time:  O(N) -- each digit pushed once, popped at most once
Space: O(N) -- the stack
```

---

### 📜 The Scroll of the Sculptor

```cpp
#include <iostream>
#include <string>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Digit-Sculpting Ritual

```cpp
string removeKdigits(string num, int k) {
    string st = "";
```

The Oracle used a **string as a stack** --
`st.back()` is the top, `st.push_back()` pushes, `st.pop_back()` pops.

A string-stack is cleaner here because the final result
IS the stack contents -- no need to drain and reverse.

---

## 🔁 Walk the Number Digit by Digit

```cpp
    for (char c : num) {
```

Each digit was processed left to right.

---

### 💥 Pop Larger Digits -- They Make the Number Bigger

```cpp
        while (!st.empty() && k > 0 && st.back() > c) {
            st.pop_back();
            k--;
        }
```

Before pushing the current digit,
the Oracle checked the stack's top.

If the top was **larger** than the current digit
and removals were still available --
pop it. That larger digit in an earlier position
was making the number unnecessarily big.

The while loop continued -- multiple consecutive larger digits
could be removed in one sweep.

> _"A '4' sitting before a '1' is a crime against smallness.
> Remove the '4'. Let the '1' take its place.
> The number shrinks dramatically."_

---

### 📥 Push the Current Digit

```cpp
        st.push_back(c);
    }
```

After all pops, the current digit joined the stack.
The stack maintained a (mostly) non-decreasing order.

---

## ✂️ Handle Remaining Removals

```cpp
    while (k > 0) {
        st.pop_back();
        k--;
    }
```

If the entire number was non-decreasing (like `"1234"`),
no pops happened during the walk.
Removals were still pending.

The largest digits were at the end --
so trim from the back.

---

## 🧹 Strip Leading Zeros

```cpp
    int start = 0;
    while (start < (int)st.size() && st[start] == '0') {
        start++;
    }
    string result = st.substr(start);
```

Leading zeros were not valid in the final number.
The Oracle found the first non-zero digit
and took the substring from there.

---

## 🏁 Return the Result

```cpp
    return result.empty() ? "0" : result;
}
```

If everything was stripped (all zeros or all removed),
return `"0"`.

---

### 🎺 The Trial of the Sculptor

```cpp
int main() {
    cout << removeKdigits("1432219", 3) << endl; // expected: 1219
    cout << removeKdigits("10200", 1)   << endl; // expected: 200
    cout << removeKdigits("10", 2)      << endl; // expected: 0
    cout << removeKdigits("112", 1)     << endl; // expected: 11
    return 0;
}
```

---

**Full trace for `"1432219"`, k = 3:**

| Step | Char | Stack before | st.back() > c? | Action              | k  | Stack after |
| ---- | ---- | ------------ | --------------- | ------------------- | -- | ----------- |
| 1    | '1'  | ""           | --              | Push '1'            | 3  | "1"         |
| 2    | '4'  | "1"          | '1' > '4'? No   | Push '4'            | 3  | "14"        |
| 3    | '3'  | "14"         | '4' > '3'? Yes  | Pop '4', k=2        | 2  | "1"         |
|      |      | "1"          | '1' > '3'? No   | Push '3'            | 2  | "13"        |
| 4    | '2'  | "13"         | '3' > '2'? Yes  | Pop '3', k=1        | 1  | "1"         |
|      |      | "1"          | '1' > '2'? No   | Push '2'            | 1  | "12"        |
| 5    | '2'  | "12"         | '2' > '2'? No   | Push '2'            | 1  | "122"       |
| 6    | '1'  | "122"        | '2' > '1'? Yes  | Pop '2', k=0        | 0  | "12"        |
|      |      | "12"         | k=0, stop        | Push '1'            | 0  | "121"       |
| 7    | '9'  | "121"        | k=0, no pops    | Push '9'            | 0  | "1219"      |

k = 0. No trailing removal needed. No leading zeros.

**Answer: "1219"** ✓

Three digits removed: '4', '3', and the second '2'.
Each was a local peak that made the number larger.

---

**Full trace for `"10200"`, k = 1:**

| Step | Char | Stack  | st.back() > c? | Action           | k | Stack after |
| ---- | ---- | ------ | --------------- | ---------------- | - | ----------- |
| 1    | '1'  | ""     | --              | Push '1'         | 1 | "1"         |
| 2    | '0'  | "1"    | '1' > '0'? Yes  | Pop '1', k=0     | 0 | ""          |
|      |      | ""     | empty            | Push '0'         | 0 | "0"         |
| 3    | '2'  | "0"    | k=0             | Push '2'         | 0 | "02"        |
| 4    | '0'  | "02"   | k=0             | Push '0'         | 0 | "020"       |
| 5    | '0'  | "020"  | k=0             | Push '0'         | 0 | "0200"      |

Strip leading zeros: "0200" → "200".

**Answer: "200"** ✓

Removing '1' let '0' take the leading position,
which was then stripped as a leading zero.

---

**Trace for `"10"`, k = 2:**

| Step | Char | Stack | Action           | k |
| ---- | ---- | ----- | ---------------- | - |
| 1    | '1'  | ""    | Push '1'         | 2 |
| 2    | '0'  | "1"   | Pop '1', k=1     | 1 |
|      |      | ""    | Push '0'         | 1 |

k = 1 remaining. Trim from back: pop '0'. Stack = "".
Result is empty → return **"0"** ✓

---

**Trace for `"112"`, k = 1:**

| Step | Char | Stack | st.back() > c? | Action    | k |
| ---- | ---- | ----- | --------------- | --------- | - |
| 1    | '1'  | ""    | --              | Push '1'  | 1 |
| 2    | '1'  | "1"   | '1' > '1'? No  | Push '1'  | 1 |
| 3    | '2'  | "11"  | '1' > '2'? No  | Push '2'  | 1 |

k = 1 remaining. Trim from back: pop '2'. Stack = "11".

**Answer: "11"** ✓

Non-decreasing number -- no greedy pops during the walk.
The largest digit (at the end) was trimmed.

---

## 🔍 Why String-as-Stack Instead of `stack<char>`?

Using a `string` as the stack avoids the final step
of draining a `stack<char>` into a string (which reverses order).

With a string:
-   `push_back(c)` = push
-   `pop_back()` = pop
-   `back()` = top
-   The string IS the result -- just strip leading zeros.

This is a common C++ trick for stack problems
where the final output is a string.

---

### 🧠 Memory of the Sculptor's Law

-   **Monotonic increasing stack** (string used as stack)
-   **Greedy rule:** while `st.back() > current digit` AND `k > 0` → pop
-   **Push** every digit after pops
-   **Remaining k:** trim from the back (number is non-decreasing at that point)
-   **Strip leading zeros** from the result
-   **Empty result** → return `"0"`
-   Leftmost digits matter most -- removing a large early digit
    has more impact than removing a large late digit
-   **Time:** O(N) -- each digit pushed once, popped at most once
-   **Space:** O(N) -- the string-stack
-   **Edge cases:**
    -   k = num.length → return "0"
    -   Non-decreasing number → trim k digits from the end
    -   Leading zeros after removal → stripped

Thus is remembered the saga of **Remove K Digits**,
where the Oracle sculpted the smallest possible number
by walking the digits with a monotonic stack --
popping every peak that made the number larger,
spending her K removals on the greediest cuts --
then trimming the tail if removals remained,
stripping the leading zeros,
and presenting the leanest, smallest number
that could be carved from the original stone. 🔢✂️✨
