# Python If Statements 

## 1. Introduction

The `if` statement is used for decision-making in Python.

It evaluates a condition (an expression that returns `True` or `False`):

- If `True` → executes the block
- If `False` → skips the block

```python
x = 10

if x > 5:
    print("x is greater than 5")
````

---

## 2. Truthy and Falsy Values

Python treats many values as `True` or `False` automatically.

### Falsy Values (evaluates to False)

* `0`
* `0.0`
* `""` (empty string)
* `None`
* `[]`, `{}`, `()` (empty collections)
* `False`

```python
if "":
    print("Won't run")
```

### Truthy Values (evaluates to True)

* Non-zero numbers → `5`, `-3`
* Non-empty strings → `"False"`
* Non-empty collections

```python
if "False":
    print("This runs!")
```

---

## 3. The `elif` Keyword

`elif` = "else if"

Used to check multiple conditions in sequence.

```python
x = 10

if x > 10:
    print("Greater than 10")
elif x == 10:
    print("Equal to 10")
elif x > 5:
    print("Greater than 5")
```

### Key Point

* Python stops checking once a condition is `True`
* Improves performance vs multiple `if` statements

---

## 4. When to Use `elif`

Use `elif` when:

* Conditions are mutually exclusive
* Only one block should run

Bad example:

```python
if x > 5:
    print("A")

if x > 3:
    print("B")
```

Better:

```python
if x > 5:
    print("A")
elif x > 3:
    print("B")
```

---

## 5. The `else` Keyword

`else` runs when all previous conditions are False

```python
x = 2

if x > 5:
    print("Big")
else:
    print("Small")
```

---

## 6. Shorthand If (One-liner)

```python
if x > 5: print("Big")
```

Use only when:

* Single statement
* Improves readability

---

## 7. Shorthand If...Else (Ternary Operator)

```python
print("Big") if x > 5 else print("Small")
```

Assignment example:

```python
status = "Adult" if age >= 18 else "Minor"
```

---

## 8. Multiple Conditions in One Line

```python
a = 330
b = 330

print("A") if a > b else print("=") if a == b else print("B")
```

Use carefully to maintain readability.

---

## 9. When to Use Ternary Operators

Use when:

* Conditions are simple
* Quick assignments are needed
* Improves clarity

Avoid when logic becomes complex.

---

## 10. Logical Operators

| Operator | Meaning                      |
| -------- | ---------------------------- |
| `and`    | Both conditions must be True |
| `or`     | At least one must be True    |
| `not`    | Reverses condition           |

```python
if x > 5 and x < 20:
    print("In range")
```

---

## 11. Using Parentheses for Clarity

```python
temperature = 25
is_raining = False
is_weekend = True

if (temperature > 20 and not is_raining) or is_weekend:
    print("Great day!")
```

---

## 12. Nested If Statements

```python
x = 10

if x > 5:
    if x < 20:
        print("Between 5 and 20")
```

---

## 13. Nested If vs Logical Operators

Nested:

```python
if x > 5:
    if x < 20:
        print("Valid")
```

Better:

```python
if 5 < x < 20:
    print("Valid")
```

---

## 14. The `pass` Statement

`pass` is a placeholder that does nothing.

```python
if x > 5:
    pass
```

---

## 15. `pass` vs Comments

```python
if x > 5:
    # TODO: implement later
```

This causes an error.

Correct:

```python
if x > 5:
    pass
```

---

## 16. Loop Control Statements

### break

Stops loop immediately

```python
for i in range(5):
    if i == 3:
        break
```

### continue

Skips current iteration

```python
for i in range(5):
    if i == 3:
        continue
    print(i)
```

---

## 17. Python `match` Statement

Alternative to multiple if-else statements.

```python
day = 4

match day:
    case 1:
        print("Monday")
    case 2:
        print("Tuesday")
    case _:
        print("Other day")
```

---

## 18. Default Case

```python
match day:
    case 1:
        print("Mon")
    case _:
        print("Unknown")
```

---

## 19. Combining Multiple Values

```python
day = 4

match day:
    case 1 | 2 | 3 | 4 | 5:
        print("Weekday")
    case 6 | 7:
        print("Weekend")
```

---

## 20. Best Practices

* Validate inputs before conditions
* Avoid deep nesting
* Use logical operators for cleaner code
* Be careful with truthy values

```python
if user_input is not None:
    process()
```

---

## 21. Common Mistakes

Using `=` instead of `==`:

```python
if x = 5:   # Error
```

Correct:

```python
if x == 5:
```

Truthy mistake:

```python
if "False":   # This is True
```

Unreadable one-liners:

```python
print("A") if x > 5 else print("B") if x > 3 else print("C")
```

---

## 22. Summary

* `if` checks condition
* `elif` handles multiple conditions
* `else` is fallback
* Truthy/Falsy affects behavior
* Ternary is shorthand
* `match` replaces multiple conditions
* `pass` is placeholder

---

## 23. Practice Ideas

```python
status = 404

if status == 200:
    print("OK")
elif status == 404:
    print("Not Found")
else:
    print("Error")
```

* Build login validation
* Create role-based access
* Filter API responses
* Handle HTTP status codes

---

