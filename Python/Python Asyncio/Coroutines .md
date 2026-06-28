# Why Does `print(result)` Show a Coroutine Object Instead of the Function Output?

One of the most confusing parts of learning `asyncio` is understanding why this code:

```python
import asyncio

async def greet_after_delay():
    print("Let's start")
    await asyncio.sleep(5)
    print("End")

result = greet_after_delay()

print(result)
```

prints something like:

```text
<coroutine object greet_after_delay at 0x00000123456789>
```

instead of:

```text
Let's start
End
```

---

## Understanding What a Variable Stores

A variable stores **whatever value is returned by the expression on the right side of the assignment operator (`=`).**

### Normal Function

```python
def add():
    return 10

result = add()

print(result)
```

Execution Flow:

```text
add()

↓

Function executes immediately

↓

Returns 10

↓

result = 10

↓

print(result)

↓

10
```

Here, `result` stores the integer `10`.

---

### Async Function

```python
import asyncio

async def add():
    return 10

result = add()

print(result)
```

Execution Flow:

```text
add()

↓

Function DOES NOT execute

↓

Python creates a Coroutine Object

↓

result = Coroutine Object

↓

print(result)

↓

<coroutine object add at 0x...>
```

Notice that `10` is **never returned**, because the coroutine has never been executed.

---

# What Does `result` Actually Store?

After this line:

```python
result = greet_after_delay()
```

Memory looks like this:

```text
result
   │
   ▼
Coroutine Object
```

It does **not** look like this:

```text
result
   │
   ▼
"Let's start"
"End"
```

The function body has not run yet.

---

# Why Does Python Print the Memory Address?

When you print an object, Python displays its **string representation**.

For a coroutine object, that representation looks like:

```text
<coroutine object greet_after_delay at 0x00000123456789>
```

Breaking it down:

```text
<coroutine object greet_after_delay at 0x00000123456789>

coroutine
    │
    └── Object Type

greet_after_delay
    │
    └── Async function that created the coroutine

0x00000123456789
    │
    └── Memory address (object identity in hexadecimal)
```

The memory address changes every time you run the program because a new coroutine object is created each time.

---

# How Do We Get the Actual Output?

A coroutine must be executed by the Event Loop.

Example:

```python
import asyncio

async def add():
    return 10

result = asyncio.run(add())

print(result)
```

Execution Flow:

```text
add()

↓

Coroutine Object Created

↓

asyncio.run()

↓

Event Loop Starts

↓

Coroutine Executes

↓

Returns 10

↓

result = 10

↓

print(result)

↓

10
```

Now the variable stores the function's return value instead of the coroutine object.

---

# Mental Model

Imagine ordering a pizza.

## Normal Function

```text
Order Pizza

↓

Pizza is cooked immediately

↓

You receive Pizza

↓

result = Pizza
```

---

## Async Function

```text
Order Pizza

↓

Restaurant gives you an Order Token

↓

result = Order Token

↓

Kitchen cooks later (Event Loop)

↓

Pizza becomes available
```

The coroutine object is like the **order token**.

It represents work that **will happen later**, not the completed result.

---

# Common Beginner Mistake

Many beginners think this:

```python
result = greet_after_delay()
```

means:

```text
Run the function

↓

Store the output
```

This is **incorrect**.

What actually happens is:

```text
Call async function

↓

Create Coroutine Object

↓

Store Coroutine Object

↓

Wait until the Event Loop executes it
```

---

# Important Interview Question

### Q: Why does `print(result)` print `<coroutine object ...>` instead of the function output?

**Answer:**

Calling an async function does **not execute** the function. It creates and returns a **Coroutine Object**. Therefore, the variable stores the coroutine object, not the function's return value. The coroutine must be executed using `await` or `asyncio.run()` before the actual result becomes available.

---

# Key Takeaways

* Variables store whatever the right-hand expression returns.
* Calling a normal function returns its final value.
* Calling an async function returns a **Coroutine Object**.
* `print(result)` prints the coroutine object's representation because that is what the variable contains.
* The Event Loop (`await` or `asyncio.run()`) is responsible for executing the coroutine and producing the real output.
* A coroutine object represents **future work**, not completed work.
