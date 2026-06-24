
# 🔁 JavaScript Loops – Complete Guide

Understanding loops is very important for writing efficient and clean code.
Loops help us **repeat tasks automatically** instead of writing the same code multiple times.

---

# 1️⃣ Standard `for` Loop

## ✅ Why We Use It

We use the standard `for` loop when:

* We know **how many times** we want to run the loop.
* We need **index control**.
* We want full control over initialization, condition, and increment.

## 🧠 How It Works

```js
for (initialization; condition; increment) {
   // code block
}
```

### Step-by-step:

1. Initialization runs once.
2. Condition is checked before every iteration.
3. If condition is true → code runs.
4. Increment runs.
5. Repeat until condition becomes false.

## 💻 Example

```js
for (let i = 0; i < 5; i++) {
    console.log("Number:", i);
}
```

### 🔍 Execution Flow

* i = 0 → print → i++
* i = 1 → print → i++
* i = 2 → print → i++
* i = 3 → print → i++
* i = 4 → print → i++
* i = 5 → condition false → stop

---

# 2️⃣ `for...of` Loop

## ✅ Why We Use It

We use `for...of` when:

* We want to iterate over **values**
* Used with Arrays, Strings, Maps, Sets
* We don't care about index

## 🧠 How It Works

It directly gives the **value** of each element.

## 💻 Example (Array)

```js
let numbers = [10, 20, 30];

for (let num of numbers) {
    console.log(num);
}
```

### 🔍 Output

```
10
20
30
```

It automatically picks each value one by one.

---

# 3️⃣ `for...in` Loop

## ✅ Why We Use It

We use `for...in` when:

* We want to iterate over **keys**
* Mainly used for **Objects**
* Can also be used for arrays (but not recommended)

## 🧠 How It Works

It gives the **key (property name)** of an object.

## 💻 Example (Object)

```js
let user = {
    name: "Krishna",
    age: 22,
    role: "Security Learner"
};

for (let key in user) {
    console.log(key, ":", user[key]);
}
```

### 🔍 Output

```
name : Krishna
age : 22
role : Security Learner
```

⚠️ For arrays, prefer `for...of`.

---

# 4️⃣ `forEach()` Method

## ✅ Why We Use It

We use `forEach()` when:

* Working with arrays
* Want cleaner, readable code
* Don't need to break/stop loop

⚠️ Cannot use `break` or `continue`

## 🧠 How It Works

It runs a callback function for each array element.

## 💻 Example

```js
let numbers = [1, 2, 3];

numbers.forEach(function(num) {
    console.log(num);
});
```

Or using arrow function:

```js
numbers.forEach((num) => {
    console.log(num);
});
```

---

# 5️⃣ `for...else` (Important Clarification)

🚨 JavaScript **does NOT support `for...else`**

But in **Python**, we have `for...else`.

## 📌 Example in Python

```python
for i in range(5):
    print(i)
else:
    print("Loop finished successfully")
```

### 🔍 How It Works

* `else` runs only if loop completes normally
* If loop breaks → else does NOT execute

---

# 🔥 When To Use What?

| Loop Type             | Best Used When                | Gives            |
| --------------------- | ----------------------------- | ---------------- |
| `for`                 | Need index control            | Index            |
| `for...of`            | Iterate values                | Value            |
| `for...in`            | Iterate object keys           | Key              |
| `forEach()`           | Clean array iteration         | Value            |
| `for...else` (Python) | Detect normal loop completion | Completion block |

---

# 🧩 Quick Comparison

```js
let arr = ["a", "b", "c"];
```

### Standard `for`

```js
for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}
```

### `for...of`

```js
for (let value of arr) {
    console.log(value);
}
```

### `for...in`

```js
for (let index in arr) {
    console.log(index);
}
```

### `forEach`

```js
arr.forEach(value => console.log(value));
```

---

# 🎯 Final Summary

* Use **standard `for`** when you need full control.
* Use **`for...of`** for values (best for arrays).
* Use **`for...in`** for object properties.
* Use **`forEach()`** for clean functional-style looping.
* Python has `for...else`, JavaScript does not.

---
