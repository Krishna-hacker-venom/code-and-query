# Arrow Operators in Programming: `->` vs `=>`

Quick, practical explanation of the two most confusing arrow-like symbols in modern programming.

## 1. `->` (single arrow / arrow operator)

**Used in:** C, C++, Rust (method syntax), Go (pointers), D, etc.

**Meaning:**  
Access a member (field or method) **through a pointer** or reference.

```c
// C / C++
struct Person {
    int age;
    char name[50];
};

int main() {
    struct Person p = {25, "Krishna"};
    struct Person* ptr = &p;

    // These are equivalent:
    printf("%d\n", (*ptr).age);     // classic (ugly) way
    printf("%d\n", ptr->age);       // clean & common way

    ptr->age = 26;                  // changes the original struct
}
```

**Golden rule:**

| You have          | Use this   | Example             |
|-------------------|------------|----------------------|
| Normal variable   | `.` (dot)  | `obj.field`         |
| Pointer/reference | `->`       | `ptr->field`        |

**Most common beginner mistake:**

```c
Person p;
p->age = 20;     // WRONG! → segmentation fault or compile error
p.age = 20;      // CORRECT
```

## 2. `=>` (fat arrow / hash rocket)

**Used in:** JavaScript, TypeScript, PHP, Ruby, CoffeeScript, etc.

Main two uses:

### A. Arrow functions (very popular in JS/TS)

```javascript
// Traditional function
function add(a, b) {
    return a + b;
}

// Arrow function (short syntax)
const add = (a, b) => a + b;

// With body (need curly braces + return)
const greet = name => {
    console.log("Hello!");
    return `Hi ${name}`;
};
```

### B. Key-value pairs (associative arrays / objects)

```php
// PHP
$person = [
    'name'     => 'Krishna',
    'city'     => 'Mumbai',
    'age'      => 25,
    'is_coder' => true
];

// Laravel style (very common)
User::create([
    'name'     => 'Amit',
    'email'    => 'amit@example.com',
    'password' => bcrypt('secret123')
]);
```

```javascript
// JavaScript → uses : (colon) instead of =>
const person = {
    name: "Krishna",
    city: "Mumbai"
};
```

## Quick Comparison Table

| Symbol | Common Languages             | Primary Purpose                          | Nickname         | Mnemonic                              |
|--------|------------------------------|------------------------------------------|------------------|---------------------------------------|
| `->`   | C, C++, Rust, Go             | Pointer / reference member access        | Arrow operator   | "→ go there and get this"             |
| `=>`   | JavaScript, PHP, Ruby, TS    | Arrow function OR key ⇒ value mapping    | Fat arrow        | "maps to" or "becomes"                |

## Summary – When to use which?

```text
If you're dealing with pointers/references/structs in low-level languages → use ->

If you're writing short functions or key-value pairs in modern scripting languages → use =>
```

Happy coding! 🚀

```
