#### Single Dispatch
A generic function where the implementation is chosen depending on the type of a single argument
```python
from functools import singledispatch

@singledispatch
def some_func(arg):
    raise ValueError(f" arg of type {type(arg)} not supported")

@some_func.register(int)
def _(arg: int):
    print("I got an integer")

@some_func.register(str)
def _(arg: str):
    print("I got a string")


some_func(1)  # this will invoke version for integer
some_func('2')   # this will call str version
some_func(['2'])   # this will throw a valueError
```