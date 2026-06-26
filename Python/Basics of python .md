# Python Fundamentals

> **Track:** Programming Foundations
> **Platform:** Self-Study / Reference
> **Focus:** What Python is, how it works, versioning, package management

---

## Table of Contents

- [What is Python](#what-is-python)
- [Python Versions](#python-versions)
- [How Python Works / Runs](#how-python-works--runs)
- [pip vs pip3 and Other Package Managers](#pip-vs-pip3-and-other-package-managers)
- [How to Download Packages and Modules](#how-to-download-packages-and-modules)

---

## What is Python

**Definition:**
Python is a high-level, interpreted, general-purpose programming language created by Guido van Rossum and first released in 1991. It is designed with readability and simplicity in mind, using indentation to define code blocks instead of curly braces or keywords.

**Key characteristics:**
- **Interpreted** — code is executed line by line at runtime, not pre-compiled to machine code
- **Dynamically typed** — variable types are determined at runtime, not declared by the programmer
- **Garbage collected** — memory is managed automatically
- **Multi-paradigm** — supports procedural, object-oriented, and functional programming styles
- **Cross-platform** — runs on Windows, Linux, macOS with little or no modification

**What Python is used for:**
- Web development (Django, Flask, FastAPI)
- Automation and scripting
- Data science and machine learning (NumPy, pandas, TensorFlow, PyTorch)
- Penetration testing and security tooling (Metasploit modules, custom exploits, recon scripts)
- API development and backend services
- CTF challenge scripting

**Hacker / Attacker Perspective:**
- Python is the most common language used in offensive security tooling — exploits, payloads, automation
- Most CVE proof-of-concept (PoC) scripts are written in Python
- Tools like sqlmap, impacket, pwntools, and Scapy are Python-based
- Quick to write throwaway scripts for brute-forcing, fuzzing, or parsing HTTP responses

---

## Python Versions

### Python 2 vs Python 3

| Feature | Python 2 | Python 3 |
|---|---|---|
| Release year | 2000 | 2008 |
| End of life | January 1, 2020 | Actively maintained |
| `print` | Statement: `print "hello"` | Function: `print("hello")` |
| Integer division | `5 / 2 = 2` | `5 / 2 = 2.5` |
| Unicode | ASCII by default | Unicode (UTF-8) by default |
| `range()` | Returns a list | Returns an iterator (memory efficient) |
| `input()` | Dangerous — executes code | Safe — returns a string |

**Python 2 is dead.** It received its final release (2.7.18) in April 2020. You should never start a new project in Python 2.

---

### Why Python Version Numbers Matter

Python follows **semantic versioning**: `MAJOR.MINOR.PATCH`

- `MAJOR` — breaking changes (2.x → 3.x was a full rewrite)
- `MINOR` — new features, backwards compatible within the same major version (3.10 → 3.11)
- `PATCH` — bug and security fixes only (3.11.0 → 3.11.9)

**Notable Python 3 milestones:**

| Version | Key Addition | Why It Matters |
|---|---|---|
| 3.5 | `async` / `await` syntax | Asynchronous programming made clean |
| 3.6 | f-strings (`f"hello {name}"`) | String formatting became readable |
| 3.8 | Walrus operator (`:=`) | Assign and test in one expression |
| 3.10 | `match` / `case` (pattern matching) | Structural pattern matching like switch |
| 3.11 | ~60% speed improvement | Faster execution by default |
| 3.12 | Better error messages | Easier debugging |

**Why this matters for security work:**
- Many older exploit scripts are written for Python 2 — you need to know the syntax differences to port them
- Some tools require specific Python 3.x minor versions — mismatches break imports
- `print "x"` in a script immediately tells you it was written for Python 2

---

### Checking Your Python Version

```bash
python --version
python3 --version
python3.11 --version   # check a specific minor version
```

---

## How Python Works / Runs

### The Execution Pipeline

When you run a `.py` file, Python goes through these stages:

```
Source Code (.py)
       |
       v
  [Lexer / Tokenizer]
       |
       v
  [Parser → AST]         (Abstract Syntax Tree)
       |
       v
  [Compiler → Bytecode]  (.pyc files in __pycache__)
       |
       v
  [Python Virtual Machine (PVM)]
       |
       v
  Execution (output / side effects)
```

1. **Lexer** — breaks source code into tokens (keywords, operators, identifiers)
2. **Parser** — checks syntax and builds an Abstract Syntax Tree (AST)
3. **Compiler** — converts the AST to bytecode (`.pyc` files stored in `__pycache__/`)
4. **PVM (Python Virtual Machine)** — interprets and executes the bytecode line by line

**Key point:** Python compiles to bytecode, not machine code. The bytecode runs on the PVM, which is why Python is platform-independent but slower than compiled languages like C.

---

### CPython vs Other Implementations

The Python you download from python.org is **CPython** — the reference implementation written in C.

| Implementation | Language | Use Case |
|---|---|---|
| **CPython** | C | Default, most compatible |
| **PyPy** | Python/RPython | Faster execution via JIT compilation |
| **Jython** | Java | Runs on the JVM |
| **IronPython** | C# | Runs on .NET |
| **MicroPython** | C | Microcontrollers and embedded systems |

For security work and general use, you will always be using CPython unless a tool specifically requires otherwise.

---

### Running Python

```bash
# Run a script
python3 script.py

# Interactive REPL (Read-Eval-Print Loop)
python3

# Run a one-liner
python3 -c "print('hello')"

# Run a module directly
python3 -m http.server 8080
python3 -m venv myenv
```

---

### The `__pycache__` Directory

When Python runs a `.py` file, it compiles it to bytecode and caches it in a `__pycache__/` directory as a `.pyc` file. On subsequent runs, if the source hasn't changed, Python uses the cached bytecode to skip re-compilation and start faster.

**Hacker note:** `.pyc` files can be decompiled back to readable Python source using tools like `uncompyle6` or `decompile3`. If you find a `.pyc` file during a CTF or engagement, you can recover the logic.

```bash
pip install uncompyle6
uncompyle6 script.cpython-311.pyc
```

---

## pip vs pip3 and Other Package Managers

### What is pip?

`pip` is Python's default package installer. It pulls packages from **PyPI** (Python Package Index) at [https://pypi.org](https://pypi.org).

```bash
pip install requests
pip uninstall requests
pip list                  # show installed packages
pip show requests         # details about a specific package
pip freeze                # list packages with pinned versions (for requirements.txt)
```

---

### pip vs pip3

| Command | Points to | When to use |
|---|---|---|
| `pip` | Whichever Python is set as default (`python`) | Unreliable — depends on system config |
| `pip3` | Python 3 specifically | More explicit on systems where both Python 2 and 3 coexist |
| `python3 -m pip` | The pip tied to `python3` explicitly | Most reliable — no ambiguity |

**Best practice:** Always use `python3 -m pip install <package>` to guarantee you're installing into the correct Python environment.

```bash
# These can all point to different Pythons depending on your system
pip install requests
pip3 install requests
python3 -m pip install requests   # most explicit and reliable
```

---

### Why the Ambiguity Exists

On many Linux systems (especially Kali), both Python 2 and Python 3 are installed. The `python` command may point to either version depending on system defaults. Calling `pip` without qualification can install a package into the wrong Python version, causing `ModuleNotFoundError` at runtime even though the package appears installed.

```bash
# Diagnose which Python pip is tied to
pip --version
# Output example: pip 23.0 from /usr/lib/python3/dist-packages/pip (python 3.11)
```

---

### Other Package Managers and Tools

| Tool | What it does |
|---|---|
| `pip` | Default package installer, pulls from PyPI |
| `pip3` | Same as pip but explicitly for Python 3 |
| `pipx` | Installs Python CLI tools in isolated environments (avoids polluting system Python) |
| `conda` | Package and environment manager used in data science (Anaconda/Miniconda) |
| `poetry` | Dependency management and packaging tool with lock files |
| `uv` | Very fast pip/venv replacement written in Rust (modern alternative) |
| `venv` | Built-in module for creating virtual environments |
| `virtualenv` | Third-party virtual environment tool with more features than `venv` |

---

## How to Download Packages and Modules

### Standard Installation

```bash
# Install a package
python3 -m pip install requests

# Install a specific version
python3 -m pip install requests==2.31.0

# Install minimum version
python3 -m pip install "requests>=2.28.0"

# Upgrade an existing package
python3 -m pip install --upgrade requests

# Uninstall
python3 -m pip uninstall requests
```

---

### Installing from a requirements.txt File

A `requirements.txt` file lists all dependencies for a project. Standard practice for sharing or reproducing environments.

```bash
# Install all packages from requirements.txt
python3 -m pip install -r requirements.txt

# Generate requirements.txt from current environment
python3 -m pip freeze > requirements.txt
```

**Example `requirements.txt`:**
```
requests==2.31.0
flask>=2.3.0
python-dotenv
beautifulsoup4
```

---

### Virtual Environments

A **virtual environment** is an isolated Python installation for a specific project. It prevents package version conflicts between projects and keeps your system Python clean.

```bash
# Create a virtual environment
python3 -m venv venv

# Activate it (Linux / macOS / Kali)
source venv/bin/activate

# Activate it (Windows)
venv\Scripts\activate

# Your prompt changes to show the active env:
# (venv) user@kali:~$

# Install packages inside the virtual environment
pip install requests

# Deactivate when done
deactivate
```

**Why this matters:** When you activate a virtual environment, `pip` and `python` inside it point only to that environment. Packages installed there are isolated and won't affect system Python.

---

### Installing from GitHub Directly

```bash
# Install directly from a GitHub repo (latest commit)
pip install git+https://github.com/user/repo.git

# Install a specific branch
pip install git+https://github.com/user/repo.git@main

# Install a specific tag or commit
pip install git+https://github.com/user/repo.git@v1.2.0
```

---

### Installing in Editable / Dev Mode

Used when you are developing a package and want changes to the source to take effect immediately without reinstalling.

```bash
pip install -e .
```

---

### Modules vs Packages

| Term | Definition |
|---|---|
| **Module** | A single `.py` file containing functions, classes, or variables |
| **Package** | A directory of modules with an `__init__.py` file |
| **Library** | A collection of packages (often used loosely to mean any installable third-party code) |

```python
# Importing a module from the standard library
import os
import sys
import json

# Importing a third-party package
import requests
from bs4 import BeautifulSoup

# Importing a specific function from a module
from pathlib import Path
```

---

### Standard Library vs Third-Party

**Standard library** — ships with Python, no installation needed:
- `os`, `sys`, `json`, `re`, `socket`, `subprocess`, `hashlib`, `base64`, `http`, `urllib`, `pathlib`, `argparse`, `logging`

**Third-party** — must be installed via pip:
- `requests`, `flask`, `django`, `numpy`, `pandas`, `scapy`, `pwntools`, `paramiko`, `cryptography`

---

### Hacker / Security Perspective

**Useful packages for offensive security work:**

| Package | Use |
|---|---|
| `requests` | HTTP requests, web scraping, API interaction |
| `pwntools` | CTF exploitation framework — buffer overflows, ROP chains, shellcode |
| `scapy` | Packet crafting, sniffing, network fuzzing |
| `paramiko` | SSH client/server in Python |
| `impacket` | Windows protocol exploitation (SMB, Kerberos, NTLM) |
| `cryptography` | Cryptographic operations |
| `beautifulsoup4` | HTML parsing — useful for scraping and recon |
| `sqlmap` (tool, not package) | SQL injection automation |
| `python-nmap` | Nmap wrapper for Python |

```bash
# Install a security-focused toolkit
pip install pwntools
pip install scapy
pip install impacket
pip install requests beautifulsoup4
```

**Note on Kali Linux:** Many security packages come pre-installed or are available via `apt` alongside pip. When both are available, prefer `apt` for system tools and `pip` inside a virtual environment for project-specific dependencies.

```bash
# Example: install impacket on Kali via apt
sudo apt install python3-impacket

# Or via pip in a venv
python3 -m venv impacket-env
source impacket-env/bin/activate
pip install impacket
```

---

> **Note:** Always verify packages before installing in a security context — typosquatting attacks on PyPI are real. Confirm the package name, author, and download count on [https://pypi.org](https://pypi.org) before running `pip install`.
