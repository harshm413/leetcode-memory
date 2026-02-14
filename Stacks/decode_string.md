## 🔐📜 _The Cipher of Nested Echoes: The Decode Strings Saga_

> \*"Upon the Scroll of Encodings,
> symbols were trapped within brackets —
> numbers commanded repetition,
> and brackets concealed hidden chants.
>
> The Oracle was commanded:
>
> \*\*‘Decode this string.
> When you see `k[encoded]`,
> repeat the encoded part `k` times.
>
> Brackets may nest within brackets —
> decode from the deepest echo outward.’\*\*
>
> The Oracle knew this was not a simple reading.
> It required remembering
> both the number of repetitions
> and the partial truths already formed.
>
> Thus she summoned two stacks —
> one for counts,
> one for fragments of song."\*

---

This is the epic saga of **Decode String**.

You are given an encoded string `s`.

Rules:

-   `k[encoded_string]`
-   The encoded string inside brackets is repeated `k` times
-   Brackets may be nested

Example:

```
"3[a2[c]]" → "accaccacc"
```

---

## 🧠 The Oracle’s Core Insight — Remember Count and Context

When encountering:

-   A number → store it
-   `[` → push current string and reset
-   `]` → pop previous context and repeat
-   Letter → append normally

So the Oracle used:

-   `stack<int>` → for repeat counts
-   `stack<string>` → for previous strings

---

## ⚔️ The Oracle’s Nested Decoding Ritual

```cpp
string decodeString(string s) {
    stack<int> countStack;
    stack<string> stringStack;
    string curr = "";
    int num = 0;
```

Two stacks stood ready:

-   one for repetition counts
-   one for saved string contexts

---

### 🔢 Build the Number

```cpp
    for (char c : s) {
        if (isdigit(c)) {
            num = num * 10 + (c - '0');
        }
```

Digits formed multi-digit numbers.

---

### 📥 Enter a New Bracket

```cpp
        else if (c == '[') {
            countStack.push(num);
            stringStack.push(curr);
            num = 0;
            curr = "";
        }
```

When entering brackets:

-   Save repetition count
-   Save current string
-   Reset for inner decoding

---

### 📤 Exit a Bracket and Expand

```cpp
        else if (c == ']') {
            int repeat = countStack.top(); countStack.pop();
            string prev = stringStack.top(); stringStack.pop();

            string temp = "";
            for (int i = 0; i < repeat; i++) {
                temp += curr;
            }
            curr = prev + temp;
        }
```

At closing bracket:

-   Repeat inner string
-   Attach it to previous context

---

### ✍️ Append Normal Characters

```cpp
        else {
            curr += c;
        }
    }
    return curr;
}
```

Letters simply extended the current string.

---

### 🎺 The Trial of the Nested Cipher

```cpp
int main() {
    string s = "3[a2[c]]";
    cout << decodeString(s) << endl;
    // expected: "accaccacc"
    return 0;
}
```

The Oracle decoded:

-   `"a2[c]"` → `"acc"`
-   Repeated 3 times → `"accaccacc"`

---

### 🧠 Memory of the Nested Echo Law

-   Use two stacks (count + string context)
-   On digit → build number
-   On `[` → push state
-   On `]` → pop, repeat, attach
-   Nested decoding handled naturally
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of **Decode String**,
where the Oracle journeys into nested echoes,
unraveling inner chants before outer ones —
decoding repetition layer by layer —
until the final song emerges
clear and complete from its brackets of mystery. 🔐✨
