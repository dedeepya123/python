# 04 - Functions as Objects

## Prerequisite

From previous notes:

```text
Everything in Python is an object.

Objects have:
    - Value
    - Type
    - Identity

Every object is an instance of some class.

Classes define behavior through attributes and methods.
```

Natural Question:

> If everything in Python is an object, does that include functions?

Answer:

```text
Yes.

Functions are objects too.
```

---

# Functions Are Objects

Consider:

```python
def greet():
    print("Hello")
```

Most beginners think:

```text
I created a function.
```

A better mental model is:

```text
I created a function object.
```

Mental Model:

```text
greet
  │
  ▼
Function Object
```

Just like:

```python
x = 10
```

creates:

```text
x
 │
 ▼
int object
```

Similarly:

```python
def greet():
    pass
```

creates:

```text
greet
 │
 ▼
function object
```

---

# Verifying That Functions Are Objects

```python
def greet():
    pass

print(type(greet))
```

Output:

```python
<class 'function'>
```

This tells us:

```text
greet is an object.

greet is an instance of the class function.
```

Mental Model:

```text
greet
  │
  ▼
function object
  │
  ▼
function class
  │
  ▼
object
```

---

# Function Names Are Variables

The name of a function is simply a variable pointing to a function object.

Example:

```python
def greet():
    print("hello")
```

Mental Model:

```text
greet
  │
  ▼
Function Object
```

---

# Multiple Variables Can Refer to the Same Function

```python
def greet():
    print("hello")

another = greet
```

Mental Model:

```text
greet ───┐
         │
         ▼
    Function Object
         ▲
         │
another ─┘
```

Both names point to the same function object.

Example:

```python
another()
```

Output:

```python
hello
```

---

# Function Invocation

Consider:

```python
greet()
```

This looks like special syntax.

Under the hood, function objects implement:

```python
__call__()
```

Mental Model:

```text
greet()
   │
   ▼
greet.__call__()
```

Just like:

```python
a + b
```

becomes:

```python
a.__add__(b)
```

Function calls are powered by the object's `__call__()` mechanism.

---

# Functions Have Attributes

Since functions are objects, they can store information.

Example:

```python
def greet():
    pass

print(greet.__name__)
```

Output:

```python
greet
```

---

## __doc__

```python
def greet():
    """Say hello."""
    pass

print(greet.__doc__)
```

Output:

```python
Say hello.
```

---

## __module__

```python
print(greet.__module__)
```

Output:

```python
__main__
```

---

## Common Function Attributes

```text
Function Object

├── __name__
├── __doc__
├── __module__
├── __defaults__
├── __annotations__
└── __call__()
```

---

# Functions Can Be Assigned to Variables

```python
def greet():
    print("Hello")

f = greet
```

Mental Model:

```text
f ─────► Function Object
```

Now:

```python
f()
```

works exactly like:

```python
greet()
```

---

# Functions Can Be Stored in Data Structures

List:

```python
funcs = [print, len]
```

Dictionary:

```python
ops = {
    "print": print,
    "len": len
}
```

Mental Model:

```text
List
 ├── Function Object
 ├── Function Object
 └── Function Object
```

Since functions are objects, they can be stored anywhere objects can be stored.

---

# Functions Can Be Passed as Arguments

Example:

```python
def greet():
    print("Hello")


def execute(fn):
    fn()
```

Usage:

```python
execute(greet)
```

Output:

```python
Hello
```

Mental Model:

```text
greet
   │
   ▼
 passed into
   ▼
execute(fn)
```

The parameter `fn` becomes another reference to the same function object.

---

# Functions Can Be Returned

Example:

```python
def get_printer():
    return print
```

Usage:

```python
p = get_printer()

p("Hello")
```

Output:

```python
Hello
```

Mental Model:

```text
Function
   │
   ▼
Returns Function Object
```

Functions can create and return other functions.

---

# First-Class Objects

Functions in Python are called:

```text
First-Class Objects
```

Meaning they can:

✅ Be assigned to variables

✅ Be passed as arguments

✅ Be returned from functions

✅ Be stored in containers

✅ Have attributes

✅ Be created dynamically

---

# Functions Can Hold State

Functions can have custom attributes attached to them.

Example:

```python
def greet():
    pass

greet.count = 10

print(greet.count)
```

Output:

```python
10
```

Mental Model:

```text
Function Object

Attributes:

count = 10
```

This works because functions are ordinary objects.

---

# What Python Creates Internally

When Python sees:

```python
def greet():
    print("hello")
```

It roughly creates:

```text
Function Object

Code:
    print("hello")

Attributes:
    __name__ = "greet"
    __module__ = "__main__"
    __defaults__ = ...
    __annotations__ = ...
```

and binds:

```text
greet ─────► Function Object
```

---

# Why This Matters

Many advanced Python features rely on functions being objects.

Examples:

```python
sorted(data, key=my_function)
```

Function passed as argument.

---

```python
map(my_function, data)
```

Function passed as argument.

---

```python
decorator(function)
```

Function passed into another function.

---

```python
callback()
```

Function stored and executed later.

---

# PyTorch Examples

PyTorch heavily relies on functions as objects.

Forward Hooks:

```python
model.register_forward_hook(hook_fn)
```

You pass a function object.

---

Applying Functions:

```python
model.apply(init_weights)
```

You pass a function object.

---

Dataset Transforms:

```python
dataset.map(transform_fn)
```

You pass a function object.

---

# Function Hierarchy

```text
greet
  │
  ▼
Function Object
  │
  ▼
function class
  │
  ▼
object
```

---

# Quick Summary

```text
Functions are objects.

Function names are variables pointing to function objects.

Functions are instances of the class function.

Functions can:

    - Be stored
    - Be passed
    - Be returned
    - Have attributes
    - Have methods

Functions support __call__(), which enables:

    fn()
```

---

# Golden Mental Models

### Mental Model 1

```text
A function is an object.
```

---

### Mental Model 2

```text
Function names are variables.

greet

is simply a variable pointing to a function object.
```

---

### Mental Model 3

```text
Functions are instances of the class function.
```

```text
greet
  │
  ▼
function
  │
  ▼
object
```

---

### Mental Model 4

```text
Calling a function

greet()

approximately becomes

greet.__call__()
```

---

### Mental Model 5

```text
Functions can be:

stored
passed
returned
assigned

because they are normal Python objects.
```

---

# Final Picture

```text
greet
   │
   ▼
Function Object
   │
   ├── Code
   ├── __name__
   ├── __doc__
   ├── __module__
   ├── __defaults__
   ├── __annotations__
   └── __call__()
   │
   ▼
function class
   │
   ▼
object
```

## Golden Rule

```text
A function is not merely executable code.

A function is an object that contains executable code.

The function name is simply a variable that references that object.
```
