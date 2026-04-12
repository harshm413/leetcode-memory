## 🔓📜 _The Unfolding of Nested Scrolls: The Decode String Saga_

> \_"In the Kingdom of Compressed Messages,
> scribes wrote in a strange shorthand:
>
> `3[abc]` meant `abcabcabc`.
> `2[a3[b]]` meant `abbbabbb`.
>
> Numbers told how many times to repeat.
> Brackets enclosed what to repeat.
> And brackets could nest inside brackets --
> layers within layers, scrolls within scrolls.
>
> The Oracle was commanded:
>
> **'Decode the compressed string.
> Unfold every nested repetition
> until the full message is revealed.'**
>
> The Oracle reached for a stack.
>
> She would walk the string character by character.
> When she met a `[`, she saved her current progress
> and the current repeat count onto the stack --
> then started fresh inside the bracket.
>
> When she met a `]`, she popped the saved state,
> repeated the inner string the required number of times,
> and appended it to the outer string.
>
> Layer by layer, the nested scrolls unfolded --
> innermost first, exactly as a stack demands."\_

---

This is the saga of **Decode String**.

Given an encoded string like `"3[a2[c]]"`:

-   `k[encoded_string]` means repeat `encoded_string` exactly `k` times.
-   Brackets may be nested.
-   `k` is always a positive integer.
-   Input is always valid.

Return the decoded string.

```
Input:  "3[a]2[bc]"
Output: "aaabcbc"

Input:  "3[a2[c]]"
Output: "accaccacc"

Input:  "2[abc]3[cd]ef"
Output: "abcabccdcdcdef"

Input:  "abc3[cd]xyz"
Output: "abccdcdcdxyz"
```

---

## 🧠 The Oracle's Core Insight -- Stack Saves Context at Every `[`

The nesting means we need to **pause** the current string,
dive into a bracket, decode it, then **resume** where we left off.

This is exactly what a stack does -- save and restore context.

Two stacks (or one stack of pairs):
-   **Count stack** -- the repeat count before each `[`.
-   **String stack** -- the string built so far before each `[`.

The algorithm:

```
Walk character by character:

Digit     → build the multi-digit number (k = k*10 + digit)
'['       → push current (string, count) onto stack, reset both
Letter    → append to current string
']'       → pop (prevString, count), current = prevString + current * count
```

When `]` is hit, the inner string is complete.
Pop the saved context, repeat the inner string, and append to the outer.

```
Time:  O(N × maxRepeat) -- output can be much larger than input
Space: O(N) -- the stacks
```

---

### 📜 The Scroll of the Compressed Kingdom

```cpp
#include <iostream>
#include <string>
#include <stack>
using namespace std;
```

---

## ⚔️ The Oracle's Decoding Ritual

```cpp
string decodeString(string s) {
    stack<string> strStack;
    stack<int> countStack;
    string current = "";
    int k = 0;
```

The Oracle prepared:

-   `strStack` -- saved outer strings, paused at each `[`.
-   `countStack` -- saved repeat counts for each bracket level.
-   `current` -- the string being built at the current nesting level.
-   `k` -- the repeat count being assembled digit by digit.

---

## 🔁 Walk the Encoded String

```cpp
    for (char c : s) {
```

Each character was inspected one by one.

---

### 🔢 Digit -- Build the Repeat Count

```cpp
        if (isdigit(c)) {
            k = k * 10 + (c - '0');
        }
```

Digits were accumulated into `k`.

`k * 10 + digit` handled multi-digit numbers:
`"12["` → first `k=1`, then `k=12`.

---

### 📥 `[` -- Save Context and Dive Deeper

```cpp
        else if (c == '[') {
            strStack.push(current);
            countStack.push(k);
            current = "";
            k = 0;
        }
```

A bracket opened. Time to dive into a nested level.

The Oracle saved the current state:
-   `current` (the string built so far at this level) → pushed to `strStack`.
-   `k` (the repeat count for this bracket) → pushed to `countStack`.

Then she reset both -- starting fresh inside the bracket.

> _"Before entering a nested scroll,
> the Oracle bookmarks her place in the outer scroll
> and begins a blank page within."_

---

### 📤 `]` -- Pop, Repeat, and Resume

```cpp
        else if (c == ']') {
            string outer = strStack.top(); strStack.pop();
            int count = countStack.top(); countStack.pop();
            string repeated = "";
            for (int i = 0; i < count; i++) {
                repeated += current;
            }
            current = outer + repeated;
        }
```

A bracket closed. The inner string was complete.

The Oracle popped the saved context:
-   `outer` -- the string that was being built before this bracket.
-   `count` -- how many times to repeat the inner string.

She repeated `current` exactly `count` times,
then appended it to `outer`.

The result became the new `current` --
resuming the outer level with the decoded inner content attached.

> _"The inner scroll is unrolled,
> repeated as commanded,
> and stitched back into the outer scroll
> exactly where the Oracle left off."_

---

### 🔤 Letter -- Append to Current String

```cpp
        else {
            current += c;
        }
    }
    return current;
}
```

Plain letters were simply appended to `current`.
They belonged to whatever nesting level was active.

When the entire string was processed,
`current` held the fully decoded message.

---

### 🎺 The Trial of the Nested Scrolls

```cpp
int main() {
    cout << decodeString("3[a]2[bc]")   << endl; // aaabcbc
    cout << decodeString("3[a2[c]]")    << endl; // accaccacc
    cout << decodeString("2[abc]3[cd]ef") << endl; // abcabccdcdcdef
    cout << decodeString("abc3[cd]xyz") << endl; // abccdcdcdxyz
    return 0;
}
```

---

**Full trace for `"3[a2[c]]"`:**

| Step | Char | k  | current | strStack | countStack | Action                    |
| ---- | ---- | -- | ------- | -------- | ---------- | ------------------------- |
| 1    | `3`  | 3  | ""      | []       | []         | Build k=3                 |
| 2    | `[`  | 0  | ""      | [""]     | [3]        | Save ("",3), reset        |
| 3    | `a`  | 0  | "a"     | [""]     | [3]        | Append 'a'                |
| 4    | `2`  | 2  | "a"     | [""]     | [3]        | Build k=2                 |
| 5    | `[`  | 0  | ""      | ["","a"] | [3,2]      | Save ("a",2), reset       |
| 6    | `c`  | 0  | "c"     | ["","a"] | [3,2]      | Append 'c'                |
| 7    | `]`  | 0  | "acc"   | [""]     | [3]        | Pop("a",2), "a"+"cc"="acc"|
| 8    | `]`  | 0  | "accaccacc" | []   | []         | Pop("",3), ""+"accaccacc" |

**Answer: "accaccacc"** ✓

Step 5: entering the inner bracket `2[c]`, saved `"a"` and count `2`.
Step 7: inner `"c"` repeated 2 times → `"cc"`, appended to saved `"a"` → `"acc"`.
Step 8: `"acc"` repeated 3 times → `"accaccacc"`.

---

**Full trace for `"3[a]2[bc]"`:**

| Step | Char | k | current   | strStack | countStack | Action                     |
| ---- | ---- | - | --------- | -------- | ---------- | -------------------------- |
| 1    | `3`  | 3 | ""        | []       | []         | k=3                        |
| 2    | `[`  | 0 | ""        | [""]     | [3]        | Save, reset                |
| 3    | `a`  | 0 | "a"       | [""]     | [3]        | Append                     |
| 4    | `]`  | 0 | "aaa"     | []       | []         | Pop("",3), "aaa"           |
| 5    | `2`  | 2 | "aaa"     | []       | []         | k=2                        |
| 6    | `[`  | 0 | ""        | ["aaa"]  | [2]        | Save, reset                |
| 7    | `b`  | 0 | "b"       | ["aaa"]  | [2]        | Append                     |
| 8    | `c`  | 0 | "bc"      | ["aaa"]  | [2]        | Append                     |
| 9    | `]`  | 0 | "aaabcbc" | []       | []         | Pop("aaa",2), "aaa"+"bcbc" |

**Answer: "aaabcbc"** ✓

---

**Trace for `"abc3[cd]xyz"`:**

| Step | Char | k | current        | Action                          |
| ---- | ---- | - | -------------- | ------------------------------- |
| 1-3  | abc  | 0 | "abc"          | Append letters                  |
| 4    | `3`  | 3 | "abc"          | k=3                             |
| 5    | `[`  | 0 | ""             | Save("abc",3), reset            |
| 6-7  | cd   | 0 | "cd"           | Append letters                  |
| 8    | `]`  | 0 | "abccdcdcd"    | Pop("abc",3), "abc"+"cdcdcd"    |
| 9-11 | xyz  | 0 | "abccdcdcdxyz" | Append letters                  |

**Answer: "abccdcdcdxyz"** ✓

Letters outside brackets are simply appended -- no repetition.

---

## 🔍 Why Two Stacks, Not One?

You could use a single `stack<pair<string, int>>` instead.
Two separate stacks make the logic slightly clearer --
one for strings, one for counts -- but both approaches are equivalent.

The key insight is that **every `[` creates a new context**
and **every `]` resolves it**. The stack depth equals the nesting depth.

---

### 🧠 Memory of the Nested Scroll Law

-   **Two stacks:** `strStack` (saved outer strings), `countStack` (repeat counts)
-   **`current`** = string being built at the current nesting level
-   **Digit** → `k = k * 10 + (c - '0')` (handles multi-digit numbers)
-   **`[`** → push `(current, k)`, reset both to `("", 0)`
-   **`]`** → pop `(outer, count)`, `current = outer + current * count`
-   **Letter** → `current += c`
-   After full traversal, `current` holds the decoded string
-   Stack depth = nesting depth of brackets
-   **Time:** O(output length) -- repetition can expand the string significantly
-   **Space:** O(N) -- stacks for nesting levels
-   **Edge cases:**
    -   No brackets → return the string as-is
    -   Multi-digit numbers like `100[a]` → handled by `k*10+digit`
    -   Deeply nested → stack handles arbitrary depth

Thus is remembered the saga of **Decode String**,
where the Oracle walked a compressed message
carrying two stacks of saved context --
pausing at every `[` to bookmark her place,
building the inner string fresh,
and at every `]` unrolling the inner scroll,
repeating it as commanded,
stitching it back into the outer message --
layer by layer, innermost first,
until the full decoded truth was revealed. 🔓📜✨
