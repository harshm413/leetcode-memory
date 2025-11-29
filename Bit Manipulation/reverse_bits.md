## 🔁 _The Mirror of Binary Winds: The Reverse Bits Saga_

> \*"In the Whispering Realm of Binary,
> a number’s essence is carried by 32 winds —
> each wind a bit, blowing either quiet (0) or fierce (1).
>
> One day the Oracle was commanded:
> ‘Reverse the winds.
> Let the last become first,
> the first become last,
> until the entire pattern faces its perfect mirror.’
>
> Thus she entered the Chamber of Bit Mirrors,
> where each gust is flipped into position,
> and the binary soul is reversed."\*

---

In the ancient binary kingdoms,
every number was written in **32 sacred bits**,
fixed in length, aligned under the watch of time.

The Oracle was given a number `n`
and tasked with reversing its bit order.

If the winds read:

`b₃₁ b₃₀ ... b₂ b₁ b₀`

She must return:

`b₀ b₁ ... b₂₉ b₃₀ b₃₁`

The spirit of the number would stand opposite itself —
a perfect mirror.

Thus began the saga of **Reverse Bits**.

---

### 📜 The Scroll of Binary Winds

```cpp
#include <iostream>
using namespace std;
```

The bits lay silent until the Oracle touched them.

---

## 🌬️ The Oracle’s Mirror Ritual

_Shift, capture, place, repeat 32 times_

```cpp
uint32_t reverseBits(uint32_t n) {
    uint32_t res = 0;
```

She began with an empty vessel —
a new binary spirit with no winds yet.

---

### 🌪️ Step 1 — One Wind at a Time

```cpp
    for (int i = 0; i < 32; i++) {
        res <<= 1;
        res |= (n & 1);
        n >>= 1;
    }
    return res;
}
```

In each of the 32 iterations:

1. **Shift res left** — preparing space for a new wind.
2. **Extract the lowest bit of n** (`n & 1`).
3. **Place that bit into res** using OR.
4. **Shift n right** to inspect the next wind.

Like turning pages of a scroll from end to start,
she rebuilt the binary sequence in exact reverse.

When all 32 winds had been repositioned,
the reversed spirit was complete.

---

### 🎺 The Trial of the Bit-Mirror Chamber

```cpp
int main() {
    uint32_t n = 0b00000010100101000001111010011100;
    uint32_t reversed = reverseBits(n);

    cout << reversed << endl;
    return 0;
}
```

The Oracle reversed the winds,
and the binary scroll faced its perfect reflection.

---

### 🧠 Memory of the Mirror Ritual

-   `res <<= 1` — shift space for new bit
-   `res |= (n & 1)` — place lowest bit of n into res
-   `n >>= 1` — move to next bit of original number
-   Repeat 32 times
-   **Time:** O(32) → constant
-   **Space:** O(1)

Thus is remembered the saga of **Reverse Bits**,
where the Oracle rewinds the binary winds,
transforming the last into first
until a perfect mirror spirit stands reborn. 🔁✨
