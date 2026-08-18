# Type Conversion in C

## 1. What is Type Conversion?

**Type conversion** means changing a value from one data type to another data type.

For example:

```text
int → float
int → double
float → int
char → int
```

Example:

```c
int num = 10;
float value = num;
```

Conceptually:

```text
10 (int)
   ↓
type conversion
   ↓
10.0 (float)
```

---

# 2. Why Do We Need Type Conversion?

Different data types store different kinds of values.

### `int`

Stores whole numbers:

```text
10
20
100
-5
```

### `float`

Stores decimal numbers:

```text
10.5
20.75
3.14
```

Sometimes we have a value in one type but need to use it as another type.

Example:

```c
int a = 10;
float b = a;
```

Here, the `int` value is converted to `float`.

---

# 3. Two Types of Type Conversion

There are two important types:

1. **Implicit Type Conversion**
2. **Explicit Type Conversion**

---

# 4. Implicit Type Conversion

**Implicit conversion means C automatically converts the value to another type.**

You don't explicitly tell C to perform the conversion.

Example:

```c
int a = 10;
float b = a;
```

Here:

```text
a = 10
type = int

        ↓
   automatic conversion

b = 10.0
type = float
```

C automatically converts the `int` value into a `float`.

### Example

```c
#include <stdio.h>

int main() {
    int a = 10;
    float b = a;

    printf("%f", b);

    return 0;
}
```

Output:

```text
10.000000
```

---

# 5. When Should I Use Implicit Conversion?

Use implicit conversion when:

* The conversion is safe.
* You don't need precise control over the conversion.
* You are converting from a type with less precision/range to a type with more precision/range.

For example:

```c
int a = 10;
double b = a;
```

This is generally safe:

```text
int
 ↓
double
```

The value `10` can be represented exactly as `10.0`.

### Common safe direction

```text
int → float
int → double
float → double
```

However, remember that **`int → float` is not guaranteed to preserve every possible large integer exactly**, because `float` has limited precision.

---

# 6. Explicit Type Conversion

**Explicit conversion means you manually tell C to convert a value to another type.**

This is also called **type casting**.

Syntax:

```c
(type)value
```

Example:

```c
int a = 10;

float b = (float)a;
```

Here:

```text
(float)
   ↓
"Convert a to float"
```

Conceptually:

```text
a = 10 (int)
      ↓
   (float)a
      ↓
10.0 (float)
```

---

# 7. Example of Explicit Conversion

```c
#include <stdio.h>

int main() {
    int a = 10;

    printf("%f", (float)a);

    return 0;
}
```

Output:

```text
10.000000
```

Here `(float)a` explicitly converts `a` to `float`.

---

# 8. When Should I Use Explicit Conversion?

Use explicit conversion when **you want control over the conversion**.

Especially when:

### 1. You want integer division to produce a decimal result

Consider:

```c
int a = 5;
int b = 2;

float result = a / b;
```

You might expect:

```text
2.5
```

But you get:

```text
2.000000
```

Why?

Because:

```text
a = int
b = int

int / int
   ↓
integer division
   ↓
2
```

You can explicitly convert one value:

```c
float result = (float)a / b;
```

Now:

```text
(float)5 / 2
      ↓
5.0 / 2
      ↓
2.5
```

---

# 9. Explicit Conversion Can Also Lose Data

Be careful when converting from a decimal type to an integer.

Example:

```c
float x = 10.75;

int y = (int)x;
```

Result:

```text
y = 10
```

The `.75` is removed.

Conceptually:

```text
10.75
  ↓
(int)
  ↓
10
```

This is called **loss of information/data**.

---

# 10. Implicit vs Explicit Conversion

| Feature                  | Implicit                     | Explicit                     |
| ------------------------ | ---------------------------- | ---------------------------- |
| Who performs conversion? | C automatically              | Programmer                   |
| Do you write a cast?     | No                           | Yes                          |
| Syntax                   | `float b = a;`               | `float b = (float)a;`        |
| Control                  | Less control                 | More control                 |
| Useful for               | Natural/safe conversions     | Intentional conversions      |
| Can lose data?           | Yes, depending on conversion | Yes, depending on conversion |

---

# 11. Very Important: `%f` Does NOT Perform Type Conversion

This was the issue in your original example.

You wrote:

```c
int num = 10;

printf("%f", num);
```

`num` is:

```text
num = 10
type = int
```

But `%f` tells `printf()` to expect a floating-point argument (`double` after the normal argument promotions).

So there is a mismatch.

### `%d`

```c
printf("%d", num);
```

Means:

```text
%d → expect integer
num → integer
✓ Correct
```

### `%f`

```c
printf("%f", num);
```

Means:

```text
%f → expect floating-point argument
num → integer
✗ Wrong
```

If you want to explicitly convert it:

```c
printf("%f", (float)num);
```

The cast performs the conversion; **`%f` only tells `printf()` how to interpret the argument.**

---

# 12. Format Specifier vs Type Conversion

Don't confuse these two concepts.

### Type conversion

Changes the type:

```c
(float)num
```

Conceptually:

```text
10 int
 ↓
(float)
 ↓
10.0 float
```

### Format specifier

Tells `printf()` what type of value it should receive/display:

```c
%d
%f
%c
%s
```

For example:

```c
printf("%d", num);
```

`%d` does not convert `num`.

It tells `printf()`:

> "The argument I'm giving you is an integer."

---

# 13. Practical Rule: When Should I Use Which?

### Use implicit conversion when:

The conversion is natural and safe enough for your situation.

Example:

```c
int age = 22;
double x = age;
```

You can let C handle it:

```c
double x = age;
```

---

### Use explicit conversion when:

You **specifically need a different type** for an operation.

Example:

```c
int a = 5;
int b = 2;

float result = (float)a / b;
```

You are deliberately saying:

> "I want floating-point division."

---

# 14. Easy Analogy

Imagine you have:

```text
₹10
```

and you want to write it as:

```text
₹10.00
```

The value is essentially the same, but the **representation/type is different**.

### Implicit

Someone automatically formats it:

```text
₹10 → ₹10.00
```

### Explicit

You specifically tell them:

```text
"Convert ₹10 to decimal format."
```

That's similar to:

```c
(float)10
```

---

# 15. Remember These 4 Things

```text
1. Type conversion = changing one data type into another.

2. Implicit conversion = C does it automatically.

3. Explicit conversion = YOU tell C to convert using a cast.

4. %f does NOT convert an int to float.
   It is only a printf format specifier.
```

### Most important example

```c
int num = 10;

printf("%d", num);          // Correct
printf("%f", num);          // Wrong: type mismatch
printf("%f", (float)num);   // Explicit conversion
```

The mental model to remember is:

```text
          TYPE CONVERSION
               │
       ┌───────┴───────┐
       ↓               ↓
   Implicit         Explicit
   automatic        programmer
       │               │
   int → float      (float)num
```
