# Python Dictionaries 

## What is a Dictionary?

A **dictionary** in Python is used to store data in **key:value pairs**.

```python
user = {
    "username": "krishna",
    "role": "admin",
    "active": True
}
```

* Key → identifier
* Value → actual data

---

## Key Features

* Ordered (Python 3.7+)
* Changeable (mutable)
* No duplicate keys
* Fast lookup (O(1))

---

## Creating a Dictionary

### Using `{}`

```python
data = {
    "name": "Krishna",
    "age": 20
}
```

### Using `dict()`

```python
data = dict(name="Krishna", age=20)
```

---

## Accessing Items

```python
print(data["name"])   # Error if not found
print(data.get("name"))  # Safe (returns None)
```

---

## Dictionary Methods

```python
data.keys()     # all keys
data.values()   # all values
data.items()    # key-value pairs
```

---

## Updating and Adding

```python
data["age"] = 21

data.update({
    "city": "Mumbai"
})
```

---

## Removing Items

```python
data.pop("age")
data.popitem()
del data["name"]
data.clear()
```

---

## Looping

```python
for key, value in data.items():
    print(key, value)
```

---

## Nested Dictionary

```python
users = {
    "user1": {"name": "Krishna"},
    "user2": {"name": "Raj"}
}

print(users["user1"]["name"])
```

---

# Advanced Dictionary Tricks

## 1. Dictionary Comprehension

```python
squares = {x: x*x for x in range(5)}
print(squares)
```

---

## With Condition

```python
even_squares = {x: x*x for x in range(10) if x % 2 == 0}
```

---

## 2. Merging Dictionaries

### Using `update()`

```python
a = {"x": 1}
b = {"y": 2}

a.update(b)
```

---

### Using `|` operator (Python 3.9+)

```python
a = {"x": 1}
b = {"y": 2}

c = a | b
```

---

## 3. Default Values (`setdefault()`)

```python
data = {"name": "Krishna"}

data.setdefault("age", 20)
print(data)
```

---

## 4. Counting with Dictionary

```python
logs = ["200", "404", "200", "500"]

count = {}

for code in logs:
    count[code] = count.get(code, 0) + 1

print(count)
```

---

## 5. Dictionary from Lists

```python
keys = ["a", "b", "c"]
values = [1, 2, 3]

data = dict(zip(keys, values))
```

---

## 6. Sorting Dictionary

```python
data = {"a": 3, "b": 1, "c": 2}

sorted_data = dict(sorted(data.items(), key=lambda x: x[1]))
```

---

## 7. Swapping Keys and Values

```python
data = {"a": 1, "b": 2}

swapped = {v: k for k, v in data.items()}
```

---

## 8. Using `fromkeys()`

```python
keys = ["a", "b", "c"]

data = dict.fromkeys(keys, 0)
```

---

## 9. Removing Duplicates Using Dictionary

```python
nums = [1, 2, 2, 3, 3]

unique = list(dict.fromkeys(nums))
```

---

## 10. Deep vs Shallow Copy

```python
import copy

original = {"a": [1, 2]}
shallow = original.copy()
deep = copy.deepcopy(original)
```

---

# Hacker Mindset

## API Handling

```python
response = {
    "status": 200,
    "data": {
        "user": "krishna"
    }
}

print(response["data"]["user"])
```

---

## HTTP Headers

```python
headers = {
    "User-Agent": "Mozilla/5.0",
    "Authorization": "Bearer token"
}
```

---

## JSON and Dictionary

Most APIs return JSON, which Python converts into dictionaries.

---

## Real Use in Bug Bounty

* Parsing API responses
* Modifying request data
* Automating attacks
* Handling authentication tokens

---

# Common Mistakes

## Duplicate Keys

```python
data = {"a": 1, "a": 2}
```

Output:

```
{'a': 2}
```

---

## KeyError

```python
print(data["missing"])  # Error
```

Use:

```python
print(data.get("missing"))
```

---

# Summary

* Dictionary stores data in key:value format
* Fast and efficient lookup
* Widely used in APIs and automation
* Important for bug bounty and security testing

---

