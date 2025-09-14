## 🔐 _The Locked Letters: The Encode & Decode Strings Saga_

> _"When words must cross borders of silence,
> they are given keys — lengths and seals —
> so a listener may open them exactly as they were closed."_

---

The kingdom kept many messages, some short, some long, some empty — each a secret to be safely carried across uncertain roads. The Couriers feared loss or fusion: when messages were strung together, how would a receiver know where one ended and the next began?

The Archivist devised a simple law: every message would wear a crown of its length and a single sigil `#`. With that crown no message would be mistaken for another — even blank messages would be honored. Thus began the ritual of **Encode & Decode**.

---

### 📜 The Tools of Sealing

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

The archivists gathered parchment (`string`) and ledgers (`vector<string>`). Their seal would be a human-readable crown: the length, a `#`, then the message itself.

---

### 🔏 The Sealing Spell — `encode`

```cpp
string encode(const vector<string>& strs) {
    string encoded;
```

For each scroll (string) to be sent, the Archivist wrote its length first. The length is the key — the exact count of letters the receiver must read. This key is trustworthy across storms and crowded roads.

---

```cpp
    for (const string& s : strs) {
        encoded += to_string(s.size()) + "#" + s;
    }
    return encoded;
}
```

So each message became: `"<len>#<content>"`. Concatenate them, and you have a single sealed bundle. Even an empty message becomes `0#`, which the receiver will honor as an empty gift.

---

### 🔓 The Unsealing Rite — `decode`

```cpp
vector<string> decode(const string& s) {
    vector<string> result;
    size_t i = 0;
```

At the receiving hearth, the reader scanned the bundle. Starting from the first character, the reader must first parse the digits of the crown to learn how many letters to take next.

---

```cpp
    while (i < s.size()) {
        // read the length until the '#'
        size_t j = i;
        while (j < s.size() && s[j] != '#') j++;
        int len = stoi(s.substr(i, j - i));
```

The reader walks from `i` to the sigil `#`, collecting digits that form the length. `stoi` translates this numeric crown into the exact number of letters that follow. If the crown says `0`, the reader will accept an empty string as the next message.

---

```cpp
        // move past '#'
        j++;
        // extract the next 'len' characters
        result.push_back(s.substr(j, len));
        // advance i to the start of the next encoded item
        i = j + len;
    }
    return result;
}
```

Armed with the length, the receiver plucks exactly `len` characters and places them into the result. Then the reader steps to the next crown, repeating until the bundle is exhausted — and every original message is restored, intact.

---

### 🎺 The Trial of Messages

```cpp
int main() {
    vector<string> original = {"hello", "", "world", "a#b#c", "😊"};
    string encoded = encode(original);
    cout << "Encoded: " << encoded << endl;
    vector<string> decoded = decode(encoded);
    cout << "Decoded:" << endl;
    for (auto& str : decoded) cout << "[" << str << "]" << endl;
    return 0;
}
```

A set of messages crossed the river: `"hello"`, an empty `""`, `"world"`, a tricky `"a#b#c"` that already wore sigils, and even an emoji `"😊"`. Each bore its crown so that when the receiver opened the bundle, every message returned exactly as it was — sigils, emptiness, and all.

---

### 🧠 Memory of the Seals

-   **Encoding rule:** `to_string(len) + "#" + content` — length is the immutable crown.
-   **Decoding steps:** read digits until `#` → parse length → read exactly `len` chars → repeat.
-   **Why this works:** The `#` separates the length from content and lengths are explicit; even empty strings are uniquely represented as `0#`.
-   **Edge cases handled:** strings containing `#` are safe because the crown is the numeric length placed before content; unicode (like emoji) is preserved if treated consistently by the runtime encoding.

Thus, messages travel sealed and whole — the Encode & Decode ritual ensures the voices of the kingdom arrive without confusion.
