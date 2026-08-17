# Segmentation Fault in C

## 1. What is a Segmentation Fault?

A **segmentation fault** is a **runtime error** that occurs when a program tries to access memory that it is not allowed to access.

> **Simple definition:** The program tried to read from or write to an invalid/inaccessible memory location.

---

## 2. Simple Analogy

Think of computer memory like an apartment building:

```text
Memory
┌─────────────────────┐
│ Room 100 → n1       │
│ Room 101 → n2       │
│ Room 102 → c        │
│ Room 103 → other    │
│ ...                 │
└─────────────────────┘
```

Your program is allowed to access certain memory locations.

If the program tries to access a memory location that it does not have permission to use:

```text
Program
   ↓
Invalid memory access
   ↓
Operating System blocks it
   ↓
💥 Segmentation Fault
```

---

# 3. Example: `scanf()` Causing Segmentation Fault

Incorrect code:

```c
#include <stdio.h>

int main() {
    int n1;

    printf("Enter the number: ");
    scanf("%i", n1);

    return 0;
}
```

The problem is:

```c
scanf("%i", n1);
```

`scanf()` needs the **address of the variable**, not its value.

Correct:

```c
scanf("%i", &n1);
```

---

# 4. `n1` vs `&n1`

Suppose:

```c
int n1 = 10;
```

There are two important concepts:

```text
n1   → value stored in n1
&n1  → memory address of n1
```

For example:

```text
Variable: n1
Value:    10
Address:  5000
```

Therefore:

```text
n1  = 10
&n1 = 5000
```

---

# 5. Why Does `scanf()` Need `&`?

`scanf()` has to **store the user's input inside the variable**.

For example:

```c
int n1;

scanf("%i", &n1);
```

The user enters:

```text
25
```

`scanf()` needs to know:

> "Where should I put 25?"

`&n1` gives it the location of `n1`.

```text
        &n1
         ↓
    Memory address
         ↓
      ┌──────┐
      │  n1  │
      │  25  │
      └──────┘
```

---

# 6. What Happens Without `&`?

Incorrect:

```c
scanf("%i", n1);
```

Suppose `n1` contains some garbage/uninitialized value:

```text
n1 = 348923
```

`scanf()` may interpret that value as an address.

Conceptually:

```text
scanf()
   ↓
Uses 348923 as a memory address
   ↓
Attempts to write user input there
   ↓
Address may be invalid/inaccessible
   ↓
💥 Segmentation Fault
```

The exact behavior is **undefined**, so a segmentation fault is a common possible result.

---

# 7. Correct Program

```c
#include <stdio.h>

int main() {
    int n1, n2;

    printf("Enter the number: ");
    scanf("%i", &n1);

    printf("Enter the second number: ");
    scanf("%i", &n2);

    int c = n1 + n2;

    printf("%i", c);

    return 0;
}
```

Example:

```text
Enter the number: 10
Enter the second number: 20
30
```

---

# 8. `scanf()` vs `printf()`

A very important rule:

### `scanf()`

Usually needs the **address**:

```c
scanf("%d", &n1);
```

### `printf()`

Usually needs the **value**:

```c
printf("%d", n1);
```

Remember:

```text
scanf  → WHERE should I store the input?
         → address → &variable

printf → WHAT value should I display?
         → value → variable
```

---

# 9. Common Causes of Segmentation Faults

## A. Invalid or uninitialized pointer

```c
int *p;

*p = 10;
```

`p` has not been initialized to point to valid memory.

Potential result:

```text
💥 Segmentation Fault
```

---

## B. Dereferencing a NULL pointer

```c
int *p = NULL;

*p = 10;
```

`p` points to `NULL`, not to a valid integer object.

Trying to access:

```c
*p
```

can cause a segmentation fault.

---

## C. Accessing an array incorrectly

```c
int arr[3] = {10, 20, 30};

printf("%d", arr[100]);
```

The valid indexes are:

```text
arr[0]
arr[1]
arr[2]
```

But:

```text
arr[100]  → outside the array
```

This is an **out-of-bounds access** and causes undefined behavior, which can include a segmentation fault.

---

## D. Using memory after `free()`

```c
int *p = malloc(sizeof(int));

*p = 10;

free(p);

*p = 20;
```

After:

```c
free(p);
```

the allocated memory is no longer yours to use.

Accessing it afterward is called **use-after-free** and causes undefined behavior.

---

# 10. Error Classification

| Error | Meaning |
|---|---|
| Syntax error | Code violates C syntax |
| Compilation error | Compiler cannot successfully compile the program |
| Runtime error | Problem occurs while the program is running |
| Segmentation fault | Invalid/inaccessible memory access |
| Logic error | Program runs but produces the wrong result |

Your `scanf()` example follows this flow:

```text
Source Code
    ↓
Compiler
    ↓
Compilation succeeds ✅
    ↓
Program starts
    ↓
scanf("%i", n1)
    ↓
Incorrect memory address usage
    ↓
Invalid memory access
    ↓
💥 Segmentation Fault
```

---

# 11. How to Debug a Segmentation Fault

When you see:

```text
Segmentation fault
```

ask:

### 1. Am I using an uninitialized pointer?

```c
int *p;
```

### 2. Am I dereferencing a NULL pointer?

```c
*p
```

when:

```c
p == NULL
```

### 3. Am I accessing an array outside its bounds?

```c
arr[100]
```

when the array only contains 3 elements.

### 4. Did I use memory after `free()`?

```c
free(p);
*p = 10;
```

### 5. Did I forget `&` in `scanf()`?

Incorrect:

```c
scanf("%d", n1);
```

Correct:

```c
scanf("%d", &n1);
```

---

# 12. Key Concept

A segmentation fault is fundamentally about **memory access**.

```text
                MEMORY
                   │
        ┌──────────┴──────────┐
        │                     │
   Valid access          Invalid access
        │                     │
        ↓                     ↓
   Program continues      OS blocks access
                              │
                              ↓
                      💥 Segmentation Fault
```

## One-line definition

> **Segmentation fault = a runtime failure caused by an invalid or unauthorized memory access.**

## Easy memory trick

```text
scanf()  → address → &variable
printf() → value   → variable
```
