# Python Lambda Functions 

> **Simple Analogy:** A lambda is like a **sticky note with a quick instruction** — it's not a full recipe book (regular function), just a fast, one-line note you scribble for a single task and toss away. You wouldn't write a sticky note for cooking a 5-course meal, but "add 10% tip" fits perfectly.

---

## Table of Contents

- [What is a Lambda?](#what-is-a-lambda)
- [Syntax](#syntax)
- [Lambda vs def](#lambda-vs-def)
- [Where to Use Lambdas](#where-to-use-lambdas)
- [Examples](#examples)
  - [Basic Usage](#1-basic-usage)
  - [With map()](#2-with-map)
  - [With filter()](#3-with-filter)
  - [With sorted()](#4-with-sorted)
  - [With reduce()](#5-with-reduce)
  - [Inside a Function (Closure)](#6-inside-a-function-closure)
  - [Conditional Logic](#7-conditional-logic-ternary)
  - [Multiple Arguments](#8-multiple-arguments)
  - [No Arguments](#9-no-arguments)
- [Common Mistakes](#common-mistakes)
- [When NOT to Use Lambdas](#when-not-to-use-lambdas)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

---

## What is a Lambda?

A **lambda function** is an **anonymous**, **inline**, **single-expression** function defined using the `lambda` keyword.

- **Anonymous** → has no name (unless assigned to a variable)
- **Inline** → defined in one line
- **Single-expression** → can only contain one expression (no statements, no multi-lines)

Lambdas are part of Python's **functional programming** tools and are commonly paired with `map()`, `filter()`, `sorted()`, and `reduce()`.

---

## Syntax

```python
lambda arguments : expression
```

| Part          | Meaning                                      |
|---------------|----------------------------------------------|
| `lambda`      | Keyword to define the function               |
| `arguments`   | Zero or more comma-separated inputs          |
| `:`           | Separator between arguments and expression   |
| `expression`  | A single expression whose result is returned |

> **Note:** No `return` keyword needed — the expression's value is **automatically returned**.

---

## Lambda vs `def`

```python
# Regular function
def square(x):
    return x ** 2

# Equivalent lambda
square = lambda x: x ** 2
```

| Feature              | `def`                        | `lambda`                    |
|----------------------|------------------------------|-----------------------------|
| Name                 | Has a name                   | Anonymous (can be assigned) |
| Lines                | Multiple lines allowed       | Single expression only      |
| Statements           | Allowed (`if`, `for`, etc.)  | No                          |
| Docstrings           | Supported                    | Not supported               |
| Best used when       | Complex, reusable logic      | Short, throwaway logic       |

---

## Where to Use Lambdas

Lambdas shine when you need a **quick, short function passed as an argument** — especially with:

- `map()` — transform each item
- `filter()` — keep items matching a condition
- `sorted()` / `sort()` — custom sort keys
- `reduce()` — accumulate values
- GUI callbacks (e.g., `tkinter`)
- Dictionary dispatch tables

---

## Examples

### 1. Basic Usage

```python
# Assign to a variable and call it
greet = lambda name: f"Hello, {name}!"
print(greet("Alice"))  # Hello, Alice!

# Immediately invoked lambda (IIFE)
result = (lambda x, y: x + y)(3, 7)
print(result)  # 10
```

---

### 2. With `map()`

> **Analogy:** Like running every item on a conveyor belt through the same machine.

```python
numbers = [1, 2, 3, 4, 5]

# Square each number
squares = list(map(lambda x: x ** 2, numbers))
print(squares)  # [1, 4, 9, 16, 25]

# Convert Celsius to Fahrenheit
celsius = [0, 20, 37, 100]
fahrenheit = list(map(lambda c: (c * 9/5) + 32, celsius))
print(fahrenheit)  # [32.0, 68.0, 98.6, 212.0]
```

---

### 3. With `filter()`

> **Analogy:** Like a bouncer at a club — only lets in items that pass the condition.

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Keep only even numbers
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4, 6, 8, 10]

# Keep words longer than 4 characters
words = ["cat", "elephant", "dog", "python", "ox"]
long_words = list(filter(lambda w: len(w) > 4, words))
print(long_words)  # ['elephant', 'python']
```

---

### 4. With `sorted()`

> **Analogy:** Tell the sorting machine *which pocket* to look in when comparing items.

```python
# Sort list of tuples by second element
pairs = [(1, 'banana'), (2, 'apple'), (3, 'cherry')]
sorted_pairs = sorted(pairs, key=lambda x: x[1])
print(sorted_pairs)  # [(2, 'apple'), (1, 'banana'), (3, 'cherry')]

# Sort list of dicts by a specific key
people = [
    {"name": "Charlie", "age": 30},
    {"name": "Alice",   "age": 25},
    {"name": "Bob",     "age": 35},
]
by_age = sorted(people, key=lambda p: p["age"])
print(by_age)
# [{'name': 'Alice', 'age': 25}, {'name': 'Charlie', 'age': 30}, {'name': 'Bob', 'age': 35}]

# Sort strings case-insensitively
names = ["banana", "Apple", "cherry", "date"]
print(sorted(names, key=lambda s: s.lower()))
# ['Apple', 'banana', 'cherry', 'date']
```

---

### 5. With `reduce()`

> **Analogy:** Like a snowball rolling downhill — each step combines the current result with the next item.

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# Sum all elements
total = reduce(lambda acc, x: acc + x, numbers)
print(total)  # 15

# Find the maximum value
maximum = reduce(lambda a, b: a if a > b else b, numbers)
print(maximum)  # 5
```

---

### 6. Inside a Function (Closure)

> Lambdas can **capture variables** from their surrounding scope.

```python
def multiplier(factor):
    return lambda x: x * factor

double = multiplier(2)
triple = multiplier(3)

print(double(5))   # 10
print(triple(5))   # 15
print(double(10))  # 20
```

---

### 7. Conditional Logic (Ternary)

```python
# Using ternary expression inside lambda
classify = lambda x: "positive" if x > 0 else ("negative" if x < 0 else "zero")

print(classify(5))   # positive
print(classify(-3))  # negative
print(classify(0))   # zero

# Absolute value
absolute = lambda x: x if x >= 0 else -x
print(absolute(-7))  # 7
```

---

### 8. Multiple Arguments

```python
add        = lambda x, y: x + y
power      = lambda base, exp: base ** exp
full_name  = lambda first, last: f"{first} {last}"

print(add(3, 4))              # 7
print(power(2, 10))           # 1024
print(full_name("John", "Doe"))  # John Doe
```

---

### 9. No Arguments

```python
say_hello = lambda: "Hello, World!"
get_pi    = lambda: 3.14159

print(say_hello())  # Hello, World!
print(get_pi())     # 3.14159
```

---

## Common Mistakes

```python
# WRONG: Trying to use statements (raises SyntaxError)
# f = lambda x: if x > 0: return x    # WRONG

# CORRECT: Use ternary expression instead
f = lambda x: x if x > 0 else 0

# WRONG: Multi-line lambda (not supported)
# g = lambda x:
#     x + 1                            # WRONG

# CORRECT: Use def for multi-line logic
def g(x):
    result = x + 1
    return result

# WRONG: Overusing lambda where def is clearer
process = lambda data: [x**2 for x in data if x % 2 == 0]

# CORRECT: Better as a named def for readability
def process_evens(data):
    """Return squares of even numbers."""
    return [x**2 for x in data if x % 2 == 0]
```

---

## When NOT to Use Lambdas

Avoid lambdas when:

| Situation                              | Use instead        |
|----------------------------------------|--------------------|
| Function needs a docstring             | `def`              |
| Logic spans more than one expression   | `def`              |
| Function is reused in multiple places  | `def`              |
| Readability suffers                    | `def`              |
| You need `try/except` inside it        | `def`              |

> **Rule of thumb:** If you have to think twice to read a lambda, rewrite it as a `def`.

---

## Quick Reference Cheat Sheet

```python
# Syntax
lambda args: expression

# Basic
double  = lambda x: x * 2
add     = lambda x, y: x + y
no_arg  = lambda: 42

# With builtins
map(lambda x: x**2, iterable)
filter(lambda x: x > 0, iterable)
sorted(iterable, key=lambda x: x['field'])

from functools import reduce
reduce(lambda acc, x: acc + x, iterable)

# Conditional
classify = lambda x: "yes" if x else "no"

# Closure / factory
def make_adder(n):
    return lambda x: x + n

add5 = make_adder(5)
add5(10)  # 15

# IIFE (immediately invoked)
(lambda x, y: x + y)(3, 4)  # 7
```

---


