# Python Asyncio Notes - Understanding Coroutines

## Table of Contents

1. What is `asyncio`?
2. Understanding `async def`
3. What Happens When You Call an Async Function?
4. What is a Coroutine Object?
5. Understanding `<coroutine object ...>`
6. What is a Coroutine?
7. Normal Function vs Coroutine
8. How `await` Works
9. Why Doesn't an Async Function Execute Immediately?
10. Event Loop
11. Complete Execution Flow
12. Key Takeaways

---

# What is `asyncio`?

`asyncio` is Python's built-in library for writing **asynchronous programs**.

Instead of waiting for one task to finish before starting another, `asyncio` allows multiple tasks to make progress concurrently.

Example:

```python
import asyncio
```

---

# Understanding `async def`

Example:

```python
async def greet_after_delay():
    print("Let's start")
    await asyncio.sleep(5)
    print("End")
```

Adding the keyword `async` before `def` creates an **asynchronous function** (also called a *coroutine function*).

Unlike normal functions, async functions are designed to pause and resume execution.

---

# What Happens When You Call an Async Function?

Example:

```python
result = greet_after_delay()
```

Many beginners expect this to execute the function immediately.

It **does not**.

Instead, Python creates a **Coroutine Object**.

Nothing inside the function runs.

Example:

```python
async def greet():
    print("Hello")

result = greet()
```

Output:

```python
<coroutine object greet at 0x...>
```

Notice that `"Hello"` is never printed because the coroutine has not been executed.

---

# What is a Coroutine Object?

A coroutine object is an object created when an async function is called.

Think of it as a **paused function waiting to be executed**.

It contains:

* The code to execute
* Current execution state
* Local variables
* Information about where execution should resume

Analogy:

Calling an async function is like receiving a movie ticket.

```text
Movie Ticket

Movie : greet_after_delay
Seat  : 0x12345678

The movie hasn't started yet.
```

The ticket only represents the future execution.

---

# Understanding `<coroutine object greet_after_delay at 0x...>`

Example output:

```text
<coroutine object greet_after_delay at 0x00000123456789>
```

Let's break it down.

## Part 1

```text
coroutine
```

The object's type.

Python is telling you this object is a coroutine.

---

## Part 2

```text
object
```

Everything in Python is an object.

Examples:

```python
5                # Integer Object
"Hello"          # String Object
[1,2,3]          # List Object
```

Likewise,

```python
greet_after_delay()
```

creates a Coroutine Object.

---

## Part 3

```text
greet_after_delay
```

This is the name of the async function that created the coroutine.

---

## Part 4

```text
0x00000123456789
```

This is the object's memory address (identity shown in hexadecimal).

The address changes every time you run the program because a new coroutine object is created each time.

---

# What is a Coroutine?

## Definition

A coroutine is a **special function that can pause its execution and later continue from exactly where it stopped**.

Unlike normal functions, coroutines remember:

* Current line of execution
* Local variables
* Execution state

Example:

```python
async def example():
    x = 10

    await asyncio.sleep(5)

    print(x)
```

After five seconds,

```
x
```

is still `10`.

The coroutine remembered its state while it was paused.

---

# Normal Function vs Coroutine

| Normal Function                  | Coroutine                      |
| -------------------------------- | ------------------------------ |
| Executes immediately             | Returns a coroutine object     |
| Cannot pause                     | Can pause using `await`        |
| Blocks while waiting             | Allows other coroutines to run |
| Starts from beginning every call | Resumes where it paused        |

---

# How `await` Works

Example:

```python
await asyncio.sleep(5)
```

This **does not block the entire program**.

Instead it means:

> Pause this coroutine.
> While I'm waiting, let another coroutine run.

Execution Flow:

```
Coroutine Starts
        │
        ▼
Runs normally
        │
        ▼
await
        │
        ▼
Coroutine Pauses
        │
        ▼
Other coroutines execute
        │
        ▼
Operation finishes
        │
        ▼
Coroutine resumes
```

---

# Why Doesn't an Async Function Execute Immediately?

Python separates two different things.

## Step 1

Create a coroutine object.

```python
result = greet_after_delay()
```

## Step 2

Execute it.

```python
asyncio.run(result)
```

or

```python
await result
```

This separation allows Python's Event Loop to decide **when** each coroutine should run.

---

# Event Loop

The Event Loop is the engine that executes coroutines.

Example:

```python
asyncio.run(greet_after_delay())
```

Execution Flow:

```
Create Event Loop
        │
        ▼
Start Coroutine
        │
        ▼
Execute code
        │
        ▼
await encountered
        │
        ▼
Pause coroutine
        │
        ▼
Run another coroutine
        │
        ▼
Resume paused coroutine
        │
        ▼
Finish
```

---

# Complete Execution Flow

Example:

```python
import asyncio

async def greet_after_delay():
    print("Let's start")

    await asyncio.sleep(5)

    print("End")

result = greet_after_delay()

print(result)

print(type(result))
```

Execution:

```
Program Starts
      │
      ▼
Import asyncio
      │
      ▼
Create async function
      │
      ▼
Call async function
      │
      ▼
Coroutine Object Created
      │
      ▼
Nothing inside function executes
      │
      ▼
print(result)
      │
      ▼
<coroutine object greet_after_delay at ...>
      │
      ▼
print(type(result))
      │
      ▼
<class 'coroutine'>
```

Notice:

```
print("Let's start")
```

never executes because the coroutine was never run.

---

# Important Terminology

## Async Function (Coroutine Function)

Defined using:

```python
async def greet():
    ...
```

This is only the blueprint.

---

## Coroutine Object

Created when calling the async function.

```python
coro = greet()
```

Output:

```text
<coroutine object greet at 0x...>
```

---

## Executing a Coroutine

Using

```python
await greet()
```

or

```python
asyncio.run(greet())
```

---

# Beginner Mistakes

❌ Wrong

```python
result = greet()
```

"I executed the async function."

No.

You only created a coroutine object.

---

❌ Wrong

```python
await
```

"Stops the whole program."

No.

It pauses **only the current coroutine**.

---

❌ Wrong

```python
asyncio.sleep(5)
```

"It blocks like time.sleep()."

No.

It yields control back to the Event Loop.

---

# Mental Model

Think of an async function like a recipe.

```
async def greet()
```

↓

Recipe

---

```
greet()
```

↓

Recipe Card (Coroutine Object)

---

```
await greet()
```

↓

Chef starts cooking (Event Loop executes the coroutine)

---

# Key Takeaways

* `async def` creates an asynchronous function.
* Calling an async function **does not execute it**.
* It returns a **Coroutine Object**.
* A coroutine object contains everything needed to execute the function later.
* `await` pauses only the current coroutine.
* While paused, other coroutines can run.
* The Event Loop schedules, pauses, resumes, and completes coroutine execution.
* Coroutines are the foundation of Python's `asyncio` library.
