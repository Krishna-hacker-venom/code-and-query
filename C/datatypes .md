# C Programming — My Mistakes & Notes

## 1. Declaring Different Data Types

### ❌ Mistake

```c
int num, float pointss, char character;
```

This is invalid C syntax because different data types cannot be declared this way in a single declaration.

### ✅ Correct

```c
int num;
float pointss;
char character;
```

---

## 2. `char` vs String

### ❌ Mistake

```c
char character = "krishna";
```

`char` can store only **one character**.

### ✅ One character

```c
char c = 'k';
```

Use **single quotes** for a character.

### ✅ String

```c
char ca[] = "krishna";
```

Use a character array to store a string.

Use **double quotes** for a string.

### Remember

```text
'k'          → char
"krishna"    → string
```

---

## 3. `printf()` Format Specifiers

Every format specifier in `printf()` needs a corresponding value.

### Format specifiers

| Specifier | Data type         | Example     |
| --------- | ----------------- | ----------- |
| `%i`      | `int`             | `2`         |
| `%f`      | `float`           | `2.5`       |
| `%c`      | `char`            | `'k'`       |
| `%s`      | string / `char[]` | `"krishna"` |

### ❌ Mistake

```c
printf("this is number:%i", n, "this is float");
```

The format string only contains `%i`, but two values are provided.

`"this is float"` has no format specifier, so it is not printed.

### ✅ Correct

```c
printf("this is number: %i\n", n);
printf("this is float: %f\n", f);
```

---

## 4. Garbage Values in `printf()`

### ❌ Mistake

```c
printf("this is number :%i\n this is float :%f\n this is character:%c\n this is char array:%s", ca);
```

The format string asks for **4 values**:

```text
%i → int
%f → float
%c → char
%s → string
```

But only **one value** was provided:

```c
ca
```

This causes **undefined behavior**, which can result in garbage values.

### ✅ Correct

```c
printf(
    "this is number: %i\n"
    "this is float: %f\n"
    "this is character: %c\n"
    "this is char array: %s\n",
    n, f, c, ca
);
```

The order must match:

```text
%i → n
%f → f
%c → c
%s → ca
```

---

# Important `printf()` Rule

Think of the format string as a set of slots:

```c
printf("%i %f %c %s", n, f, c, ca);
```

The matching is:

```text
%i  → n
%f  → f
%c  → c
%s  → ca
```

The **number and type of arguments should match the format specifiers**.

---

# My Correct Example

```c
#include <stdio.h>

int main() {
    int n = 2;
    float f = 2.5;
    char c = 'k';
    char ca[] = "krishna";

    printf("this is number: %i\n", n);
    printf("this is float: %f\n", f);
    printf("this is character: %c\n", c);
    printf("this is char array: %s\n", ca);

    return 0;
}
```

## Output

```text
this is number: 2
this is float: 2.500000
this is character: k
this is char array: krishna
```

---

# Quick Revision

```text
int      → %i
float    → %f
char     → %c
string   → %s
```

### Quotes

```text
'k'          → character
"krishna"    → string
```

### `printf()`

```c
printf("format", values);
```

The values must match the format specifiers in **number, order, and type**.
