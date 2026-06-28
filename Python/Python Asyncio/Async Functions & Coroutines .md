# Python Asyncio Notes - Complete Guide to Async Functions & Coroutines

> **Goal:** Understand *how* `asyncio` works instead of memorizing syntax.

---

# Table of Contents

1. Introduction
2. Why Async Programming?
3. What is `asyncio`?
4. What is `async def`?
5. Async Function vs Normal Function
6. What Happens When You Call an Async Function?
7. What is a Coroutine?
8. What is a Coroutine Object?
9. Async Function vs Coroutine Object
10. Understanding `<coroutine object ...>`
11. Why Does `print(result)` Show a Coroutine Object?
12. Why Doesn't Python Execute Async Functions Immediately?
13. Understanding `await`
14. Event Loop
15. Complete Execution Flow
16. Mental Models
17. Common Beginner Mistakes
18. Interview Questions
19. Key Takeaways

---

# 1. Introduction

Python normally executes code **one line at a time**.

Example:

```python
print("A")
print("B")
print("C")
```

Output

```
A
B
C
```

The next line never starts until the previous one finishes.

This is called **synchronous programming**.

---

# 2. Why Async Programming?

Imagine downloading a file.

```python
download_file()
```

Suppose downloading takes **10 seconds**.

During those 10 seconds the CPU mostly waits.

```
Start Download
      │
      ▼
Waiting...
Waiting...
Waiting...
Waiting...
      │
      ▼
Download Finished
```

This wastes CPU time.

Instead, Python can say:

> "While waiting for the download, I'll execute something else."

This idea is called **Asynchronous Programming**.

---

# 3. What is `asyncio`?

`asyncio` is Python's built-in library for asynchronous programming.

It allows multiple tasks to make progress without blocking each other.

Example:

```python
import asyncio
```

It provides:

* Event Loop
* Coroutines
* Tasks
* await
* asyncio.sleep()
* create_task()
* gather()

---

# 4. What is `async def`?

Example

```python
async def greet():
    print("Hello")
```

Adding `async` before `def` creates an **Async Function**.

An async function is also called a **Coroutine Function**.

It is only a blueprint.

Nothing executes yet.

---

# 5. Async Function vs Normal Function

Normal function

```python
def hello():
    return "Hello"
```

Calling it

```python
hello()
```

Immediately executes.

---

Async function

```python
async def hello():
    return "Hello"
```

Calling it

```python
hello()
```

Does **not** execute.

Instead it creates a **Coroutine Object**.

---

# 6. What Happens When You Call an Async Function?

Example

```python
async def greet():
    print("Hello")

result = greet()
```

Many beginners expect:

```
Hello
```

This is incorrect.

Actual execution:

```
greet()

↓

Coroutine Object Created

↓

Stored inside result
```

Nothing inside the function executes.

---

# 7. What is a Coroutine?

A **Coroutine** is an object representing the execution of an asynchronous function.

A coroutine can:

* Start
* Pause
* Resume
* Finish

while remembering:

* Local variables
* Current execution line
* Execution state

Example

```python
async def example():
    x = 10

    await asyncio.sleep(5)

    print(x)
```

The coroutine remembers

```
x = 10
```

even after waiting.

---

# 8. What is a Coroutine Object?

Calling an async function creates a Coroutine Object.

Example

```python
async def greet():
    print("Hello")

coro = greet()
```

Memory

```
coro
 │
 ▼
Coroutine Object
```

It stores

* Function code
* Local state
* Next instruction
* Resume location

Think of it as a paused program waiting for the Event Loop.

---

# 9. Async Function vs Coroutine Object

| Async Function              | Coroutine Object                      |
| --------------------------- | ------------------------------------- |
| Created using `async def`   | Created by calling the async function |
| Blueprint                   | Instance of the blueprint             |
| Doesn't execute             | Can be executed                       |
| Similar to class definition | Similar to object                     |

Example

```python
async def greet():
    return "Hello"
```

This is an Async Function.

Calling

```python
coro = greet()
```

creates a Coroutine Object.

---

# 10. Understanding `<coroutine object greet at 0x...>`

Suppose

```python
async def greet():
    pass

coro = greet()

print(coro)
```

Output

```
<coroutine object greet at 0x00000123456789>
```

Breakdown

```
coroutine
    │
    └── Object Type

greet
    │
    └── Async function name

0x00000123456789
    │
    └── Memory address
```

Every run creates a new coroutine object.

Therefore the memory address changes.

---

# 11. Why Does `print(result)` Show a Coroutine Object?

Example

```python
async def add():
    return 10

result = add()

print(result)
```

Output

```
<coroutine object add at 0x...>
```

Why?

Because variables store whatever the right side returns.

Normal function

```python
def add():
    return 10

result = add()
```

Execution

```
add()

↓

Runs

↓

Returns 10

↓

result = 10
```

---

Async function

```python
async def add():
    return 10

result = add()
```

Execution

```
add()

↓

Creates Coroutine Object

↓

result = Coroutine Object
```

The coroutine has never executed.

Therefore

```
print(result)
```

prints

```
Coroutine Object
```

instead of

```
10
```

---

# 12. Why Doesn't Python Execute Async Functions Immediately?

Imagine 10,000 downloads.

If every async function executed immediately

Python would lose control over scheduling.

Instead Python separates

**Creating Work**

from

**Executing Work**

Execution

```
Call Async Function

↓

Create Coroutine Object

↓

Give it to Event Loop

↓

Event Loop decides

• When to start
• When to pause
• When to resume
• When to finish
```

This design makes asynchronous programming possible.

---

# 13. Understanding `await`

Example

```python
await asyncio.sleep(5)
```

`await` means

> Pause THIS coroutine.

It does NOT mean

> Stop the whole program.

Execution

```
Coroutine Starts

↓

Runs

↓

await

↓

Coroutine Pauses

↓

Other Coroutines Execute

↓

Sleep Finishes

↓

Coroutine Resumes
```

---

# 14. Event Loop

The Event Loop is the engine of `asyncio`.

It executes coroutine objects.

Example

```python
asyncio.run(greet())
```

Internally

```
Create Event Loop

↓

Start Coroutine

↓

Run Until await

↓

Pause Coroutine

↓

Run Other Coroutines

↓

Resume

↓

Finish
```

Without an Event Loop

coroutines never execute.

---

# 15. Complete Execution Flow

Example

```python
import asyncio

async def greet():

    print("Start")

    await asyncio.sleep(5)

    print("End")

result = greet()

print(result)
```

Execution

```
Program Starts

↓

Import asyncio

↓

Create Async Function

↓

Call greet()

↓

Coroutine Object Created

↓

Stored in result

↓

print(result)

↓

<coroutine object greet at ...>
```

Notice

```
print("Start")
```

never runs.

---

Now

```python
asyncio.run(greet())
```

Execution

```
Program Starts

↓

Event Loop Created

↓

Coroutine Starts

↓

print("Start")

↓

await sleep()

↓

Coroutine Pauses

↓

Sleep Completes

↓

Coroutine Resumes

↓

print("End")

↓

Finish
```

---

# 16. Mental Models

## Recipe

```
async def greet()

↓

Recipe
```

Calling

```
greet()

↓

Recipe Card
```

Event Loop

```
await greet()

↓

Chef Cooks
```

---

## Movie Ticket

Calling an async function is like buying a movie ticket.

```
Movie Ticket

↓

Movie hasn't started
```

The Event Loop starts the movie.

---

## Pizza Ordering

Normal Function

```
Order Pizza

↓

Pizza Arrives

↓

Eat Pizza
```

Async Function

```
Order Pizza

↓

Receive Order Token

↓

Kitchen Cooks Later

↓

Pizza Ready
```

The coroutine object is the order token.

---

# 17. Common Beginner Mistakes

## Mistake 1

```python
result = greet()
```

Thinking

```
Function Executed
```

Wrong.

It only creates a coroutine object.

---

## Mistake 2

Thinking

```
await

↓

Stops Python
```

Wrong.

It pauses only the current coroutine.

---

## Mistake 3

Thinking

```python
asyncio.sleep()
```

works like

```python
time.sleep()
```

Wrong.

`time.sleep()`

Blocks the entire thread.

`asyncio.sleep()`

Pauses only the coroutine.

---

## Mistake 4

Printing a coroutine

```python
print(greet())
```

Expecting

```
Hello
```

Instead

```
<coroutine object greet at ...>
```

because the coroutine hasn't executed.

---

# 18. Interview Questions

## Q1

What is asyncio?

**Answer**

A library for asynchronous programming using coroutines and an Event Loop.

---

## Q2

What is an async function?

**Answer**

A function defined using `async def`.

Calling it creates a coroutine object.

---

## Q3

What is a coroutine?

**Answer**

A coroutine is an object representing the execution of an asynchronous function.

It can pause and later resume execution without losing its state.

---

## Q4

What is a Coroutine Object?

**Answer**

A coroutine object is created when an async function is called.

It contains everything required to execute the async function later.

---

## Q5

Why does `print(greet())` show `<coroutine object ...>`?

**Answer**

Because calling an async function returns a coroutine object instead of executing the function immediately.

---

## Q6

How do you execute a coroutine?

Using

```python
await coroutine
```

or

```python
asyncio.run(coroutine)
```

---

## Q7

What does `await` do?

It pauses only the current coroutine and lets the Event Loop execute other coroutines.

---

## Q8

What is the Event Loop?

The Event Loop schedules and executes coroutine objects.

---

# 19. Key Takeaways

* `asyncio` is Python's asynchronous programming library.
* `async def` creates an async (coroutine) function.
* Calling an async function **does not execute it**.
* Calling an async function creates a **Coroutine Object**.
* A coroutine object contains everything needed to execute the async function later.
* Variables store whatever the right-hand expression returns.
* Therefore, `result = greet()` stores a coroutine object, not the function's return value.
* `print(result)` prints the coroutine object's representation because that is what the variable contains.
* `await` pauses only the current coroutine.
* The Event Loop starts, pauses, resumes, and finishes coroutine execution.
* `asyncio.run()` creates an Event Loop and executes the coroutine.
* Think of an async function as a **blueprint**, a coroutine object as a **recipe card or order token**, and the Event Loop as the **chef** that executes it.

---

# Final Mental Model

```
async def greet():
        │
        ▼
Async Function (Blueprint)

greet()
        │
        ▼
Coroutine Object

await greet()
or
asyncio.run(greet())
        │
        ▼
Event Loop Executes Coroutine

Coroutine Finishes
        │
        ▼
Returns Actual Value
```

> **Golden Rule:**
> **`async def` defines the work → calling it creates a coroutine object → the Event Loop executes the coroutine → only then do you get the actual result.**
