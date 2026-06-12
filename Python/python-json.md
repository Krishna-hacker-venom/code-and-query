# Python JSON 

## What is JSON?

JSON (JavaScript Object Notation) is a lightweight data format used for data exchange.

It is heavily used in:
- APIs (REST, GraphQL)
- Web applications
- Mobile apps
- Configuration files

If you understand JSON, you understand how modern apps communicate.

---

## Python `json` Module

```python
import json
````

---

## Python → JSON Conversion

| Python | JSON   |
| ------ | ------ |
| dict   | Object |
| list   | Array  |
| tuple  | Array  |
| str    | String |
| int    | Number |
| float  | Number |
| True   | true   |
| False  | false  |
| None   | null   |

---

## `json.dumps()` (Python → JSON String)

```python
import json

data = {
    "user": "admin",
    "active": True,
    "roles": ["user", "admin"],
    "age": 25
}

json_data = json.dumps(data)
print(json_data)
```

---

## Pretty Print (Debugging)

```python
print(json.dumps(data, indent=4))
```

---

## Compact JSON (WAF Evasion)

```python
json.dumps(data, separators=(",", ":"))
```

---

## Write JSON to File (`json.dump()`)

```python
with open("data.json", "w") as f:
    json.dump(data, f, indent=4)
```

---

## JSON → Python (`json.loads()`)

`loads()` converts JSON string into Python object.

### Example

```python
import json

json_string = '{"user": "admin", "age": 25}'
data = json.loads(json_string)

print(data["user"])
```

---

## Read JSON from File (`json.load()`)

```python
with open("data.json", "r") as f:
    data = json.load(f)

print(data)
```

---

## dump() vs dumps() vs load() vs loads()

| Function | Purpose         |
| -------- | --------------- |
| dump()   | Python → File   |
| dumps()  | Python → String |
| load()   | File → Python   |
| loads()  | String → Python |

---

# Hacker Mindset: Real Usage

## 1. API Interaction with Python

Use JSON in real-world hacking with `requests`:

```python
import requests

url = "https://target.com/api/login"

payload = {
    "username": "admin",
    "password": "admin123"
}

response = requests.post(url, json=payload)

print(response.text)
```

---

## 2. Modify JSON Requests (Burp Suite Style)

Original:

```json
{
  "role": "user"
}
```

Attack:

```json
{
  "role": "admin"
}
```

Test for:

* Broken Access Control
* Privilege Escalation

---

## 3. JSON Fuzzing Script

```python
import requests

url = "https://target.com/api/user"

payloads = [
    {"id": 1},
    {"id": "1"},
    {"id": -1},
    {"id": None},
    {"id": " OR 1=1"}
]

for p in payloads:
    r = requests.post(url, json=p)
    print(p, r.status_code)
```

---

## 4. Null Injection

```json
{
  "email": null
}
```

Why test?

* Backend might skip validation
* Can bypass required fields

---

## 5. Type Confusion Attack

```json
{
  "price": "100"
}
```

instead of:

```json
{
  "price": 100
}
```

Possible issues:

* Logic bypass
* Calculation errors

---

## 6. Nested JSON Attacks

```json
{
  "user": {
    "role": "admin"
  }
}
```

Test:

* Deep nesting
* Unexpected structures

---

## 7. JSON Injection (NoSQL / SQL)

```json
{
  "username": "admin",
  "password": {"$ne": null}
}
```

Used in:

* MongoDB Injection

---

## 8. JWT Payload Analysis (JSON Based)

JWT payload is JSON:

```json
{
  "user": "admin",
  "role": "user"
}
```

Try modifying:

```json
{
  "role": "admin"
}
```

Then re-sign or test for weak validation.

---

## 9. Response Analysis

Example response:

```json
{
  "id": 101,
  "role": "user",
  "isAdmin": false
}
```

Look for:

* Hidden fields
* Debug info
* IDOR opportunities

---

# Advanced JSON Tricks

## Sort Keys (Diffing Responses)

```python
json.dumps(data, indent=4, sort_keys=True)
```

---

## Unicode Handling

```python
json.dumps(data, ensure_ascii=False)
```

Useful for:

* Encoding bugs
* WAF bypass

---

## Custom Encoder (Advanced)

```python
import json

class CustomEncoder(json.JSONEncoder):
    def default(self, obj):
        return str(obj)

json.dumps(data, cls=CustomEncoder)
```

---

## Handling Errors

```python
import json

try:
    json.loads("invalid json")
except json.JSONDecodeError as e:
    print("Error:", e)
```

---

# Common Mistakes

## 1. Single Quotes ❌

```json
{'user': 'admin'}
```

Correct:

```json
{"user": "admin"}
```

---

## 2. Trailing Comma ❌

```json
{
  "user": "admin",
}
```

---

## 3. Non-Serializable Data ❌

```python
json.dumps(set([1,2,3]))
```

Fix:

```python
json.dumps(list(set([1,2,3])))
```

---

# Real Bug Bounty Tips

* Always intercept JSON requests in Burp Suite
* Modify:

  * IDs
  * Roles
  * Boolean flags
* Try:

  * null
  * empty strings ""
  * large numbers
  * nested objects
* Automate attacks using Python scripts
* Compare responses carefully

---

# Practice Labs

Try on:

* Public APIs
* CTF challenges
* Your own test environments

Build:

* JSON fuzzers
* API automation tools
* Response analyzers

---

# Summary

* JSON is critical for modern hacking
* `dumps()` and `dump()` → create JSON
* `loads()` and `load()` → parse JSON
* JSON manipulation = real-world vulnerabilities
* Mastering JSON = stronger bug bounty skills

---

Just tell me 👍
```
