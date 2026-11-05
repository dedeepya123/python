# Python Object Model (Mental Model)

## Everything in Python is an Object

In Python, every piece of data is an **object**.

Examples:

```python
x = 10          # int object
name = "Bob"    # str object
lst = [1, 2]    # list object

def foo():
    pass        # function object

class Car:
    pass        # class object
```

Integers, strings, lists, functions, classes, tensors, and even modules are all objects.

---

## What is an Object?

An object is a piece of data that exists in memory and has:

1. **Value** → actual data
2. **Type** → what kind of object it is
3. **Identity** → unique identity (can be obtained using `id()`)

Example:

```python
x = 10

print(x)        # Value
print(type(x))  # Type
print(id(x))    # Identity
```

Example Output:

```python
10
<class 'int'>
140734568123456
```

(The identity value will vary on every run.)

### Mental Model

```text
        Object
    ┌─────────────┐
    │ Value = 10  │
    │ Type = int  │
    │ Id = 12345  │
    └─────────────┘
```

---

## What is a Variable?

A variable is simply a **name bound to an object**.

```python
x = 10
```

Here:

```text
x = variable name
10 = object
```

### Mental Model

```text
x ─────► Object(10)
```

The variable does not contain the object.

It only refers to it.

---

## What is a Reference?

A reference is the connection between a variable name and an object.

```python
x = 10
```

Mental Model:

```text
x ─────► Object(10)
```

The arrow represents the reference.

Think of the variable as a label attached to an object.

---

## Important Rule

### Variables Store References, Not Objects

Many beginners think:

```python
x = 10
```

means:

```text
x contains 10
```

A better Python mental model is:

```text
x refers to the object 10
```

or

```text
x ─────► Object(10)
```

---

## Multiple References Can Point to the Same Object

Example:

```python
a = [1, 2, 3]
b = a
```

Mental Model:

```text
a ──┐
    │
    ▼
 [1, 2, 3]
    ▲
    │
b ──┘
```

Both `a` and `b` point to the same list object.

---

## Changes Through One Reference Are Visible Through Another

```python
a = [1, 2, 3]
b = a

b.append(4)

print(a)
```

Output:

```python
[1, 2, 3, 4]
```

Why?

Because there is only one list object.

Mental Model:

```text
Before

a ──┐
    ▼
 [1,2,3]
    ▲
    │
b ──┘


After b.append(4)

a ──┐
    ▼
 [1,2,3,4]
    ▲
    │
b ──┘
```

Both names still point to the same object.

---

## Quick Summary

```text
Object
    A piece of data in memory having:
    - Value
    - Type
    - Identity

Variable
    A name bound to an object.

Reference
    The link between a variable name and an object.

Important Rule
    Variables do NOT store objects.
    Variables store references to objects.

Multiple variables can reference the same object.
```

---

## Golden Mental Model

Whenever you see:

```python
x = something
```

Do NOT think:

```text
x contains something
```

Think:

```text
x ─────► object
```

Examples:

```python
x = 10

x ─────► int object (10)
```

```python
name = "Bob"

name ─────► str object ("Bob")
```

```python
lst = [1, 2, 3]

lst ─────► list object ([1,2,3])
```

This single mental model explains:

- Variable assignment
- Function arguments
- Mutable vs immutable objects
- Lists and dictionaries
- Classes and objects
- PyTorch tensors
- `nn.Module`
- Shallow copy vs deep copy
- Most of Python's behavior

**Golden Rule:**

```text
Variables are names.
Objects hold data.
References connect names to objects.
```
