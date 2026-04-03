## 🔐📜 _The Sealed Letters and the Length Crown: The Encode and Decode Strings Saga_

> \_"The Kingdom kept many messages —
> some short, some long, some empty,
> some containing strange symbols and sigils within.
>
> The Royal Courier was tasked with carrying
> all these messages across the Great Divide —
> a border where only a single, unbroken string could pass.
>
> The problem was clear:
> if the messages were simply joined together —
> 'hello' and 'world' becoming 'helloworld' —
> the receiver would have no way of knowing
> where one message ended and the next began.
>
> What if a message itself contained the separator?
> What if a message was empty?
> What if the separator appeared as content?
>
> Every naive delimiter — comma, pipe, space —
> could appear inside the messages themselves
> and cause catastrophic confusion.
>
> The Archivist devised the only foolproof solution:
>
> **Give every message a crown before it —
> a numeric label declaring its exact length,
> followed by a sacred sigil `#` as the seal.**
>
> The receiver, upon opening the bundle,
> would first read the length declared in the crown,
> then pluck exactly that many characters —
> no more, no less.
>
> The length was immutable.
> The length never lied.
> No matter what characters hid inside —
> sigils, pipes, hashes, even empty strings —
> the crown made every message findable."\_

---

This is the saga of **Encode and Decode Strings**.

Design two functions:

-   `string encode(vector<string>& strs)` — combine a list of strings into one single string for transmission.
-   `vector<string> decode(string& s)` — recover the original list of strings from the encoded string.

The encoding must handle:

```
Empty strings:         "" → encoded as "0#"
Strings with '#':   "a#b" → encoded as "3#a#b"
Any character set:  "😊" → encoded as "4#😊" (4 bytes in UTF-8)
```

```
Input:  ["hello", "", "world", "a#b#c"]
Encode: "5#hello0#5#world5#a#b#c"
Decode: ["hello", "", "world", "a#b#c"]
```

---

## 🧠 The Oracle's Core Insight — Length Before Content

The only delimiter that can never be confused with content is one that
**tells you how long the content is before you read it**.

```
Encoding rule:  for each string s → to_string(s.size()) + "#" + s
Decoding rule:  read digits until '#' → that is the length → read exactly that many chars
```

This works for **any** possible string content because:

-   The length is always a non-negative integer — unambiguous.
-   The `#` is just a separator between the length digits and the content.
-   Even if the content contains `#` characters — we already know the length,
    so we skip exactly `len` characters without caring what they contain.
-   Empty strings get length `0` and are encoded as `"0#"` — perfectly recoverable.

No escaping. No special characters. No edge cases.
The length is the key that opens every message.

---

### 📜 The Scroll of the Sealed Messages

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🔏 The Sealing Spell — `encode`

_Crown every message with its length and the sigil `#`_

```cpp
string encode(vector<string>& strs) {
    string encoded = "";
```

The Archivist opened an empty bundle — `encoded` — ready to receive messages.

---

```cpp
    for (const string& s : strs) {
        encoded += to_string(s.size()) + "#" + s;
    }
    return encoded;
}
```

For every message `s` in the list:

1. **Convert `s.size()` to a string** — the crown declaring the exact length.
2. **Append `"#"`** — the sacred sigil sealing the crown from the content.
3. **Append `s` itself** — the actual message content.

Each message became: `"<length>#<content>"`.
All messages were concatenated into one sealed bundle.

**Examples:**

```
"hello"  → "5#hello"
""       → "0#"
"a#b#c"  → "5#a#b#c"
"😊"     → "4#😊"
```

Even `"a#b#c"` — which contains the sigil `#` three times —
was safely encoded as `"5#a#b#c"`.
The decoder would read `5`, then pluck exactly 5 characters,
correctly recovering `"a#b#c"` without confusion.

---

## 🔓 The Unsealing Rite — `decode`

_Read the crown, pluck exactly that many characters, repeat_

```cpp
vector<string> decode(string& s) {
    vector<string> result;
    int i = 0;
```

The receiver opened the sealed bundle at position `i = 0`.
`result` would hold the recovered messages.

---

```cpp
    while (i < (int)s.size()) {
```

The receiver walked the bundle from left to right —
as long as there were characters remaining to process.

---

### 👑 Read the Crown — Find the Length

```cpp
        int j = i;
        while (s[j] != '#') j++;
        int len = stoi(s.substr(i, j - i));
```

`j` began at `i` and walked forward until it hit the `'#'` sigil.

All characters from `i` to `j-1` were the **length digits** —
the numeric crown declaring how many characters followed.

`stoi` translated these digits into the integer `len`.

> _"The receiver read the crown first.
> The crown is always digits.
> The `#` is always right after the digits.
> No ambiguity. No guessing."_

---

### 📩 Skip the Sigil and Pluck Exactly `len` Characters

```cpp
        j++;
        result.push_back(s.substr(j, len));
        i = j + len;
    }
    return result;
}
```

`j++` skipped past the `'#'` sigil — now `j` pointed to the first character of the message content.

`s.substr(j, len)` plucked **exactly `len` characters** —
the precise content of this message.

`i` jumped to `j + len` — the start of the next crown.

The recovered message was added to `result`.

This continued until the entire bundle was exhausted —
every crown read, every message recovered.

> _"The crown never lies.
> If the crown says 5 — exactly 5 characters are taken.
> If those 5 characters contain '#' — it does not matter.
> They are content. The crown has already spoken."_

---

### 🎺 The Trial of the Sealed Messages

```cpp
int main() {
    vector<string> original = {"hello", "", "world", "a#b#c", "😊"};

    string encoded = encode(original);
    cout << "Encoded: " << encoded << endl;
    // "5#hello0#5#world5#a#b#c4#😊"

    vector<string> decoded = decode(encoded);
    cout << "Decoded:" << endl;
    for (auto& str : decoded) {
        cout << "[" << str << "]" << endl;
    }
    // [hello]
    // []
    // [world]
    // [a#b#c]
    // [😊]
    return 0;
}
```

**Encode trace for `["hello", "", "world", "a#b#c"]`:**

| Message | Length | Crown | Encoded Chunk |
| ------- | ------ | ----- | ------------- |
| "hello" | 5      | "5#"  | "5#hello"     |
| ""      | 0      | "0#"  | "0#"          |
| "world" | 5      | "5#"  | "5#world"     |
| "a#b#c" | 5      | "5#"  | "5#a#b#c"     |

Full encoded string: `"5#hello0#5#world5#a#b#c"`

**Decode trace for `"5#hello0#5#world5#a#b#c"`:**

| i   | j walks to '#' | len | j++ points to | substr(j, len) | new i |
| --- | -------------- | --- | ------------- | -------------- | ----- |
| 0   | j=1            | 5   | j=2           | "hello"        | 7     |
| 7   | j=8            | 0   | j=9           | ""             | 9     |
| 9   | j=10           | 5   | j=11          | "world"        | 16    |
| 16  | j=17           | 5   | j=18          | "a#b#c"        | 23    |

Recovered: `["hello", "", "world", "a#b#c"]` ✓

The `#` characters inside `"a#b#c"` were completely invisible to the decoder —
the crown `5` had already declared the exact boundaries.

---

### 🧠 Memory of the Length Crown Law

-   **Encode:** `for each string s → encoded += to_string(s.size()) + "#" + s`
-   **Decode:** `while i < s.size()` → walk `j` from `i` to the `#` → `len = stoi(substr(i, j-i))` → `j++` → `result.push_back(substr(j, len))` → `i = j + len`
-   The `#` after the length is a fixed separator — it will **never** be confused with content because the decoder always reads digits first and only then looks for the `#`
-   Empty strings are perfectly handled: `"0#"` → len=0 → `substr(j, 0) = ""`
-   Strings containing `#` are perfectly handled: the crown declares length, content is extracted blindly
-   **Time:** O(N) encode — O(total characters) decode
-   **Space:** O(total characters) for the encoded bundle

Thus is remembered the saga of **Encode and Decode Strings**,
where the Archivist solved an ancient problem of ambiguity
by crowning every message with its own length —
the one identifier that could never be faked,
never confused, never lost —
so that every sealed message survived the journey
across the Great Divide
and was recovered exactly as it was sent. 🔐📜✨
