---
share: true
title: "Python"
---

Comments start with `#`

Use `\` to join lines where it would otherwise be ambiguous:

```python
if 1900 < year < 2100 and 1 <= month <= 12 \
   and 1 <= day <= 31 and 0 <= hour < 24 \
   and 0 <= minute < 60 and 0 <= second < 60:   # Looks like a valid date
        return 1
```

Names starting with `__` are mangled to avoid name clashes

### Operators

`**` is exponentiation
`//` is floor division
`@` is an overloadable operator for matrix multiplication (Python 3.5)
`~` is unary binary not / boolean invert
`:=` is just an assignment expression, a regular ` = ` can't be used inside expressions
`is` / `is not` tests object identity, i.e. `x is y` -> `id(x) == id(y)`

### Assignment

```python
>>> l = [1, 2, 3, 4, 5]
>>> head, *tail = l
>>> headb
1
>>> tail
[2, 3, 4, 5]
```

```python
>>> string = "Hello!"
>>> *start, last = string
>>> start
['H', 'e', 'l', 'l', 'o']
>>> last
'!'
```

```python
x = 123
print(x) # 123
del x
print(x) # NameError: name 'x' is not defined
```

### Assert

```python
assert x == y, ':('

if __debug__:
    if not x == y: raise AssertionError(':(')
```

### Basic Control Flow

* `pass`: no-op
* `yield x`: Emit x from generator
* `yield from x` Emit all values in iterable x
* `return` 
* `raise`
* `break`
* `continue`

```python
if x < y: print(x); print(y);
# Is the same as
if x < y:
  print(x)
  print(y)
```

```python
if expression1:
   ...
elif expression2:
   ...
else:
   ...
```

```python
while condition:
  ...
```

```python
for x, y in {'foo': 'bar'}.items():
    print(f'{x!r}: {y!r}')
```

```python
try:
    ...
except E as n:
    ...
except:
    ...
finally:
    ...
else:
    # Only reachable if the try block completed normally, and
    # excepts above do not handle exceptions thrown here
    ...
```

```python
try:
...     raise ExceptionGroup("eg",
...         [ValueError(1), TypeError(2), OSError(3), OSError(4)])
... except* TypeError as e:
...     print(f'caught {type(e)} with nested {e.exceptions}')
... except* OSError as e:
...     print(f'caught {type(e)} with nested {e.exceptions}')
...
# caught <class 'ExceptionGroup'> with nested (TypeError(2),)
# caught <class 'ExceptionGroup'> with nested (OSError(3), OSError(4))
#  + Exception Group Traceback (most recent call last):
#  |   File "<stdin>", line 2, in <module>
#  | ExceptionGroup: eg
#  +-+---------------- 1 ----------------
#    | ValueError: 1
#    +------------------------------------
```

```python
# Matching specific values
match command.split():
    case ["quit"]:
        print("Goodbye!")
        quit_game()
    case ["look"]:
        current_room.describe()
    case ["get", obj]:
        character.get(obj, current_room)
    case ["go", direction]:
        current_room = current_room.neighbor(direction)
    # The rest of your commands go here

# Adding a wildcard
match command.split():
    case ["quit"]: ... # Code omitted for brevity
    case ["go", direction]: ...
    case ["drop", *objects]: ...
    ... # Other cases
    case _:
        print(f"Sorry, I couldn't understand {command!r}")

# Or patterns
match command.split():
    ... # Other cases
    case ["north"] | ["go", "north"]:
        current_room = current_room.neighbor("north")
    case ["get", obj] | ["pick", "up", obj] | ["pick", obj, "up"]:
        ... # Code for picking up the given object

# Capturing matched sub-patterns
match command.split():
    case ["go", ("north" | "south" | "east" | "west") as direction]:
        current_room = current_room.neighbor(direction)

# Adding conditions to patterns
match command.split():
    case ["go", direction] if direction in current_room.exits:
        current_room = current_room.neighbor(direction)
    case ["go", _]:
        print("Sorry, you can't go that way")
```

PEP 636 – Structural Pattern Matching: Tutorial: https://peps.python.org/pep-0636/

### Function definitions

```python
# Decoratiors are syntactic sugar

@staticmethod
def f(arg):
    ...

# Is the same as:

def f(arg):
    ...
f = staticmethod(f)
```

```python
# / dissalows fun(x=y)
def fun(x,/):
    ...

fun(x=123) # TypeError: fun() got some positional-only arguments passed as keyword arguments: 'x'

# * dissalows fun(x)
def fun(*, x):
    ...

fun(123) # TypeError: fun() takes 0 positional arguments but 1 was given
```

```python
# Dynamic positional arguments
def fun(*x):
    print(repr(x))

fun(1, 2, 3) # Prints (1, 2, 3)

# Dynamic keyword arguments 
def fun(**x):
    print(repr(x))

fun(x=123, y=456) # Prints {'x': 123, 'y': 456}
```

```python
# Default argument
def fun(x: int = 69):
    return x * 2
```

### Classes

```python
class Foo:
    ...

# Inherit from Bar
class Foo(Bar):
    ...
```

`@staticmethod` turns a method into one that doesn't have a `self` parameter, somehow

### Builtin types

* `None`
* `NotImplemented`
* `Ellipsis` / `...`: Basically a TODO
* `numbers.Number`
	* `numbers.Integral`
		* `int`
		* `bool`
	* `numbers.Real` (double)
	* `numbers.Complex`
* Sequences: finite lists, indexable, sliceable
	* Immutable sequences
		* Strings
		* Tuples
		* Bytes
	* Mutable sequences
		* Lists
		* `bytearray`
		* `array.array`
* `set`: https://docs.python.org/3/library/stdtypes.html#set
* `frozenset`
* `dict`

### Strings

Byte literals start with `b` and produce a `bytes` instead of a `str`:

```python
b'abcd'
```

Strings in python support multiple encodings, before 3.3 it was just utf-16 but now it chooses whichever the widest character is.

`\N{name}` can be used to refer to a character in the unicode database by name:

```python
>>> '\N{FULL BLOCK}'
'█'
```

Python uses ref counting????

String interpolation:

```python
>>> name = "Fred"
>>> f"He said his name is {name!r}."
"He said his name is 'Fred'."
>>> f"He said his name is {repr(name)}."  # repr() is equivalent to !r
"He said his name is 'Fred'."
>>> width = 10
>>> precision = 4
>>> value = decimal.Decimal("12.34567")
>>> f"result: {value:{width}.{precision}}"  # nested fields
'result:      12.35'
>>> today = datetime(year=2017, month=1, day=27)
>>> f"{today:%B %d, %Y}"  # using date format specifier
'January 27, 2017'
>>> f"{today=:%B %d, %Y}" # using date format specifier and debugging
'today=January 27, 2017'
>>> number = 1024
>>> f"{number:#0x}"  # using integer format specifier
'0x400'
>>> foo = "bar"
>>> f"{ foo = }" # preserves whitespace
" foo = 'bar'"
>>> line = "The mill's closed"
>>> f"{line = }"
'line = "The mill\'s closed"'
>>> f"{line = :20}"
"line = The mill's closed   "
>>> f"{line = !r:20}"
'line = "The mill\'s closed" '
```

Formatting rules: https://docs.python.org/3/library/string.html#formatspec

`str.encode` converts a `str` to `bytes` and `bytes.decode` does the opposite

`array.array` is like dart typed data: https://docs.python.org/3/library/array.html#module-array

### Slices

```python
>>> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9][0]
0
>>> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9][0:2]
[0, 1]
>>> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9][:2]
[0, 1]
>>> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9][0::2]
[0, 2, 4, 6, 8]
```

### Dicts

* `list(d)` returns a list of keys
* `len(d)` returns the number of entries
* `__missing__` is called if a key is not present, otherwise throw a `KeyError`
* `del d[key]` deletes an entry
* `iter(d)` returns an iterator over keys
* `d.get(key[, default])` can get a value or return a default
* `d.items()` returns a key, value tuple dictview of the dict
* `d | other` merges two maps

### with/as

```python
with EXPRESSION as TARGET:
    SUITE
```
is semantically equivalent to:
```python
manager = (EXPRESSION)
enter = type(manager).__enter__
exit = type(manager).__exit__
value = enter(manager)
hit_except = False

try:
    TARGET = value
    SUITE
except:
    hit_except = True
    if not exit(manager, *sys.exc_info()):
        raise
finally:
    if not hit_except:
        exit(manager, None, None, None)
```

### Types

`T[X, Y]` creates a `GenericAlias` representing `T` parametrized by `X` and `Y`, for example `list[float]` is a float list and `dict[str, int]` is a map from strings to ints, these types are usually only checked by the linter and not at runtime

`X | Y` is a union type, `X | None` describes an optional type

```python
>>> isinstance("", int | str)
True
>>> issubclass(str, int | str)
True
```

### Async

```python
import asyncio

>>> async def main():
...     print('hello')
...     await asyncio.sleep(1)
...     print('world')

>>> asyncio.run(main())
hello
world
```

### Exceptions

* All exceptions implement `BaseException`
* When an exception is thrown in an `except` or `finally`, it sets the new exception's `__context__` to the one being handled

```python
try:
...     print(1 / 0)
... except Exception as exc:
...     raise RuntimeError("Something bad happened") from exc
...
# Traceback (most recent call last):
#  File "<stdin>", line 2, in <module>
# ZeroDivisionError: division by zero
#
# The above exception was the direct cause of the following exception:
#
# Traceback (most recent call last):
#  File "<stdin>", line 4, in <module>
# RuntimeError: Something bad happened

try:
...     print(1 / 0)
... except:
...     raise RuntimeError("Something bad happened") from None
...
# Traceback (most recent call last):
#   File "<stdin>", line 4, in <module>
# RuntimeError: Something bad happened
```

### Generator Function

```python
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1

gen = infinite_sequence()
print([next(a) for _ in range(10)])
# [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Modules

```python
import foo                 # foo imported and bound locally
import foo.bar.baz         # foo, foo.bar, and foo.bar.baz imported, foo bound locally
import foo.bar.baz as fbb  # foo, foo.bar, and foo.bar.baz imported, foo.bar.baz bound as fbb
from foo.bar import baz    # foo, foo.bar, and foo.bar.baz imported, foo.bar.baz bound as baz
from foo import attr       # foo imported and foo.attr bound as attr
from foo import *
```

`nonlocal x` makes the current block reference a variable in the parent function
`global x` makes the current block assume x a global